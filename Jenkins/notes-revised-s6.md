# Jenkins Session 6: Docker Integration and Image Pipelines

---

## 1. Docker-Jenkins Integration

### Why Use Docker in Jenkins?
- **Consistency**: Build and test in the same container image
- **Isolation**: No dependencies on Jenkins agent host
- **Portability**: Same image runs in staging and production
- **Speed**: Layer caching reduces build times

### Integration Approaches
1. **Docker Installed on Agent**:
   - Jenkins agents have Docker CLI and daemon
   - Build images directly on agent host
2. **Docker-in-Docker (dind)**:
   - Run Docker daemon inside a container
   - Useful for ephemeral build agents
3. **Kubernetes Docker Agents**:
   - Use Kubernetes plugin to spin up dind containers
   - Scale build capacity dynamically

---

## 2. Configuring Docker for Jenkins

### Method 1: Docker Group Membership
```bash
# Add Jenkins user to docker group (Linux)
sudo usermod -aG docker jenkins
```
- Allows Jenkins to run Docker commands without sudo
- Ensure group membership is effective by restarting Jenkins or logging out/in

### Method 2: Docker Socket Permissions
```bash
# Set world-writable permissions (not secure for production)
sudo chmod 666 /var/run/docker.sock
```
- Grants all users access to Docker daemon
- Acceptable for isolated CI environments, not multi-tenant

### Verifying Configuration
```bash
# As jenkins user
docker version
docker run hello-world
```
- Confirm Jenkins agent can list images and run containers

---

## 3. Writing a Dockerfile

### Python Application Example

**Project Structure:**
```
my-python-app/
├── app.py
├── requirements.txt
├── Dockerfile
└── tests/
    └── test_app.py
```

**app.py:**
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from Docker!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

**requirements.txt:**
```
flask==2.3.2
pytest==7.4.0
```

**Dockerfile:**
```dockerfile
# Base image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy dependencies and install
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

# Expose port
EXPOSE 5000

# Default command
CMD ["python", "app.py"]
```

### Best Practices
- Use slim or alpine base images to reduce size
- Leverage multi-stage builds for optimized artifacts
- Pin dependency versions for reproducible builds
- Minimize layers by combining commands
- Use `.dockerignore` to exclude unnecessary files

---

## 4. Jenkins Pipeline for Docker Images

### Declarative Pipeline Example
```groovy
pipeline {
    agent { label 'docker-enabled' }
    environment {
        IMAGE_NAME = 'registry.company.com/my-python-app'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}", "./")
                }
            }
        }
        stage('Run Tests in Container') {
            steps {
                script {
                    docker.image("${IMAGE_NAME}:${IMAGE_TAG}").inside {
                        sh 'pytest tests/'
                    }
                }
            }
            post {
                always {
                    junit 'tests/**/*.xml'
                }
            }
        }
        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.company.com', 'registry-credentials') {
                        docker.image("${IMAGE_NAME}:${IMAGE_TAG}").push()
                    }
                }
            }
        }
    }
    post {
        cleanup {
            deleteDir() // Clean workspace
        }
    }
}
```

### Scripted Pipeline Example
```groovy
node('docker') {
    stage('Checkout') {
        checkout scm
    }
    stage('Build Image') {
        sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
    }
    stage('Test') {
        sh "docker run --rm ${IMAGE_NAME}:${BUILD_NUMBER} pytest tests/"
    }
    stage('Push') {
        withCredentials([usernamePassword(credentialsId: 'registry-credentials', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
            sh "docker login -u $USER -p $PASS registry.company.com"
            sh "docker push ${IMAGE_NAME}:${BUILD_NUMBER}"
        }
    }
    stage('Cleanup') {
        sh "docker rmi ${IMAGE_NAME}:${BUILD_NUMBER}"
    }
}
```

---

## 5. Advanced Docker Pipeline Techniques

### Multi-Stage Builds
```dockerfile
# Stage 1: Build
FROM python:3.9 as builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --user -r requirements.txt
COPY . .
RUN pytest tests/ --junitxml=results.xml

# Stage 2: Runtime
FROM python:3.9-slim
WORKDIR /app
COPY --from=builder /root/.local /root/.local
ENV PATH=/root/.local/bin:$PATH
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]
```

### Layer Caching
- Use stable base images
- Copy `requirements.txt` separately to leverage cache
- Avoid `ADD` or `COPY . .` before installing dependencies

### Dynamic Docker Arguments
```groovy
stage('Build Image') {
    steps {
        script {
            def buildArgs = ['ENV=production', "VERSION=${BUILD_NUMBER}"]
            docker.build("${IMAGE_NAME}:${IMAGE_TAG}", "--build-arg ${buildArgs.join(' --build-arg ')} .")
        }
    }
}
```

---

## 6. Hands-On Exercise

**Task:** Build a Python Flask application pipeline with Docker

1. **Application Code:** Use provided `app.py` and `requirements.txt`
2. **Dockerfile:** Author a multi-stage Dockerfile with tests
3. **Jenkins Pipeline:** Create a declarative pipeline that:
   - Checks out code
   - Builds Docker image
   - Runs tests inside container
   - Pushes image to registry
   - Cleans up local images
4. **Credentials:** Securely configure registry credentials in Jenkins
5. **Optimization:** Implement layer caching and multi-stage build
6. **Reporting:** Publish test results with JUnit

---

## 7. Summary and Best Practices

- Ensure Jenkins agent has Docker access (docker group or socket perms)
- Write efficient Dockerfiles (multi-stage, minimal layers)
- Leverage `docker.build` and `inside` for pipeline simplicity
- Secure registry credentials with Jenkins Credentials store
- Use JUnit reports for test visibility
- Clean workspace and images to save disk space

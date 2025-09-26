# Jenkins Training Notes

Jenkins is an open-source automation server that helps developers and teams automate various stages of the software development process. Written in Java, Jenkins provides hundreds of plugins that extend its functionality and enable integration with virtually any tool in the DevOps ecosystem.

**Key Features:**
- Free and open-source with active community support
- Cross-platform compatibility (Linux, Windows, macOS
- Extensive plugin ecosystem (1800+ plugins available)
- Built-in web interface for easy management
- Supports version control systems like Git, SVN, Mercurial
- Integration with build tools like Maven, Gradle, Ant

**Primary Use Cases:**
- Automating repetitive tasks like building, testing, and deploying software
- Implementing Continuous Integration and Continuous Delivery pipelines
- Orchestrating complex workflows across multiple development stages
- Managing distributed builds across multiple machines

## What is Continuous Integration (CI)?

Continuous Integration is the practice of automatically and frequently integrating code changes into a shared source code repository. Developers merge their changes back to the main branch as often as possible, with automated builds and tests running against each integration.

**How CI Works:**
1. Developers commit code changes to version control (e.g., Git)
2. Automated tools validate that the code is error-free
3. Build and test processes run automatically
4. Integration issues are detected early in the development cycle

**Benefits of CI:**
- Early detection of bugs and integration conflicts
- Faster feedback loops for development teams
- Reduced "integration hell" from delayed merges
- Improved code quality through automated testing
- Constant feedback on software status

## What is Continuous Delivery (CD)?

Continuous Delivery extends CI by automatically preparing code changes for release to production. It ensures that software can be released at any time through a reliable, repeatable deployment process.

**How CD Works:**
1. Code passes through CI process successfully
2. Automated tools build software into deployable artifacts
3. Code moves through automated staging and testing environments
4. Software is always maintained in a deployable state
5. Deployment can be triggered manually or automatically

**Benefits of CD:**
- Shorter release cycles with faster time-to-market
- Lower risk of errors in production deployments
- Easier rollbacks when issues occur
- More frequent integration of user feedback
- Reduced manual intervention in deployment process

**CD vs Continuous Deployment:**
- **Continuous Delivery:** Code is automatically prepared for release but requires manual approval for production deployment
- **Continuous Deployment:** Every change that passes the pipeline is automatically released to production without human intervention

## Jenkins Architecture

Jenkins uses a master-slave architecture (now called controller-agent) to manage distributed builds and scale workloads.

**Core Components:**

### Jenkins Controller (Master)
- Central control point that manages the entire Jenkins environment
- Schedules and dispatches build jobs to available agents
- Maintains build configurations, plugins, and user interfaces
- Handles the web UI and REST API
- Stores job configurations and build histories
- Manages security and user permissions

### Jenkins Agents (Slaves)
- Worker machines that execute the actual build tasks
- Can run on different operating systems (Linux, Windows, macOS)
- Communicate with the controller via TCP/IP protocols
- Execute jobs assigned by the master node
- Report build status and logs back to the controller

### Supporting Components
- **Plugins:** Extend Jenkins functionality for various integrations
- **Jobs/Pipelines:** Configurable tasks that define build, test, and deployment processes
- **Workspaces:** Temporary directories where builds are executed
- **Build Queue:** Manages pending jobs waiting for execution

**Architecture Benefits:**
- **Scalability:** Distribute workload across multiple machines
- **Performance:** Parallel execution of multiple builds
- **Environment Diversity:** Different agents for different platforms
- **Resource Optimization:** Efficient utilization of available hardware
- **Fault Tolerance:** Builds continue even if individual agents fail

## Quick Setup: Jenkins on Local Machine Using Docker

Setting up Jenkins locally with Docker is straightforward and ideal for development, testing, or training purposes.[10][11]

**Prerequisites:**
- Docker installed on your machine
- Minimum 4GB RAM recommended for small teams
- 10GB+ available disk space
- Modern web browser (Chrome, Firefox, Edge, Safari)

**Step 1: Pull Jenkins Docker Image**
```bash
docker pull jenkins/jenkins:latest
```

**Step 2: Create Docker Network (Optional but Recommended)**
```bash
docker network create jenkins
```

**Step 3: Run Jenkins Container**
```bash
docker run -d \
  --name jenkins-server \
  --restart=on-failure \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:latest
```

**Command Explanation:**
- `-d`: Run in detached mode (background)
- `--name`: Assign a name to the container
- `--restart=on-failure`: Automatically restart if container stops
- `-p 8080:8080`: Map web interface port
- `-p 50000:50000`: Map agent communication port
- `-v jenkins_home:/var/jenkins_home`: Persist data in Docker volume

**Step 4: Access Jenkins**
1. Open browser and navigate to `http://localhost:8080`
2. Retrieve initial admin password:
   ```bash
   docker exec jenkins-server cat /var/jenkins_home/secrets/initialAdminPasswor```  ```
3. Complete setup wizard:
   - Install suggested plugins
   - Create admin user account
   - Configure Jenkins URL

**Step 5: Container Management**
```bash
# Stop Jenkins
docker stop jenkins-server

# Start Jenkins
docker start jenkins-server

# View logs
docker logs jenkins-server

# Remove container (data persists in volume)
docker rm jenkins-server
```

## Setting Up Simple Pipelines

Jenkins pipelines define automated workflows as code, making CI/CD processes repeatable and version-controlled.

**Pipeline Types:**
- **Freestyle Projects:** Point-and-click configuration through web UI
- **Pipeline Projects:** Code-based configuration using Jenkinsfile

### Creating a Basic Pipeline

**Step 1: Create New Pipeline Job**
1. Click "New Item" in Jenkins dashboard
2. Enter job name (e.g., "Hello-World-Pipeline")
3. Select "Pipeline" project type
4. Click "OK"

**Step 2: Configure Pipeline Script**
In the Pipeline section, select "Pipeline script" and add:

```groovy
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                echo 'hello world'
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline execution```mpleted'
        }
        success {
            echo 'Succeeded!'     
        }
        failure {
            echo 'Build failed'
        }
    }
}
```

**Step 3: Save and Execute**
1. Click "Save"
2. Click "Build Now"
3. Check "Console Output" for execution results

**Pipeline Components Explained:**
- **pipeline{}:** Root block defining the entire pipeline
- **agent any:** Run on any available agent
- **stages{}:** Container for all pipeline stages
- **stage():** Individual phase of the pipeline
- **steps{}:** Commands to execute within a stage

## Setting Up Pipeline with GitHub Source

Integrating Jenkins with GitHub enables automatic triggering of builds when code changes occur

### Prerequisites
- GitHub account with a repository
- Git plugin installed in Jenkins (included in suggested plugins)
- Jenkins accessible from internet (for webhooks) or polling configured

### Step 1: Create GitHub Repository Pipeline

**Create Pipeline Job:**
1. New Item > Pipeline > Enter name (e.g., "GitHub-Pipeline")
2. In Pipeline section, select "Pipeline script from SCM"
3. Choose "Git" from SCM dropdown
4. Enter repository URL: `https://github.com/yourusername/your-repo.git`
5. Specify branch (e.g., `main` or `master`)
6. Set Script Path: `Jenkinsfile` (default)

### Step 2: Create Jenkinsfile in Repository

Create a file named `Jenkinsfile` in your repository root:

```groovy
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                echo 'hello world'
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline execution```mpleted'
        }
        success {
            echo 'Succeeded!'     
        }
        failure {
            echo 'Build failed'
        }
    }
}
```

### Step 3: Configure GitHub Webhook (Optional)

For automatic build triggering on code push:

**In GitHub Repository:**
1. Go to Settings > Webhooks
2. Click "Add webhook"
3. Payload URL: `http://your-jenkins-url/github-webhook/`
4. Content type: `application/json`
5. Select "Just the push event"
6. Ensure "Active" is checked
7. Click "Add webhook"

**In Jenkins Job:**
1. Go to job configuration
2. Under "Build Triggers," select "GitHub hook trigger for GITScm polling"
3. Save configuration

### Step 4: Alternative - Polling Configuration

If webhooks aren't feasible, configure polling:

1. In job configuration, select "Poll SCM" under Build Triggers
2. Set schedule using cron syntax:
   - `H/5 * * * *` (every 5 minutes)
   - `H/15 * * * *` (every 15 minutes)
3. Jenkins will check repository for changes at specified intervals

### Step 5: Test the Pipeline

1. Make a change to your repository
2. Commit and push to GitHub
3. Observe Jenkins automatically triggering the build (if webhook configured)
4. Or manually trigger with "Build Now"
5. Check build console output for execution details

**Advanced Pipeline Features:**
- **Parallel stages:** Execute multiple stages simultaneously
- **Conditional execution:** Run stages based on specific criteria
- **Environment variables:** Use dynamic values in pipeline
- **Artifact archiving:** Store build outputs for later use
- **Notification integration:** Send alerts via email, Slack, etc.

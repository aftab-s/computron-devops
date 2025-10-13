# Jenkins Session 4: Deployment Acceleration - Comprehensive Notes

## Session Overview

This session focuses on advanced Jenkins pipeline features that accelerate deployment processes. We'll explore parallel stages, environment variables, credential management, and shared libraries to build more efficient and maintainable CI/CD pipelines.

## Learning Objectives

After completing this session, you'll be able to:
- Implement parallel stages to reduce pipeline execution time
- Manage environment variables effectively across pipelines
- Handle credentials securely in Jenkins pipelines
- Understand and create basic shared libraries
- Apply deployment acceleration techniques
- Compare multi-staged pipelines with normal pipelines

---

## Multibranch Pipelines Review

### Key Concepts from Session 3

**Multibranch Pipeline Benefits:**
- Each branch with its own Jenkinsfile is built independently
- Simplifies handling of feature branches and pull requests
- Automatically detects branches in a repository
- Provides isolation between different development streams

**Implementation Recap:**
```groovy
// Branch-specific behavior in Jenkinsfile
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo "Building branch: ${env.BRANCH_NAME}"
            }
        }
        stage('Deploy to Production') {
            when { branch 'main' }
            steps {
                echo 'Deploying to production...'
            }
        }
    }
}
```

### Python Application Integration

From our previous session, we established a solid foundation for Python application builds:
- Virtual environment management
- Dependency installation
- Testing with pytest
- Code quality checks
- Build artifact creation

Now we'll enhance these pipelines with advanced features.

---

## Scheduled Triggers Deep Dive

### Advanced Cron Expressions

Building on the basic cron syntax from Session 3, let's explore more sophisticated scheduling patterns:

**Complex Scheduling Examples:**
```bash
# Every 15 minutes during business hours on weekdays
H/15 8-17 * * 1-5

# Multiple times per day with load balancing
H 6,12,18 * * *

# Monthly on the first Monday
H H 1-7 * 1

# Quarterly builds
H H 1 1,4,7,10 *

# Weekend maintenance builds
H 2 * * 0,6

# Every 6 hours starting at 2 AM
H 2-23/6 * * *
```

**Environment-Specific Scheduling:**
```groovy
pipeline {
    agent any
    
    triggers {
        // Different schedules based on branch
        cron(getBuildSchedule())
    }
    
    stages {
        stage('Scheduled Build') {
            steps {
                script {
                    echo "Branch: ${env.BRANCH_NAME}"
                    echo "Schedule: ${getBuildSchedule()}"
                }
            }
        }
    }
}

def getBuildSchedule() {
    switch(env.BRANCH_NAME) {
        case 'main':
            return 'H 2 * * *'      // Daily at 2 AM
        case 'develop':
            return 'H 2 * * 1-5'    // Weekdays at 2 AM  
        case ~/feature\/.*/:
            return 'H 2 * * 0'      // Sundays only
        default:
            return ''               // No automatic builds
    }
}
```

---

## Parallel Stages Implementation

### Understanding Parallel Execution

Parallel stages allow multiple stages to run simultaneously, significantly reducing pipeline execution time. This is particularly effective for:
- Independent testing phases
- Multi-platform builds
- Different types of quality checks
- Deployment to multiple environments

### Basic Parallel Stage Structure

```groovy
pipeline {
    agent any
    
    environment {
        APP_NAME = 'demo-parallel-stages'
    }
    
    stages {
        stage('Build') {
            steps { 
                echo 'Building application...' 
                echo "Application Name: ${APP_NAME}"
            }
        }
        
        stage('Test') {
            parallel {
                stage('Unit Tests') {
                    steps { 
                        echo 'Running unit tests...'
                        sh 'sleep 10 && echo "Unit tests completed"'
                    }
                }
                stage('Integration Tests') {
                    steps { 
                        echo 'Running integration tests...'
                        sh 'sleep 15 && echo "Integration tests completed"' 
                    }
                }
                stage('Security Tests') {
                    steps {
                        echo 'Running security tests...'
                        sh 'sleep 12 && echo "Security tests completed"'
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
            }
        }
    }
}
```

### Advanced Parallel Patterns

**Multi-Platform Builds:**
```groovy
pipeline {
    agent none
    
    stages {
        stage('Build') {
            parallel {
                stage('Linux Build') {
                    agent { label 'linux' }
                    steps {
                        sh '''
                            echo "Building on Linux"
                            python3 -m venv venv
                            . venv/bin/activate
                            pip install -r requirements.txt
                            python setup.py build
                        '''
                    }
                }
                stage('Windows Build') {
                    agent { label 'windows' }
                    steps {
                        bat '''
                            echo "Building on Windows"
                            python -m venv venv
                            venv\\Scripts\\activate.bat
                            pip install -r requirements.txt
                            python setup.py build
                        '''
                    }
                }
                stage('macOS Build') {
                    agent { label 'macos' }
                    steps {
                        sh '''
                            echo "Building on macOS"
                            python3 -m venv venv
                            . venv/bin/activate
                            pip install -r requirements.txt
                            python setup.py build
                        '''
                    }
                }
            }
        }
    }
}
```

**Parallel Testing with Different Configurations:**
```groovy
stage('Compatibility Tests') {
    parallel {
        stage('Python 3.8') {
            agent {
                docker { image 'python:3.8' }
            }
            steps {
                sh '''
                    pip install -r requirements.txt
                    python -m pytest tests/ -v
                '''
            }
        }
        stage('Python 3.9') {
            agent {
                docker { image 'python:3.9' }
            }
            steps {
                sh '''
                    pip install -r requirements.txt
                    python -m pytest tests/ -v
                '''
            }
        }
        stage('Python 3.10') {
            agent {
                docker { image 'python:3.10' }
            }
            steps {
                sh '''
                    pip install -r requirements.txt
                    python -m pytest tests/ -v
                '''
            }
        }
    }
}
```

**Parallel Quality Gates:**
```groovy
stage('Quality Assurance') {
    parallel {
        stage('Code Coverage') {
            steps {
                sh '''
                    python -m pytest --cov=src --cov-report=xml
                    python -c "
                    import xml.etree.ElementTree as ET
                    tree = ET.parse('coverage.xml')
                    coverage = float(tree.getroot().attrib['line-rate']) * 100
                    print(f'Coverage: {coverage:.1f}%')
                    if coverage < 80:
                        raise Exception(f'Coverage {coverage:.1f}% below threshold 80%')
                    "
                '''
            }
        }
        stage('Code Quality') {
            steps {
                sh '''
                    flake8 src/ --max-line-length=88
                    black --check src/
                    mypy src/ --ignore-missing-imports
                '''
            }
        }
        stage('Security Scan') {
            steps {
                sh '''
                    safety check
                    bandit -r src/ -ll
                '''
            }
        }
        stage('Performance Test') {
            steps {
                sh '''
                    echo "Running performance benchmark..."
                    python -m pytest tests/performance/ -v
                '''
            }
        }
    }
}
```

### Handling Parallel Stage Results

**Collecting Results from Parallel Stages:**
```groovy
pipeline {
    agent any
    
    stages {
        stage('Parallel Analysis') {
            parallel {
                stage('Static Analysis') {
                    steps {
                        script {
                            env.STATIC_ANALYSIS_RESULT = sh(
                                script: 'flake8 src/ --count',
                                returnStdout: true
                            ).trim()
                        }
                    }
                }
                stage('Test Coverage') {
                    steps {
                        script {
                            env.COVERAGE_RESULT = sh(
                                script: 'python -m pytest --cov=src --cov-report=term-missing | grep TOTAL | awk \'{print $4}\'',
                                returnStdout: true
                            ).trim()
                        }
                    }
                }
            }
        }
        
        stage('Results Summary') {
            steps {
                script {
                    echo "Static Analysis Issues: ${env.STATIC_ANALYSIS_RESULT}"
                    echo "Test Coverage: ${env.COVERAGE_RESULT}"
                    
                    // Make decisions based on parallel stage results
                    def issues = env.STATIC_ANALYSIS_RESULT as Integer
                    if (issues > 0) {
                        unstable("Found ${issues} static analysis issues")
                    }
                }
            }
        }
    }
}
```

---

## Environment Variables Management

### Built-in Environment Variables

Jenkins provides numerous built-in environment variables that are invaluable for pipeline logic:

**Common Built-in Variables:**
```groovy
pipeline {
    agent any
    
    stages {
        stage('Environment Info') {
            steps {
                script {
                    echo "Job Name: ${env.JOB_NAME}"
                    echo "Build Number: ${env.BUILD_NUMBER}"
                    echo "Build ID: ${env.BUILD_ID}"
                    echo "Build URL: ${env.BUILD_URL}"
                    echo "Jenkins URL: ${env.JENKINS_URL}"
                    echo "Workspace: ${env.WORKSPACE}"
                    echo "Node Name: ${env.NODE_NAME}"
                    
                    // Git-related variables (when using SCM)
                    echo "Branch Name: ${env.BRANCH_NAME}"
                    echo "Git Commit: ${env.GIT_COMMIT}"
                    echo "Git URL: ${env.GIT_URL}"
                    
                    // Change-related variables (for PRs)
                    if (env.CHANGE_ID) {
                        echo "Change ID: ${env.CHANGE_ID}"
                        echo "Change URL: ${env.CHANGE_URL}"
                        echo "Change Title: ${env.CHANGE_TITLE}"
                        echo "Change Author: ${env.CHANGE_AUTHOR}"
                    }
                }
            }
        }
    }
}
```

### Custom Environment Variables

**Pipeline-Level Environment Variables:**
```groovy
pipeline {
    agent any
    
    environment {
        // Static values
        APP_NAME = 'my-python-app'
        APP_VERSION = '1.0.0'
        
        // Dynamic values using Groovy expressions
        BUILD_VERSION = "${APP_VERSION}.${BUILD_NUMBER}"
        DEPLOY_ENV = "${env.BRANCH_NAME == 'main' ? 'production' : 'staging'}"
        
        // Computed values
        BUILD_TIMESTAMP = "${new Date().format('yyyy-MM-dd HH:mm:ss')}"
    }
    
    stages {
        stage('Use Environment Variables') {
            steps {
                echo "Building ${APP_NAME} version ${BUILD_VERSION}"
                echo "Target environment: ${DEPLOY_ENV}"
                echo "Build timestamp: ${BUILD_TIMESTAMP}"
                
                // Environment variables are available in shell commands
                sh 'echo "App: $APP_NAME, Version: $BUILD_VERSION"'
            }
        }
    }
}
```

**Stage-Level Environment Variables:**
```groovy
pipeline {
    agent any
    
    stages {
        stage('Database Migration') {
            environment {
                DB_HOST = 'localhost'
                DB_PORT = '5432'
                DB_NAME = 'myapp_db'
                // Credentials will be covered in the next section
            }
            steps {
                echo "Connecting to database at ${DB_HOST}:${DB_PORT}"
                sh '''
                    echo "Database: $DB_NAME"
                    echo "Host: $DB_HOST:$DB_PORT"
                '''
            }
        }
        
        stage('API Testing') {
            environment {
                API_BASE_URL = "${env.BRANCH_NAME == 'main' ? 'https://api.prod.com' : 'https://api.staging.com'}"
                API_TIMEOUT = '30'
            }
            steps {
                echo "Testing API at ${API_BASE_URL}"
                sh 'curl -f --max-time $API_TIMEOUT $API_BASE_URL/health'
            }
        }
    }
}
```

### Dynamic Environment Variable Generation

**Reading from Files:**
```groovy
pipeline {
    agent any
    
    stages {
        stage('Load Configuration') {
            steps {
                script {
                    // Load version from file
                    env.APP_VERSION = readFile('VERSION').trim()
                    
                    // Load configuration from JSON
                    def config = readJSON file: 'config.json'
                    env.API_ENDPOINT = config.api.endpoint
                    env.MAX_RETRIES = config.api.max_retries.toString()
                    
                    // Load from properties file
                    def props = readProperties file: 'app.properties'
                    env.DATABASE_URL = props.database_url
                }
            }
        }
        
        stage('Use Loaded Config') {
            steps {
                echo "Version: ${env.APP_VERSION}"
                echo "API Endpoint: ${env.API_ENDPOINT}"
                echo "Max Retries: ${env.MAX_RETRIES}"
                echo "Database URL: ${env.DATABASE_URL}"
            }
        }
    }
}
```

**Conditional Environment Variables:**
```groovy
pipeline {
    agent any
    
    stages {
        stage('Set Environment') {
            steps {
                script {
                    // Set variables based on conditions
                    if (env.BRANCH_NAME == 'main') {
                        env.DEPLOY_TARGET = 'production'
                        env.LOG_LEVEL = 'WARN'
                        env.DEBUG_MODE = 'false'
                    } else if (env.BRANCH_NAME == 'develop') {
                        env.DEPLOY_TARGET = 'staging'
                        env.LOG_LEVEL = 'INFO'
                        env.DEBUG_MODE = 'false'
                    } else {
                        env.DEPLOY_TARGET = 'development'
                        env.LOG_LEVEL = 'DEBUG'
                        env.DEBUG_MODE = 'true'
                    }
                    
                    // Time-based variables
                    def now = new Date()
                    env.IS_WEEKEND = (now.getDay() == 0 || now.getDay() == 6).toString()
                    env.BUILD_HOUR = now.getHours().toString()
                }
            }
        }
        
        stage('Deploy') {
            steps {
                echo "Deploying to: ${env.DEPLOY_TARGET}"
                echo "Log level: ${env.LOG_LEVEL}"
                echo "Debug mode: ${env.DEBUG_MODE}"
                echo "Weekend build: ${env.IS_WEEKEND}"
            }
        }
    }
}
```

---

## Credentials Management

### Understanding Jenkins Credentials

Credentials in Jenkins are securely stored secrets that can include:
- **Username/Password pairs** for basic authentication
- **SSH keys** for secure shell access
- **API tokens** for service integration
- **Certificates** for SSL/TLS authentication
- **Secret text** for API keys and tokens
- **Secret files** for configuration files with sensitive data

### Using Credentials in Pipelines

**Basic Credential Usage:**
```groovy
pipeline {
    agent any
    
    stages {
        stage('Deploy with Credentials') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'github-credentials',
                        passwordVariable: 'GITHUB_TOKEN',
                        usernameVariable: 'GITHUB_USER'
                    )
                ]) {
                    sh '''
                        echo "Authenticating as user: $GITHUB_USER"
                        # Token is automatically masked in logs
                        git clone https://$GITHUB_USER:$GITHUB_TOKEN@github.com/company/repo.git
                    '''
                }
            }
        }
    }
}
```

**Multiple Credential Types:**
```groovy
pipeline {
    agent any
    
    stages {
        stage('Deploy Application') {
            steps {
                withCredentials([
                    // Database credentials
                    usernamePassword(
                        credentialsId: 'database-credentials',
                        passwordVariable: 'DB_PASSWORD',
                        usernameVariable: 'DB_USER'
                    ),
                    // API key
                    string(
                        credentialsId: 'api-key',
                        variable: 'API_KEY'
                    ),
                    // AWS credentials
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-credentials',
                     accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                     secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'],
                    // SSH key for deployment
                    sshUserPrivateKey(
                        credentialsId: 'deployment-ssh-key',
                        keyFileVariable: 'SSH_KEY_FILE',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {
                    sh '''
                        # Database migration
                        export DATABASE_URL="postgresql://$DB_USER:$DB_PASSWORD@db.company.com/myapp"
                        python manage.py migrate
                        
                        # Upload to S3
                        aws s3 cp dist/ s3://my-app-bucket/ --recursive
                        
                        # Deploy via SSH
                        ssh -i $SSH_KEY_FILE $SSH_USER@production-server "sudo systemctl restart myapp"
                        
                        # Notify API
                        curl -X POST -H "Authorization: Bearer $API_KEY" \\
                             https://api.company.com/deployments
                    '''
                }
            }
        }
    }
}
```

**Environment-Specific Credentials:**
```groovy
pipeline {
    agent any
    
    stages {
        stage('Deploy') {
            steps {
                script {
                    def credentialsId = getCredentialsForEnvironment(env.BRANCH_NAME)
                    
                    withCredentials([
                        usernamePassword(
                            credentialsId: credentialsId,
                            passwordVariable: 'DEPLOY_PASSWORD',
                            usernameVariable: 'DEPLOY_USER'
                        )
                    ]) {
                        sh '''
                            echo "Deploying with credentials for environment"
                            # Deployment logic here
                        '''
                    }
                }
            }
        }
    }
}

def getCredentialsForEnvironment(branchName) {
    switch(branchName) {
        case 'main':
            return 'production-credentials'
        case 'develop':
            return 'staging-credentials'
        default:
            return 'development-credentials'
    }
}
```

### Credential Security Best Practices

**Scoping Credentials:**
```groovy
// Use environment-level credential scoping
environment {
    // Don't expose credentials as environment variables
    // Use withCredentials instead
}

stages {
    stage('Secure Deployment') {
        steps {
            withCredentials([
                string(credentialsId: 'api-key', variable: 'API_KEY')
            ]) {
                script {
                    // Credentials are only available within this block
                    def apiKey = env.API_KEY
                    
                    // Don't log credentials
                    echo "API key length: ${apiKey.length()}"
                    
                    // Use for API calls
                    sh 'curl -H "Authorization: Bearer $API_KEY" https://api.example.com/deploy'
                }
            }
            // API_KEY is no longer available here
        }
    }
}
```

**Credential Validation:**
```groovy
stage('Validate Credentials') {
    steps {
        withCredentials([
            usernamePassword(
                credentialsId: 'service-account',
                passwordVariable: 'SERVICE_PASSWORD',
                usernameVariable: 'SERVICE_USER'
            )
        ]) {
            script {
                // Test credentials before using them
                def testResult = sh(
                    script: '''
                        curl -s -f -u "$SERVICE_USER:$SERVICE_PASSWORD" \\
                             https://api.service.com/auth/test
                    ''',
                    returnStatus: true
                )
                
                if (testResult != 0) {
                    error('Credential validation failed')
                }
                
                echo 'Credentials validated successfully'
            }
        }
    }
}
```

---

## Introduction to Shared Libraries

### What Are Shared Libraries?

Shared libraries in Jenkins allow you to:
- **Reuse common pipeline code** across multiple projects
- **Centralize CI/CD logic** in a version-controlled repository
- **Maintain consistency** across different teams and projects
- **Collaborate on CI/CD code** just like application code
- **Version control** your pipeline functions and templates

### Shared Library Structure

```
jenkins-shared-library/
├── vars/
│   ├── deployToK8s.groovy        # Global variable (step)
│   ├── runTests.groovy           # Global variable (step)
│   └── notifySlack.groovy        # Global variable (step)
├── src/
│   └── com/
│       └── company/
│           └── jenkins/
│               ├── Utils.groovy   # Utility class
│               └── Config.groovy  # Configuration class
├── resources/
│   ├── templates/
│   │   └── Dockerfile.template   # Resource files
│   └── config/
│       └── environments.json     # Configuration files
└── README.md
```

### Basic Shared Library Functions

**vars/deployToK8s.groovy:**
```groovy
#!/usr/bin/env groovy

def call(Map config) {
    // Default configuration
    def defaultConfig = [
        appName: env.JOB_NAME.toLowerCase(),
        namespace: 'default',
        imageTag: env.BUILD_NUMBER,
        replicas: 3
    ]
    
    // Merge provided config with defaults
    def deployConfig = defaultConfig + config
    
    echo "Deploying ${deployConfig.appName} to Kubernetes"
    echo "Namespace: ${deployConfig.namespace}"
    echo "Image tag: ${deployConfig.imageTag}"
    echo "Replicas: ${deployConfig.replicas}"
    
    sh """
        # Update deployment
        kubectl set image deployment/${deployConfig.appName} \\
            ${deployConfig.appName}=${deployConfig.appName}:${deployConfig.imageTag} \\
            -n ${deployConfig.namespace}
        
        # Wait for rollout
        kubectl rollout status deployment/${deployConfig.appName} \\
            -n ${deployConfig.namespace} --timeout=300s
        
        # Scale if needed
        kubectl scale deployment/${deployConfig.appName} \\
            --replicas=${deployConfig.replicas} \\
            -n ${deployConfig.namespace}
    """
    
    echo "Deployment completed successfully"
}
```

**vars/runTests.groovy:**
```groovy
#!/usr/bin/env groovy

def call(Map config = [:]) {
    def testConfig = [
        testType: config.testType ?: 'pytest',
        testDir: config.testDir ?: 'tests/',
        coverage: config.coverage ?: true,
        parallel: config.parallel ?: false
    ]
    
    echo "Running ${testConfig.testType} tests"
    
    switch(testConfig.testType) {
        case 'pytest':
            runPytest(testConfig)
            break
        case 'unittest':
            runUnittest(testConfig)
            break
        case 'jest':
            runJest(testConfig)
            break
        default:
            error("Unsupported test type: ${testConfig.testType}")
    }
}

def runPytest(config) {
    def command = "python -m pytest ${config.testDir} -v"
    
    if (config.coverage) {
        command += " --cov=src --cov-report=xml --cov-report=html"
    }
    
    if (config.parallel) {
        command += " -n auto"  // pytest-xdist for parallel execution
    }
    
    command += " --junitxml=test-results.xml"
    
    sh command
    
    // Publish results
    junit 'test-results.xml'
    
    if (config.coverage) {
        publishHTML([
            allowMissing: false,
            alwaysLinkToLastBuild: true,
            keepAll: true,
            reportDir: 'htmlcov',
            reportFiles: 'index.html',
            reportName: 'Coverage Report'
        ])
    }
}

def runUnittest(config) {
    sh "python -m unittest discover -s ${config.testDir} -v"
}

def runJest(config) {
    sh "npm test -- --coverage --testResultsProcessor=jest-junit"
    junit 'junit.xml'
}
```

**vars/notifySlack.groovy:**
```groovy
#!/usr/bin/env groovy

def call(Map config) {
    def slackConfig = [
        channel: config.channel ?: '#builds',
        color: config.color ?: 'good',
        message: config.message ?: 'Build notification',
        includeChanges: config.includeChanges ?: true
    ]
    
    def message = slackConfig.message
    
    // Add build information
    message += "\n*Job:* ${env.JOB_NAME}"
    message += "\n*Build:* #${env.BUILD_NUMBER}"
    message += "\n*Branch:* ${env.BRANCH_NAME ?: 'N/A'}"
    message += "\n*URL:* ${env.BUILD_URL}"
    
    // Add change information if requested
    if (slackConfig.includeChanges) {
        def changes = getChangeString()
        if (changes) {
            message += "\n*Changes:*\n${changes}"
        }
    }
    
    slackSend(
        channel: slackConfig.channel,
        color: slackConfig.color,
        message: message
    )
}

def getChangeString() {
    def changes = ""
    def changeLogSets = currentBuild.changeSets
    
    for (int i = 0; i < changeLogSets.size(); i++) {
        def entries = changeLogSets[i].items
        for (int j = 0; j < entries.length; j++) {
            def entry = entries[j]
            changes += "• ${entry.msg} - ${entry.author}\n"
        }
    }
    
    return changes.trim()
}
```

### Using Shared Libraries in Pipelines

**Basic Usage:**
```groovy
@Library('jenkins-shared-library@main') _

pipeline {
    agent any
    
    stages {
        stage('Test') {
            steps {
                // Use shared library function
                runTests([
                    testType: 'pytest',
                    coverage: true,
                    parallel: true
                ])
            }
        }
        
        stage('Deploy') {
            when { branch 'main' }
            steps {
                deployToK8s([
                    appName: 'my-python-app',
                    namespace: 'production',
                    replicas: 5
                ])
            }
        }
    }
    
    post {
        success {
            notifySlack([
                channel: '#deployments',
                color: 'good',
                message: '✅ Deployment successful!'
            ])
        }
        failure {
            notifySlack([
                channel: '#alerts',
                color: 'danger',
                message: '❌ Build failed!'
            ])
        }
    }
}
```

**Advanced Shared Library Usage:**
```groovy
@Library('jenkins-shared-library@v1.2.0') _

pipeline {
    agent any
    
    environment {
        APP_NAME = 'advanced-python-app'
    }
    
    stages {
        stage('Setup') {
            steps {
                script {
                    // Use shared library class
                    def utils = new com.company.jenkins.Utils()
                    env.BUILD_VERSION = utils.generateVersion(env.BUILD_NUMBER)
                    
                    def config = new com.company.jenkins.Config()
                    env.DEPLOY_CONFIG = config.getEnvironmentConfig(env.BRANCH_NAME)
                }
            }
        }
        
        stage('Build & Test') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        runTests([
                            testType: 'pytest',
                            testDir: 'tests/unit',
                            coverage: true
                        ])
                    }
                }
                stage('Integration Tests') {
                    steps {
                        runTests([
                            testType: 'pytest',
                            testDir: 'tests/integration',
                            coverage: false
                        ])
                    }
                }
            }
        }
        
        stage('Deploy') {
            when { branch 'main' }
            steps {
                script {
                    def deployConfig = readJSON text: env.DEPLOY_CONFIG
                    
                    deployToK8s([
                        appName: env.APP_NAME,
                        namespace: deployConfig.namespace,
                        imageTag: env.BUILD_VERSION,
                        replicas: deployConfig.replicas
                    ])
                }
            }
        }
    }
}
```

### Shared Library Best Practices

**Version Management:**
```groovy
// Use specific versions for production
@Library('jenkins-shared-library@v2.1.0') _

// Use latest for development
@Library('jenkins-shared-library@main') _

// Use branch for testing new features
@Library('jenkins-shared-library@feature/new-deployment') _
```

**Error Handling in Shared Libraries:**
```groovy
// vars/deployWithRetry.groovy
def call(Map config, int maxRetries = 3) {
    def attempt = 0
    def success = false
    
    while (attempt < maxRetries && !success) {
        attempt++
        echo "Deployment attempt ${attempt}/${maxRetries}"
        
        try {
            deployToK8s(config)
            success = true
        } catch (Exception e) {
            echo "Deployment attempt ${attempt} failed: ${e.getMessage()}"
            
            if (attempt >= maxRetries) {
                throw e
            } else {
                echo "Retrying in 30 seconds..."
                sleep(30)
            }
        }
    }
    
    echo "Deployment successful after ${attempt} attempt(s)"
}
```

---

## Multi-Staged vs Normal Pipelines Comparison

### Normal Pipeline Structure

```groovy
// Normal sequential pipeline
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh 'python setup.py build'
            }
        }
        stage('Unit Tests') {
            steps {
                sh 'python -m pytest tests/unit/'
            }
        }
        stage('Integration Tests') {
            steps {
                sh 'python -m pytest tests/integration/'
            }
        }
        stage('Security Tests') {
            steps {
                sh 'bandit -r src/'
            }
        }
        stage('Deploy') {
            steps {
                sh 'deploy.sh'
            }
        }
    }
}
```

**Execution Time:** Sequential execution means total time = sum of all stage times

### Multi-Staged (Parallel) Pipeline Structure

```groovy
// Multi-staged parallel pipeline
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh 'python setup.py build'
            }
        }
        stage('Parallel Testing') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        sh 'python -m pytest tests/unit/'
                    }
                }
                stage('Integration Tests') {
                    steps {
                        sh 'python -m pytest tests/integration/'
                    }
                }
                stage('Security Tests') {
                    steps {
                        sh 'bandit -r src/'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                sh 'deploy.sh'
            }
        }
    }
}
```

**Execution Time:** Parallel execution means time = longest parallel stage time

### Performance Comparison

| Aspect | Normal Pipeline | Multi-Staged Pipeline |
|--------|-----------------|----------------------|
| **Execution Time** | Sum of all stages | Longest parallel stage |
| **Resource Usage** | One agent at a time | Multiple agents simultaneously |
| **Feedback Speed** | Slower overall feedback | Faster feedback on failures |
| **Complexity** | Simple, linear | More complex orchestration |
| **Debugging** | Easier to trace | May require parallel log analysis |
| **Agent Requirements** | 1 agent minimum | Multiple agents for best performance |

### Use Cases for Each Approach

**Use Normal Pipelines When:**
- Stages have dependencies on previous stages
- Limited agent resources available
- Simple, straightforward workflows
- Sequential processing is required
- Debugging and troubleshooting simplicity is prioritized

**Use Multi-Staged Pipelines When:**
- Independent stages can run in parallel
- Fast feedback is critical
- Sufficient agent resources are available
- Complex testing matrix (multiple environments/versions)
- CI/CD pipeline optimization is a priority

---

## Practical Implementation Exercise

Let's create a comprehensive pipeline that demonstrates all the concepts from this session:

```groovy
@Library('jenkins-shared-library@main') _

pipeline {
    agent any
    
    environment {
        APP_NAME = 'python-deployment-accelerator'
        REGISTRY_URL = 'registry.company.com'
        BUILD_VERSION = "${env.BUILD_NUMBER}"
    }
    
    triggers {
        // Build daily for main branch, weekends for others
        cron(env.BRANCH_NAME == 'main' ? 'H 2 * * *' : 'H 2 * * 0')
    }
    
    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timeout(time: 30, unit: 'MINUTES')
        timestamps()
    }
    
    stages {
        stage('Environment Setup') {
            steps {
                script {
                    // Set environment-specific variables
                    if (env.BRANCH_NAME == 'main') {
                        env.DEPLOY_ENV = 'production'
                        env.REPLICAS = '5'
                        env.NAMESPACE = 'prod'
                    } else if (env.BRANCH_NAME == 'develop') {
                        env.DEPLOY_ENV = 'staging'
                        env.REPLICAS = '3'
                        env.NAMESPACE = 'staging'
                    } else {
                        env.DEPLOY_ENV = 'development'
                        env.REPLICAS = '1'
                        env.NAMESPACE = 'dev'
                    }
                    
                    echo "Environment: ${env.DEPLOY_ENV}"
                    echo "Replicas: ${env.REPLICAS}"
                    echo "Namespace: ${env.NAMESPACE}"
                }
            }
        }
        
        stage('Build') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                    pip install -r requirements-dev.txt
                '''
            }
        }
        
        stage('Parallel Quality Gates') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        runTests([
                            testType: 'pytest',
                            testDir: 'tests/unit',
                            coverage: true,
                            parallel: true
                        ])
                    }
                }
                
                stage('Integration Tests') {
                    steps {
                        runTests([
                            testType: 'pytest',
                            testDir: 'tests/integration',
                            coverage: false
                        ])
                    }
                }
                
                stage('Code Quality') {
                    steps {
                        sh '''
                            . venv/bin/activate
                            flake8 src/ --max-line-length=88
                            black --check src/
                            mypy src/ --ignore-missing-imports
                        '''
                    }
                }
                
                stage('Security Scan') {
                    steps {
                        sh '''
                            . venv/bin/activate
                            safety check
                            bandit -r src/ -ll
                        '''
                    }
                }
            }
        }
        
        stage('Build Image') {
            when {
                anyOf {
                    branch 'main'
                    branch 'develop'
                    changeRequest()
                }
            }
            steps {
                script {
                    def imageTag = "${REGISTRY_URL}/${APP_NAME}:${BUILD_VERSION}"
                    
                    sh """
                        docker build -t ${imageTag} .
                        docker push ${imageTag}
                    """
                    
                    env.IMAGE_TAG = imageTag
                }
            }
        }
        
        stage('Deploy') {
            when { 
                anyOf {
                    branch 'main'
                    branch 'develop'
                }
            }
            steps {
                withCredentials([
                    kubeconfigFile(credentialsId: 'k8s-config', variable: 'KUBECONFIG')
                ]) {
                    deployToK8s([
                        appName: env.APP_NAME,
                        namespace: env.NAMESPACE,
                        imageTag: env.IMAGE_TAG,
                        replicas: env.REPLICAS as Integer
                    ])
                }
            }
        }
        
        stage('Post-Deploy Tests') {
            when { 
                anyOf {
                    branch 'main'
                    branch 'develop'
                }
            }
            parallel {
                stage('Health Check') {
                    steps {
                        script {
                            def healthUrl = "https://${APP_NAME}.${NAMESPACE}.company.com/health"
                            sh """
                                for i in {1..30}; do
                                    if curl -f ${healthUrl}; then
                                        echo "Health check passed"
                                        exit 0
                                    fi
                                    echo "Health check attempt \$i failed, retrying..."
                                    sleep 10
                                done
                                echo "Health check failed after 30 attempts"
                                exit 1
                            """
                        }
                    }
                }
                
                stage('Smoke Tests') {
                    steps {
                        sh '''
                            . venv/bin/activate
                            python -m pytest tests/smoke/ -v
                        '''
                    }
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        
        success {
            notifySlack([
                channel: '#deployments',
                color: 'good',
                message: "✅ ${APP_NAME} deployed successfully to ${env.DEPLOY_ENV}!",
                includeChanges: true
            ])
        }
        
        failure {
            notifySlack([
                channel: '#alerts',
                color: 'danger',
                message: "❌ ${APP_NAME} deployment failed in ${env.DEPLOY_ENV}!",
                includeChanges: true
            ])
        }
        
        unstable {
            notifySlack([
                channel: '#builds',
                color: 'warning',
                message: "⚠️ ${APP_NAME} build completed with warnings",
                includeChanges: false
            ])
        }
    }
}
```

---

## Summary

In this session, we covered advanced Jenkins pipeline features that significantly accelerate deployment processes:

### Key Concepts Mastered:
- **Parallel Stages**: Reduced pipeline execution time through concurrent processing
- **Environment Variables**: Dynamic configuration and conditional logic
- **Credentials Management**: Secure handling of sensitive information
- **Shared Libraries**: Code reuse and standardization across teams
- **Deployment Acceleration**: Techniques to speed up CI/CD pipelines

### Performance Improvements Achieved:
- **Time Reduction**: Parallel execution can reduce pipeline time by 50-70%
- **Resource Optimization**: Better utilization of available agents
- **Faster Feedback**: Quicker identification of issues through parallel testing
- **Code Reuse**: Shared libraries eliminate duplicate pipeline code

### Best Practices Learned:
- Use parallel stages for independent operations
- Implement proper credential scoping and security
- Version control shared libraries
- Environment-specific configuration management
- Comprehensive error handling and notification

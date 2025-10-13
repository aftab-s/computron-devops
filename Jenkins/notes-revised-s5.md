# Jenkins Session 5: Groovy Scripting and Shared Libraries - Comprehensive Notes

## Session Overview

This session dives deep into Groovy scripting within Jenkins, exploring how to leverage this powerful language for automation, custom pipeline steps, and shared libraries. 

## Introduction to Groovy in Jenkins

### What is Groovy?

Groovy is a dynamic scripting language that runs on the Java Virtual Machine (JVM) and serves as the foundation for Jenkins' Pipeline-as-Code functionality. It bridges the gap between simple scripting and full Java development.

**Key Characteristics:**
- **Dynamic Language**: Variables don't need explicit type declarations
- **JVM-Based**: Full access to Java libraries and ecosystem
- **DSL-Friendly**: Excellent for creating domain-specific languages
- **Jenkins Integration**: Native support throughout Jenkins platform

### Why Groovy for Jenkins?

**Beginner-Friendly Features:**
- Simpler syntax than Java
- Optional semicolons and type declarations
- Built-in string interpolation
- Powerful collection handling

**Enterprise-Ready Capabilities:**
- Seamless Java integration
- Object-oriented programming support
- Functional programming features
- Robust error handling

**Jenkins-Specific Benefits:**
- Powers Pipeline DSL (Domain Specific Language)
- Enables custom build steps and functions
- Supports complex automation logic
- Provides access to Jenkins internal APIs

### Groovy Use Cases in Jenkins

**Pipeline Development:**
- Custom build logic and conditional execution
- Complex data processing and transformation
- Integration with external APIs and services
- Dynamic pipeline generation

**Administrative Tasks:**
- Bulk job updates and maintenance
- User and permission management
- Plugin configuration and updates
- System monitoring and reporting

---

## Groovy Syntax and Language Fundamentals

### Variables and Data Types

**Dynamic Variables:**
```groovy
// Dynamic typing - Groovy infers the type
def message = "Hello Jenkins"
def count = 42
def isActive = true
def items = ['item1', 'item2', 'item3']
def config = [name: 'MyApp', version: '1.0']

// Variables can change type
def flexible = "Initially a string"
flexible = 100  // Now it's a number
flexible = ['now', 'an', 'array']  // Now it's a list
```

**Explicit Typing:**
```groovy
// Explicit type declarations for clarity or performance
String applicationName = "Jenkins Pipeline"
int buildNumber = 123
boolean deployToProduction = false
List<String> environments = ['dev', 'staging', 'prod']
Map<String, String> credentials = [
    username: 'jenkins-user',
    password: 'secret-key'
]
```

**Advanced Data Structures:**
```groovy
// Nested maps and lists
def deploymentConfig = [
    environments: [
        dev: [
            url: 'https://dev.example.com',
            replicas: 1,
            resources: [cpu: '100m', memory: '256Mi']
        ],
        prod: [
            url: 'https://prod.example.com',
            replicas: 3,
            resources: [cpu: '500m', memory: '1Gi']
        ]
    ],
    notifications: [
        slack: '#deployments',
        email: 'team@company.com'
    ]
]

// Accessing nested values
println deploymentConfig.environments.prod.replicas  // Output: 3
println deploymentConfig.notifications.slack         // Output: #deployments
```

### String Handling

**String Types and Interpolation:**
```groovy
// Single quotes - literal strings
def literalString = 'This is exactly what you see'

// Double quotes - allows interpolation
def name = 'Jenkins'
def greeting = "Hello, ${name}!"  // Output: Hello, Jenkins!

// Multi-line strings
def longText = """
This is a multi-line string
that preserves formatting
and can include ${name}
"""

// Slashy strings - great for regex and paths
def regex = /\d{4}-\d{2}-\d{2}/  // Date pattern
def path = /path/to/my/file.txt/
```

**String Operations:**
```groovy
def text = "Jenkins Pipeline Automation"

// Basic operations
println text.length()           // 27
println text.toLowerCase()      // jenkins pipeline automation
println text.toUpperCase()      // JENKINS PIPELINE AUTOMATION
println text.contains('Pipeline')  // true

// String manipulation
def words = text.split(' ')     // ['Jenkins', 'Pipeline', 'Automation']
def replaced = text.replace('Jenkins', 'GitHub Actions')
def trimmed = '  extra spaces  '.trim()

// Advanced string operations
def template = 'Build ${app} version ${version} for ${env}'
def result = template
    .replace('${app}', 'MyApp')
    .replace('${version}', '1.2.3')
    .replace('${env}', 'production')
```

### Control Structures

**Conditional Logic:**
```groovy
// Basic if-else
def environment = 'production'
if (environment == 'production') {
    println 'Deploying to production with extra checks'
} else if (environment == 'staging') {
    println 'Deploying to staging environment'
} else {
    println 'Deploying to development environment'
}

// Ternary operator
def replicas = (environment == 'production') ? 5 : 2

// Switch statement
switch (environment) {
    case 'production':
        replicas = 5
        enableMonitoring = true
        break
    case 'staging':
        replicas = 2
        enableMonitoring = true
        break
    default:
        replicas = 1
        enableMonitoring = false
}

// Groovy truth (null, empty, zero are false)
def config = [:]
if (config) {
    println 'Config has values'
} else {
    println 'Config is empty'  // This will execute
}
```

**Loops and Iteration:**
```groovy
// Traditional for loop
for (int i = 0; i < 5; i++) {
    println "Build step ${i + 1}"
}

// Enhanced for loop
def environments = ['dev', 'staging', 'prod']
for (env in environments) {
    println "Deploying to ${env}"
}

// While loop
def attempts = 0
while (attempts < 3) {
    println "Deployment attempt ${attempts + 1}"
    attempts++
}

// Groovy each method (functional style)
environments.each { env ->
    println "Processing environment: ${env}"
}

// Each with index
environments.eachWithIndex { env, index ->
    println "${index + 1}. Environment: ${env}"
}
```

### Functions and Closures

**Function Definition:**
```groovy
// Basic function
def greet(name) {
    return "Hello, ${name}!"
}

// Function with typed parameters
def calculateDeploymentTime(int buildDuration, String environment) {
    def baseTime = buildDuration + 60  // Base deployment time
    def multiplier = (environment == 'production') ? 2 : 1
    return baseTime * multiplier
}

// Function with default parameters
def deployApplication(appName, version = '1.0', environment = 'dev') {
    println "Deploying ${appName} version ${version} to ${environment}"
}

// Usage examples
println greet('Jenkins User')
println calculateDeploymentTime(300, 'production')
deployApplication('MyApp')  // Uses defaults
deployApplication('MyApp', '2.0', 'prod')  // All parameters
```

**Closures (Lambda Functions):**
```groovy
// Basic closure
def sayHello = { name ->
    println "Hello, ${name}!"
}
sayHello('World')

// Closure with multiple parameters
def calculateTotal = { price, tax, discount ->
    return price * (1 + tax) * (1 - discount)
}
def total = calculateTotal(100, 0.08, 0.10)

// Closure as parameter
def processEnvironments(environments, processor) {
    environments.each { env ->
        processor(env)
    }
}

processEnvironments(['dev', 'prod']) { env ->
    println "Configuring ${env} environment"
}

// Closure with Jenkins pipeline context
def withRetry = { attempts, action ->
    def success = false
    def currentAttempt = 0
    
    while (!success && currentAttempt < attempts) {
        try {
            action()
            success = true
        } catch (Exception e) {
            currentAttempt++
            if (currentAttempt >= attempts) {
                throw e
            }
            println "Attempt ${currentAttempt} failed, retrying..."
            sleep(5000)  // Wait 5 seconds before retry
        }
    }
}
```

---

## Groovy in Jenkins Pipelines

### Script Blocks in Declarative Pipelines

**Basic Script Block Usage:**
```groovy
pipeline {
    agent any
    
    environment {
        APP_NAME = 'groovy-demo'
    }
    
    stages {
        stage('Dynamic Logic') {
            steps {
                script {
                    // Groovy code within script block
                    def buildInfo = [
                        timestamp: new Date().toString(),
                        branch: env.BRANCH_NAME ?: 'main',
                        buildNumber: env.BUILD_NUMBER
                    ]
                    
                    println "Build Info: ${buildInfo}"
                    
                    // Dynamic stage execution
                    if (buildInfo.branch == 'main') {
                        echo 'Running production deployment checks'
                        
                        // Custom deployment logic
                        def environments = ['staging', 'production']
                        environments.each { env ->
                            echo "Preparing deployment to ${env}"
                        }
                    } else {
                        echo 'Running development build'
                    }
                    
                    // Store information for later stages
                    env.BUILD_TIMESTAMP = buildInfo.timestamp
                    env.DEPLOY_READY = (buildInfo.branch in ['main', 'develop']).toString()
                }
            }
        }
        
        stage('Conditional Deployment') {
            when {
                expression { env.DEPLOY_READY == 'true' }
            }
            steps {
                script {
                    echo "Deployment initiated at ${env.BUILD_TIMESTAMP}"
                    
                    // Complex deployment logic
                    def deploymentStrategy = determineDeploymentStrategy(env.BRANCH_NAME)
                    executeDeployment(deploymentStrategy)
                }
            }
        }
    }
}

// Functions defined outside pipeline (at script level)
def determineDeploymentStrategy(branchName) {
    switch (branchName) {
        case 'main':
            return [
                type: 'blue-green',
                replicas: 5,
                healthCheckTimeout: 300
            ]
        case 'develop':
            return [
                type: 'rolling',
                replicas: 2,
                healthCheckTimeout: 120
            ]
        default:
            return [
                type: 'recreate',
                replicas: 1,
                healthCheckTimeout: 60
            ]
    }
}

def executeDeployment(strategy) {
    echo "Executing ${strategy.type} deployment with ${strategy.replicas} replicas"
    
    // Simulate deployment steps
    for (int i = 1; i <= strategy.replicas; i++) {
        echo "Deploying replica ${i}/${strategy.replicas}"
        sleep(2)  // Simulate deployment time
    }
    
    echo "Waiting for health check (${strategy.healthCheckTimeout}s timeout)"
    // Health check logic would go here
}
```

### Advanced Pipeline Patterns with Groovy

**Dynamic Stage Generation:**
```groovy
pipeline {
    agent any
    
    stages {
        stage('Setup') {
            steps {
                script {
                    // Read configuration from file or API
                    def config = [
                        environments: ['dev', 'staging', 'prod'],
                        testSuites: ['unit', 'integration', 'e2e'],
                        deploymentRegions: ['us-east-1', 'eu-west-1']
                    ]
                    
                    // Store config for other stages
                    env.PIPELINE_CONFIG = writeJSON returnText: true, json: config
                }
            }
        }
        
        stage('Dynamic Testing') {
            steps {
                script {
                    def config = readJSON text: env.PIPELINE_CONFIG
                    
                    // Create parallel testing stages dynamically
                    def testStages = [:]
                    
                    config.testSuites.each { suite ->
                        testStages["${suite} Tests"] = {
                            node {
                                echo "Running ${suite} tests"
                                
                                // Execute test suite
                                try {
                                    sh "npm run test:${suite}"
                                    echo "${suite} tests passed"
                                } catch (Exception e) {
                                    echo "${suite} tests failed: ${e.getMessage()}"
                                    throw e
                                }
                            }
                        }
                    }
                    
                    // Execute all test stages in parallel
                    parallel testStages
                }
            }
        }
        
        stage('Multi-Region Deployment') {
            when {
                branch 'main'
            }
            steps {
                script {
                    def config = readJSON text: env.PIPELINE_CONFIG
                    
                    // Deploy to each region sequentially
                    config.deploymentRegions.each { region ->
                        echo "Deploying to region: ${region}"
                        
                        deployToRegion(region, [
                            appName: env.APP_NAME,
                            version: env.BUILD_NUMBER,
                            environment: 'production'
                        ])
                        
                        // Wait and verify deployment
                        waitForDeployment(region, 300)  // 5 minute timeout
                    }
                }
            }
        }
    }
}

def deployToRegion(region, config) {
    echo "Starting deployment to ${region}"
    
    // Region-specific deployment logic
    def deploymentConfig = [
        region: region,
        replicas: getReplicasForRegion(region),
        resources: getResourcesForRegion(region)
    ]
    
    echo "Deployment config: ${deploymentConfig}"
    
    // Simulate deployment
    sh """
        kubectl config use-context ${region}
        kubectl set image deployment/${config.appName} \\
            ${config.appName}=${config.appName}:${config.version} \\
            --record
    """
}

def getReplicasForRegion(region) {
    def replicaMap = [
        'us-east-1': 5,   // Primary region
        'eu-west-1': 3,   // Secondary region
        'ap-south-1': 2   // Tertiary region
    ]
    return replicaMap[region] ?: 1
}

def getResourcesForRegion(region) {
    def resourceMap = [
        'us-east-1': [cpu: '500m', memory: '1Gi'],
        'eu-west-1': [cpu: '300m', memory: '512Mi'],
        'ap-south-1': [cpu: '200m', memory: '256Mi']
    ]
    return resourceMap[region] ?: [cpu: '100m', memory: '128Mi']
}

def waitForDeployment(region, timeoutSeconds) {
    echo "Waiting for deployment in ${region} to be ready"
    
    def elapsed = 0
    def checkInterval = 30  // Check every 30 seconds
    
    while (elapsed < timeoutSeconds) {
        try {
            def status = sh(
                script: "kubectl get deployment ${env.APP_NAME} -o jsonpath='{.status.readyReplicas}'",
                returnStdout: true
            ).trim()
            
            def expected = getReplicasForRegion(region)
            
            if (status.toInteger() >= expected) {
                echo "Deployment in ${region} is ready (${status}/${expected} replicas)"
                return true
            }
            
            echo "Deployment in ${region} not ready yet (${status}/${expected} replicas)"
            sleep(checkInterval)
            elapsed += checkInterval
            
        } catch (Exception e) {
            echo "Error checking deployment status: ${e.getMessage()}"
            sleep(checkInterval)
            elapsed += checkInterval
        }
    }
    
    error("Deployment in ${region} failed to become ready within ${timeoutSeconds} seconds")
}
```

---

## Jenkins Script Console

### Accessing the Script Console

The Jenkins Script Console is a powerful tool for running Groovy scripts directly against the Jenkins instance:

**Access Path:**
1. Navigate to **Manage Jenkins**
2. Click **Script Console**
3. Enter Groovy code in the text area
4. Click **Run** to execute

**Security Considerations:**
- Requires **Overall/Administer** permission
- Scripts run with full Jenkins privileges
- Use caution in production environments
- Consider creating specific admin accounts for script execution

### Common Administrative Tasks

**Listing and Managing Jobs:**
```groovy
// List all jobs
println "All Jobs:"
Jenkins.instance.getAllItems(Job.class).each { job ->
    println "- ${job.name} (${job.class.simpleName})"
}

// Find jobs by pattern
println "\nJobs matching pattern 'test-*':"
Jenkins.instance.getAllItems(Job.class).findAll { job ->
    job.name.startsWith('test-')
}.each { job ->
    println "- ${job.name}"
}

// Get job details
def jobName = 'my-pipeline-job'
def job = Jenkins.instance.getItemByFullName(jobName)
if (job) {
    println "Job: ${job.name}"
    println "Description: ${job.description}"
    println "Last build: #${job.lastBuild?.number ?: 'N/A'}"
    println "Last successful build: #${job.lastSuccessfulBuild?.number ?: 'N/A'}"
    println "Next build number: ${job.nextBuildNumber}"
} else {
    println "Job '${jobName}' not found"
}
```

**Plugin Management:**
```groovy
// List installed plugins
println "Installed Plugins:"
Jenkins.instance.pluginManager.plugins.sort { it.shortName }.each { plugin ->
    println "- ${plugin.shortName} (${plugin.version}) - ${plugin.isEnabled() ? 'Enabled' : 'Disabled'}"
}

// Find plugins by name pattern
println "\nPlugins containing 'git':"
Jenkins.instance.pluginManager.plugins.findAll { plugin ->
    plugin.shortName.toLowerCase().contains('git')
}.each { plugin ->
    println "- ${plugin.shortName} (${plugin.version})"
}

// Check for plugin updates
println "\nPlugins with available updates:"
Jenkins.instance.pluginManager.plugins.findAll { plugin ->
    plugin.hasUpdate()
}.each { plugin ->
    println "- ${plugin.shortName}: ${plugin.version} -> ${plugin.updateInfo.version}"
}
```

**User and Security Management:**
```groovy
// List all users
println "All Users:"
Jenkins.instance.securityRealm.allUsers.each { user ->
    println "- ${user.id} (${user.fullName})"
}

// Find users by pattern
println "\nUsers with 'admin' in name:"
Jenkins.instance.securityRealm.allUsers.findAll { user ->
    user.id.toLowerCase().contains('admin') || 
    user.fullName.toLowerCase().contains('admin')
}.each { user ->
    println "- ${user.id} (${user.fullName})"
}

// Check user permissions (requires specific authorization strategy)
def userName = 'jenkins-user'
def user = Jenkins.instance.securityRealm.loadUserByUsername(userName)
println "Permissions for ${userName}:"

// This would need to be adapted based on your authorization strategy
try {
    def authStrategy = Jenkins.instance.authorizationStrategy
    if (authStrategy.hasPermission(user.impersonate(), Jenkins.ADMINISTER)) {
        println "- Has ADMINISTER permission"
    }
    if (authStrategy.hasPermission(user.impersonate(), Jenkins.READ)) {
        println "- Has READ permission"
    }
} catch (Exception e) {
    println "- Unable to check permissions: ${e.getMessage()}"
}
```

**System Information and Monitoring:**
```groovy
// System information
println "Jenkins System Information:"
println "Version: ${Jenkins.VERSION}"
println "URL: ${Jenkins.instance.rootUrl}"
println "Node name: ${Jenkins.instance.displayName}"
println "Quiet period: ${Jenkins.instance.quietPeriod} seconds"
println "SCM checkout retry count: ${Jenkins.instance.scmCheckoutRetryCount}"

// Java and system properties
println "\nJava Information:"
println "Java version: ${System.getProperty('java.version')}"
println "Java vendor: ${System.getProperty('java.vendor')}"
println "Java home: ${System.getProperty('java.home')}"
println "Jenkins home: ${System.getProperty('jenkins.home')}"

// Memory information
def runtime = Runtime.getRuntime()
def mb = 1024 * 1024
println "\nMemory Information:"
println "Total memory: ${runtime.totalMemory() / mb} MB"
println "Free memory: ${runtime.freeMemory() / mb} MB"
println "Used memory: ${(runtime.totalMemory() - runtime.freeMemory()) / mb} MB"
println "Max memory: ${runtime.maxMemory() / mb} MB"

// Disk space
def jenkinsHome = new File(System.getProperty('jenkins.home'))
println "\nDisk Space (Jenkins Home):"
println "Total space: ${jenkinsHome.totalSpace / (1024 * 1024 * 1024)} GB"
println "Free space: ${jenkinsHome.freeSpace / (1024 * 1024 * 1024)} GB"
println "Usable space: ${jenkinsHome.usableSpace / (1024 * 1024 * 1024)} GB"
```

**Build Queue and Executor Management:**
```groovy
// Build queue information
println "Build Queue Status:"
def queue = Jenkins.instance.queue
println "Queue length: ${queue.items.length}"

if (queue.items.length > 0) {
    println "Queued items:"
    queue.items.each { item ->
        println "- ${item.task.name} (waiting ${item.inQueueSince} ms)"
    }
}

// Executor information
println "\nExecutor Status:"
Jenkins.instance.computers.each { computer ->
    println "Node: ${computer.name}"
    println "  Online: ${!computer.offline}"
    println "  Executors: ${computer.numExecutors}"
    
    computer.executors.eachWithIndex { executor, index ->
        def currentBuild = executor.currentExecutable
        if (currentBuild) {
            println "  Executor ${index}: Running ${currentBuild.parent.name} #${currentBuild.number}"
        } else {
            println "  Executor ${index}: Idle"
        }
    }
}

// Cancel stuck builds (use with caution!)
println "\nLong-running builds (>30 minutes):"
def thirtyMinutesAgo = System.currentTimeMillis() - (30 * 60 * 1000)

Jenkins.instance.computers.each { computer ->
    computer.executors.each { executor ->
        def currentBuild = executor.currentExecutable
        if (currentBuild && currentBuild.timeInMillis < thirtyMinutesAgo) {
            println "- ${currentBuild.parent.name} #${currentBuild.number} (${(System.currentTimeMillis() - currentBuild.timeInMillis) / (60 * 1000)} minutes)"
            
            // Uncomment to actually interrupt (dangerous!)
            // executor.interrupt()
            // println "  -> Interrupted"
        }
    }
}
```

---

## Shared Libraries Deep Dive

### Understanding Shared Library Structure

A Jenkins shared library is a Git repository with a specific directory structure that provides reusable Groovy code for pipelines:

```
jenkins-shared-library/
├── src/                           # Groovy source files (classes)
│   └── org/
│       └── company/
│           └── jenkins/
│               ├── Utils.groovy         # Utility class
│               ├── DeploymentHelper.groovy  # Deployment utilities
│               └── NotificationService.groovy  # Notification handling
├── vars/                          # Global variables/functions
│   ├── deployApp.groovy              # Deployment function
│   ├── deployApp.txt                 # Documentation for deployApp
│   ├── runTests.groovy               # Testing function
│   ├── runTests.txt                  # Documentation for runTests
│   ├── sendNotification.groovy       # Notification function
│   └── buildDockerImage.groovy       # Docker build function
├── resources/                     # Static files and templates
│   ├── templates/
│   │   ├── Dockerfile.template       # Docker template
│   │   ├── kubernetes-deployment.yaml  # K8s template
│   │   └── sonarqube-config.xml      # SonarQube config
│   ├── scripts/
│   │   ├── setup-environment.sh      # Environment setup
│   │   └── health-check.py           # Health check script
│   └── config/
│       ├── environments.json         # Environment configuration
│       └── notification-templates.json  # Notification templates
└── README.md                      # Library documentation
```

### Creating Basic Shared Library Functions

**vars/greet.groovy - Simple Function:**
```groovy
#!/usr/bin/env groovy

/**
 * Greets a user with a customizable message
 * @param name The name to greet
 * @param message Optional custom greeting message
 */
def call(String name, String message = null) {
    def greeting = message ?: "Hello"
    echo "${greeting}, ${name}! Welcome to Jenkins."
    
    // Return information that might be useful
    return [
        name: name,
        message: greeting,
        timestamp: new Date().toString()
    ]
}

/**
 * Greet multiple users
 * @param names List of names to greet
 */
def call(List<String> names) {
    def results = []
    
    names.each { name ->
        def result = call(name)
        results.add(result)
    }
    
    echo "Greeted ${names.size()} users"
    return results
}

/**
 * Greet with configuration map
 * @param config Configuration map with name, message, and other options
 */
def call(Map config) {
    def name = config.name ?: 'Unknown'
    def message = config.message ?: 'Hello'
    def includeTime = config.includeTime ?: false
    
    def greeting = message
    if (includeTime) {
        greeting += " at ${new Date().format('yyyy-MM-dd HH:mm:ss')}"
    }
    
    echo "${greeting}, ${name}!"
    
    return [
        name: name,
        message: greeting,
        config: config
    ]
}
```

**vars/greet.txt - Documentation:**
```
Greets users with customizable messages.

Usage examples:

// Simple greeting
greet('John Doe')

// Custom message
greet('Jane Smith', 'Good morning')

// Multiple users
greet(['Alice', 'Bob', 'Charlie'])

// Configuration-based greeting
greet([
    name: 'Team Lead',
    message: 'Welcome back',
    includeTime: true
])

Parameters:
- name (String): Name of the person to greet
- message (String, optional): Custom greeting message
- names (List<String>): List of names for bulk greeting
- config (Map): Configuration object with name, message, and options
```

### Advanced Shared Library Functions

**vars/deployApp.groovy - Complex Deployment Function:**
```groovy
#!/usr/bin/env groovy

/**
 * Deploy application to specified environment
 * @param config Deployment configuration
 */
def call(Map config) {
    // Validate required parameters
    validateConfig(config)
    
    // Set defaults
    def deployConfig = setDefaults(config)
    
    echo "Starting deployment of ${deployConfig.appName} to ${deployConfig.environment}"
    
    try {
        // Pre-deployment checks
        performPreDeploymentChecks(deployConfig)
        
        // Execute deployment based on strategy
        switch (deployConfig.strategy) {
            case 'blue-green':
                executeBlueGreenDeployment(deployConfig)
                break
            case 'rolling':
                executeRollingDeployment(deployConfig)
                break
            case 'canary':
                executeCanaryDeployment(deployConfig)
                break
            default:
                executeStandardDeployment(deployConfig)
        }
        
        // Post-deployment verification
        performPostDeploymentChecks(deployConfig)
        
        // Send success notification
        sendDeploymentNotification(deployConfig, 'success')
        
        echo "✅ Deployment of ${deployConfig.appName} to ${deployConfig.environment} completed successfully"
        return [status: 'success', config: deployConfig]
        
    } catch (Exception e) {
        echo "❌ Deployment failed: ${e.getMessage()}"
        
        // Rollback if enabled
        if (deployConfig.autoRollback) {
            performRollback(deployConfig)
        }
        
        // Send failure notification
        sendDeploymentNotification(deployConfig, 'failure', e.getMessage())
        
        throw e
    }
}

def validateConfig(config) {
    def requiredFields = ['appName', 'version', 'environment']
    def missingFields = []
    
    requiredFields.each { field ->
        if (!config.containsKey(field) || !config[field]) {
            missingFields.add(field)
        }
    }
    
    if (missingFields) {
        error("Missing required configuration fields: ${missingFields.join(', ')}")
    }
    
    // Validate environment
    def validEnvironments = ['dev', 'staging', 'production']
    if (!validEnvironments.contains(config.environment)) {
        error("Invalid environment '${config.environment}'. Must be one of: ${validEnvironments.join(', ')}")
    }
}

def setDefaults(config) {
    def defaults = [
        strategy: 'rolling',
        replicas: getDefaultReplicas(config.environment),
        healthCheckTimeout: 300,
        autoRollback: true,
        notifications: true,
        runTests: true
    ]
    
    return defaults + config  // Config overrides defaults
}

def getDefaultReplicas(environment) {
    def replicaMap = [
        'dev': 1,
        'staging': 2,
        'production': 5
    ]
    return replicaMap[environment] ?: 1
}

def performPreDeploymentChecks(config) {
    echo "Performing pre-deployment checks..."
    
    // Check if target environment is healthy
    def healthStatus = sh(
        script: "kubectl get nodes --no-headers | grep Ready | wc -l",
        returnStdout: true
    ).trim().toInteger()
    
    if (healthStatus < 1) {
        error("Target environment is not healthy - no ready nodes")
    }
    
    // Check if previous deployment exists
    def existingDeployment = sh(
        script: "kubectl get deployment ${config.appName} -n ${config.environment} --ignore-not-found",
        returnStdout: true
    ).trim()
    
    if (existingDeployment) {
        echo "Existing deployment found - will update"
        config.isUpdate = true
    } else {
        echo "No existing deployment - will create new"
        config.isUpdate = false
    }
    
    // Verify image exists
    sh "docker pull ${config.appName}:${config.version}"
    
    echo "✅ Pre-deployment checks passed"
}

def executeRollingDeployment(config) {
    echo "Executing rolling deployment..."
    
    def deploymentYaml = libraryResource('templates/kubernetes-deployment.yaml')
    def processedYaml = deploymentYaml
        .replace('{{APP_NAME}}', config.appName)
        .replace('{{VERSION}}', config.version)
        .replace('{{ENVIRONMENT}}', config.environment)
        .replace('{{REPLICAS}}', config.replicas.toString())
    
    writeFile file: 'deployment.yaml', text: processedYaml
    
    sh """
        kubectl apply -f deployment.yaml -n ${config.environment}
        kubectl rollout status deployment/${config.appName} -n ${config.environment} --timeout=${config.healthCheckTimeout}s
    """
}

def executeBlueGreenDeployment(config) {
    echo "Executing blue-green deployment..."
    
    // Implementation for blue-green deployment
    def currentColor = getCurrentColor(config)
    def newColor = (currentColor == 'blue') ? 'green' : 'blue'
    
    echo "Current active: ${currentColor}, deploying to: ${newColor}"
    
    // Deploy to inactive color
    deployToColor(config, newColor)
    
    // Run health checks
    verifyDeployment(config, newColor)
    
    // Switch traffic
    switchTraffic(config, newColor)
    
    echo "Blue-green deployment completed - now serving from ${newColor}"
}

def executeCanaryDeployment(config) {
    echo "Executing canary deployment..."
    
    def canaryPercent = config.canaryPercent ?: 10
    
    // Deploy canary version with small percentage of traffic
    deployCanary(config, canaryPercent)
    
    // Monitor metrics for specified duration
    def monitoringDuration = config.canaryMonitoringDuration ?: 300  // 5 minutes
    monitorCanary(config, monitoringDuration)
    
    // If metrics are good, promote to full deployment
    promoteCanary(config)
}

def performPostDeploymentChecks(config) {
    echo "Performing post-deployment checks..."
    
    // Health check
    def appUrl = getAppUrl(config)
    def healthCheckPassed = false
    def attempts = 0
    def maxAttempts = 30
    
    while (!healthCheckPassed && attempts < maxAttempts) {
        try {
            def response = sh(
                script: "curl -f -s ${appUrl}/health",
                returnStdout: true
            )
            
            if (response.contains('healthy') || response.contains('ok')) {
                healthCheckPassed = true
                echo "✅ Health check passed"
            }
        } catch (Exception e) {
            echo "Health check attempt ${attempts + 1} failed, retrying..."
            sleep(10)
        }
        attempts++
    }
    
    if (!healthCheckPassed) {
        error("Health check failed after ${maxAttempts} attempts")
    }
    
    // Run smoke tests if enabled
    if (config.runTests) {
        runSmokeTests(config)
    }
}

def runSmokeTests(config) {
    echo "Running smoke tests..."
    
    // Load and execute smoke test script
    def smokeTestScript = libraryResource('scripts/smoke-tests.sh')
    writeFile file: 'smoke-tests.sh', text: smokeTestScript
    sh 'chmod +x smoke-tests.sh'
    
    sh """
        export APP_URL=${getAppUrl(config)}
        export APP_NAME=${config.appName}
        export ENVIRONMENT=${config.environment}
        ./smoke-tests.sh
    """
    
    echo "✅ Smoke tests passed"
}

def getAppUrl(config) {
    // Generate URL based on environment and app name
    def baseUrls = [
        'dev': 'https://dev.company.com',
        'staging': 'https://staging.company.com',
        'production': 'https://company.com'
    ]
    
    def baseUrl = baseUrls[config.environment] ?: 'http://localhost'
    return "${baseUrl}/${config.appName}"
}

def sendDeploymentNotification(config, status, errorMessage = null) {
    if (!config.notifications) {
        return
    }
    
    def color = (status == 'success') ? 'good' : 'danger'
    def emoji = (status == 'success') ? '✅' : '❌'
    
    def message = "${emoji} Deployment ${status}: ${config.appName} v${config.version} to ${config.environment}"
    
    if (errorMessage) {
        message += "\nError: ${errorMessage}"
    }
    
    // Use notification service (would be another shared library function)
    sendSlackNotification([
        channel: '#deployments',
        color: color,
        message: message
    ])
}

def performRollback(config) {
    echo "Performing automatic rollback..."
    
    try {
        sh "kubectl rollout undo deployment/${config.appName} -n ${config.environment}"
        sh "kubectl rollout status deployment/${config.appName} -n ${config.environment} --timeout=300s"
        echo "✅ Rollback completed successfully"
    } catch (Exception e) {
        echo "❌ Rollback failed: ${e.getMessage()}"
        // Could send critical alert here
    }
}
```

### Using Shared Libraries in Pipelines

**Basic Library Usage:**
```groovy
@Library('jenkins-shared-library@main') _

pipeline {
    agent any
    
    environment {
        APP_NAME = 'my-web-app'
        VERSION = "${env.BUILD_NUMBER}"
    }
    
    stages {
        stage('Welcome') {
            steps {
                script {
                    // Use the greet function
                    def greeting = greet([
                        name: 'Development Team',
                        message: 'Starting build',
                        includeTime: true
                    ])
                    
                    echo "Greeting result: ${greeting}"
                }
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building application...'
                sh 'npm install && npm run build'
            }
        }
        
        stage('Test') {
            steps {
                // Use shared library test function
                runTests([
                    testType: 'jest',
                    coverage: true,
                    parallel: true
                ])
            }
        }
        
        stage('Deploy to Staging') {
            steps {
                script {
                    // Use the deployApp function
                    def deployResult = deployApp([
                        appName: env.APP_NAME,
                        version: env.VERSION,
                        environment: 'staging',
                        strategy: 'rolling',
                        replicas: 2,
                        healthCheckTimeout: 300,
                        autoRollback: true
                    ])
                    
                    echo "Deployment result: ${deployResult.status}"
                }
            }
        }
        
        stage('Deploy to Production') {
            when {
                branch 'main'
            }
            input {
                message: 'Deploy to production?'
                ok: 'Deploy'
                parameters {
                    choice(
                        name: 'DEPLOYMENT_STRATEGY',
                        choices: ['rolling', 'blue-green', 'canary'],
                        description: 'Deployment strategy'
                    )
                }
            }
            steps {
                script {
                    deployApp([
                        appName: env.APP_NAME,
                        version: env.VERSION,
                        environment: 'production',
                        strategy: params.DEPLOYMENT_STRATEGY,
                        replicas: 5,
                        canaryPercent: 10,
                        healthCheckTimeout: 600,
                        autoRollback: true
                    ])
                }
            }
        }
    }
    
    post {
        always {
            greet('Build Complete')
        }
    }
}
```

### Shared Library Best Practices

**Version Management:**
```groovy
// Use specific version tags for production
@Library('jenkins-shared-library@v2.1.0') _

// Use latest for development
@Library('jenkins-shared-library@main') _

// Load multiple libraries
@Library(['jenkins-shared-library@v2.1.0', 'security-library@v1.0.0']) _

// Load library with custom identifier
library identifier: 'my-lib@main', retriever: modernSCM([
    $class: 'GitSCMSource',
    remote: 'https://github.com/company/jenkins-library.git'
])
```

**Error Handling and Logging:**
```groovy
// vars/safeExecute.groovy
def call(String description, Closure action) {
    echo "🚀 Starting: ${description}"
    def startTime = System.currentTimeMillis()
    
    try {
        def result = action()
        def duration = System.currentTimeMillis() - startTime
        echo "✅ Completed: ${description} (${duration}ms)"
        return result
    } catch (Exception e) {
        def duration = System.currentTimeMillis() - startTime
        echo "❌ Failed: ${description} (${duration}ms)"
        echo "Error: ${e.getMessage()}"
        
        // Could send alerts, log to external systems, etc.
        logError(description, e)
        
        throw e
    }
}

def logError(description, exception) {
    // Log to external monitoring system
    def errorData = [
        timestamp: new Date().toString(),
        description: description,
        error: exception.getMessage(),
        pipeline: env.JOB_NAME,
        build: env.BUILD_NUMBER
    ]
    
    echo "Logging error: ${errorData}"
    
    // Could send to Elasticsearch, Splunk, etc.
}
```

---

## Practical Implementation Workshop

Let's create a comprehensive shared library that demonstrates all the concepts we've covered:

### Workshop: Building a Complete CI/CD Shared Library

**Project Structure:**
```
jenkins-workshop-library/
├── src/org/workshop/jenkins/
│   ├── BuildHelper.groovy
│   ├── TestRunner.groovy
│   └── DeploymentManager.groovy
├── vars/
│   ├── buildPipeline.groovy
│   ├── testApplication.groovy
│   └── deployToEnvironment.groovy
├── resources/
│   ├── templates/
│   └── scripts/
└── README.md
```

**vars/buildPipeline.groovy - Complete Pipeline Template:**
```groovy
#!/usr/bin/env groovy

def call(Map config) {
    pipeline {
        agent any
        
        environment {
            APP_NAME = "${config.appName}"
            BUILD_VERSION = "${env.BUILD_NUMBER}"
        }
        
        options {
            buildDiscarder(logRotator(numToKeepStr: '10'))
            timeout(time: 30, unit: 'MINUTES')
            timestamps()
        }
        
        stages {
            stage('Initialize') {
                steps {
                    script {
                        echo "🚀 Starting build for ${config.appName}"
                        
                        // Initialize build helper from src/
                        def buildHelper = new org.workshop.jenkins.BuildHelper()
                        env.BUILD_CONFIG = buildHelper.generateBuildConfig(config)
                    }
                }
            }
            
            stage('Build') {
                steps {
                    script {
                        def buildConfig = readJSON text: env.BUILD_CONFIG
                        
                        buildConfig.buildSteps.each { step ->
                            echo "Executing: ${step.name}"
                            sh step.command
                        }
                    }
                }
            }
            
            stage('Test') {
                parallel {
                    stage('Unit Tests') {
                        steps {
                            testApplication([
                                type: 'unit',
                                coverage: true,
                                reports: true
                            ])
                        }
                    }
                    stage('Integration Tests') {
                        steps {
                            testApplication([
                                type: 'integration',
                                coverage: false,
                                reports: true
                            ])
                        }
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
                    script {
                        def environment = (env.BRANCH_NAME == 'main') ? 'production' : 'staging'
                        
                        deployToEnvironment([
                            appName: config.appName,
                            version: env.BUILD_VERSION,
                            environment: environment,
                            strategy: config.deploymentStrategy ?: 'rolling'
                        ])
                    }
                }
            }
        }
        
        post {
            always {
                cleanWs()
            }
            success {
                echo "✅ Pipeline completed successfully"
            }
            failure {
                echo "❌ Pipeline failed"
            }
        }
    }
}
```

**Usage in Jenkinsfile:**
```groovy
@Library('jenkins-workshop-library@main') _

// Simple usage - the shared library provides the entire pipeline
buildPipeline([
    appName: 'my-web-application',
    deploymentStrategy: 'blue-green',
    testSuites: ['unit', 'integration', 'e2e'],
    notifications: [
        slack: '#deployments',
        email: 'team@company.com'
    ]
])
```

---

## Summary and Best Practices

### Key Concepts Mastered

**Groovy Fundamentals:**
- Dynamic typing and flexible syntax
- Closures and functional programming
- String interpolation and manipulation
- Control structures and loops

**Jenkins Integration:**
- Script blocks in declarative pipelines
- Script Console for administrative tasks
- Pipeline DSL and Jenkins APIs
- Error handling and debugging

**Shared Libraries:**
- Library structure and organization
- Creating reusable pipeline components
- Version management and deployment
- Documentation and best practices

### Best Practices Summary

**Code Organization:**
- Use clear, descriptive function names
- Implement proper error handling
- Document all public functions
- Follow consistent coding standards

**Security Considerations:**
- Validate all input parameters
- Use proper credential management
- Restrict Script Console access
- Implement appropriate logging

**Maintenance and Versioning:**
- Use semantic versioning for libraries
- Test library changes thoroughly
- Maintain backward compatibility
- Keep documentation up to date

### Next Steps

**Advanced Topics to Explore:**
- Complex shared library architectures
- Integration with external APIs
- Custom DSL development
- Performance optimization techniques

**Practical Exercises:**
- Build your own shared library
- Convert existing pipelines to use shared components
- Implement advanced deployment strategies
- Create administrative automation scripts

---

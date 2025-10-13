# Jenkins Session 1: Introduction and Fundamentals

## Learning Goals

After this session, you'll understand what Jenkins is, why teams use it, and how to get your first automation pipeline running.

## Why use Jenkins ?

**The Integration Nightmare**
- Developers work on features separately for weeks
- When they try to combine their code, everything breaks
- "It worked on my laptop" becomes the team's most-heard phrase
- Integration becomes a dreaded, multi-day event

**Manual Deployment Pain**
- Someone has to manually copy files to servers
- Different people deploy differently, causing inconsistencies
- Deployments often happen late at night or weekends
- When something goes wrong, rolling back is complicated

**Quality Issues**
- Testing happens too late in the process
- Bugs found in production are expensive to fix
- No consistent way to check code quality
- Security issues discovered after deployment

Jenkins helps solve them through automation.

## What is Jenkins?

Jenkins is an automation server. Main functions include:
- Monitor code repository for changes
- Automatically build and test code when changes happen
- Deploy application to different environments
- Send notifications about build status
- Run scheduled tasks like backups or reports

## Core Concepts

**Controller (Master)**
This is the main Jenkins server that coordinates everything. It:
- Hosts the web interface.
- Schedules and manages jobs
- Stores build results and configurations
- Handles user authentication

**Agents (Slaves)**
These are worker machines that do the actual build work. They:
- Execute the jobs assigned by the controller
- Can run on different operating systems
- Provide isolation between different types of builds
- Can be physical machines, VMs, or containers

**Jobs/Projects**
These define what work Jenkins should do. Common types include:
- Building and testing code
- Deploying applications
- Running maintenance scripts
- Generating reports

**Builds**
Each time a job runs, it creates a build. Builds have:
- A sequential number (#1, #2, #3, etc.)
- A status (Success, Failure, Unstable)
- Console logs showing what happened
- Artifacts (files produced during the build)

## Installing Jenkins

### Quick Start with Docker (Recommended for Learning)

This is the fastest way to get Jenkins running: (pre-requisites: Docker)

```bash
docker run -d -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  --name jenkins jenkins/jenkins:lts
```

Get the initial admin password:
```bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

### Linux Installation (Ubuntu/Debian)

First, install Java since Jenkins requires it:
```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
```

Add Jenkins repository and install:
```bash
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins -y
```

Start Jenkins:
```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

Get the initial password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

## Initial Setup

Once Jenkins is running, open http://localhost:8080 in your browser.

**Step 1: Unlock Jenkins**
Paste the initial admin password you found during installation.

**Step 2: Install Plugins**
Choose "Install suggested plugins" unless you have specific needs. This installs commonly used plugins like Git integration, pipeline support, and build tools.

**Step 3: Create Admin User**
Create your first user account. Use a strong password since this account has full administrative access.

**Step 4: Instance Configuration**
The default Jenkins URL is usually correct for local development. Click "Save and Finish."

## Exploring the Interface

Let's take a tour of the Jenkins interface:

**Dashboard**
- Shows all the jobs/projects
- Displays build history and status
- Provides quick access to system management

**Left Sidebar**
- New Item: Create new jobs
- People: User management
- Build History: Recent builds across all jobs
- Manage Jenkins: System configuration
- My Views: Customize the dashboard

**Job Dashboard**
When you click on a job, you'll see:
- Build history with status indicators
- Workspace (files used for builds)
- Configuration options
- Recent changes from version control

## Your First Job: Freestyle Project

Create a simple job to get familiar with Jenkins.

1. Click "New Item" from the dashboard
2. Enter name: "hello-world-job"
3. Select "Freestyle project"
4. Click OK

**General Configuration:**
- Add description: "My first Jenkins job"
- Check "Discard old builds" and keep last 10 builds

**Build Steps:**
Click "Add build step" and choose "Execute shell" (Linux/Mac) or "Execute Windows batch command" (Windows).

For Linux/Mac:
```bash
echo "Hello from Jenkins!"
echo "Current date: $(date)"
echo "Running on: $(hostname)"
echo "Build number: $BUILD_NUMBER"
```

Click "Save" and then "Build Now" to run your first job!

## Your First Pipeline

Pipelines are more powerful than freestyle jobs because they're defined as code. Let's create one:

1. Click "New Item"
2. Enter name: "hello-world-pipeline"
3. Select "Pipeline"
4. Click OK

In the Pipeline section, enter this script:

```groovy
pipeline {
    agent any
    
    stages {
        stage('Hello') {
            steps {
                echo 'Hello, Jenkins Pipeline!'
            }
        }
        
        stage('Environment') {
            steps {
                echo "Build number: ${env.BUILD_NUMBER}"
                echo "Job name: ${env.JOB_NAME}"
                sh 'date'
                sh 'whoami'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'echo "All tests passed!"'
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline finished!'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
```

Save and build this pipeline. Notice how it shows the different stages as they execute.

## Understanding Build Triggers

Jobs can be triggered in several ways:

**Manual Triggering**
Click "Build Now" to start a job immediately.

**SCM Polling**
Jenkins can check your version control system for changes:
```
H/5 * * * *    # Check every 5 minutes
H 8-17 * * 1-5 # Check hourly during business hours, weekdays only
```

**Scheduled Builds**
Run jobs at specific times using cron syntax:
```
H 2 * * *      # Daily at 2 AM (H randomizes the minute)
H 22 * * 1-5   # 10 PM on weekdays
0 */4 * * *    # Every 4 hours
```

**Webhooks**
Your version control system can notify Jenkins immediately when code changes. This is the most efficient method.

## User Management and Security

By default, Jenkins allows anyone to do anything. For real use, you need proper security.

**Enable Security:**
1. Go to Manage Jenkins → Configure Global Security
2. Check "Enable security"
3. Security Realm: Choose "Jenkins' own user database"
4. Authorization: Start with "Logged-in users can do anything"

**Create Users:**
1. Go to Manage Jenkins → Manage Users
2. Click "Create User"
3. Fill in the details and create accounts for your team members

**Credentials Management:**
Jenkins provides secure storage for passwords, API keys, and certificates:
1. Go to Manage Jenkins → Manage Credentials
2. Add credentials for Git repositories, deployment targets, etc.
3. Reference them in jobs using their ID

## Setting Up Notifications

Getting notified about build results is crucial for team coordination.

**Email Notifications:**
1. Go to Manage Jenkins → Configure System
2. Find "E-mail Notification" section
3. Configure your SMTP server settings
4. Test the configuration

In jobs, add "E-mail Notification" as a post-build action.

**Slack Integration:** (will be covered as a working session)
1. Install the Slack Notification plugin
2. Configure your Slack workspace details
3. Add Slack notifications to your pipelines:

```groovy
post {
    success {
        slackSend channel: '#builds',
                  color: 'good',
                  message: "Build succeeded: ${env.JOB_NAME} - ${env.BUILD_NUMBER}"
    }
    failure {
        slackSend channel: '#builds',
                  color: 'danger',
                  message: "Build failed: ${env.JOB_NAME} - ${env.BUILD_NUMBER}"
    }
}
```

## Best Practices for Beginners

**Job Naming**
Use clear, descriptive names with consistent patterns:
- my-web-app-build
- user-service-tests
- database-backup-daily

**Build Cleanup**
Configure jobs to keep only recent builds to save disk space:
- Keep last 10 builds
- Keep builds for 30 days
- Keep artifacts for fewer builds than logs

**Use Folders**
Install the Folders plugin to organize related jobs:
- Frontend Applications
- Backend Services
- Database Scripts
- Deployment Jobs

**Monitor Your Builds**
- Check the build history regularly
- Investigate failed builds promptly
- Monitor build duration trends

## Common Issues and Solutions

**Build Fails with "Command not found"**
- The tool isn't installed on the Jenkins agent
- The tool isn't in the PATH
- Solution: Install the tool or configure tool locations in Global Tool Configuration

**Permission Denied Errors**
- Jenkins user doesn't have proper file permissions
- Solution: Check file ownership and permissions

**Workspace Issues**
- Files from previous builds interfering
- Solution: Enable "Delete workspace before build starts"

**Builds Never Start**
- No available agents/executors
- Solution: Check agent status, increase executor count, or add more agents

## Hands-On Exercise

Let's build something practical together. We'll create a simple CI pipeline for a basic project.

**Project Setup:**
1. Create a new GitHub repository (or use an existing one)
2. Add a simple script or application
3. Include a basic test

**Jenkins Pipeline:**
```groovy
pipeline {
    agent any
    
    environment {
        PROJECT_NAME = 'jenkins-demo'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/hkm7/jenkins-demo/tree/main'
            }
        }
        
        stage('read') {
            steps {
                echo "Reading ${PROJECT_NAME}..."
                sh 'ls -la'
				sh "cat Dockerfile"
            }
        }
        
        stage('Output') {
            steps {
                echo 'Running output...'
                sh "cat my-file.txt"
            }
        }
    }
    
    post {
        always {
            echo 'Cleaning up...'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs.'
        }
    }
}
```

## Additional Resources

**Documentation:**
- Official Jenkins documentation: jenkins.io/doc/
- Pipeline syntax reference: jenkins.io/doc/book/pipeline/syntax/

**Community:**
- Jenkins community forums: community.jenkins.io
- Stack Overflow: Search for Jenkins-related questions

**Plugins:**
- Browse available plugins: plugins.jenkins.io
- Essential plugins: Blue Ocean, Pipeline Stage View, Build Timeout


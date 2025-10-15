# Jenkins Session 3: Managing Pipelines - Comprehensive Notes

## Session Overview

This session builds on pipeline fundamentals to cover advanced pipeline management techniques. We'll explore multibranch pipelines, scheduled builds, and practical implementation with Python applications.

## Learning Objectives

After completing this session, you'll be able to:
- Create and manage multibranch pipeline jobs
- Configure scheduled triggers using cron syntax
- Build and test Python applications in Jenkins
- Implement proper branch-based workflows
- Handle feature branches and pull requests automatically

---

## Multibranch Pipelines

### What Are Multibranch Pipelines?

Multibranch pipelines represent one of Jenkins' most powerful features for modern development workflows. Instead of creating separate jobs for each branch, Jenkins automatically discovers branches in your repository and creates pipeline jobs for each one that contains a Jenkinsfile.

**Key Benefits:**
- **Automatic Branch Discovery**: Jenkins scans your repository and finds all branches with Jenkinsfiles
- **Independent Builds**: Each branch builds in isolation with its own workspace
- **Pull Request Integration**: Automatically build and test pull requests before merging
- **Feature Branch Support**: Developers can test their changes without affecting main branch builds
- **Simplified Management**: One job configuration handles multiple branches

### How Multibranch Pipelines Work

When you create a multibranch pipeline job, Jenkins:

1. **Scans the Repository**: Looks for branches containing a Jenkinsfile
2. **Creates Sub-Jobs**: Automatically creates a pipeline job for each qualifying branch
3. **Monitors Changes**: Watches for new branches, deleted branches, and Jenkinsfile changes
4. **Triggers Builds**: Runs builds when code changes are pushed to any branch

### Setting Up a Multibranch Pipeline

**Step 1: Create the Job**
1. From Jenkins dashboard, click "New Item"
2. Enter job name (e.g., "my-app-multibranch")
3. Select "Multibranch Pipeline"
4. Click "OK"

**Step 2: Configure Branch Sources**
1. **Add Source**: Choose your version control system (Git, GitHub, Bitbucket, etc.)
2. **Repository URL**: Enter your repository URL
3. **Credentials**: Add credentials if repository is private
4. **Behaviors**: Configure additional behaviors like:
   - Discover branches
   - Discover pull requests from origin
   - Discover pull requests from forks

**Step 3: Build Configuration**
1. **Script Path**: Usually "Jenkinsfile" (default)
2. **Scan Multibranch Pipeline Triggers**: Configure how often to scan for new branches
3. **Orphaned Item Strategy**: What to do with builds from deleted branches

**Step 4: Branch Discovery Settings**
```groovy
// Example branch discovery configuration
// This goes in your multibranch pipeline configuration, not Jenkinsfile

Branch Sources:
  - Git:
      Repository URL: https://github.com/company/my-app.git
      Credentials: github-credentials
      Behaviors:
        - Discover branches
        - Discover pull requests from origin
        - Clean before checkout
        - Prune stale remote-tracking branches
```

### Jenkinsfile for Multibranch Pipelines

Your Jenkinsfile can adapt behavior based on the branch being built:

```groovy
pipeline {
    agent any
    
    environment {
        APP_NAME = 'my-python-app'
        // Branch-specific environment variables
        DEPLOY_ENV = "${env.BRANCH_NAME == 'main' ? 'production' : 'staging'}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo "Building branch: ${env.BRANCH_NAME}"
                echo "Deploy environment: ${DEPLOY_ENV}"
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building Python application...'
                sh '''
                    python -m pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running Python tests...'
                sh '''
                    python -m pytest tests/ -v --junitxml=test-results.xml
                '''
            }
            post {
                always {
                    junit 'test-results.xml'
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
                echo "Deploying to ${DEPLOY_ENV} environment"
                script {
                    if (env.BRANCH_NAME == 'main') {
                        echo 'Deploying to production...'
                        // Production deployment logic
                    } else {
                        echo 'Deploying to staging...'
                        // Staging deployment logic
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
            echo "Build successful for branch: ${env.BRANCH_NAME}"
        }
        failure {
            echo "Build failed for branch: ${env.BRANCH_NAME}"
        }
    }
}
```

### Advanced Multibranch Features

**Branch Indexing:**
Configure how often Jenkins scans for new branches:
```groovy
// In multibranch pipeline configuration
triggers {
    periodic(5) // Scan every 5 minutes
}
```

**Branch-Specific Logic:**
```groovy
stage('Security Scan') {
    when {
        not { branch 'feature/*' }  // Skip security scan for feature branches
    }
    steps {
        echo 'Running security scan...'
        // Security scanning logic
    }
}

stage('Performance Test') {
    when {
        branch 'main'  // Only run performance tests on main branch
    }
    steps {
        echo 'Running performance tests...'
        // Performance testing logic
    }
}
```

**Pull Request Handling:**
```groovy
stage('PR Validation') {
    when {
        changeRequest()  // Only run for pull requests
    }
    steps {
        echo "Validating PR-${env.CHANGE_ID}"
        // PR-specific validation logic
    }
}
```

---

## Scheduled Triggers

### Understanding Cron Syntax in Jenkins

Jenkins uses a modified cron syntax to schedule builds. The standard format has five fields:

```
┌───────────── minute (0 - 59)
│ ┌─────────── hour (0 - 23)
│ │ ┌───────── day of month (1 - 31)
│ │ │ ┌─────── month (1 - 12)
│ │ │ │ ┌───── day of week (0 - 7) (Sunday is both 0 and 7)
│ │ │ │ │
* * * * *
```

### Jenkins-Specific Enhancements

Jenkins adds several enhancements to standard cron:

**Hash Symbol (H):**
Instead of using specific times, use `H` to distribute load:
```bash
H * * * *        # Every hour at a hash-based minute (e.g., 17, 17, 17...)
H H * * *        # Once per day at a hash-based time
H H * * H        # Once per week at a hash-based day/time
```

The hash is based on the job name, ensuring consistent but distributed scheduling.

### Common Scheduling Patterns

**Basic Patterns:**
```bash
# Every minute (usually for testing only)
* * * * *

# Every 5 minutes
H/5 * * * *

# Every 15 minutes
H/15 * * * *

# Every hour at hash-based minute
H * * * *

# Every 2 hours
H H/2 * * *

# Daily at 2 AM (hash-based minute)
H 2 * * *

# Weekdays at 9 AM
H 9 * * 1-5

# Weekends only
H H * * 0,6

# First day of every month
H H 1 * *

# Every Monday at 8 AM
H 8 * * 1
```

**Advanced Patterns:**
```bash
# Multiple times per day
H 6,12,18 * * *          # 6 AM, 12 PM, 6 PM

# Business hours only
H 8-17 * * 1-5           # Every hour from 8 AM to 5 PM, weekdays

# Quarterly builds
H H 1 1,4,7,10 *         # First day of each quarter

# Avoid peak hours
H H(0-7,18-23) * * *     # Only during off-peak hours

# Holiday-aware scheduling (requires additional logic)
H 2 * * * && date +%m%d | grep -v -E "(1225|0101|0704)"
```

### Configuring Scheduled Triggers

**Method 1: Through UI**
1. Open job configuration
2. Scroll to "Build Triggers" section
3. Check "Build periodically"
4. Enter cron expression in the text box
5. Save configuration

**Method 2: In Jenkinsfile**
```groovy
pipeline {
    agent any
    
    triggers {
        // Build every weekday at 2 AM
        cron('H 2 * * 1-5')
        
        // You can have multiple triggers
        // cron('H 14 * * *')  // Also at 2 PM daily
    }
    
    stages {
        stage('Scheduled Build') {
            steps {
                echo "This is a scheduled build at ${new Date()}"
            }
        }
    }
}
```

**Method 3: Using Properties**
```groovy
properties([
    pipelineTriggers([
        cron('H 2 * * 1-5'),  // Weekdays at 2 AM
        pollSCM('H/15 * * * *')  // Poll SCM every 15 minutes
    ])
])

pipeline {
    agent any
    stages {
        // Your pipeline stages
    }
}
```

### Best Practices for Scheduling

**Load Distribution:**
- Always use `H` instead of fixed times to distribute load
- Stagger builds across teams to avoid resource contention
- Consider time zones for global teams

**Resource Optimization:**
```groovy
// Don't run heavy jobs during business hours
triggers {
    cron('H H(18-6) * * *')  // Only run between 6 PM and 6 AM
}
```

**Conditional Scheduling:**
```groovy
pipeline {
    agent any
    
    triggers {
        // Different schedules for different branches
        cron(env.BRANCH_NAME == 'main' ? 'H 2 * * *' : 'H 2 * * 0')
    }
    
    stages {
        stage('Build') {
            steps {
                script {
                    if (currentBuild.getBuildCauses('hudson.triggers.TimerTrigger$TimerTriggerCause')) {
                        echo 'This build was triggered by a timer'
                    }
                }
            }
        }
    }
}
```

---

## Python Application Build Pipeline

Let's create a comprehensive Python build pipeline that demonstrates best practices.

### Project Structure

```
my-python-app/
├── Jenkinsfile
├── requirements.txt
├── requirements-dev.txt
├── setup.py
├── src/
│   ├── __init__.py
│   ├── main.py
│   └── utils.py
├── tests/
│   ├── __init__.py
│   ├── test_main.py
│   └── test_utils.py
├── .gitignore
└── README.md
```

### Sample Python Application

**src/main.py:**
```python
#!/usr/bin/env python3
"""
Sample Python application for Jenkins demo
"""

def add_numbers(a, b):
    """Add two numbers and return the result."""
    return a + b

def multiply_numbers(a, b):
    """Multiply two numbers and return the result."""
    return a * b

def divide_numbers(a, b):
    """Divide two numbers and return the result."""
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b

def main():
    """Main function."""
    print("Python Calculator Demo")
    print("======================")
    
    # Demo calculations
    result1 = add_numbers(10, 5)
    result2 = multiply_numbers(7, 3)
    result3 = divide_numbers(20, 4)
    
    print(f"10 + 5 = {result1}")
    print(f"7 * 3 = {result2}")
    print(f"20 / 4 = {result3}")
    
    return "Application completed successfully"

if __name__ == "__main__":
    main()
```

**tests/test_main.py:**
```python
#!/usr/bin/env python3
"""
Test cases for main.py
"""

import pytest
import sys
import os

# Add src directory to path
sys.path.insert(0, os.path.join(os.path.dirname(__file__), '..', 'src'))

from main import add_numbers, multiply_numbers, divide_numbers, main

class TestCalculatorFunctions:
    """Test class for calculator functions."""
    
    def test_add_numbers(self):
        """Test addition function."""
        assert add_numbers(2, 3) == 5
        assert add_numbers(-1, 1) == 0
        assert add_numbers(0, 0) == 0
        assert add_numbers(10, -5) == 5
    
    def test_multiply_numbers(self):
        """Test multiplication function."""
        assert multiply_numbers(3, 4) == 12
        assert multiply_numbers(-2, 3) == -6
        assert multiply_numbers(0, 100) == 0
        assert multiply_numbers(7, 1) == 7
    
    def test_divide_numbers(self):
        """Test division function."""
        assert divide_numbers(10, 2) == 5
        assert divide_numbers(15, 3) == 5
        assert divide_numbers(-10, 2) == -5
        assert divide_numbers(7, 2) == 3.5
    
    def test_divide_by_zero(self):
        """Test division by zero raises exception."""
        with pytest.raises(ValueError, match="Cannot divide by zero"):
            divide_numbers(10, 0)
    
    def test_main_function(self):
        """Test main function runs without error."""
        result = main()
        assert result == "Application completed successfully"

class TestEdgeCases:
    """Test edge cases and error conditions."""
    
    def test_large_numbers(self):
        """Test with large numbers."""
        result = add_numbers(999999999, 1)
        assert result == 1000000000
    
    def test_floating_point(self):
        """Test with floating point numbers."""
        result = add_numbers(1.5, 2.5)
        assert result == 4.0
        
        result = multiply_numbers(2.5, 4)
        assert result == 10.0
```

**requirements.txt:**
```
# Production dependencies
requests==2.31.0
flask==2.3.2
```

**requirements-dev.txt:**
```
# Development dependencies
pytest==7.4.0
pytest-cov==4.1.0
black==23.7.0
flake8==6.0.0
mypy==1.4.1
```

### Comprehensive Python Pipeline

```groovy
pipeline {
    agent any
    
    environment {
        APP_NAME = 'python-calculator'
        PYTHON_VERSION = '3.9'
        VIRTUAL_ENV = "${WORKSPACE}/venv"
    }
    
    options {
        // Keep builds for 30 days, max 10 builds
        buildDiscarder(logRotator(daysToKeepStr: '30', numToKeepStr: '10'))
        
        // Timeout if build takes longer than 20 minutes
        timeout(time: 20, unit: 'MINUTES')
        
        // Add timestamps to console output
        timestamps()
    }
    
    triggers {
        // Build daily at 2 AM for main branch
        cron(env.BRANCH_NAME == 'main' ? 'H 2 * * *' : '')
        
        // Poll SCM every 15 minutes for changes
        pollSCM('H/15 * * * *')
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo "Building ${APP_NAME} from branch: ${env.BRANCH_NAME}"
                echo "Build number: ${env.BUILD_NUMBER}"
                echo "Workspace: ${env.WORKSPACE}"
            }
        }
        
        stage('Setup Python Environment') {
            steps {
                echo 'Setting up Python virtual environment...'
                sh '''
                    # Create virtual environment
                    python3 -m venv ${VIRTUAL_ENV}
                    
                    # Activate virtual environment
                    . ${VIRTUAL_ENV}/bin/activate
                    
                    # Upgrade pip
                    pip install --upgrade pip
                    
                    # Install dependencies
                    pip install -r requirements.txt
                    pip install -r requirements-dev.txt
                    
                    # Verify installation
                    pip list
                    python --version
                '''
            }
        }
        
        stage('Code Quality') {
            parallel {
                stage('Linting') {
                    steps {
                        echo 'Running code linting...'
                        sh '''
                            . ${VIRTUAL_ENV}/bin/activate
                            
                            # Run flake8 for style checking
                            flake8 src/ tests/ --max-line-length=88 --extend-ignore=E203,W503
                            
                            # Run black for code formatting check
                            black --check src/ tests/
                        '''
                    }
                    post {
                        always {
                            // Archive linting results
                            sh '''
                                . ${VIRTUAL_ENV}/bin/activate
                                flake8 src/ tests/ --format=junit-xml --output-file=flake8-results.xml || true
                            '''
                            junit 'flake8-results.xml'
                        }
                    }
                }
                
                stage('Type Checking') {
                    steps {
                        echo 'Running type checking...'
                        sh '''
                            . ${VIRTUAL_ENV}/bin/activate
                            mypy src/ --ignore-missing-imports || true
                        '''
                    }
                }
            }
        }
        
        stage('Unit Tests') {
            steps {
                echo 'Running unit tests...'
                sh '''
                    . ${VIRTUAL_ENV}/bin/activate
                    
                    # Run tests with coverage
                    pytest tests/ -v \
                        --junitxml=test-results.xml \
                        --cov=src \
                        --cov-report=html:htmlcov \
                        --cov-report=xml:coverage.xml \
                        --cov-report=term
                '''
            }
            post {
                always {
                    // Publish test results
                    junit 'test-results.xml'
                    
                    // Publish coverage report
                    publishHTML([
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'htmlcov',
                        reportFiles: 'index.html',
                        reportName: 'Coverage Report'
                    ])
                    
                    // Archive coverage XML for further processing
                    archiveArtifacts artifacts: 'coverage.xml', allowEmptyArchive: true
                }
            }
        }
        
        stage('Integration Tests') {
            when {
                anyOf {
                    branch 'main'
                    branch 'develop'
                    changeRequest()  // Run for pull requests
                }
            }
            steps {
                echo 'Running integration tests...'
                sh '''
                    . ${VIRTUAL_ENV}/bin/activate
                    
                    # Run the application as integration test
                    python src/main.py
                    
                    # Additional integration tests would go here
                    echo "Integration tests completed successfully"
                '''
            }
        }
        
        stage('Build Package') {
            steps {
                echo 'Building Python package...'
                sh '''
                    . ${VIRTUAL_ENV}/bin/activate
                    
                    # Install build tools
                    pip install build wheel
                    
                    # Build package
                    python -m build
                    
                    # List created packages
                    ls -la dist/
                '''
            }
            post {
                success {
                    // Archive build artifacts
                    archiveArtifacts artifacts: 'dist/*', allowEmptyArchive: false
                }
            }
        }
        
        stage('Security Scan') {
            when {
                not { branch 'feature/*' }  // Skip for feature branches
            }
            steps {
                echo 'Running security scan...'
                sh '''
                    . ${VIRTUAL_ENV}/bin/activate
                    
                    # Install security tools
                    pip install safety bandit
                    
                    # Check for known security vulnerabilities in dependencies
                    safety check --json --output safety-report.json || true
                    
                    # Run bandit for security issues in code
                    bandit -r src/ -f json -o bandit-report.json || true
                '''
            }
            post {
                always {
                    // Archive security reports
                    archiveArtifacts artifacts: '*-report.json', allowEmptyArchive: true
                }
            }
        }
        
        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                echo 'Deploying application...'
                script {
                    // Deployment logic would go here
                    echo "Deploying ${APP_NAME} version ${env.BUILD_NUMBER}"
                    
                    // Example deployment steps:
                    sh '''
                        . ${VIRTUAL_ENV}/bin/activate
                        
                        # Package for deployment
                        tar -czf ${APP_NAME}-${BUILD_NUMBER}.tar.gz src/ requirements.txt
                        
                        # Upload to deployment server (example)
                        echo "Would deploy ${APP_NAME}-${BUILD_NUMBER}.tar.gz to production"
                    '''
                }
            }
        }
    }
    
    post {
        always {
            echo 'Cleaning up...'
            sh '''
                # Remove virtual environment
                rm -rf ${VIRTUAL_ENV}
                
                # Clean up temporary files
                find . -type f -name "*.pyc" -delete
                find . -type d -name "__pycache__" -exec rm -rf {} + || true
            '''
        }
        
        success {
            echo "✅ Pipeline completed successfully for ${env.BRANCH_NAME}"
            // Send success notification
            emailext (
                subject: "✅ Build Success: ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
                body: "The build was successful.\n\nBranch: ${env.BRANCH_NAME}\nBuild: ${env.BUILD_URL}",
                to: "${env.CHANGE_AUTHOR_EMAIL ?: 'team@company.com'}"
            )
        }
        
        failure {
            echo "❌ Pipeline failed for ${env.BRANCH_NAME}"
            // Send failure notification
            emailext (
                subject: "❌ Build Failed: ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
                body: "The build failed. Please check the console output.\n\nBranch: ${env.BRANCH_NAME}\nBuild: ${env.BUILD_URL}",
                to: "${env.CHANGE_AUTHOR_EMAIL ?: 'team@company.com'}"
            )
        }
        
        unstable {
            echo "⚠️ Pipeline completed with warnings for ${env.BRANCH_NAME}"
        }
    }
}
```

---

## Practical Exercises

### Exercise 1: Create a Multibranch Pipeline

1. **Create a GitHub Repository**:
   - Create a new repository with main and develop branches
   - Add the Python application code above
   - Create a feature branch with some changes

2. **Set Up Multibranch Pipeline**:
   - Create a multibranch pipeline job in Jenkins
   - Configure it to use your GitHub repository
   - Set up branch discovery and pull request detection

3. **Test the Pipeline**:
   - Push changes to different branches
   - Create a pull request
   - Observe how Jenkins creates separate builds for each branch

### Exercise 2: Configure Scheduled Builds

 **Add Scheduling to Pipeline**:
   - Modify your Jenkinsfile to include cron triggers
   - Set different schedules for different branches
   - Test the scheduling by checking build history

---

## Best Practices

### Multibranch Pipeline Best Practices

**Repository Structure**:
- Keep Jenkinsfile in repository root
- Use consistent naming for branches
- Include comprehensive README with build instructions

**Branch Strategy**:
- Use feature branches for development
- Protect main branch with required status checks
- Clean up stale branches regularly

**Performance Optimization**:
- Use pipeline caching for dependencies
- Parallelize independent stages
- Clean up workspaces after builds

### Scheduling Best Practices

**Load Distribution**:
- Always use H for hash-based scheduling
- Stagger builds across teams
- Consider global time zones

**Resource Management**:
- Schedule heavy builds during off-peak hours
- Use conditional scheduling based on branch
- Monitor build queue length

### Python Pipeline Best Practices

**Environment Management**:
- Always use virtual environments
- Pin dependency versions
- Separate dev and production dependencies

**Testing Strategy**:
- Include unit, integration, and security tests
- Maintain high code coverage
- Use quality gates to prevent bad code from advancing

**Artifact Management**:
- Archive important build artifacts
- Use semantic versioning
- Clean up old artifacts regularly

---

## Troubleshooting Common Issues

### Multibranch Pipeline Issues

**Problem**: Branches not being discovered
**Solution**: 
- Check repository permissions
- Verify webhook configuration
- Review branch discovery settings

**Problem**: Builds not triggering on push
**Solution**:
- Confirm webhook is configured correctly
- Check GitHub/Git integration settings
- Verify repository access permissions

### Scheduling Issues

**Problem**: Builds not running at scheduled time
**Solution**:
- Verify cron syntax using online validators
- Check Jenkins system time zone
- Review build queue for capacity issues

**Problem**: All builds running at same time
**Solution**:
- Replace fixed times with H syntax
- Stagger schedules across different jobs
- Review load balancing configuration

### Python Build Issues

**Problem**: Dependencies not installing
**Solution**:
- Check Python version compatibility
- Verify requirements.txt format
- Review network connectivity for package downloads

**Problem**: Tests failing inconsistently
**Solution**:
- Check for test dependencies on external services
- Review test isolation
- Verify environment variable configuration

---

## Summary

In this session, we covered:

- **Multibranch Pipelines**: Automated branch discovery and independent builds for each branch
- **Scheduled Triggers**: Using cron syntax with Jenkins enhancements for automated builds
- **Python Application Builds**: Complete pipeline with testing, linting, and deployment
- **Best Practices**: Performance optimization, resource management, and troubleshooting

These concepts form the foundation for managing complex development workflows with multiple branches, automated testing, and continuous integration practices.

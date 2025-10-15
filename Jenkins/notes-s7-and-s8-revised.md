# Enterprise Jenkins Architecture & Production Deployment

## Why Enterprise Architecture Matters

Most organizations start with a single Jenkins instance running on a spare server. This works fine for small teams, but as you scale, you'll hit these walls:

**Performance Bottlenecks**
- Single controller handling 100+ jobs becomes slow
- Build queue backs up during peak hours
- Memory issues when running complex pipelines
- Disk space fills up with build artifacts

**Reliability Problems** 
- Single point of failure affects entire organization
- No disaster recovery plan
- Manual backup processes that often fail
- Downtime affects all development teams

**Security and Compliance Issues**
- Everyone has admin access because permissions are too complex
- Credentials stored insecurely 
- No audit trail for compliance requirements
- Difficulty isolating different environments

**Operational Overhead**
- Plugin management becomes nightmare across teams
- Different teams need different tool versions
- No consistent deployment patterns
- Manual scaling during busy periods

## Enterprise Architecture Principles

**Horizontal Scaling Over Vertical**
Instead of making one Jenkins instance bigger, multiple instances are created and load distributed. This gives better isolation, performance, and reliability.

**Infrastructure as Code**
Everything should be defined in version-controlled configuration files.

**Security by Design**
Build security into the architecture from day one. Use proper authentication, authorization, secret management, and network segmentation.

**Observability First**
Comprehensive monitoring, logging, and alerting are non-negotiable in enterprise environments.

## Multi-Controller Architecture Patterns

Let's explore different ways to scale Jenkins horizontally:

### Pattern 1: Team-Based Controllers

Each team gets their own Jenkins controller. This provides the best isolation but requires more operational overhead.

```yaml
# Team-based architecture
controllers:
  frontend-team:
    namespace: jenkins-frontend
    resources:
      cpu: "2"
      memory: "4Gi"
    storage: "100Gi"
    
  backend-team:
    namespace: jenkins-backend
    resources:
      cpu: "4" 
      memory: "8Gi"
    storage: "200Gi"
    
  platform-team:
    namespace: jenkins-platform
    resources:
      cpu: "8"
      memory: "16Gi" 
    storage: "500Gi"
```

**When to use:** Large organizations with distinct teams that rarely interact.

**Pros:**
- Complete isolation between teams
- Teams can customize their Jenkins setup
- Failure in one team doesn't affect others
- Clear ownership and responsibility

**Cons:**
- Higher operational overhead
- Duplicate effort across teams
- Harder to share common practices
- More infrastructure costs

### Pattern 2: Environment-Based Controllers

Separate controllers for different environments (dev, staging, production).

```yaml
environments:
  development:
    security_level: low
    auto_deployment: enabled
    resource_limits: flexible
    
  staging:
    security_level: medium
    auto_deployment: enabled
    resource_limits: moderate
    approval_required: false
    
  production:
    security_level: high
    auto_deployment: disabled
    resource_limits: strict
    approval_required: true
    audit_logging: comprehensive
```

**When to use:** Organizations with strict environment separation requirements or compliance needs.

**Pros:**
- Clear environment boundaries
- Different security policies per environment
- Easier compliance and auditing
- Safer production deployments

**Cons:**
- Pipeline complexity increases
- Need to sync configurations across environments
- Potential for environment drift

### Pattern 3: Workload-Based Controllers

Different controllers optimized for different types of work.

```yaml
workloads:
  build_controller:
    purpose: "Code compilation and packaging"
    agents: "high-cpu"
    optimization: "parallel_builds"
    
  test_controller:
    purpose: "Running test suites"
    agents: "high-memory"
    optimization: "test_parallelization"
    
  deploy_controller:
    purpose: "Application deployment"
    agents: "network_optimized"
    optimization: "deployment_pipelines"
    security: "enhanced"
```

**When to use:** Organizations with very different performance requirements for different types of work.

## High Availability Setup

A production Jenkins environment needs to handle failures gracefully.

### Active-Passive Controller Configuration

The most common HA pattern uses two controllers: one active, one standby.

```yaml
# docker-compose.yml for HA setup
version: '3.8'
services:
  jenkins-primary:
    image: jenkins/jenkins:lts
    volumes:
      - jenkins_home:/var/jenkins_home
      - ./config:/var/jenkins_home/casc_configs
    environment:
      - CASC_JENKINS_CONFIG=/var/jenkins_home/casc_configs
    ports:
      - "8080:8080"
      - "50000:50000"
    networks:
      - jenkins-network
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/login"]
      interval: 30s
      timeout: 10s
      retries: 3
      
  jenkins-standby:
    image: jenkins/jenkins:lts
    volumes:
      - jenkins_home:/var/jenkins_home:ro  # Read-only
      - ./config:/var/jenkins_home/casc_configs:ro
    environment:
      - CASC_JENKINS_CONFIG=/var/jenkins_home/casc_configs
    ports:
      - "8081:8080"
    networks:
      - jenkins-network
    profiles:
      - standby
    depends_on:
      - jenkins-primary

  haproxy:
    image: haproxy:2.4
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg:ro
      - ./ssl:/etc/ssl/certs:ro
    depends_on:
      - jenkins-primary
    networks:
      - jenkins-network

volumes:
  jenkins_home:
    driver: local
    driver_opts:
      type: nfs
      o: addr=nfs-server.company.com,rw
      device: ":/jenkins_home"

networks:
  jenkins-network:
    driver: bridge
```

### Load Balancer Configuration

```bash
# haproxy.cfg
global
    daemon
    maxconn 4096
    ssl-default-bind-ciphers ECDHE+AESGCM:ECDHE+CHACHA20:!aNULL:!MD5:!DSS
    ssl-default-bind-options no-sslv3 no-tlsv10 no-tlsv11

defaults
    mode http
    timeout connect 5000ms
    timeout client 50000ms
    timeout server 50000ms
    option httpchk GET /login

frontend jenkins_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/jenkins.pem
    redirect scheme https if !{ ssl_fc }
    
    # Sticky sessions for Jenkins
    cookie JSESSIONID prefix nocache
    
    default_backend jenkins_controllers

backend jenkins_controllers
    balance roundrobin
    option httpchk GET /api/json
    
    server jenkins1 jenkins-primary:8080 check cookie jenkins1
    server jenkins2 jenkins-standby:8080 check cookie jenkins2 backup
```

### Shared Storage Configuration

Enterprise Jenkins needs shared storage for the JENKINS_HOME directory:

```yaml
# Kubernetes PersistentVolume example
apiVersion: v1
kind: PersistentVolume
metadata:
  name: jenkins-pv
spec:
  capacity:
    storage: 1Ti
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  storageClassName: nfs
  nfs:
    server: nfs-server.company.com
    path: /exports/jenkins
    
---
apiVersion: v1  
kind: PersistentVolumeClaim
metadata:
  name: jenkins-pvc
  namespace: jenkins
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 1Ti
  storageClassName: nfs
```

## Configuration as Code (JCasC)

Manual configuration doesn't scale in enterprise environments. Everything should be defined as code.

### Complete JCasC Configuration

```yaml
# jenkins.yaml - Complete enterprise configuration
jenkins:
  systemMessage: "Enterprise Jenkins - Managed by Configuration as Code"
  numExecutors: 0  # Use agents only
  mode: EXCLUSIVE
  scmCheckoutRetryCount: 3
  quietPeriod: 5
  
  # Global pipeline libraries
  globalLibraries:
    libraries:
      - name: "enterprise-pipeline-library"
        defaultVersion: "main"
        allowVersionOverride: true
        implicit: false
        retriever:
          modernSCM:
            scm:
              git:
                remote: "https://github.com/company/jenkins-pipeline-library.git"
                credentialsId: "github-enterprise-token"
                
  # Security realm - LDAP integration
  securityRealm:
    ldap:
      configurations:
        - server: "ldaps://ldap.company.com:636"
          rootDN: "dc=company,dc=com"
          managerDN: "cn=jenkins,ou=service,dc=company,dc=com"
          managerPasswordSecret: "${LDAP_BIND_PASSWORD}"
          userSearchBase: "ou=people"
          userSearch: "uid={0}"
          groupSearchBase: "ou=groups"
          groupSearchFilter: "member={0}"
          cache:
            size: 500
            ttl: 600
            
  # Authorization strategy
  authorizationStrategy:
    roleBased:
      roles:
        global:
          - name: "jenkins-admins"
            description: "Jenkins system administrators"
            permissions:
              - "Overall/Administer"
            members:
              - "platform-team"
              
          - name: "developers"
            description: "Application developers"
            permissions:
              - "Overall/Read"
              - "Job/Build"
              - "Job/Cancel"
              - "Job/Read"
              - "Job/Workspace"
              - "Run/Replay"
              - "Run/Update"
            members:
              - "dev-team"
              - "qa-team"
              
          - name: "viewers"
            description: "Read-only access"
            permissions:
              - "Overall/Read"
              - "Job/Read"
            members:
              - "management"
              - "business-analysts"
              
        projectRoles:
          - name: "project-owners"
            pattern: ".*"
            permissions:
              - "Job/Build"
              - "Job/Cancel"
              - "Job/Configure" 
              - "Job/Create"
              - "Job/Delete"
              - "Job/Read"
              - "Job/Workspace"
            members:
              - "project-leads"

  # Security settings
  remotingSecurity:
    enabled: true
    
  crumbIssuer:
    standard:
      excludeClientIPFromCrumb: false
      
  markupFormatter:
    rawHtml:
      disableSyntaxHighlighting: false

# Tool configurations
tool:
  git:
    installations:
      - name: "git"
        home: "/usr/bin/git"
        
  maven:
    installations:
      - name: "maven-3.8"
        properties:
          - installSource:
              installers:
                - maven:
                    id: "3.8.6"
      - name: "maven-3.9"
        properties:
          - installSource:
              installers:
                - maven:
                    id: "3.9.1"
                    
  jdk:
    installations:
      - name: "openjdk-11"
        properties:
          - installSource:
              installers:
                - adoptOpenJdkInstaller:
                    id: "jdk-11.0.19+7"
      - name: "openjdk-17"
        properties:
          - installSource:
              installers:
                - adoptOpenJdkInstaller:
                    id: "jdk-17.0.7+7"

# Cloud configurations for dynamic agents
clouds:
  - kubernetes:
      name: "enterprise-k8s"
      serverUrl: "https://kubernetes.company.com"
      namespace: "jenkins-agents"
      credentialsId: "k8s-service-account"
      containerCap: 200
      maxRequestsPerHost: 100
      connectTimeout: 60000
      readTimeout: 60000
      skipTlsVerify: false
      
      templates:
        - name: "maven-builder"
          label: "maven java"
          containers:
            - name: "maven"
              image: "maven:3.9-openjdk-17"
              alwaysPullImage: true
              workingDir: "/home/jenkins/agent"
              command: "sleep"
              args: "infinity"
              resourceRequestCpu: "500m"
              resourceRequestMemory: "1Gi"
              resourceLimitCpu: "2"
              resourceLimitMemory: "4Gi"
              
        - name: "node-builder"
          label: "nodejs npm"
          containers:
            - name: "node"
              image: "node:18-alpine"
              alwaysPullImage: true
              workingDir: "/home/jenkins/agent"
              command: "sleep"
              args: "infinity"
              resourceRequestCpu: "250m"
              resourceRequestMemory: "512Mi"
              resourceLimitCpu: "1"
              resourceLimitMemory: "2Gi"
              
        - name: "docker-builder"
          label: "docker dind"
          containers:
            - name: "docker"
              image: "docker:24-dind"
              alwaysPullImage: true
              privileged: true
              workingDir: "/home/jenkins/agent"
              resourceRequestCpu: "500m"
              resourceRequestMemory: "1Gi"
              resourceLimitCpu: "2"
              resourceLimitMemory: "4Gi"

# Credentials management
credentials:
  system:
    domainCredentials:
      - credentials:
          - usernamePassword:
              scope: GLOBAL
              id: "github-enterprise-token"
              description: "GitHub Enterprise API Token"
              username: "jenkins-automation"
              password: "${GITHUB_TOKEN}"
              
          - string:
              scope: GLOBAL
              id: "sonarqube-token"
              description: "SonarQube Analysis Token"
              secret: "${SONAR_TOKEN}"
              
          - aws:
              scope: GLOBAL
              id: "aws-production-credentials"
              description: "AWS Production Account"
              accessKey: "${AWS_ACCESS_KEY}"
              secretKey: "${AWS_SECRET_KEY}"
              
          - file:
              scope: GLOBAL
              id: "k8s-service-account"
              description: "Kubernetes Service Account"
              fileName: "kubeconfig"
              secretBytes: "${base64:${KUBECONFIG_CONTENT}}"

# System configuration
unclassified:
  # Location configuration
  location:
    url: "https://jenkins.company.com"
    adminAddress: "devops-team@company.com"
    
  # Slack notifications
  slack:
    teamDomain: "company"
    token: "${SLACK_BOT_TOKEN}"
    room: "#jenkins-notifications"
    sendAs: "Jenkins"
    
  # Email configuration
  mailer:
    smtpHost: "smtp.company.com"
    smtpPort: 587
    useSsl: true
    charset: "UTF-8"
    authentication:
      username: "jenkins@company.com"
      password: "${SMTP_PASSWORD}"
    replyToAddress: "noreply@company.com"
    
  # Global pipeline libraries
  globalPipelineLibraries:
    libraries:
      - name: "shared-pipeline-library"
        defaultVersion: "main"
        allowVersionOverride: true
        implicit: false
        retriever:
          modernSCM:
            scm:
              git:
                remote: "https://github.com/company/jenkins-shared-library.git"
                credentialsId: "github-enterprise-token"
                
  # Build discarders
  buildDiscarders:
    configuredBuildDiscarders:
      - "logRotator":
          daysToKeepStr: "30"
          numToKeepStr: "50"
          artifactDaysToKeepStr: "14"
          artifactNumToKeepStr: "10"
          
  # Security settings
  gitHubPluginConfig:
    hookUrl: "https://jenkins.company.com/github-webhook/"
    
  # Timestamper
  timestamper:
    allPipelines: true
    
  # Workspace cleanup
  workspaceCleanupPlugin:
    deleteDirs: true
    skipWhenFailed: false
    
  # Pipeline speed/durability settings  
  pipelineSpeedDurabilitySettings:
    durabilityHint: PERFORMANCE_OPTIMIZED
```

## Enterprise Security Implementation

Security in enterprise environments requires multiple layers of protection:

### Authentication Integration

```yaml
# SAML integration example
jenkins:
  securityRealm:
    saml:
      idpMetadataConfiguration:
        url: "https://sso.company.com/metadata"
      displayNameAttributeName: "displayName"
      emailAttributeName: "email"
      groupsAttributeName: "groups"
      maximumAuthenticationLifetime: 86400
      advancedConfiguration:
        forceAuthn: false
        authnContextClassRef: "urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport"
        spEntityId: "jenkins.company.com"
```

### Network Security

```yaml
# Network policies for Kubernetes deployment
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: jenkins-network-policy
  namespace: jenkins
spec:
  podSelector:
    matchLabels:
      app: jenkins
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: ingress-nginx
    ports:
    - protocol: TCP
      port: 8080
  - from:
    - podSelector:
        matchLabels:
          app: jenkins-agent
    ports:
    - protocol: TCP
      port: 50000
  egress:
  - to: []
    ports:
    - protocol: TCP
      port: 443  # HTTPS
    - protocol: TCP  
      port: 80   # HTTP
    - protocol: UDP
      port: 53   # DNS
```

### Secret Management

Tools like AWS secret manager or hashicorp vault can be used besides the inbuilt credentials. Plugins help with these.

## Monitoring and Observability

Enterprise Jenkins needs comprehensive monitoring.

## Backup and Disaster Recovery

Enterprise environments need robust backup and recovery procedures.

### Automated Backup Strategy

```bash
#!/bin/bash
# jenkins-backup.sh - Enterprise backup script

JENKINS_HOME="/var/jenkins_home"
BACKUP_DIR="/backups/jenkins"
DATE=$(date +%Y%m%d_%H%M%S)
RETENTION_DAYS=30

# Create backup directories
mkdir -p "$BACKUP_DIR"/{daily,weekly,monthly,config}

# Function to create backup
create_backup() {
    local backup_type=$1
    local backup_path="$BACKUP_DIR/$backup_type"
    
    echo "Starting $backup_type backup at $(date)"
    
    # Stop Jenkins gracefully
    kubectl scale deployment jenkins --replicas=0 -n jenkins
    sleep 30
    
    # Backup Jenkins home (excluding workspace and builds)
    tar -czf "$backup_path/jenkins_home_$DATE.tar.gz" \
        --exclude="$JENKINS_HOME/workspace" \
        --exclude="$JENKINS_HOME/builds" \
        --exclude="$JENKINS_HOME/logs" \
        --exclude="$JENKINS_HOME/.m2/repository" \
        "$JENKINS_HOME"
    
    # Backup configuration only (smaller, faster)
    tar -czf "$backup_path/jenkins_config_$DATE.tar.gz" \
        "$JENKINS_HOME/config.xml" \
        "$JENKINS_HOME/jobs" \
        "$JENKINS_HOME/users" \
        "$JENKINS_HOME/secrets" \
        "$JENKINS_HOME/plugins" \
        "$JENKINS_HOME/casc_configs"
    
    # Restart Jenkins
    kubectl scale deployment jenkins --replicas=1 -n jenkins
    
    # Wait for Jenkins to be ready
    timeout 300 bash -c 'until curl -f http://jenkins:8080/login; do sleep 10; done'
    
    echo "Completed $backup_type backup at $(date)"
}

# Determine backup type based on day
case $(date +%u) in
    7)  # Sunday - Weekly backup
        create_backup "weekly"
        ;;
    1)  # Monday - Check if monthly needed
        if [ $(date +%d) -le 7 ]; then
            create_backup "monthly"
        else
            create_backup "daily"
        fi
        ;;
    *)  # Daily backup
        create_backup "daily"
        ;;
esac

# Cleanup old backups
find "$BACKUP_DIR/daily" -name "*.tar.gz" -mtime +$RETENTION_DAYS -delete
find "$BACKUP_DIR/weekly" -name "*.tar.gz" -mtime +$((RETENTION_DAYS * 4)) -delete
find "$BACKUP_DIR/monthly" -name "*.tar.gz" -mtime +$((RETENTION_DAYS * 12)) -delete

# Sync to cloud storage
aws s3 sync "$BACKUP_DIR" s3://company-jenkins-backups/ --delete

# Send notification
curl -X POST -H 'Content-type: application/json' \
    --data "{\"text\":\"Jenkins backup completed successfully\"}" \
    "$SLACK_WEBHOOK_URL"

echo "Backup process completed at $(date)"
```

### Disaster Recovery Procedures

```bash
#!/bin/bash
# jenkins-restore.sh - Disaster recovery script

BACKUP_SOURCE="s3://company-jenkins-backups/"
JENKINS_HOME="/var/jenkins_home"
RESTORE_DATE=${1:-$(date +%Y%m%d)}

echo "Starting Jenkins disaster recovery for date: $RESTORE_DATE"

# Scale down Jenkins
kubectl scale deployment jenkins --replicas=0 -n jenkins
sleep 30

# Backup current state (if any)
if [ -d "$JENKINS_HOME" ]; then
    mv "$JENKINS_HOME" "${JENKINS_HOME}_pre_restore_$(date +%Y%m%d_%H%M%S)"
fi

# Download backup from S3
aws s3 cp "$BACKUP_SOURCE/daily/jenkins_home_${RESTORE_DATE}*.tar.gz" /tmp/

# Extract backup
latest_backup=$(ls -t /tmp/jenkins_home_${RESTORE_DATE}*.tar.gz | head -1)
if [ -f "$latest_backup" ]; then
    echo "Restoring from: $latest_backup"
    tar -xzf "$latest_backup" -C /var/
else
    echo "ERROR: No backup found for date $RESTORE_DATE"
    exit 1
fi

# Fix permissions
chown -R 1000:1000 "$JENKINS_HOME"
chmod -R 755 "$JENKINS_HOME"

# Scale up Jenkins
kubectl scale deployment jenkins --replicas=1 -n jenkins

# Wait for Jenkins to be ready
echo "Waiting for Jenkins to start..."
timeout 600 bash -c 'until curl -f http://jenkins:8080/api/json; do sleep 10; done'

if [ $? -eq 0 ]; then
    echo "Jenkins disaster recovery completed successfully"
    curl -X POST -H 'Content-type: application/json' \
        --data "{\"text\":\"Jenkins disaster recovery completed successfully\"}" \
        "$SLACK_WEBHOOK_URL"
else
    echo "ERROR: Jenkins failed to start after recovery"
    exit 1
fi
```

## Performance Optimization

Enterprise Jenkins deployments need careful performance tuning.

### JVM Optimization

```bash
# /etc/default/jenkins - Production JVM settings
JENKINS_JAVA_OPTIONS="-server \
  -Xms8g \
  -Xmx16g \
  -XX:+UseG1GC \
  -XX:MaxGCPauseMillis=200 \
  -XX:+UseStringDeduplication \
  -XX:+DisableExplicitGC \
  -XX:+UseCompressedOops \
  -XX:+UseCompressedClassPointers \
  -Djava.awt.headless=true \
  -Djenkins.model.Jenkins.logStartupPerformance=true \
  -Dhudson.DNSMultiCast.disabled=true \
  -Dhudson.udp=-1 \
  -Djenkins.install.runSetupWizard=false \
  -Dhudson.model.DirectoryBrowserSupport.CSP=\"sandbox allow-scripts; default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';\""

# Additional performance settings
JENKINS_ARGS="--sessionTimeout=60 \
  --sessionEviction=3600 \
  --httpsPort=-1 \
  --httpPort=8080"
```

### Database Performance

```sql
-- PostgreSQL optimization for Jenkins (if using external DB)
-- postgresql.conf settings
shared_buffers = 2GB
effective_cache_size = 6GB
work_mem = 256MB
maintenance_work_mem = 512MB
checkpoint_completion_target = 0.9
wal_buffers = 64MB
default_statistics_target = 1000
random_page_cost = 1.1
max_connections = 200

-- Create indexes for common Jenkins queries
CREATE INDEX idx_builds_job_name ON builds(job_name);
CREATE INDEX idx_builds_build_number ON builds(build_number);
CREATE INDEX idx_builds_timestamp ON builds(timestamp);
```

### Agent Optimization

```yaml
# Kubernetes resource optimization for agents
apiVersion: v1
kind: ResourceQuota
metadata:
  name: jenkins-agents-quota
  namespace: jenkins-agents
spec:
  hard:
    requests.cpu: "100"
    requests.memory: "200Gi"
    limits.cpu: "200" 
    limits.memory: "400Gi"
    pods: "100"
    persistentvolumeclaims: "50"

---
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: jenkins-agents-pdb
  namespace: jenkins-agents
spec:
  minAvailable: 5
  selector:
    matchLabels:
      jenkins: agent
```

## Compliance and Security Scanning

Enterprise environments often have compliance requirements.

### Audit Logging

```yaml
# audit-policy.yaml for Kubernetes
apiVersion: audit.k8s.io/v1
kind: Policy
rules:
- level: Request
  namespaces: ["jenkins", "jenkins-agents"]
  resources:
  - group: ""
    resources: ["pods", "services", "secrets", "configmaps"]
  - group: "apps"
    resources: ["deployments", "replicasets"]
  verbs: ["create", "update", "patch", "delete"]
```

### Security Scanning Pipeline

```groovy
// Jenkinsfile for security scanning
pipeline {
    agent { label 'security-scanner' }
    
    environment {
        SONAR_TOKEN = credentials('sonarqube-token')
        SNYK_TOKEN = credentials('snyk-token')
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Security Scans') {
            parallel {
                stage('SAST - SonarQube') {
                    steps {
                        sh """
                            sonar-scanner \
                                -Dsonar.projectKey=enterprise-jenkins \
                                -Dsonar.sources=. \
                                -Dsonar.host.url=https://sonar.company.com \
                                -Dsonar.login=${SONAR_TOKEN}
                        """
                    }
                }
                
                stage('Dependency Scanning') {
                    steps {
                        sh 'snyk test --severity-threshold=high'
                        sh 'snyk monitor'
                    }
                }
                
                stage('Container Scanning') {
                    steps {
                        sh """
                            trivy image --exit-code 1 --no-progress \
                                --severity HIGH,CRITICAL \
                                jenkins/jenkins:lts
                        """
                    }
                }
                
                stage('Infrastructure Scanning') {
                    steps {
                        sh 'checkov -d . --framework kubernetes --framework terraform'
                    }
                }
            }
        }
        
        stage('Compliance Check') {
            steps {
                sh '''
                    # Check for required security policies
                    kubectl get networkpolicies -n jenkins
                    kubectl get podsecuritypolicies
                    
                    # Verify RBAC configuration
                    kubectl auth can-i --list --as=system:serviceaccount:jenkins:jenkins
                '''
            }
        }
    }
    
    post {
        always {
            publishHTML([
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'security-reports',
                reportFiles: '*.html',
                reportName: 'Security Scan Report'
            ])
        }
        failure {
            slackSend channel: '#security-alerts',
                      color: 'danger',
                      message: "Security scan failed for Jenkins infrastructure: ${BUILD_URL}"
        }
    }
}
```

## Deployment Strategy

Rolling out enterprise Jenkins requires careful planning.

### Phased Rollout Plan

**Phase 1: Infrastructure Setup**
- Deploy Kubernetes cluster or VM infrastructure
- Set up networking and security policies
- Deploy monitoring and logging stack
- Configure backup systems

**Phase 2: Core Services**
- Deploy first Jenkins controller
- Configure LDAP/SAML authentication
- Set up basic agent pools
- Test with simple jobs

**Phase 3: Team Onboarding**
- Migrate teams one by one
- Provide training and documentation
- Set up team-specific configurations
- Monitor performance and adjust

**Phase 4: Advanced Features**
- Deploy additional controllers if needed
- Set up advanced monitoring and alerting
- Implement compliance reporting
- Fine-tune performance

### Migration Checklist

```bash
#!/bin/bash
# Pre-migration checklist script

echo "Jenkins Enterprise Migration Checklist"
echo "====================================="

# Infrastructure checks
echo "1. Infrastructure Readiness:"
kubectl cluster-info || echo "❌ Kubernetes cluster not ready"
curl -f https://jenkins.company.com/login || echo "❌ Jenkins not accessible"
nslookup ldap.company.com || echo "❌ LDAP server not accessible"

# Security checks  
echo "2. Security Configuration:"
kubectl get networkpolicies -n jenkins || echo "❌ Network policies not configured"
kubectl get secrets -n jenkins | grep -q jenkins-secrets || echo "❌ Secrets not configured"

# Monitoring checks
echo "3. Monitoring Setup:"
curl -f http://prometheus:9090/-/healthy || echo "❌ Prometheus not healthy"
curl -f http://grafana:3000/api/health || echo "❌ Grafana not healthy"

# Backup checks
echo "4. Backup System:"
aws s3 ls s3://company-jenkins-backups/ || echo "❌ Backup storage not accessible"
systemctl is-active jenkins-backup.timer || echo "❌ Backup schedule not active"

echo "Migration checklist completed"
```

## Troubleshooting Common Enterprise Issues

**Issue: Controllers become unresponsive under load**
- Check JVM heap usage and adjust memory settings
- Review build queue and consider adding more agents
- Look for plugin conflicts or resource-intensive jobs
- Monitor network connectivity between controllers and agents

**Issue: Agents failing to connect**
- Verify network policies and firewall rules
- Check Kubernetes service account permissions
- Review agent resource requests and limits
- Examine Jenkins controller logs for connection errors

**Issue: Slow build performance**
- Profile builds to identify bottlenecks
- Check if agents have sufficient resources
- Review workspace cleanup settings
- Consider using faster storage for workspaces

**Issue: Authentication failures**
- Test LDAP/SAML connectivity manually
- Check certificate validity and trust stores
- Review Jenkins security configuration
- Verify user group memberships in directory service

## Best Practices Summary

**Architecture:**
- Start with team-based or environment-based controller separation
- Use Configuration as Code for all settings
- Implement proper monitoring from day one
- Plan for disaster recovery before you need it

**Security:**
- Integrate with corporate identity systems
- Use principle of least privilege for all access
- Implement network segmentation
- Regular security scanning and updates

**Performance:**
- Size controllers appropriately for workload
- Use dynamic agents instead of static ones
- Implement proper resource limits and quotas
- Monitor and tune JVM settings regularly

**Operations:**
- Automate everything possible
- Document all procedures and runbooks
- Test backup and recovery procedures regularly
- Provide comprehensive training to teams

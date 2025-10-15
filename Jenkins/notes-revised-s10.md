# Jenkins, GitHub Webhooks & Ansible Setup Notes

## 1. Setting up Jenkins on Docker with JCasC Setup

### Directory Structure
```
jenkins-setup/
├── Dockerfile
├── jenkins.yaml         # JCasC configuration
├── plugins.txt          # Required plugins
```

### Step 1: Create plugins.txt
```
configuration-as-code:latest
git:latest
github:latest
workflow-aggregator:latest
blueocean:latest
```

### Step 2: Create jenkins.yaml (JCasC Configuration)
```yaml
jenkins:
  systemMessage: "Jenkins configured automatically by JCasC"
  securityRealm:
    local:
      allowsSignup: false
      users:
        - id: "admin"
          password: "${ADMIN_PASSWORD}"
  authorizationStrategy:
    loggedInUsersCanDoAnything:
      allowAnonymousRead: false

credentials:
  system:
    domainCredentials:
      - credentials:
          - usernamePassword:
              scope: GLOBAL
              id: "github-creds"
              username: "${GITHUB_USER}"
              password: "${GITHUB_TOKEN}"
              description: "GitHub credentials"

unclassified:
  location:
    url: "http://localhost:8080/"
  
tools:
  git:
    installations:
      - name: "Default"
        home: "/usr/bin/git"
```

### Step 3: Create Dockerfile
```dockerfile
FROM jenkins/jenkins:lts

# Copy plugin list and install plugins
COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN jenkins-plugin-cli --plugin-file /usr/share/jenkins/ref/plugins.txt

# Copy JCasC configuration
COPY jenkins.yaml /usr/share/jenkins/ref/jenkins.yaml

# Set environment variable for JCasC
ENV CASC_JENKINS_CONFIG=/usr/share/jenkins/ref/jenkins.yaml

# Skip initial setup wizard
ENV JAVA_OPTS -Djenkins.install.runSetupWizard=false
```

### Step 4: Build and Run
```bash
# Build the image
docker build -t jenkins-jcasc .

# Run the container
docker run -d \
  --name jenkins \
  -p 8081:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -e ADMIN_PASSWORD=admin123 \
  -e GITHUB_USER=yourusername \
  -e GITHUB_TOKEN=your_token_here \
  jenkins-jcasc
```

### Verification
- Access Jenkins at `http://localhost:8081`
- Login with admin/admin123
- Check Configuration as Code page: `http://localhost:8081/configuration-as-code/`

---

## 2. Setting up GitHub Webhook Step by Step

### Prerequisites
- Jenkins running and accessible
- GitHub repository
- GitHub plugin installed in Jenkins

### Step 1: Configure GitHub Webhook
1. Go to your GitHub repository
2. Click **Settings** → **Webhooks** → **Add webhook**
3. Set **Payload URL**: `http://your-jenkins-url/github-webhook/`
4. Set **Content type**: `application/json`
5. Select **Just the push event** or customize events
6. Ensure **Active** is checked
7. Click **Add webhook**

### Step 2: Configure Jenkins Job
1. Create a new **Freestyle project** or **Pipeline**
2. Go to **Source Code Management** → **Git**
3. Enter your GitHub repository URL
4. Add credentials if repository is private
5. Under **Build Triggers**, check **GitHub hook trigger for GITScm polling**

### Step 3: Test the Integration
1. Make a commit to your repository
2. Push changes to GitHub
3. Check Jenkins for automatic build trigger
4. Verify build logs show webhook trigger

### Troubleshooting
- Ensure Jenkins URL is accessible from GitHub
- Check webhook delivery logs in GitHub
- Verify Jenkins security settings allow webhook access
- Check firewall/proxy settings

---

## 3. Installing Ansible on WSL Ubuntu

### Prerequisites
- Windows 10/11 with WSL enabled
- Ubuntu installed from Microsoft Store

### Step 1: Update System
```bash
sudo apt update && sudo apt upgrade -y
```

### Step 2: Install Prerequisites
```bash
sudo apt install software-properties-common -y
```

### Step 3: Add Ansible Repository
```bash
sudo add-apt-repository --yes --update ppa:ansible/ansible
```

### Step 4: Install Ansible
```bash
sudo apt install ansible -y
```

### Step 5: Verify Installation
```bash
ansible --version
ansible-playbook --version
```

### Alternative Installation (via pip)
```bash
sudo apt install python3-pip -y
pip3 install ansible
```

---

## 4. Introduction to Ansible

### What is Ansible?
Ansible is an open-source IT automation tool that uses:
- **Agentless architecture** - No software needed on managed nodes
- **YAML syntax** - Human-readable configuration files
- **SSH connectivity** - Secure remote execution
- **Idempotent operations** - Same result on repeated runs

### Key Components

#### Control Node
- Machine where Ansible is installed
- Runs playbooks and manages inventory

#### Managed Nodes
- Remote systems managed by Ansible
- No agent installation required

#### Inventory
- List of managed nodes
- Can be static (file) or dynamic (script/plugin)

#### Playbooks
- YAML files containing plays and tasks
- Define desired system state

#### Modules
- Units of work executed by Ansible
- Examples: file, copy, service, package

### Core Concepts

#### Idempotency
- Running same playbook multiple times produces same result
- Tasks check current state before making changes

#### Ad-hoc Commands
```bash
ansible all -m ping
ansible webservers -m service -a "name=httpd state=started"
```

#### Inventory Example
```ini
[webservers]
web1.example.com
web2.example.com

[databases]
db1.example.com
db2.example.com

[all:vars]
ansible_user=ubuntu
```

---

## 5. Creating Your First Ansible Playbook

### Basic Playbook Structure
```yaml
---
- name: Play name
  hosts: target_hosts
  become: yes/no
  vars:
    variable_name: value
  tasks:
    - name: Task description
      module_name:
        parameter: value
```

### Example Playbook with Shell Task

#### Create inventory.ini
```ini
[local]
localhost ansible_connection=local

[servers]
server1 ansible_host=192.168.1.100 ansible_user=ubuntu
server2 ansible_host=192.168.1.101 ansible_user=ubuntu
```

#### Create first-playbook.yml
```yaml
---
- name: My First Ansible Playbook
  hosts: localhost
  gather_facts: yes
  become: no
  
  vars:
    message: "Hello from Ansible!"
    log_file: "/tmp/ansible-demo.log"
  
  tasks:
    - name: Display system information
      debug:
        msg: "Running on {{ ansible_hostname }} with OS {{ ansible_distribution }}"
    
    - name: Create a directory
      file:
        path: /tmp/ansible-demo
        state: directory
        mode: '0755'
    
    - name: Run shell command to create log file
      shell: |
        echo "{{ message }}" > {{ log_file }}
        echo "Timestamp: $(date)" >> {{ log_file }}
        echo "User: $(whoami)" >> {{ log_file }}
      args:
        creates: "{{ log_file }}"
    
    - name: Check if file was created
      stat:
        path: "{{ log_file }}"
      register: log_file_stat
    
    - name: Display file status
      debug:
        msg: "Log file exists: {{ log_file_stat.stat.exists }}"
    
    - name: Run shell script command
      shell: |
        #!/bin/bash
        for i in {1..3}; do
          echo "Loop iteration $i" >> /tmp/loop-output.txt
        done
      args:
        executable: /bin/bash
        creates: /tmp/loop-output.txt
    
    - name: Read and display file content
      shell: cat {{ log_file }}
      register: file_content
      changed_when: false
    
    - name: Show file content
      debug:
        var: file_content.stdout_lines
```

### Running the Playbook
```bash
# Check syntax
ansible-playbook --syntax-check first-playbook.yml

# Dry run (check mode)
ansible-playbook -i inventory.ini --check first-playbook.yml

# Run the playbook
ansible-playbook -i inventory.ini first-playbook.yml

# Run with verbose output
ansible-playbook -i inventory.ini -v first-playbook.yml
```

### Advanced Shell Task Examples

#### Running Multiple Commands
```yaml
- name: Multiple shell commands
  shell: |
    echo "Starting setup..."
    mkdir -p /tmp/setup
    cd /tmp/setup
    echo "Setup complete" > status.txt
  args:
    creates: /tmp/setup/status.txt
```

#### Conditional Shell Execution
```yaml
- name: Conditional shell task
  shell: systemctl restart nginx
  when: ansible_distribution == "Ubuntu"
  become: yes
```

#### Capturing Shell Output
```yaml
- name: Get system uptime
  shell: uptime
  register: system_uptime
  changed_when: false

- name: Display uptime
  debug:
    msg: "System uptime: {{ system_uptime.stdout }}"
```

### Best Practices
1. Use specific modules instead of shell when possible
2. Make tasks idempotent with `creates`, `removes`, or `when` conditions
3. Use `changed_when: false` for read-only shell commands
4. Handle errors with `ignore_errors` or `failed_when`
5. Use variables for reusability
6. Add meaningful task names and comments

## Ansible
***

**Ansible** is an open-source software that automates **software provisioning**, **configuration management**, and **application deployment**.

* **Ansible** is commonly used for tasks like software installation, configuration, and system updates across multiple servers or devices in a network. It's also used for **Orchestration**, **Security**, and **compliance**.
* It uses the **YAML Scripting language** which works on **KEY-VALUE PAIR** format.

* It helps reduce manual work, improve consistency, and save time in managing complex environments.

### Main Features of Ansible:

* **Agentless**: There is no software or agent to be installed on the client that communicates back to the server.
* **Simple and extensible**: Ansible is written in **Python** and uses **YAML** for playbook language, both of which are considered relatively easy to learn.

### PLAYBOOK:

Ansible **playbooks** are a way to send commands to remote computers in a scripted way. Instead of using Ansible commands individually to remotely configure computers from the command line, you can configure entire complex environments by passing a script to one or more systems.

### WHY WE USE ANSIBLE:

While managing multiple servers it's hard to keep their configuration identical. If we have multiple servers that need the same setup configured in all of them, while doing this one-to-one on each server, there might be a chance to miss some configuration steps in some servers.

### Ansible Modules

They are pre written tools that can do specific job, we just need to add instructions

### Parameter

Instructions to your module on what to do 

### Dashes

They are Items in list
They follow order by order

### Default Colour schemes - 
ansible follow a colour scheme that :
  if the output is Red its a error 
  if the output is Yellow, its changes made 
  if the output is Green, its no changes made
  if the output is Blue, task is skipped

### gather_facts : yes
ansible gather system info of hosts , Default = yes
"ansible_facts" will fail if gather_facts are not yes

### YAML Principles
 - Document Start: Every YAML file begins with --
 - Indentation: Uses spaces only (never tabs), typically 2 spaces per level
 - Case Sensitivity: All keys, values, and variable names are case-sensitive
 - Data Types: Supports strings, numbers, booleans, lists, and dictionaries

#### YAML Syntax Elements
- Space after colon is mandatory
- List using hyphens
- Nested dictionary structure
-  Use 
   "ansible-playbook --syntax-check " to verify structure

### Playbook Example and Rules
```
 Playbook starts with document separator- name: Descriptive name for the play
 hosts: target_hosts_or_groups
 become: yes  # Enable privilege escalation
 gather_facts: yes  # Collect system information
 vars:
 #Play-level variables
 variable_name: value
 tasks:
 #List of tasks to execute- name: Task description
 module_name:
 parameter: value
 handlers:
 #Event-driven tasks- name: Handler description
 module_name:
 parameter: value
```
 ### Play definitions
 
 name: Descriptive identifier for the play (optional but recommended)
 hosts: Target hosts or groups from inventory
 become: Enables privilege escalation (sudo/su)
 gather_facts: Controls automatic fact collection (default: yes)

 ### Task structure
 ```
 tasks:- name: Install Nginx web server
 package:
 name: nginx
 state: present
 notify: restart nginx- name: Copy configuration file
 template:
 src: nginx.conf.j2
 dest: /etc/nginx/nginx.conf
 backup: yes
 notify: restart nginx
 ```

 ###  Multi-Play Playbooks
 Running multiple play in a single playbook
 its is in a order 
 ex:
 ```
 ---
# First play - configure web servers- name: Configure web servers
 hosts: webservers
 become: yes
 tasks:- name: Install Apache
 package:
 name: httpd
 state: present
 # Second play - configure database servers  - name: Configure database servers
 hosts: databases
 become: yes
 tasks:- name: Install MySQL
 package:
name: mysql-server
 state: present

 ```
 ### Basic Execution Commands
 ```
 ansible-playbook playbook.yml -   Run playbook with default inventory
 
 ansible-playbook -i custom_inventory.ini playbook.yml -  Specify custom inventory
 
 ansible-playbook -v playbook.yml -  Verbose output for debugging
 
 ansible-playbook --syntax-check playbook.yml -  Check syntax before execution
 
 ansible-playbook --check playbook.yml -  Dry run - show what would change
 
 ansible-playbook --diff playbook.yml -  Show differences for changed files

  ```
  ### Variables
   Variable allow playbooks to adapt to different environments without code duplication

   We can set variable in inside Playbook Variables,External Variable file, Command Line Variables

   1.Playbook variable
```
   ----
   name: Web server configuration
 hosts: webservers
 vars:
 http_port: 80
 document_root: /var/www/html
 server_admin: admin@example.com
 tasks:- name: Configure Apache port
 lineinfile:
 path: /etc/apache2/ports.conf
regexp: '^Listen'
 line: "Listen {{ http_port }}
```
 2. External Variable File
 There will be variable file (vars.yml) and its mentioned in playbook for references
```
#vars.yml--
database_host: db.example.com
 database_port: 3306
 database_name: webapp
 max_connections: 200
 
 #playbook.yml---- name: Configure application
 hosts: appservers
 vars_files:- vars.yml
 tasks:- name: Template database config
 template:
 src: database.conf.j2
 dest: /etc/app/database.conf
 ```

 ### Host and Group Variables

Host and group variables provide structured approaches to managing configuration differences across our infrastructure

Directry structure of variables:

 ```
project/
 ├── ansible.cfg
 ├── inventory.ini
 ├── playbooks/
 ├── group_vars/
 │   ├── all.yml          
# Variables for all hosts
 │   ├── webservers.yml   # Variables for webserver group
 │   └── databases.yml    
└── host_vars/
 ├── web01.yml        
# Variables for database group
 # Variables specific to web01
 └── db01.yml         
# Variables specific to db01
 ```

 

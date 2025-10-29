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

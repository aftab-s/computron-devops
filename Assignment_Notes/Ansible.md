# Ansible

## Core Concepts

- **Agentless Architecture** : No software installation is required on the managed nodes. It typically works over **SSH/WinRM**. 
- Ansible relies on **Python**. 
- Uses **Declarative YAML syntax** for human-readable playbooks. 
- **Idempotent Operations** : There is **no interdependency**, allowing you to perform **parallel, independent operations**. 
- **Extensive Module Library** : Includes over **1,300 built-in modules** to manage various systems and services. 
- **Platform Agnostic** : Manages heterogeneous environments. It uses **platform-specific agents** in the OS (e.g., Python on Linux, WinRM on Windows).
- **Control Node** : The central system where Ansible is installed and from which all tasks are managed. 
- **Managed Nodes (Hosts)** : The target systems that Ansible configures and manages. 
- **Inventory** : A configuration file that lists all the managed hosts or programs, essentially an **address book** of your infrastructure. 
- **Modules** : Small programs that run specific tasks on the managed hosts. 
- **Validate Syntax:** Use `ansible-playbook --syntax-check playbook.yml` to verify the structure.

---

## Common Commands

* **Run a Playbook** : `ansible-playbook playbook.yml` 
* **Specify Custom Inventory** : `ansible-playbook -i custom_inventory.ini playbook.yml` 
* **"Dry Run"** : `ansible-playbook --check playbook.yml` (Checks syntax and shows what *would* change) 
* **Create Syntax Check** : `ansible-playbook --syntax-check playbook.yml` 
* **Show Differences** : `ansible-playbook --diff playbook.yml` (Show the difference for changed files) 
* **Pass Environment Variables at Runtime** : `ansible-playbook -e "env=production" -e "debug=false" playbook.yml` 
* **Use a Variable File** : `ansible-playbook --extra-vars "@production-vars.yml" playbook.yml` 

---

## Common Connectivity and Configuration Issues

- **SSH Connectivity** : **SSH Key not authorized** on the managed host. **Password prompt appears** (check if key authentication is set up correctly). 
- **Wrong inventory entry** : Host unreachable error. **Verify hostname/IP** is in inventory. 
- **Firewall Blocking** : Connection timout. Check for **port 22** on the managed host. 
- **Python not installed** on the managed host. 
- **YAML Syntax** : **YAML Syntax Errors** are essential to check for. 
- Use **`vvvv`** (verbose) for detailed output and better debugging. 

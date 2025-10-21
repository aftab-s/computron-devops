# Virtualization & Deployment

## Virtualization Concepts

### Core Principles
Virtualization allows you to run multiple virtual environments on a single physical machine. It's heavily reliant on the host system's specs, including **CPU, memory, and storage**, as running many VMs consumes significant resources. This is why a lightweight keyword often distinguishes full virtualization from containerization, as Docker is considerably lighter than a full VM.

### Bare Metal and Hypervisors
**Bare Metal** refers to the first software layer installed on a fresh computer, like an **OS such as Windows or Linux**.

A **Hypervisor** is the technology that enables virtualization, allowing a single **Host Machine** to run multiple **Guest OS**. There are two main types:
* **Type 1 (Bare Metal):** Runs directly on the hardware (e.g., Proxmox, VMware ESXi).
* **Type 2 (Hosted):** Runs as an application on top of an existing OS (e.g., VirtualBox, VMware Workstation).
Examples include **EC2 Instances**, which operate on a hypervisor, and **Android**, which runs above the Linux kernel using a similar principle.

---

## Deployment & EC2 Setup

### What an EC2 Instance Provides
Every **EC2 Instance** is equipped with essential components for deployment, including a unique **IP Address**, a **Port number**, and **Preconfigured essentials** like an OS, dependencies, and security groups. The overall deployment process is often called a **CI/CD pipeline**. It involves:
1. Verifying the IP & port of the EC2 instance.
2. Generating a URL using DNS.
3. Using this URL to access the deployed webpage.

### Web App Manual Deployment: 
1. Obtain a **URL** (from EC2 or DNS).
2. Use **SSH** to establish a secure connection to the server.
3. **Copy content** into the EC2 instance.
4. Run the necessary **build command** to compile the application.
5. Set a **compressed version** of the app for efficiency.
6. The deployment process begins, making the application live.

### The Build Stage
The build stage often uses tools like **Vercel, Nginx, or Apache** to handle the final output. These tools are responsible for:
* Serving **static files**
* Handling **Routing**
* Highlighting **build errors**
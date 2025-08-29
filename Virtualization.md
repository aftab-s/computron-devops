# Virtualization

## Virtualization Concepts

### Drawbacks of Virtualization
- Heavily dependent on system specs (**CPU, memory, storage**).  
- Running multiple VMs consumes a lot of resources.  
- Keyword: **Lightweight** → Docker is lighter than full VMs.  

---

### Bare Metal
- First thing installed on a fresh computer.  
- Example: **OS like Windows, Linux**.  

---

### Hypervisor
- Enables **virtualization**.  
- Allows running multiple **Guest OS** on one **Host Machine**.  
- **Types of Hypervisors:**
  - **Type 1 (Bare Metal):** Runs directly on hardware (e.g., Proxmox, VMware ESXi).  
  - **Type 2 (Hosted):** Runs on top of an OS (e.g., VirtualBox, VMware Workstation).  

---

### Examples
- **EC2 Instances** → Run based on hypervisor.  
- **Android** → Runs above Linux kernel (virtualization concept).  

          

---

## Deployment & EC2 Setup

### Each EC2 Instance Includes:
- **IP Address**  
- **Port number**  
- **Preconfigured essentials** (OS, dependencies, security groups).  

---

### Deployment Steps
1. Verify **IP & port** of EC2 instance.  
2. Generate a **URL** using DNS.  
3. Use this URL to access the deployed webpage.  

- This process is called a **CI/CD pipeline**.  

---

## 4. Web Deployment (Manual)

### Steps to Deploy a Web App Manually:
1. Get a **URL** (from EC2 or DNS).  
2. **SSH** into the server.  
3. **Copy content** into the EC2 instance.  
4. Run **build command**.  
5. Set a **compressed version** of the app.  
6. Deployment process begins.  

---

### Build Stage
- Tools like **Vercel, Nginx, Apache** can be used.  
- They handle:
  - Serving **static files**  
  - **Routing**  
  - Highlighting **build errors**  




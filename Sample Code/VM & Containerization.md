# VM & Containerization 

## 1. Virtualization (VMs)
- **Concept**: Running multiple operating systems on a single physical machine.
- **Components**:
  - **Host Machine**: Physical computer with hardware resources.
  - **Hypervisor (VMM – Virtual Machine Monitor)**: Software layer that creates and manages Virtual Machines (VMs).
  - **Guest Machines (VMs)**: Each has its own OS (e.g., Kali, CentOS, Red Hat EL).
- **Characteristics**:
  - Each VM is **isolated**.
  - Requires a **full OS** for each VM.
  - **Heavily dependent on host machine specification** (CPU, RAM, storage).
  - More resource-intensive (slower compared to containers).

---

## 2. Containerization (Docker)
- **Concept**: Lightweight alternative to virtualization. Packages an application with all its dependencies into a container.
- **Characteristics**:
  - Containers share the **host OS kernel** (unlike VMs).
  - Lightweight → faster startup and less resource usage.
  - Isolated but use the same underlying OS.
- **Workflow**:
  1. **Describe** → Define app + dependencies in a Dockerfile.
  2. **Package** → Build an image (contains app + dependencies).
  3. **Share** → Push image to container registries.
     - Git container registry
     - Docker Hub (public registry)
  4. **Run** → Create and run containers from images.
- **Important Terms**:
  - **Docker Image**: Blueprint for containers.
  - **Docker Hub**: Repository for storing and sharing images.

---

## 3. Key Differences: VM vs Container
| Feature              | Virtual Machines (VMs)       | Containers (Docker)          |
|----------------------|------------------------------|-------------------------------|
| OS Requirement       | Each VM has its own OS       | Share host OS kernel          |
| Size                 | Heavy (GBs)                  | Lightweight (MBs)             |
| Startup Time         | Minutes                      | Seconds                       |
| Isolation            | Strong (hardware-level)      | Strong but kernel shared      |
| Resource Usage       | High (depends on host specs) | Efficient, low overhead       |

---


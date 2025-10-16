# Kubernetes (K8s) Core Concepts 

This section outlines the fundamental concepts and architecture of Kubernetes.

## Origin and Basics
* **Origin:** Developed to manage systems where changes are applied to a central node, and all other nodes follow **"all at once."**
* **Language:** Kubernetes is primarily developed in the **Go (GO)** programming language.
* **Orchestration Analogy:** It is compared to an **orchestra**, where the **conductor** is the central figure (Master Node), and all other musicians (Worker Nodes) are in sync.

---

## K8s Architecture (Nodes)

Kubernetes operates on a distinction between its control plane and its work units.

### 1. Master Node (Control Plane)
* The **central control plane** of the cluster.
* **Role:** Issues commands and controls the worker nodes.
* **Workloads:** Applications **do not run** on the master node.
* **Requirement:** A minimum of **one** master node is needed.

### 2. Worker Node
* The nodes where the actual **applications and workloads** run.

### 3. Single-Image Setup
* **Common Practice:** For simpler or local deployments (e.g., Minikube), a single image is often built to run **both the master and worker nodes**.

---

# Kubernetes Essential Commands

- **Controller Manager** : **Watches the cluster** and handles actions such as maintaining the correct number of nodes and application replicas. 
- **Scheduler** :  Responsible for scheduling pods from a "spaced state" (pending) to available or empty nodes based on resource needs and history. 

This table outlines the core components of the Kubernetes control plane and common `kubectl` commands.

| Component/Command | Description |
| :--- | :--- |
| **`kubectl`** | The command-line tool used to **control the worker nodes** (i.e., apply changes, view status). |
| **`kubelet`** | A primary agent that runs on **each worker node**. It manages the pods and containers based on the user's request. |
| **`kube-api-server`** | The **API server** for the control plane. All instructions from `kubectl` are channeled through the `kube-api-server`. |
| **`etcd`** | A **distributed database** that stores the cluster's moment-to-moment flow and optimal configuration state. |
| **`kubectl get all`** | Lists **all** Kubernetes resources (Deployments, Services, Pods, etc.). |
| **`kubectl get pods`** | Lists all **pods** (which are the smallest deployable units, typically running containers). |
| **`kubectl create deployment [name] --image=[image] --replicas=[#]`** | Creates a deployment, specifying the image tag and the desired number of replicas (e.g., `--replicas=2`). |
| **`kubectl apply -f [deployment.yaml]`** | Used to run a **YAML file** that contains all deployment and resource configuration details. |
| **`kubectl delete pod [pod name]`** | Deletes a specific pod. **Note:** The replica set will automatically create a new one to maintain the desired replica count (**Self-Healing**). |

---

## Replication and Self-Healing 

* **Self-Healing:** If you set the desired number of **replicas** (e.g., to **3**) and one pod is manually deleted, a new replica will be **automatically created immediately** to maintain the target of 3 active replicas.

---

## Load Balancing (LB) and Services 

This section describes how traffic is managed and exposed in a clustered environment.

* **Context:** Used in scenarios involving many load balancers and a vast number of services (nodes/servers) where individual manual changes are impractical.
* **Diagram Concept:** User traffic passes through an **LB**, which distributes the load across multiple underlying LBs. These then route traffic to various Application containers running on Server/Node Kernels (e.g., K-Server 1, K-Server 2, etc.).

### Service Types

* **Node Port:** When used, the **port on the host machine remains unchanged**. It maps the port on the host (e.g., `localhost:available_port`) to the Pod IP.
* **Cluster IP:** When a **Cluster IP** is used, the service route is **exposed to the outside world**, and all associated pods will be configured under a **single public IP**.

### Networking Terms

* **Loopback Address:** `127.0.0.1` is a special IPv4 address called the **loopback address** or **"localhost"**. It is used for local access, e.g., `127.0.0.1:8080`.

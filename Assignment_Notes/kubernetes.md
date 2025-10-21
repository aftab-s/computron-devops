# Kubernetes (K8s) Core Concepts 

## Basics
* Developed to manage systems where changes are applied to a central node, and all other nodes follow **"all at once."**
* Kubernetes is primarily developed in the **Go (GO)** programming language.
* It is compared to an **orchestra**, where the **conductor** is the central figure (Master Node), and all other musicians (Worker Nodes) are in sync. Kubernetes uses a technology called **Service Mesh** for container network.**lstio** and **consul** are examples.

---

## K8s Architecture (Nodes)

### 1. Master Node (Control Plane)
* The **central control plane** of the cluster.
* Issues commands and controls the worker nodes.
* Applications **do not run** on the master node.


### 2. Worker Node
* The nodes where the actual **applications and workloads** run.


---

# Kubernetes Essential Commands

- **Controller Manager** : **Watches the cluster** and handles actions such as maintaining the correct number of nodes and application replicas. It monitors the whole process. Control manager also looks for healthy as well as empty nodes.
- **Scheduler** :  Responsible for scheduling pods from a "spaced state" (pending) to available or empty nodes based on resource needs and history. 
- **etcd** : A **distributed database** that stores the cluster's moment-to-moment flow and configuration state. 
- **kubectl** : The command-line tool used to **control the worker nodes** (i.e., apply changes, view status). |
- **kubelet** : A primary agent that runs on **each worker node**. It creates the pods based on the user's request. 
- **kube-api-server** : The **API server** for the control plane. All instructions from `kubectl` are channeled through the **kube-api-server**. User interacts through **kube-api-server**.
- **`kubectl get all`** : Lists **all** Kubernetes resources (Deployments, Services, Pods, etc.). 
- **`kubectl get pods`** : Lists all **pods** (which are the smallest deployable units, typically running containers). 
- **`kubectl create deployment [name] --image=[image] --replicas=[number]`** : Creates a deployment, specifying the image tag and the desired number of replicas.
    - example:
    ```
    kubectl create deployment nginx-deployment --image=nginx:alpine --replicas=2    
    #creates an NGINX deployment named nginx-deployment with 2 replicas using the nginx:alpine image.

    ```
- **`kubectl scale [resource_type]/[resource_name] --replicas=[number]`** : Used to changes the number of running replicas (pods) for a deployment increasing or decreasing them as needed.
    - example:
    ```
    kubectl scale deployment\nginx-deployment --replicas=4  
    #scales the nginx-deployment to 4 pods, meaning Kubernetes will run 4 replicas of the NGINX container.

    ```
- **`kubectl apply -f [deployment.yaml]`** : Used to run a **YAML file** that contains all deployment and resource configuration details.
- **`kubectl delete pod [pod name]`** : Deletes a specific pod. **Note:** The replica set will automatically create a new one to maintain the desired replica count (**Self-Healing**). 
    - example:
    ```
    kubectl delete pod nginx-deployment-abc   
    #deletes the specific pod named nginx-deployment-abc from your cluster.

    ```

---

- **Self-Healing:** If you set the desired number of **replicas** (e.g., to **3**) and one pod is manually deleted, a new replica will be **automatically created immediately** to maintain the target of 3 active replicas.

## Load Balancing (LB) and Services 

- Used in scenarios involving many load balancers and a vast number of services (nodes/servers) where individual manual changes are impractical.

### Service Types

- **Node Port:** When used, the **port on the host machine remains unchanged**. It maps the port on the host (e.g., `localhost:available_port`) to the Pod IP.
- **Cluster IP:** When a **Cluster IP** is used, the service route is **exposed to the outside world**, and all associated pods will be configured under a **single public IP**.

- **Loopback Address:** `127.0.0.1` is a special IPv4 address called the **loopback address** or **"localhost"**. It is used for local access, e.g., `127.0.0.1:8080`.

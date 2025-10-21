# Kubernetes 

## Kubernetes Architecture 

Kubernetes operates using two main layers(node):
1. **Master Node**  
2. **Worker Nodes( also called slave node)**

---

### 1. Master Node

- It is the **brain** of the Kubernetes cluster.  
- It issues commands and manages the worker nodes.  
- Applications dont run on the master node.  

### 2. Worker Node

- These are the machines where **applications and workloads** actually run.  
- Each worker node has components that run and manage **Pods** (Pods arr the smallest deployable units)


---

### 3. Single-Image Setup

- For **local or simple deployments**, both the **master** and **worker** components can run together on one single system.  
- Example: **Minikube** (Minikube is a tool to run Kubernetes locally)

---

##  Kubernetes Components


**kubectl** -  Command-line tool used to control the cluster (apply changes, check status)

**kubelet** - Agent running on each worker node — manages pods and containers based on instructions

**kube-api-server** -it is the main API server that acts as the communication hub between components and commad line(kubectl)

##  Kubernetes Commands
'''
kubectl get all - Lists all Kubernetes resources 
kubectl get pods - Lists all pods 
kubectl create deployment [name] --image=[image] --replicas=[#] - Creates a deployment, specifying the image tag and the desired number of replicas 
kubectl apply -f [deployment.yaml] - Used to run a YAML file that contains all deployment and resource configuration details.
kubectl delete pod [pod name]	Deletes a specific pod. 

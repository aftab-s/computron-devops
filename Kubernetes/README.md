## Prerequisites
Before you begin, make sure you have the following installed:
- [Docker](https://docs.docker.com/get-docker/): Used by Minikube to run containers.
- [kubectl](https://kubernetes.io/docs/tasks/tools/): Command-line tool to interact with your Kubernetes cluster.
- [Minikube](https://minikube.sigs.k8s.io/docs/start/): Runs a local Kubernetes cluster for testing and learning.

---

## Starting Minikube
Start your local Kubernetes cluster with:
```sh
minikube start
```
This will create a single-node Kubernetes cluster on your machine.

---


## Key Kubernetes Concepts
---

## Preventing the "Noisy Neighbor" Issue

In Kubernetes, if you don't set resource requests and limits for your containers, one pod can use up too much CPU or memory. This can slow down or crash other pods running on the same node. This problem is called the **"noisy neighbor" issue**.

**How to prevent it:**
- Always set `resources.requests` (minimum guaranteed resources) and `resources.limits` (maximum allowed resources) in your pod specs.
- This ensures fair sharing and stable performance for all workloads.

**Example (see deployment.yaml):**
```yaml
resources:
	requests:
		memory: "64Mi"
		cpu: "100m"
	limits:
		memory: "128Mi"
		cpu: "250m"
```
This means the container is guaranteed 64Mi memory and 0.1 CPU, but cannot use more than 128Mi memory or 0.25 CPU.

---

## Understanding Resource Units

When setting resource requests and limits, Kubernetes uses specific units:

- **Memory:**
	- `Mi` stands for mebibytes (1 Mi = 1024 x 1024 bytes, or 1,048,576 bytes).
	- Example: `64Mi` means 64 mebibytes of memory.
- **CPU:**
	- `m` stands for millicores (1 core = 1000m).
	- Example: `100m` means 0.1 CPU core (100 millicores), `250m` means 0.25 CPU core.

These units help you fine-tune how much CPU and memory each container can use.

# Getting Started with Kubernetes (K8s) using Minikube

Kubernetes (K8s) is an open-source platform for automating deployment, scaling, and management of containerized applications. Minikube lets you run Kubernetes locally for learning and development.

---

- **Pod:**
	- The smallest deployable unit in Kubernetes. Usually runs a single container, but can run multiple tightly coupled containers.
- **Deployment:**
	- Manages a set of identical pods. Handles rolling updates, scaling, and self-healing (restarts pods if they fail).
- **Service:**
	- Exposes your pods to the network. Types include:
		- `ClusterIP`: Internal access only (default)
		- `NodePort`: Exposes the service on a port on each node
		- `LoadBalancer`: Uses a cloud provider's load balancer (not available in Minikube by default)
- **Namespace:**
	- Logical partitioning of cluster resources. Useful for organizing resources by project or team.

---

## Workflow: Deploying an App on Kubernetes


### 1. Create a Namespace (Optional)
Namespaces help organize your resources.

**Using YAML:**
```sh
kubectl apply -f namespace.yaml
```

**Equivalent kubectl command:**
```sh
kubectl create namespace my-namespace
```
To use the namespace for all commands, add `-n my-namespace`.


### 2. Deploy an Application
Apply the deployment manifest to create pods:

**Using YAML:**
```sh
kubectl apply -f deployment.yaml
```

**Equivalent kubectl command:**
```sh
kubectl create deployment nginx-deployment --image=nginx:alpine --replicas=2
```
This will create a Deployment named `nginx-deployment` with 2 replicas (pods) running the Nginx web server.


### 3. Expose the Deployment
Create a Service to make your app accessible:

**Using YAML:**
```sh
kubectl apply -f service.yaml
```

**Equivalent kubectl command:**
```sh
kubectl expose deployment nginx-deployment --name=my-service --type=NodePort --port=80 --target-port=80 --node-port=30080
```
This creates a Service named `my-service` of type `NodePort`, exposing port 80 of your pods on port 30080 of your Minikube node.

### 4. Check Your Resources
See what's running in your cluster:
```sh
kubectl get namespaces
kubectl get deployments
kubectl get pods
kubectl get services
```

### 5. Access Your Application
To open your app in a browser:
```sh
minikube service my-service
```
This will open the Nginx welcome page in your default browser.

---

## Understanding the Manifest Files

- **deployment.yaml**
	- Defines a Deployment with 2 replicas of the Nginx container.
	- Handles rolling updates and pod management.
- **service.yaml**
	- Exposes the Deployment using a NodePort Service.
	- Allows you to access the app from outside the cluster.
- **namespace.yaml**
	- (Optional) Creates a namespace called `my-namespace` for organizing resources.

---

## Common kubectl Commands

- `kubectl get all` — List all resources in the current namespace
- `kubectl describe <resource> <name>` — Show detailed info (e.g., `kubectl describe pod nginx-deployment-xxxx`)
- `kubectl logs <pod-name>` — View logs from a pod
- `kubectl delete -f <manifest>.yaml` — Delete resources defined in a manifest

---

## Next Steps
- Try editing the manifests to change the number of replicas or the image.
- Explore more resource types: ConfigMap, Secret, Ingress, etc.
- For more, see the [Kubernetes Docs](https://kubernetes.io/docs/home/)

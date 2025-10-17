# kubernetes 
### Kubernetes is mainly used for container orchestration ,basically it used to help run,manage and scale containerised applications like docker

### Kubernetes used for multiple Servers [nodes] if change comes in 1 node (server) needs to change in all duplicate servers 
### kubernetes  google product opensource   golang language is used for making

## Master node and worker node
### master controls,plans,manages
### executes and run the apps
![Kubernetes architecture](https://www.opsramp.com/wp-content/uploads/2022/07/Kubernetes-Architecture-1536x972.png)

**`minikube install`**
### Used to install Minikube, a tool that lets you run Kubernetes locally on your computer for testing and learning.
### master -> worker
###       API 
**`kubectl start`**
### Starts a local Kubernetes cluster on your system using Minikube.
### It sets up everything you need to run Kubernetes commands.
**`kubectl get all`**
### Shows all the running resources in your cluster — like pods, services, and deployments — in the current namespace.
### NAME                             READY   STATUS    RESTARTS   AGE
### pod/my-app-5d6b7c4d9c-jp5gh      1/1     Running   0          5m
### service/kubernetes               ClusterIP   10.96.0.1   <none>   443/TCP   10m   

**`kubectl get nodes`**
### Displays all the nodes (servers) in your Kubernetes cluster.
### Each node runs your application workloads.
###  NAME           STATUS   ROLES           AGE   VERSION
### minikube       Ready    control-plane   10m   v1.30.0

### kubectl it’s a command-line tool used to interact with the Kubernetes API server.
### kubectl is the client, and
### Kubernetes API Server is the actual API.
### user interactive layer is called as kube api server

## API -APPLICATION PROGRAMMING INTERFACE
### Api is used as a meditator as in hotel waiter

## CONTROLLER MANAGER
### controls and manges

## SCHEDULER
### pod node1 node2, pod assingning to the free node, as a schedular
## etcd 
###  etcd is a key-value database used by Kubernetes to store all cluster data.

### It’s like the brain of Kubernetes — it remembers everything about your cluster.

**`kubectl apply -f .\deployment.yml`**
### kube-proxy is a network component in Kubernetes.
### It runs on every node in your cluster.
### Its main job is to forward traffic to the right pod or service, even if pod IPs keep changing.

**`kubectl delete podname`**
### for deleting a pod

## 1. API (Application Programming Interface)

* Works on a Request and Response model.
* uses JSON.
* Includes route definitions like app.route('/')
* **REST Methods:** The four primary methods shown are: GET, PUT, UPDATE, and DELETE
---

## 2. Minikube and Container 

### Minikube and Container Architecture
* Minikube runs inside a Virtual Box (Hypervisor).
* The environment includes the OS, Kubelet, Docker Daemon (Container Runtime), and Containers.
* Images are pulled from registries like DockerHub
* The docker run command uses an API.

### Workflow for Docker/Minikube
1. **Step1** Applications are built into Docker images and often pushed to a registry like DockerHub
2. **Step2** Kubernetes downloads the image and runs it as a Pod  within the Minikube VM.
3. **step3** The Docker run command interacts with the Docker API via command like kubectl,then communicates with the Kubernetes cluster.

---


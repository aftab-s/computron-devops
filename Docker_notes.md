# Containerization (Docker Basics)

## Dockerfile
- File used to describe how to build a **Docker image**.  
- Contains instructions about:
  - **OS** (Base image, e.g., Ubuntu/Alpine)
  - **Required packages** (dependencies)
  - **Application code**
  - **Repositories**

 **Note:** Git repo ≠ Docker repo  
- **Git repo** stores **source code**.  
- **Docker repo** stores **container images**.  

---

## Environment
- Docker provides an **isolated environment** for applications.

---

## Docker Image
- A file containing everything required to run an app.  
- Stored in **DockerHub** (or other registries).  

---

## Docker Container
- The **running instance** of a Docker image.

---

##  Docker Commands

- docker pull <repo>/<image> : To pull deocker images

	-  **Example:**

	  ```bash

	  docker pull grafana/grafana-enterprise		#pull an image called grafana-enterprise from the repository called grafana

	  ```

 -  docker images : To list docker images

 -  docker ps : To list running containers

 -  docker stop <container_id> : To stop a container

	-  **Example:**

	 ```bash

	 docker stop 9c		#Stops a docker container with ID that starts with  9c. Docker IDs are usually sequence of characters and integers. Either type the ID as a whole or the first two characters will also work.

	```

 -  docker version : To check version of the docker

 -  docker run -d --name=grafana -p 3000:3000 grafana/grafana:enterprise : To run a docker container

     - Flags:

	    - -d : Detached mode (doesn’t show logs while running)  

	    - --name : Assigns container name

        -  -p 3000:3000 : Maps ports. Here 3000 before colon is the port number of grafana. 3000 after colon is the system port number to be mapped ( container port to host port)




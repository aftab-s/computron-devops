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

- docker pull `<repo>/<image>` : To pull docker images

	-  **Example:**

	```

      docker pull grafana/grafana-enterprise		  #pull an image called grafana-enterprise from the repository called grafana

	```
 - docker push `<repo>/<image>` : To push docker images to docker hub

	-  **Example:**

	```

      docker push kailasrpillai/course-website		  #pull an image called course-website from the repository called kailasrpillai

	```

 -  docker images : To list docker images

 -  docker ps : To list running containers

 -  docker stop <container_id> : To stop a container

	-  **Example:**

	  ```

	 docker stop 9c		#Stops a docker container with ID that starts with  9c. Docker IDs are usually sequence of characters and integers. Either type the ID as a whole or the first two characters will also work.

	  ```

 -  docker version : To check version of the docker

 -  docker run -d --name=grafana -p 3000:3000 grafana/grafana:enterprise : To run a docker container

     - Flags:

	    - -d : Detached mode (doesn’t show logs while running)  

	    - --name : Assigns container name

        -  -p 3000:3000 : Maps ports. Here 3000 before colon is the port number of grafana. 3000 after colon is the system port number to be mapped ( container port to host port)

## Docker Layers

- **Every instruction** in a `Dockerfile` creates a new **read-only layer**.

## Common Layer-Creating Instructions

- **`FROM`:** Creates the **base image layer(s)** (e.g., `FROM ubuntu:latest`). This is the foundation of your image.
- **`RUN`:** Creates a new layer for **every command execution** (e.g., package installation like `apt-get install`, building code). Use a single `RUN` command with `&&` to combine steps and reduce layers. 
- **`ADD` / `COPY`:** Creates a layer for **adding files or directories** from the host to the image. `COPY` is generally preferred for local files. 
- **`WORKDIR`:** Creates a layer to **set the working directory** for subsequent instructions. 
- **`ENV`:** Creates a layer to **set environment variables**. This layer is typically very small.

---

## Caching Mechanism

- The lower layers (like COPY, EXPOSE, FROM, or base dependencies) remain unchanged. Docker reuses these existing layers from its cache.

- Only the top, small APP layer is rebuilt. This results in a Fast Rebuild.




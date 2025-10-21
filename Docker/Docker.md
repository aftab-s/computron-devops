
# Containerization 

## The Dockerfile
A **Dockerfile** is a plain text file containing instructions to build a **Docker image**. It's a blueprint that specifies the base **OS** (e.g., Ubuntu, Alpine), required **packages** and dependencies, and the application's own **code**. A Dockerfile can also specify which repositories to use for content. It's important to differentiate a **Git repo**, which stores source code, from a **Docker repo** (like DockerHub), which stores pre-built container images.

---

## Key Docker Concepts
Docker provides an **isolated environment** for applications, packaging everything they need to run.

* **Docker Image:** A complete file system containing all the code, libraries, and dependencies required to run an application. Images are stored in registries like **DockerHub**.
* **Docker Container:** The **running instance** of a Docker image. When an image is executed, it becomes an active, isolated container.

---

## Essential Docker Commands
Here are some common commands to get started:

* `docker pull <repo>/<image>`: Downloads a Docker image from a remote repository.
    * **Example:** `docker pull grafana/grafana-enterprise` downloads the `grafana-enterprise` image from the `grafana` repository.
* `docker images`: Lists all Docker images stored locally.
* `docker ps`: Shows all currently running containers.
* `docker stop <container_id>`: Stops a running container. You can use the full ID or just the first few characters (e.g., `docker stop 9c`).
* `docker version`: Displays Docker version information.
* `docker run -d --name=grafana -p 3000:3000 grafana/grafana:enterprise`: Runs a container with specific flags:
    * `-d`: Runs in **detached mode** (in the background).
    * `--name`: Assigns a name to the container.
    * `-p`: Maps a port (`<host_port>:<container_port>`). For example, `-p 3000:3000` maps port 3000 on your system to port 3000 inside the container.
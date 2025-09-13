containerization
Containerization is a modern method of packaging applications so that they can run consistently across different environments. For example, if you are developing an app on Windows 11 with VS Code but want it to run in a lightweight Alpine OS with specific dependencies like NumPy 1.1 and Pandas 2.0, you can use a Dockerfile to define the environment. A Dockerfile specifies the base OS, installs required packages, and sets up the application. Once the Dockerfile is created, the next step is to build an image, which packages the OS, dependencies, and the application itself into a single portable unit. This image can then be shared or stored in a repository such as Docker Hub or a private Git-based container registry, making it easy for others to pull and run the same environment. When the image is executed, Docker creates an isolated container, which is a lightweight runtime environment that ensures the application runs the same way regardless of the host system. This means your app will behave identically whether on Windows, Linux, or a cloud server. In short, containerization ensures portability, reproducibility, and isolation for modern application deployment.


Docker desktop engine 
docker is an open source   
docker developers put a new update  -----    docker git repo    ----build stage as .exe or .zip
--------------------------------
docker help  -   used for learn all available commands in docker
-d → Runs the container in the background (detached mode)
-p → Maps container port to host port for external access
docker images -  how many images downloaded in our machine
docker pull grafana/grafana pulls the Grafana image from Docker Hub, and docker run -d --name=grafana -p 3000:3000 grafana/grafana runs a new container in detached mode (-d runs in background), assigns it a custom name (--name=grafana), maps host port 3000 to container port 3000 (-p 3000:3000), and uses the Grafana image (grafana/grafana) so you can access Grafana at http://localhost:3000.
docker ps - used to view running containers list
docker stop - used to view running container stop
docker stop fe   - fe is the id of the docker running and that image is stopped
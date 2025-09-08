# 1. First create a Dockerfile

```dockerfile
FROM nginx:alpine
RUN rm -rf /usr/share/nginx/html/*
COPY . /usr/share/nginx/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

```
## **Instructions Breakdown:**

- **FROM nginx:alpine**  
  Start with a lightweight Nginx base image (Alpine version).

- **RUN rm -rf /usr/share/nginx/html/**  
  Remove the default HTML files that come with Nginx.

- **COPY . /usr/share/nginx/html/**  
  Copy your website files from the local directory into Nginx’s web root folder.

- **EXPOSE 80**  
  Document that the container will listen on port 80.

- **CMD ["nginx", "-g", "daemon off;"]**  
  Run Nginx in the foreground so the container keeps running.

---

# 2. Build the Docker image

```
docker build -t kailasrpillai/course-webpage .

```
- You asked Docker to build an image from the `Dockerfile` in the current folder (`.`).  
- The `-t` flag gave it the name/tag `kailasrpillai/course-webpage:latest` (if you don’t specify a tag, Docker defaults to `:latest`).  

---

# 3. Run a container from the image

```
docker run -d --name course-webpage -p 8081:80 kailasrpillai/course-webpage

```

- **Detached mode (-d):** Runs the container in the background.  
- **Container name:** `course-webpage`.  
- **Port mapping:** Host port `8081` → Container port `80` (accessible at [http://localhost:8081](http://localhost:8081)).  
- **Image used:** `kailasrpillai/course-webpage:latest`

---

# 4. Push the image to Docker Hub

```
docker push kailasrpillai/course-webpage

```
- You told Docker to upload the image to Docker Hub.
- Since you didn’t specify a tag, it pushed :latest.







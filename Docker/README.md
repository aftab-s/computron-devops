# Dockerfile Explanation for Coffee-Artistry

This document explains, step by step, how the Dockerfile for the Coffee-Artistry website was created and how it dockerizes the static site.

## Step-by-Step Explanation

1. **Base Image**
   ```Dockerfile
   FROM nginx:alpine
   ```
   - Uses the official lightweight Nginx image (Alpine version) to serve static files efficiently.

2. **Set Working Directory**
   ```Dockerfile
   WORKDIR /usr/share/nginx/html
   ```
   - Sets the working directory inside the container to where Nginx serves static files by default.

3. **Remove Default Content**
   ```Dockerfile
   RUN rm -rf ./*
   ```
   - Deletes the default Nginx static files to avoid conflicts with your website files.

4. **Copy Website Files**
   ```Dockerfile
   COPY index.html ./
   COPY style.css ./
   COPY assets ./assets
   ```
   - Copies your website's HTML, CSS, and assets folder into the container's Nginx directory.

5. **Expose Port**
   ```Dockerfile
   EXPOSE 80
   ```
   - Informs Docker that the container will listen on port 80 (the default HTTP port).

6. **Start Nginx**
   ```Dockerfile
   CMD ["nginx", "-g", "daemon off;"]
   ```
   - Starts the Nginx server in the foreground so the container keeps running.

## How to Build and Run


1. **Build the Docker Image**
   ```sh
   docker build -t coffee-artistry .
   ```

2. **Tag the Image for Docker Hub**
   ```sh
   docker tag coffee-artistry your-dockerhub-username/coffee-artistry:latest
   ```
   - Replace `your-dockerhub-username` with your actual Docker Hub username.

3. **Push the Image to Docker Hub**
   ```sh
   docker push your-dockerhub-username/coffee-artistry:latest
   ```
   - Make sure you are logged in to Docker Hub using `docker login`.

4. **Run the Container (Locally or Remotely)**
   ```sh
   docker run -d -p 8080:80 your-dockerhub-username/coffee-artistry:latest
   ```
   - Access your site at http://localhost:8080

---
This setup makes it easy to deploy your static website anywhere Docker is supported.

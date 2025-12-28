# 1. First create a Dockerfile

```dockerfile
# Stage 1: Specify the base image (node version 18)
FROM node:18-alpine as build

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build

# Stage 2: Serve with Nginx
FROM nginx:alpine

RUN rm -rf /usr/share/nginx/html/*

COPY --from=build /app/build /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]


```
## Instructions Breakdown:

### Stage 1: Build React App

- FROM node:18-alpine as build
→ Uses a lightweight Node.js (Alpine) image. Named build for later reference.

- WORKDIR /app
→ Creates and switches to /app as the working directory.

- COPY package*.json ./
→ Copies only package.json + package-lock.json first (so Docker can cache dependencies).

- RUN npm install
→ Installs all dependencies needed to build the React project.

- COPY . .
→ Copies the rest of the project files (React source, public folder, assets, etc.).

- RUN npm run build
→ Runs the React build command → generates optimized static files inside /app/build.

### Stage 2: Serve with Nginx

- FROM nginx:alpine
→ Starts fresh with a small Nginx image (no Node.js).

- RUN rm -rf /usr/share/nginx/html/*
→ Deletes Nginx’s default “Welcome” page.

- COPY --from=build /app/build /usr/share/nginx/html
→ Copies the React build output from stage 1 into Nginx’s web root.

- EXPOSE 80
→ Documents that this container will listen on port 80.

- CMD ["nginx", "-g", "daemon off;"]
→ Runs Nginx in the foreground (required so the container doesn’t exit).

---

### Stage 3: Build the Docker image

```
docker build -t kailasrpillai/myreactweb:v1 .

```
- You asked Docker to build an image from the `Dockerfile` in the current folder (`.`).  
- The `-t` flag gave it the name/tag `kailasrpillai/myreactweb:v1` (if you don’t specify a tag, Docker defaults to `:latest`).  

---

# Stage 4: Run a container from the image

```
docker run -d --name myreactweb -p 8090:80 kailasrpillai/myreactweb:v1

```

- **Detached mode (-d):** Runs the container in the background.  
- **Container name:** `myreactweb:v1`.  
- **Port mapping:** Host port `8090` → Container port `80` (accessible at [http://localhost:8090](http://localhost:8090)).  
- **Image used:** `kailasrpillai/myreactweb:v1`

---

# Stage 5: Push the image to Docker Hub

```
docker push kailasrpillai/myreactweb:v1

```
- You told Docker to upload the image to Docker Hub.
- If you didn’t specify a tag, it will push :latest.







# ⚽ Football Club Page 

This is a static **Football Club Website** built with HTML, CSS, and assets.  
It is containerized using **Nginx + Docker** and pushed into  **Dockerhub**  

## 📦 Steps to Build, Run & Push

### 1. Create a `Dockerfile`
```dockerfile

FROM nginx:alpine

RUN rm -rf /usr/share/nginx/html/*

COPY . /usr/share/nginx/html/

EXPOSE 80 

CMD ["nginx", "-g", "daemon off;"]

```

### 2. Build Docker Image
docker build -t football-club-page .
### 3. Run container 
docker run -d -p 8080:80 football-club-page
### 2. push to dockker hub

# Tag the image
docker tag football-club-page lezinhere/football-club-page:v1

# Push to Docker Hub
docker push lezinhere/football-club-page:v1



now it is running on http://localhost:8080/

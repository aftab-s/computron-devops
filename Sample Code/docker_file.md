# Dockerfile & Related Concepts

## 1. Dockerfile
A **Dockerfile** is a script that contains instructions to build a Docker image.

### Structure:
1. **Base Image (OS Layer)**
   ```dockerfile
   FROM nginx:alpine
   ```
   - Defines the base image used to build the container.

2. **Container Preparation**
   ```dockerfile
   WORKDIR /app
   RUN <commands>
   COPY <src> <dest>
   ```
   - **WORKDIR** → Sets working directory inside the container.  
   - **RUN** → Executes commands during image build.  
   - **COPY** → Copies files from host to container.  

3. **Start Instructions**
   ```dockerfile
   EXPOSE 80
   CMD ["nginx", "-g", "daemon off;"]
   ```
   - **EXPOSE** → Defines port(s) the container will use.  
   - **CMD** → Command that runs when container starts.  

---

## 2. Docker Compose
- Tool used to **run multiple containers together**.  
- Defines services inside a `docker-compose.yml` file.  
- Example: Running a web server + database together.

---

## 3. Microservice Architecture
- Application is broken into **independent services**.  
- Each service runs in its **own container**.  
- Easier scaling and updates compared to monolithic apps.  

---

## 4. Replicas
- **Exact duplicates of containers**.  
- Used for:
  - Load balancing (scaling).  
  - Backup and failover process.  

---

## 5. Monolithic Architecture
- Entire application runs as a **single system**.  
- Harder to scale, update, and maintain compared to microservices.  

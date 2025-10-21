# Dockerfile and Docker Compose

---

## Static vs Dynamic Content

### Static Content
- A webpage where the content does not change with user interaction.  
- No database connectivity is involved.  
- **Example:** Simple HTML page (e.g., a portfolio page with fixed text and images).  

### Dynamic Content
- A webpage connected to a database.  
- Content changes dynamically based on database updates or user input.  
- **Example:** E-commerce sites (Amazon, Flipkart product pages).  

---


## Docker Basics

### Docker Exec Command
- Used to run commands inside a running container.  
- **Example:**  
  ```
  docker exec -it <container_id> /bin/sh

  ```
### Exit Command
- **Example:**
    ```
     exit       #Used to exit out of the Docker container shell.

    ```

---

## Dockerfile

```dockerfile
FROM nginx:alpine                  #Uses the official Nginx image based on Alpine Linux (lightweight). 

RUN rm -rf /usr/share/nginx/html/*      #Removes the default Nginx HTML files. Ensures that only your custom files will be served.

COPY . /usr/share/nginx/html/        #Copies all files from the current directory (.) into the Nginx web root directory.

EXPOSE 80                          #Exposes port 80 (default HTTP port). Allows the container to serve web content to the host machine.

CMD ["nginx", "-g", "daemon off;"]      #Starts the Nginx server.

```

## RUN vs CMD

- **RUN:** Executes commands during image build (e.g., installing dependencies).
- **Example:**
    ```
    RUN rm -rf ./*

    ```


- **CMD:** Defines the default command to run when a container starts.
- **Example:**
    ```
    CMD ["nginx", "-g","daemon off;"]

    ```

---

# Docker Compose 

- Used to run multiple containers together.  
- Follows **Microservice Architecture** (multi-container setup).  
- Ensures that if one container goes down, others continue running.  

## Example: E-commerce Website

### Frontend
- Browser page  
- Product page  
- Shopping cart  
→ Runs in **one container**  

### Backend
- Payment gateway  
- Authentication  
- Order handling  
→ Runs in **separate containers**  

### Database Layer
- **DynamoDB**  
- **PostgreSQL**  
- **RDS (Relational Database Service)**  
- Each database runs independently, ensuring **isolation**.  

---

# Microservices vs Monolithic Architecture

## Microservice Architecture
- Application is broken into multiple independent services.  
- Each service runs in its own container.  
- **Example:** Amazon, Rapido.  

### Advantages
- Fault tolerance (if one service fails, others work).  
- Easy scaling.  

---

## Monolithic Architecture
- All services (Frontend, Backend, Database, AI, etc.) run in a **single container**.  
- **Example:** Netflix (earlier), Flipkart, Uber (older versions).  

### Disadvantage
- If one component fails (e.g., frontend), the whole application may crash.  

---

# Replication and High Availability
- Each container can have multiple **replicas** running across different regions.  
- If one container goes down, replicas act as backups.  
- Ensures **no downtime** for users.  

**Example:**  
- Migration from **DynamoDB to MongoDB** without downtime → Replicas handle requests while migration occurs.  

---

- Some companies use a mix of **Monolithic** and **Microservices**.  
- **Example:** Myntra, Twitter → Combination of both approaches for efficiency.

---

# Docker Compose File

## Code

```yaml
version: "3.8" 
services: 
  lezin-web: 
    image: lezinhere/coffeeweb:latest 
    ports: 
      - "8086:80" 

  kailas-web: 
    image: kailasrpillai/coffee-lancelot:v1 
    ports: 
      - "8087:80" 

  swalih-web: 
    image: swalihhh/coffee-artistry:v1 
    ports: 
      - "8088:80" 

  aftab-web: 
    image: aftab2010/coffee-artistry:v2 
    ports: 
      - "8089:80"

```

## Expalantion

- The file uses Compose version 3.8.
- It defines 4 services (containers), each running a different Docker image.
- Each service maps an internal container port (80) to a different external host port (8086–8089).
- This allows multiple web apps to run on the same machine simultaneously without port conflicts.

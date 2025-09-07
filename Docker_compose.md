# Web Content, Docker, and Architectures

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
    ```
    exit       #Used to exit out of the Docker container shell.

    ```

---


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

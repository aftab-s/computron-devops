
# Docker Networking

Docker networking allows containers to communicate with each other, either on the same host or across different hosts. This is essential for building multi-container applications.

## Types of Docker Networks

### 1. Bridge Network (default for containers)
- **Description:** Default network driver for containers on a single host. Containers can communicate with each other using container names.
- **Example:**
   - The provided `docker-compose.yaml` uses a custom bridge network called `mynetwork`.
   - You can also use the default bridge network:
      ```sh
      docker run -d --name web1 nginx:alpine
      docker run -it --rm --network bridge busybox ping web1
      ```

### 2. Host Network
- **Description:** Removes network isolation between the container and the Docker host. The container shares the host's networking namespace.
- **Example:**
   ```sh
   docker run --network host nginx:alpine
   ```
   - Useful for high-performance networking or when you want the container to use host ports directly (Linux only).

### 3. None Network
- **Description:** Disables all networking for the container. The container has its own network stack but is isolated from all networks.
- **Example:**
   ```sh
   docker run --network none nginx:alpine
   ```
   - Useful for security or testing purposes.

### 4. Overlay Network
- **Description:** Enables communication between containers across multiple Docker hosts (requires Docker Swarm or Kubernetes).
- **Example:**
   ```sh
   docker network create -d overlay myoverlay
   # Use in a Swarm service:
   docker service create --name web --network myoverlay nginx:alpine
   ```
   - Useful for distributed applications and microservices.

### 5. Macvlan Network
- **Description:** Assigns a MAC address to a container, making it appear as a physical device on the network. Useful for legacy applications that require direct access to the physical network.
- **Example:**
   ```sh
   docker network create -d macvlan --subnet=192.168.1.0/24 --gateway=192.168.1.1 -o parent=eth0 mymacvlan
   docker run --network mymacvlan --ip 192.168.1.22 nginx:alpine
   ```

---


## Example: docker-compose.yaml

The provided `docker-compose.yaml` demonstrates several Docker networking types in a beginner-friendly way.

### 1. Bridge Network (default and custom)
- **web**: Runs an Nginx server, accessible at http://localhost:8081
- **app**: Runs a BusyBox container that pings the web server using its service name (`web-server`).
- Both use a custom bridge network called `mybridge`.

### 2. Host Network (Linux only)
- **hostweb**: (Commented out by default) Shows how to use the host's network stack. Not supported on Docker Desktop for Windows/Mac.

### 3. None Network
- **nonenet**: (Commented out by default) Shows how to run a container with no network access at all.

### How to Use
1. Open a terminal in this directory.
2. Run:
   ```sh
   docker-compose up
   ```
3. You will see the app container pinging the web server, demonstrating bridge network connectivity.
4. To try host or none networks, uncomment the relevant sections in `docker-compose.yaml` and run again (host only works on Linux).

### Overlay and Macvlan
- These advanced networks require extra setup and are not included in the compose file for simplicity.
- See the README sections above for example commands and explanations.

---
Why use custom networks?
- Containers on the same custom network can communicate by name.
- Isolates your application from other containers on the host.
- Useful for microservices and multi-container setups.

---
For more, see the [Docker Networking docs](https://docs.docker.com/network/).

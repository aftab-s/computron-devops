# Docker Virtual Networks 

- **Host Network:** The Host network mode effectively removes network isolation between the container and the Docker host.

- **Bridge Network:** The Bridge network is the default networking mode for containers. It provides network isolation while enabling communication among containers on the same bridge.

- **None Network Mode:** The simplest form of network isolation, often used for debugging or running completely isolated tasks.

- **Overlay Network Mode:** Designed for **Docker Swarm** clusters, allowing containers on different physical Docker hosts to communicate securely.

- **MacVLAN Network Mode:** A more advanced option that gives a container its own unique MAC address, making it look like a physical device on the network.

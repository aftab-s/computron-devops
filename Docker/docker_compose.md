# Docker Compose

## Commands
- `docker compose up -d` → Start services in detached mode.
- `docker compose down` → Stop and remove containers, networks, and volumes created by `up`.

## YAML Format
Docker Compose uses a `docker-compose.yml` file to define services.

### Structure:
```yaml
version: '3'
services:
  service_name:
    image: <image_name>
    ports:
      - "host_port:container_port"
```

### Example:
```yaml
version: '3'
services:
  web:
    image: nginx:alpine
    ports:
      - "8080:80"
```

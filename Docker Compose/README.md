# Docker Compose Notes

Docker Compose lets you define and run multi-container Docker applications using a single YAML file. You describe your services, networks, and volumes, then start everything with one command.

## Quick Start

```bash
# Start in the folder where docker-compose.yaml lives
docker compose up -d

# See what is running
docker compose ps

# Tail logs for all services (Ctrl+C to stop viewing)
docker compose logs -f

# Stop and remove containers, networks (preserves named volumes)
docker compose down

# Stop and also remove named volumes (data loss!)
docker compose down -v
```

## File: docker-compose.yaml

- **version**: Compose specification version. Modern Docker supports the Compose Spec and you can usually omit this field.
- **services**: Each container you want to run.
- **volumes**: Named volumes for persistent data shared across service restarts.
- **networks**: Custom networks so services can talk to each other by name.

## Common Service Keys

- **image**: Use an existing image from a registry like Docker Hub.
- **build**: Build from a local Dockerfile instead of using a prebuilt image.
  - `context`: Path to build context (usually `.`)
  - `dockerfile`: Optional custom Dockerfile path
- **container_name**: Optional fixed name; omit to let Compose manage names automatically.
- **ports**: Map host:container ports. Example: `"8080:80"` exposes container port 80 on host 8080.
- **environment**: Key/value env vars. Prefer `.env` or `env_file` for secrets.
- **env_file**: Load environment variables from a file.
- **volumes**: Mount paths. Examples:
  - Named volume: `mydata:/var/lib/postgresql/data`
  - Bind mount: `./app:/usr/src/app:ro`
- **depends_on**: Control startup order. With healthchecks you can use conditions.
- **healthcheck**: How Docker decides if a container is healthy.
- **restart**: Policy like `no`, `on-failure`, `always`, or `unless-stopped`.
- **command** / **entrypoint**: Override defaults from the image.
- **deploy**: Swarm-only options (replicas, resources). Ignore for local Compose.

## Example: Minimal Web + DB

```yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "8080:80"

  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: example
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

## Environment Variables

- A `.env` file placed next to `docker-compose.yaml` is automatically loaded.
- Reference variables with `${VAR_NAME}` in the YAML.
- Do not commit real secrets; use `.env` and a secrets manager for production.

## Networks

- By default, Compose creates a single network for your project so services can reach each other by service name.
- You can define multiple networks and attach services to specific ones.

```yaml
networks:
  frontend:
  backend:

services:
  api:
    image: myorg/api:latest
    networks:
      - backend
  web:
    image: nginx:alpine
    networks:
      - frontend
```

## Healthchecks and Startup Order

```yaml
services:
  db:
    image: postgres:15
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  app:
    image: node:18-alpine
    depends_on:
      db:
        condition: service_healthy
```

## Useful Commands

- `docker compose up -d`: Start in detached mode
- `docker compose stop`: Stop containers but keep them
- `docker compose down`: Stop and remove containers and default network
- `docker compose down -v`: Also remove named volumes
- `docker compose logs -f [service]`: Stream logs
- `docker compose exec <service> <cmd>`: Run a command in a service
- `docker compose build [service]`: Build images
- `docker compose pull`: Pull images
- `docker compose restart [service]`: Restart services

## Troubleshooting Tips

- Ports already in use: change the left-hand side of `host:container` port mappings.
- Permission denied on bind mounts (Linux/macOS): adjust paths/permissions or use named volumes.
- File changes not reflecting: check bind mount path and container working directory.
- Container restarts repeatedly: check `docker compose logs` and healthcheck settings.

## Security Considerations

- Do not bake secrets into images or YAML; use env files and external secrets managers.
- Limit exposed ports; prefer internal networking between services.
- Pin image versions (e.g., `postgres:15.6`) to avoid unexpected changes.
- Keep images up to date; rebuild/pull regularly.
- Never log sensitive data; review logs carefully.

## When to Use Multiple Files

- Use an override file like `docker-compose.override.yaml` for local-only changes.
- Example:

```bash
docker compose -f docker-compose.yaml -f docker-compose.override.yaml up -d
```

## Learn More

- Compose Spec: `https://docs.docker.com/compose/compose-file/`
- CLI Reference: `https://docs.docker.com/reference/cli/docker/compose/` 
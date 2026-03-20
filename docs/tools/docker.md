# Docker

> **TODO:** This document is a stub. Expand with detailed notes, examples, and real-world usage patterns.

## What is Docker?

Docker is an open-source platform for developing, shipping, and running applications inside lightweight, portable containers.

## Key Concepts

- **Image** - A read-only template used to create containers
- **Container** - A runnable instance of an image
- **Dockerfile** - A script of instructions to build an image
- **Registry** - A storage and distribution system for images (e.g., Docker Hub)
- **Volume** - Persistent storage mounted into a container
- **Network** - Communication layer between containers

## Basic Commands

```bash
# Images
docker pull <image>          # Pull an image from registry
docker build -t <name> .     # Build image from Dockerfile
docker images                # List local images
docker rmi <image>           # Remove an image

# Containers
docker run <image>           # Create and start a container
docker run -d -p 8080:80 <image>  # Run detached, map ports
docker ps                    # List running containers
docker ps -a                 # List all containers
docker stop <container>      # Stop a container
docker rm <container>        # Remove a container
docker logs <container>      # View container logs
docker exec -it <container> bash  # Open shell in container

# Volumes
docker volume create <name>
docker volume ls

# Cleanup
docker system prune          # Remove unused resources
```

## Dockerfile Example

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "index.js"]
```

## Docker Compose (Basic)

```yaml
# docker-compose.yml
version: '3'
services:
  app:
    build: .
    ports:
      - "3000:3000"
  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secret
```

```bash
docker-compose up -d    # Start services
docker-compose down     # Stop and remove services
```

## References

- [Docker Official Docs](https://docs.docker.com/)

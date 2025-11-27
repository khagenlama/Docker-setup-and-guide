# Docker CLI Essentials

## Basic Container Commands

```bash
# Run a container
docker run <image>                    # Basic run
docker run -it ubuntu bash            # Interactive with terminal
docker run -d nginx                   # Detached (background)
docker run -p 8080:80 nginx          # Port mapping (host:container)
docker run --name myapp nginx         # Give it a name
docker run --rm alpine echo "hi"      # Remove after exit

# List containers
docker ps                             # Running containers
docker ps -a                          # All containers (including stopped)

# Stop/Start/Remove containers
docker stop <container-id>            # Graceful stop
docker kill <container-id>            # Force stop
docker start <container-id>           # Start stopped container
docker restart <container-id>         # Restart container
docker rm <container-id>              # Remove container
docker rm -f <container-id>           # Force remove (even if running)

# Execute commands in running container
docker exec -it <container-id> bash   # Open shell in running container
docker exec <container-id> ls /app    # Run single command

# View logs
docker logs <container-id>            # View logs
docker logs -f <container-id>         # Follow logs (like tail -f)
docker logs --tail 100 <container-id> # Last 100 lines
```

## Image Management

```bash
# List images
docker images                         # All local images
docker images -a                      # Include intermediate images

# Pull/Push images
docker pull nginx:latest              # Pull from Docker Hub
docker pull nginx:1.21                # Specific version
docker push myusername/myimage:tag    # Push to registry

# Build images
docker build -t myapp:latest .        # Build from Dockerfile
docker build -t myapp:v1.0 -f Dockerfile.prod .  # Use specific Dockerfile

# Remove images
docker rmi <image-id>                 # Remove image
docker rmi $(docker images -q)        # Remove all images

# Tag images
docker tag myapp:latest myapp:v1.0    # Create new tag
```

## Cleanup Commands

```bash
# Remove stopped containers
docker container prune                # Remove all stopped containers

# Remove unused images
docker image prune                    # Remove dangling images
docker image prune -a                 # Remove all unused images

# Remove everything unused
docker system prune                   # Remove containers, networks, images
docker system prune -a                # More aggressive cleanup
docker system prune -a --volumes      # Include volumes too

# Check disk usage
docker system df                      # Show Docker disk usage
```

## Volumes (Data Persistence)

```bash
# Create and use volumes
docker volume create mydata           # Create named volume
docker run -v mydata:/data nginx      # Mount named volume
docker run -v $(pwd):/app node        # Mount local directory

# List and remove volumes
docker volume ls                      # List volumes
docker volume rm mydata               # Remove volume
docker volume prune                   # Remove unused volumes
```

## Networks

```bash
# List networks
docker network ls

# Create network
docker network create mynetwork

# Run container in network
docker run --network mynetwork nginx

# Connect/disconnect container to network
docker network connect mynetwork <container-id>
docker network disconnect mynetwork <container-id>
```

## Inspect & Debug

```bash
# Get detailed info
docker inspect <container-id>         # JSON info about container
docker inspect <image-id>             # JSON info about image

# View resource usage
docker stats                          # Live resource usage
docker stats <container-id>           # Stats for specific container

# View processes
docker top <container-id>             # Running processes in container

# Copy files
docker cp <container-id>:/path/file ./local  # From container to host
docker cp ./local <container-id>:/path       # From host to container
```

## Best Practices

### 1. **Always Use Specific Tags**
```bash
# ❌ Bad - version can change
docker pull nginx:latest

# ✅ Good - locked to specific version
docker pull nginx:1.21.6
```

### 2. **Clean Up Regularly**
```bash
# Run weekly to save disk space
docker system prune -a --volumes
```

### 3. **Name Your Containers**
```bash
# ❌ Bad - random name
docker run -d nginx

# ✅ Good - meaningful name
docker run -d --name web-server nginx
```

### 4. **Use .dockerignore**
Create a `.dockerignore` file to exclude unnecessary files from builds:
```
node_modules
.git
*.log
.env
```

### 5. **Don't Run as Root**
In your Dockerfile:
```dockerfile
# Create non-root user
RUN useradd -m appuser
USER appuser
```

### 6. **Use Multi-Stage Builds**
Keep images small:
```dockerfile
# Build stage
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
CMD ["node", "dist/index.js"]
```

### 7. **Limit Resources**
```bash
# Prevent containers from consuming all resources
docker run --memory="512m" --cpus="1.0" nginx
```

### 8. **Health Checks**
Add to Dockerfile:
```dockerfile
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost/ || exit 1
```

## Quick Reference Card

```bash
# Most used commands
docker run -it --rm <image>           # Quick test
docker ps -a                          # See all containers
docker logs -f <container>            # Watch logs
docker exec -it <container> bash      # Get shell
docker stop $(docker ps -q)           # Stop all
docker system prune -a                # Clean everything
```

## Useful Aliases (add to ~/.bashrc or ~/.zshrc)

```bash
alias dps='docker ps'
alias dpsa='docker ps -a'
alias di='docker images'
alias dex='docker exec -it'
alias dlog='docker logs -f'
alias dstop='docker stop'
alias drm='docker rm'
alias dprune='docker system prune -a --volumes'
```

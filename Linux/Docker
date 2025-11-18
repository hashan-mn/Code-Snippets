# Comprehensive Docker Cheat Sheet

## Installation

```bash
# Ubuntu/Debian
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Add user to docker group (avoid sudo)
sudo usermod -aG docker $USER

# Start Docker service
sudo systemctl start docker
sudo systemctl enable docker

# Verify installation
docker --version
docker run hello-world
```

## Container Management

### Running Containers

```bash
# Run container
docker run nginx

# Run with name
docker run --name mynginx nginx

# Run in detached mode (background)
docker run -d nginx

# Run with port mapping
docker run -p 8080:80 nginx

# Run with environment variables
docker run -e ENV=production -e DEBUG=true nginx

# Run interactively
docker run -it ubuntu bash

# Run and remove after exit
docker run --rm ubuntu echo "Hello"

# Run with volume mount
docker run -v /host/path:/container/path nginx

# Run with multiple options
docker run -d --name web -p 80:80 -v $(pwd):/usr/share/nginx/html nginx

# Run with restart policy
docker run -d --restart unless-stopped nginx

# Run with resource limits
docker run -d --memory="512m" --cpus="1.5" nginx

# Run with network
docker run -d --network mynetwork nginx

# Run with hostname
docker run -d --hostname myserver nginx

# Run with user
docker run -d --user 1000:1000 nginx

# Run with working directory
docker run -d -w /app node npm start
```

### Container Lifecycle

```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# List container IDs only
docker ps -q

# Start container
docker start container_name

# Stop container
docker stop container_name

# Stop container forcefully
docker kill container_name

# Restart container
docker restart container_name

# Pause container
docker pause container_name

# Unpause container
docker unpause container_name

# Remove container
docker rm container_name

# Remove running container (force)
docker rm -f container_name

# Remove all stopped containers
docker container prune

# Remove all containers (force)
docker rm -f $(docker ps -aq)
```

### Container Inspection

```bash
# View container logs
docker logs container_name

# Follow logs (live)
docker logs -f container_name

# Show last 100 lines
docker logs --tail 100 container_name

# Show logs with timestamps
docker logs -t container_name

# Inspect container details
docker inspect container_name

# View container stats (live)
docker stats

# View stats for specific container
docker stats container_name

# View running processes in container
docker top container_name

# View port mappings
docker port container_name

# View container changes
docker diff container_name
```

### Executing Commands

```bash
# Execute command in running container
docker exec container_name command

# Execute interactively
docker exec -it container_name bash

# Execute as specific user
docker exec -u root container_name whoami

# Execute in specific directory
docker exec -w /app container_name ls

# Attach to running container
docker attach container_name

# Copy files from container
docker cp container_name:/path/file.txt ./local/

# Copy files to container
docker cp ./local/file.txt container_name:/path/
```

## Image Management

### Working with Images

```bash
# List images
docker images

# List all images (including intermediate)
docker images -a

# Pull image from registry
docker pull nginx

# Pull specific version
docker pull nginx:1.21

# Pull from different registry
docker pull gcr.io/project/image:tag

# Search for images
docker search nginx

# Remove image
docker rmi nginx

# Remove image by ID
docker rmi abc123def456

# Remove unused images
docker image prune

# Remove all images
docker rmi $(docker images -q)

# Tag image
docker tag nginx:latest mynginx:v1

# View image history
docker history nginx

# View image details
docker inspect nginx

# Save image to tar file
docker save nginx > nginx.tar
docker save -o nginx.tar nginx

# Load image from tar file
docker load < nginx.tar
docker load -i nginx.tar

# Export container as image
docker export container_name > container.tar

# Import tarball as image
docker import container.tar myimage:tag
```

### Building Images

```bash
# Build image from Dockerfile
docker build .

# Build with tag
docker build -t myapp:latest .

# Build with custom Dockerfile
docker build -f Dockerfile.dev -t myapp:dev .

# Build with build arguments
docker build --build-arg VERSION=1.0 -t myapp .

# Build without cache
docker build --no-cache -t myapp .

# Build with target stage (multi-stage)
docker build --target production -t myapp .

# Build with platform
docker build --platform linux/amd64 -t myapp .

# Show build history
docker buildx imagetools inspect myapp:latest
```

## Dockerfile Reference

### Basic Dockerfile

```dockerfile
# Base image
FROM node:18-alpine

# Metadata
LABEL maintainer="you@example.com"
LABEL version="1.0"

# Set working directory
WORKDIR /app

# Copy files
COPY package*.json ./

# Run commands
RUN npm install

# Copy application
COPY . .

# Environment variables
ENV NODE_ENV=production
ENV PORT=3000

# Expose ports
EXPOSE 3000

# Volume mount point
VOLUME ["/app/data"]

# Default user
USER node

# Health check
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:3000/health || exit 1

# Entrypoint (always runs)
ENTRYPOINT ["docker-entrypoint.sh"]

# Default command (can be overridden)
CMD ["node", "server.js"]
```

### Multi-Stage Build

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
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
CMD ["node", "dist/server.js"]
```

### Advanced Dockerfile

```dockerfile
FROM ubuntu:22.04

# Multiple RUN commands combined (reduce layers)
RUN apt-get update && \
    apt-get install -y curl git && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# ARG for build-time variables
ARG APP_VERSION=1.0.0
ARG BUILD_DATE

# Use ARG in ENV
ENV APP_VERSION=${APP_VERSION}

# Add files with permissions
ADD --chown=appuser:appuser app.tar.gz /app/

# Create user
RUN groupadd -r appuser && \
    useradd -r -g appuser appuser

# Set multiple working directories
WORKDIR /app
WORKDIR subdir  # Results in /app/subdir

# Conditional commands
RUN if [ "$BUILD_ENV" = "production" ]; then \
      npm install --production; \
    else \
      npm install; \
    fi

# ONBUILD triggers (for base images)
ONBUILD COPY . /app
ONBUILD RUN npm install

# Shell form vs Exec form
CMD npm start              # Shell form
CMD ["npm", "start"]       # Exec form (preferred)

# ENTRYPOINT + CMD combination
ENTRYPOINT ["python", "app.py"]
CMD ["--help"]  # Default argument, can be overridden
```

## Docker Compose

### docker-compose.yml Basic

```yaml
version: '3.8'

services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html
    environment:
      - ENV=production
    restart: unless-stopped
    
  db:
    image: postgres:14
    environment:
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data
    
volumes:
  postgres_data:
```

### docker-compose.yml Advanced

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile.dev
      args:
        - VERSION=1.0
    image: myapp:latest
    container_name: myapp
    hostname: app-server
    ports:
      - "3000:3000"
      - "3001:3001"
    expose:
      - "3000"
    volumes:
      - ./src:/app/src
      - /app/node_modules
      - app_logs:/app/logs
    environment:
      NODE_ENV: development
      DATABASE_URL: postgresql://user:pass@db:5432/mydb
    env_file:
      - .env
      - .env.local
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started
    networks:
      - frontend
      - backend
    restart: unless-stopped
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 1G
        reservations:
          cpus: '0.5'
          memory: 512M
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
    command: npm run dev
    entrypoint: ["/docker-entrypoint.sh"]
    user: "node"
    working_dir: /app
    stdin_open: true
    tty: true
    
  db:
    image: postgres:14-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD_FILE: /run/secrets/db_password
      POSTGRES_DB: mydb
    secrets:
      - db_password
    networks:
      - backend
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user"]
      interval: 10s
      timeout: 5s
      retries: 5
      
  redis:
    image: redis:alpine
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data
    networks:
      - backend
      
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - ./ssl:/etc/nginx/ssl:ro
    depends_on:
      - app
    networks:
      - frontend
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.app.rule=Host(`example.com`)"

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
    internal: true

volumes:
  postgres_data:
    driver: local
  redis_data:
  app_logs:

secrets:
  db_password:
    file: ./secrets/db_password.txt
```

### Docker Compose Commands

```bash
# Start services
docker-compose up

# Start in detached mode
docker-compose up -d

# Start specific service
docker-compose up -d web

# Build images
docker-compose build

# Build without cache
docker-compose build --no-cache

# Build and start
docker-compose up --build

# Stop services
docker-compose stop

# Stop and remove containers
docker-compose down

# Stop and remove containers, networks, volumes
docker-compose down -v

# View logs
docker-compose logs

# Follow logs
docker-compose logs -f

# Logs for specific service
docker-compose logs -f web

# List containers
docker-compose ps

# Execute command in service
docker-compose exec web bash

# Run one-off command
docker-compose run web npm test

# Scale services
docker-compose up -d --scale web=3

# View config
docker-compose config

# Validate config
docker-compose config --quiet

# Restart services
docker-compose restart

# Pause services
docker-compose pause

# Unpause services
docker-compose unpause

# Pull images
docker-compose pull

# Push images
docker-compose push

# View events
docker-compose events

# Top processes
docker-compose top
```

## Volume Management

```bash
# Create volume
docker volume create myvolume

# List volumes
docker volume ls

# Inspect volume
docker volume inspect myvolume

# Remove volume
docker volume rm myvolume

# Remove unused volumes
docker volume prune

# Mount volume in container
docker run -v myvolume:/data nginx

# Mount host directory (bind mount)
docker run -v /host/path:/container/path nginx

# Mount as read-only
docker run -v /host/path:/container/path:ro nginx

# Create tmpfs mount (memory)
docker run --tmpfs /tmp nginx

# Named volume with options
docker run -v myvolume:/data:rw,z nginx
```

## Network Management

```bash
# List networks
docker network ls

# Create network
docker network create mynetwork

# Create with subnet
docker network create --subnet=172.18.0.0/16 mynetwork

# Create with gateway
docker network create --gateway=172.18.0.1 mynetwork

# Create bridge network
docker network create --driver bridge mynetwork

# Create overlay network (Swarm)
docker network create --driver overlay mynetwork

# Inspect network
docker network inspect mynetwork

# Connect container to network
docker network connect mynetwork container_name

# Disconnect container
docker network disconnect mynetwork container_name

# Remove network
docker network rm mynetwork

# Remove unused networks
docker network prune

# Run container with network
docker run --network mynetwork nginx

# Run with custom DNS
docker run --dns 8.8.8.8 nginx

# Run with host network
docker run --network host nginx

# Run with no network
docker run --network none nginx
```

## Registry & Repository

```bash
# Login to Docker Hub
docker login

# Login to private registry
docker login registry.example.com

# Logout
docker logout

# Tag image for registry
docker tag myapp:latest username/myapp:latest
docker tag myapp:latest registry.example.com/myapp:latest

# Push to Docker Hub
docker push username/myapp:latest

# Push to private registry
docker push registry.example.com/myapp:latest

# Pull from private registry
docker pull registry.example.com/myapp:latest

# Search Docker Hub
docker search postgres

# Run local registry
docker run -d -p 5000:5000 --name registry registry:2

# Push to local registry
docker tag myapp localhost:5000/myapp
docker push localhost:5000/myapp
```

## Docker System

```bash
# Show disk usage
docker system df

# Show detailed disk usage
docker system df -v

# Remove unused data
docker system prune

# Remove all unused data (including volumes)
docker system prune -a --volumes

# Show system info
docker info

# Show version
docker version

# Show events
docker events

# Show events for container
docker events --filter container=mycontainer

# Show events since timestamp
docker events --since '2024-01-01'
```

## Docker Context

```bash
# List contexts
docker context ls

# Create context
docker context create mycontext --docker host=tcp://remote:2376

# Use context
docker context use mycontext

# Inspect context
docker context inspect mycontext

# Remove context
docker context rm mycontext

# Export context
docker context export mycontext

# Import context
docker context import mycontext context.tar
```

## Docker Swarm

### Initialize & Manage

```bash
# Initialize swarm
docker swarm init

# Initialize with specific IP
docker swarm init --advertise-addr 192.168.1.100

# Join worker token
docker swarm join-token worker

# Join manager token
docker swarm join-token manager

# Join swarm
docker swarm join --token TOKEN 192.168.1.100:2377

# Leave swarm
docker swarm leave

# Leave swarm (force)
docker swarm leave --force

# Update swarm
docker swarm update --autolock=true

# Unlock swarm
docker swarm unlock
```

### Service Management

```bash
# Create service
docker service create --name web nginx

# Create with replicas
docker service create --name web --replicas 3 nginx

# Create with port
docker service create --name web -p 80:80 nginx

# Create with mount
docker service create --name web --mount source=data,target=/data nginx

# Create with network
docker service create --name web --network mynetwork nginx

# Create with constraints
docker service create --name web --constraint 'node.role==worker' nginx

# Create with update config
docker service create --name web --update-delay 10s --update-parallelism 2 nginx

# List services
docker service ls

# Inspect service
docker service inspect web

# View service logs
docker service logs web

# Follow service logs
docker service logs -f web

# Scale service
docker service scale web=5

# Update service image
docker service update --image nginx:latest web

# Update service replicas
docker service update --replicas 5 web

# Rollback service
docker service rollback web

# Remove service
docker service rm web

# List tasks
docker service ps web
```

### Stack Management

```bash
# Deploy stack
docker stack deploy -c docker-compose.yml mystack

# List stacks
docker stack ls

# List stack services
docker stack services mystack

# List stack tasks
docker stack ps mystack

# Remove stack
docker stack rm mystack
```

### Node Management

```bash
# List nodes
docker node ls

# Inspect node
docker node inspect node_id

# Update node availability
docker node update --availability drain node_id
docker node update --availability active node_id

# Add label to node
docker node update --label-add type=database node_id

# Remove label
docker node update --label-rm type node_id

# Promote to manager
docker node promote node_id

# Demote to worker
docker node demote node_id

# Remove node
docker node rm node_id
```

## Security

```bash
# Run as non-root user
docker run --user 1000:1000 nginx

# Run with read-only root filesystem
docker run --read-only nginx

# Drop capabilities
docker run --cap-drop ALL --cap-add NET_BIND_SERVICE nginx

# Set security options
docker run --security-opt no-new-privileges nginx

# Use secrets (Swarm)
echo "password" | docker secret create db_password -
docker service create --secret db_password nginx

# Use configs (Swarm)
docker config create nginx.conf ./nginx.conf
docker service create --config nginx.conf nginx

# Scan image for vulnerabilities
docker scan nginx:latest

# Sign and verify images
docker trust sign myimage:tag
docker trust inspect myimage:tag

# Run with AppArmor profile
docker run --security-opt apparmor=docker-default nginx

# Run with SELinux label
docker run --security-opt label=level:s0:c100,c200 nginx
```

## Troubleshooting

```bash
# Check Docker status
sudo systemctl status docker

# Restart Docker service
sudo systemctl restart docker

# View Docker daemon logs
sudo journalctl -u docker -f

# Enable debug mode
sudo dockerd --debug

# Check container health
docker inspect --format='{{.State.Health.Status}}' container_name

# Get container IP
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name

# Debug failed build
docker build --progress=plain --no-cache .

# Run with debug output
docker run --log-driver=json-file --log-opt max-size=10m nginx

# Check resource usage
docker stats --no-stream

# Get full container logs
docker logs container_name 2>&1 | less

# Enter container as root
docker exec -u 0 -it container_name bash

# Check exit code
docker inspect -f '{{.State.ExitCode}}' container_name

# List network connections
docker exec container_name netstat -tulpn
```

## Best Practices

### Dockerfile Best Practices

```dockerfile
# Use specific tags, not latest
FROM node:18.16-alpine

# Use multi-stage builds
FROM node:18 AS builder
# Build stage...
FROM node:18-alpine
COPY --from=builder /app/dist ./dist

# Minimize layers - combine RUN commands
RUN apt-get update && apt-get install -y \
    package1 \
    package2 \
    && rm -rf /var/lib/apt/lists/*

# Order layers by change frequency
COPY package*.json ./
RUN npm install
COPY . .

# Use .dockerignore
# Create .dockerignore file:
# node_modules
# .git
# *.md

# Don't run as root
USER node

# Use COPY instead of ADD
COPY package.json ./

# Leverage build cache
RUN npm install
COPY . .

# Use LABEL for metadata
LABEL org.opencontainers.image.source="https://github.com/user/repo"

# Pin versions
RUN npm install express@4.18.2

# Health checks
HEALTHCHECK CMD curl -f http://localhost/ || exit 1
```

### Security Checklist

```bash
# 1. Use official images
FROM nginx:alpine

# 2. Scan for vulnerabilities
docker scan myimage

# 3. Don't run as root
USER appuser

# 4. Use secrets for sensitive data
docker secret create my_secret secret.txt

# 5. Limit resources
docker run --memory="512m" --cpus="1" myapp

# 6. Use read-only filesystem where possible
docker run --read-only myapp

# 7. Drop unnecessary capabilities
docker run --cap-drop ALL myapp

# 8. Use trusted registries
docker pull gcr.io/distroless/nodejs

# 9. Keep images updated
docker pull nginx:alpine
docker system prune -a
```

## Common Patterns

### Development Environment

```yaml
# docker-compose.dev.yml
version: '3.8'
services:
  app:
    build:
      context: .
      target: development
    volumes:
      - .:/app
      - /app/node_modules
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
    command: npm run dev
```

### Production Stack

```yaml
version: '3.8'
services:
  app:
    image: myapp:${VERSION}
    deploy:
      replicas: 3
      update_config:
        parallelism: 1
        delay: 10s
      restart_policy:
        condition: on-failure
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost/health"]
      interval: 30s
    networks:
      - backend
  
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - app
    networks:
      - backend
```

---

**Official Documentation**: https://docs.docker.com/
**Docker Hub**: https://hub.docker.com/

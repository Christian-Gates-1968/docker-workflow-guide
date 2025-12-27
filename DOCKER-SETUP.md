# 🐳 Docker Setup Guide - Universal Reference

## Prerequisites

```bash
# Check if Docker is installed
docker --version

# Check if Docker Compose is installed
docker-compose --version

# Verify Docker is running
docker ps
```

If Docker is not installed, download **Docker Desktop** from:
- **Windows/Mac:** https://www.docker.com/products/docker-desktop/
- **Linux:** https://docs.docker.com/engine/install/

---

## 🚀 Quick Start (First Time Setup)

### Step 1: Clone or Navigate to Your Project
```bash
# If cloning from Git
git clone https://github.com/<username>/<project-name>.git
cd <project-name>

# OR navigate to existing project
cd /path/to/your/project
```

### Step 2: Configure Environment Variables
```bash
# Copy the example environment file (if exists)
cp .env.example .env

# Edit .env and add your environment variables
# Windows: notepad .env
# Mac/Linux: nano .env
```

**Example `.env` file:**
```env
API_KEY=your_api_key_here
DATABASE_URL=postgresql://user:password@localhost:5432/dbname
NODE_ENV=production
```

### Step 3: Build and Run with Docker Compose
```bash
# Build the image and start containers in detached mode
docker-compose up -d --build
```

### Step 4: Access the Application
Open your browser and navigate to:
```
http://localhost:<PORT>
```
*(Replace `<PORT>` with the port specified in your `docker-compose.yml`)*

---

## 📋 Common Docker Commands

### View Running Containers
```bash
docker ps

# Or with formatting
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}\t{{.Ports}}"
```

### View All Containers (including stopped)
```bash
docker ps -a
```

### View Container Logs
```bash
# Follow logs in real-time (replace <container-name> with your container)
docker logs <container-name> -f

# View last 100 lines
docker logs <container-name> --tail 100

# With timestamps
docker logs <container-name> -f --timestamps
```

### Stop the Application
```bash
# Stop all services defined in docker-compose.yml
docker-compose down

# Stop specific container
docker stop <container-name>
```

### Restart the Application
```bash
# Restart all services
docker-compose restart

# Restart specific service
docker-compose restart <service-name>

# Restart specific container
docker restart <container-name>
```

### Rebuild After Code Changes
```bash
# Stop, rebuild, and start
docker-compose up -d --build

# Or individual steps:
docker-compose down
docker-compose build --no-cache
docker-compose up -d
```

### Start Existing Containers
```bash
# Start all services
docker-compose up -d

# Start specific service
docker-compose up -d <service-name>

# Start stopped container
docker start <container-name>
```

### Stop Without Removing Container
```bash
docker-compose stop

# Or specific container
docker stop <container-name>
```

### Remove Container and Network
```bash
docker-compose down

# Remove volumes as well
docker-compose down -v
```

### Remove Container, Network, and Images
```bash
docker-compose down --rmi all

# Remove local images only
docker-compose down --rmi local
```

---

## 🔧 Advanced Commands

### Access Container Shell
```bash
# Bash shell
docker exec -it <container-name> /bin/bash

# Or sh if bash not available
docker exec -it <container-name> /bin/sh

# Run as root user
docker exec -it -u root <container-name> /bin/bash
```

### Execute Commands Inside Container
```bash
# Run a single command
docker exec <container-name> <command>

# Example: Check Python version
docker exec <container-name> python --version

# Example: List files
docker exec <container-name> ls -la
```

### View Container Resource Usage
```bash
# Single container
docker stats <container-name>

# All containers
docker stats

# No streaming (single snapshot)
docker stats --no-stream
```

### Inspect Container Details
```bash
# Full JSON output
docker inspect <container-name>

# Specific field (e.g., IP address)
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container-name>

# Container state
docker inspect --format='{{.State.Status}}' <container-name>
```

### View Docker Images
```bash
# List all images
docker images

# List with formatting
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"

# List dangling images
docker images -f "dangling=true"
```

### Remove Unused Images
```bash
# Remove dangling images
docker image prune

# Remove all unused images
docker image prune -a

# Remove specific image
docker rmi <image-name>:<tag>
```

### View Docker Networks
```bash
docker network ls

# Inspect specific network
docker network inspect <network-name>
```

### View Docker Volumes
```bash
docker volume ls

# Inspect specific volume
docker volume inspect <volume-name>

# Remove unused volumes
docker volume prune
```

### Copy Files To/From Container
```bash
# Copy from host to container
docker cp /path/on/host <container-name>:/path/in/container

# Copy from container to host
docker cp <container-name>:/path/in/container /path/on/host
```

---

## 🛠️ Troubleshooting

### Container Won't Start
```bash
# Check logs for errors
docker logs <container-name>

# Check all container logs
docker-compose logs

# Check if port is already in use
# Windows:
netstat -ano | findstr :<PORT>

# Mac/Linux:
lsof -i :<PORT>

# Kill process using port (Windows)
taskkill /PID <PID> /F

# Kill process using port (Mac/Linux)
kill -9 <PID>

# Try rebuilding from scratch
docker-compose down
docker-compose build --no-cache
docker-compose up -d
```

### Code Changes Not Reflecting
```bash
# If volume mount is configured, restart should work:
docker-compose restart

# Otherwise, force rebuild:
docker-compose up -d --build

# Or clear build cache:
docker-compose build --no-cache
docker-compose up -d
```

### Clear Everything and Start Fresh
```bash
# Stop and remove containers, networks
docker-compose down

# Remove images (use your image name)
docker rmi <image-name>

# Remove all unused resources
docker system prune -a

# Rebuild and restart
docker-compose up -d --build
```

### Check Environment Variables
```bash
# View all environment variables in container
docker exec <container-name> env

# Search for specific variable
docker exec <container-name> env | grep <VARIABLE_NAME>

# Or using docker-compose
docker-compose config
```

### Docker Desktop Not Running
```bash
# Error: "Cannot connect to the Docker daemon"
# Solution: Start Docker Desktop application and wait for it to fully initialize

# On Linux, start Docker service
sudo systemctl start docker
sudo systemctl enable docker
```

### Permission Denied Errors
```bash
# Linux/Mac: Add user to docker group
sudo usermod -aG docker $USER

# Log out and log back in for changes to take effect

# Or run with sudo (not recommended for production)
sudo docker-compose up -d
```

### Container Exiting Immediately
```bash
# Check exit code and reason
docker ps -a
docker logs <container-name>

# Common issues:
# - Missing environment variables
# - Port already in use
# - Application crashes on startup
# - Incorrect CMD/ENTRYPOINT in Dockerfile
```

### Network Issues
```bash
# List networks
docker network ls

# Inspect network
docker network inspect <network-name>

# Create custom network
docker network create <network-name>

# Connect container to network
docker network connect <network-name> <container-name>
```

### Disk Space Issues
```bash
# Check Docker disk usage
docker system df

# Clean up everything (BE CAREFUL!)
docker system prune -a --volumes

# Remove specific resources
docker container prune  # Remove stopped containers
docker image prune -a   # Remove unused images
docker volume prune     # Remove unused volumes
docker network prune    # Remove unused networks
```

---

## 📦 Production Deployment

### Build for Production
```bash
# Build optimized image with specific tag
docker build -t <image-name>:production .

# Run without development volume mounts
docker run -d \
  --name <container-name> \
  -p <HOST_PORT>:<CONTAINER_PORT> \
  --env-file .env \
  --restart unless-stopped \
  <image-name>:production
```

### Multi-Stage Build (Dockerfile Example)
```dockerfile
# Build stage
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production stage
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY package*.json ./
RUN npm ci --production
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

### Push to Docker Hub
```bash
# Tag the image
docker tag <image-name>:production <dockerhub-username>/<image-name>:latest
docker tag <image-name>:production <dockerhub-username>/<image-name>:v1.0.0

# Login to Docker Hub
docker login

# Push to registry
docker push <dockerhub-username>/<image-name>:latest
docker push <dockerhub-username>/<image-name>:v1.0.0
```

### Pull From Docker Hub
```bash
# Pull latest version
docker pull <dockerhub-username>/<image-name>:latest

# Pull specific version
docker pull <dockerhub-username>/<image-name>:v1.0.0

# Run pulled image
docker run -d -p <HOST_PORT>:<CONTAINER_PORT> --env-file .env <dockerhub-username>/<image-name>:latest
```

### Deploy on Cloud Platforms

**AWS ECS / EC2:**
```bash
# Install Docker on EC2 instance
sudo yum update -y
sudo yum install docker -y
sudo service docker start

# Pull and run
docker pull <dockerhub-username>/<image-name>:latest
docker run -d -p 80:<CONTAINER_PORT> --env-file .env --restart always <dockerhub-username>/<image-name>:latest
```

**Google Cloud Run:**
```bash
# Build for Google Cloud
gcloud builds submit --tag gcr.io/<PROJECT_ID>/<image-name>

# Deploy
gcloud run deploy <service-name> --image gcr.io/<PROJECT_ID>/<image-name> --platform managed
```

**Azure Container Instances:**
```bash
# Login to Azure
az login

# Create container
az container create \
  --resource-group <resource-group> \
  --name <container-name> \
  --image <dockerhub-username>/<image-name>:latest \
  --dns-name-label <unique-dns-name> \
  --ports <PORT>
```

**Docker Swarm:**
```bash
# Initialize swarm
docker swarm init

# Deploy stack
docker stack deploy -c docker-compose.yml <stack-name>

# List services
docker service ls

# Scale service
docker service scale <stack-name>_<service-name>=3
```

**Kubernetes:**
```bash
# Create deployment
kubectl create deployment <app-name> --image=<dockerhub-username>/<image-name>:latest

# Expose service
kubectl expose deployment <app-name> --type=LoadBalancer --port=80 --target-port=<CONTAINER_PORT>

# Or use YAML manifest
kubectl apply -f k8s/deployment.yaml
```

**Example Kubernetes Deployment (k8s/deployment.yaml):**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: <dockerhub-username>/<image-name>:latest
        ports:
        - containerPort: 3000
        env:
        - name: NODE_ENV
          value: "production"
---
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 3000
  type: LoadBalancer
```

---

## 🔍 Health Checks

### Manual Health Check
```bash
# HTTP health check (if your app has health endpoint)
curl http://localhost:<PORT>/health

# Or using wget
wget -qO- http://localhost:<PORT>/health
```

### Docker Health Check in Dockerfile
```dockerfile
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:<PORT>/health || exit 1
```

### Check Container Health Status
```bash
docker inspect --format='{{.State.Health.Status}}' <container-name>

# Possible statuses: starting, healthy, unhealthy
```

### View Health Check Logs
```bash
docker inspect <container-name> | jq '.[0].State.Health'
```

---

## 📊 Monitoring & Logging

### Real-time Resource Monitoring
```bash
# Single container
docker stats <container-name>

# All containers with custom format
docker stats --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemUsage}}"
```

### Export Container Logs
```bash
# Export to file
docker logs <container-name> > app-logs.txt

# With timestamps
docker logs <container-name> --timestamps > app-logs.txt

# Last 1000 lines
docker logs <container-name> --tail 1000 > app-logs.txt
```

### Centralized Logging (Advanced)
```yaml
# docker-compose.yml with logging driver
version: '3.8'
services:
  app:
    image: my-app
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
```

### Monitor Logs from Multiple Containers
```bash
# All services in docker-compose
docker-compose logs -f

# Specific services
docker-compose logs -f <service1> <service2>

# With timestamps
docker-compose logs -f --timestamps
```

---

## 🔄 Update Workflow

### Update from Git Repository
```bash
# Pull latest code
git pull origin main

# Rebuild and restart
docker-compose down
docker-compose up -d --build

# Verify it's running
docker ps
docker logs <container-name> -f
```

### Rolling Update (Zero Downtime)
```bash
# Using Docker Compose with multiple replicas
docker-compose up -d --scale <service-name>=3 --no-recreate

# Update with new image
docker-compose pull <service-name>
docker-compose up -d --no-deps <service-name>
```

### Backup Before Update
```bash
# Backup volumes
docker run --rm -v <volume-name>:/data -v $(pwd):/backup \
  alpine tar czf /backup/backup-$(date +%Y%m%d).tar.gz /data

# Backup database container
docker exec <db-container> pg_dump -U <user> <database> > backup.sql
```

---

## 🔐 Security Best Practices

### Use Secrets for Sensitive Data
```bash
# Docker Swarm secrets
echo "my_secret_password" | docker secret create db_password -

# Use in docker-compose.yml
services:
  app:
    secrets:
      - db_password
secrets:
  db_password:
    external: true
```

### Run as Non-Root User
```dockerfile
# In Dockerfile
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser
```

### Scan Images for Vulnerabilities
```bash
# Using Docker Scan
docker scan <image-name>

# Using Trivy
trivy image <image-name>
```

### Limit Container Resources
```yaml
# docker-compose.yml
services:
  app:
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
        reservations:
          cpus: '0.25'
          memory: 256M
```

---

## 📚 Docker Compose Reference

### Basic docker-compose.yml Template
```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: my-app
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - API_KEY=${API_KEY}
    env_file:
      - .env
    volumes:
      - ./src:/app/src
      - /app/node_modules
    depends_on:
      - database
    networks:
      - app-network
    restart: unless-stopped

  database:
    image: postgres:15-alpine
    container_name: my-database
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - app-network
    restart: unless-stopped

volumes:
  db-data:

networks:
  app-network:
    driver: bridge
```

### Dockerfile Best Practices Template
```dockerfile
# Use specific version tags, not 'latest'
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Copy dependency files first (better caching)
COPY package*.json ./

# Install dependencies
RUN npm ci --production

# Copy application code
COPY . .

# Create non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s \
  CMD wget -qO- http://localhost:3000/health || exit 1

# Start application
CMD ["node", "server.js"]
```

---

## 🎯 Quick Reference Card

```bash
# BUILD & START
docker-compose up -d                    # Start all services
docker-compose up -d --build            # Build and start
docker build -t <name> .                # Build image manually

# STOP & REMOVE
docker-compose down                     # Stop all services
docker-compose down -v                  # Stop and remove volumes
docker stop <container>                 # Stop specific container
docker rm <container>                   # Remove container

# RESTART
docker-compose restart                  # Restart all services
docker restart <container>              # Restart specific container

# LOGS
docker logs <container> -f              # Follow logs
docker-compose logs -f                  # All service logs
docker logs <container> --tail 100      # Last 100 lines

# EXECUTE COMMANDS
docker exec -it <container> /bin/bash   # Interactive shell
docker exec <container> <command>       # Single command

# INSPECT & DEBUG
docker ps                               # Running containers
docker ps -a                            # All containers
docker images                           # List images
docker stats <container>                # Resource usage
docker inspect <container>              # Detailed info

# CLEANUP
docker system prune -a                  # Remove all unused resources
docker container prune                  # Remove stopped containers
docker image prune -a                   # Remove unused images
docker volume prune                     # Remove unused volumes

# ENVIRONMENT
docker exec <container> env             # View env variables
docker-compose config                   # Validate compose file

# COPY FILES
docker cp <src> <container>:<dest>      # Host to container
docker cp <container>:<src> <dest>      # Container to host

# NETWORKING
docker network ls                       # List networks
docker network inspect <network>        # Network details
docker network create <network>         # Create network

# REGISTRY
docker login                            # Login to Docker Hub
docker push <image>                     # Push to registry
docker pull <image>                     # Pull from registry
docker tag <image> <new-tag>            # Tag image
```

---

## 💡 Tips & Best Practices

### 1. **Use .dockerignore**
```
# .dockerignore
node_modules/
npm-debug.log
.git/
.env
*.md
.vscode/
.idea/
dist/
coverage/
```

### 2. **Layer Caching Strategy**
- Put frequently changing files LAST in Dockerfile
- Copy dependency files before source code
- Use multi-stage builds for smaller images

### 3. **Development vs Production**
```yaml
# docker-compose.yml for development
volumes:
  - ./src:/app/src  # Live code reload

# docker-compose.prod.yml for production
# Remove volume mounts, use built code
```

### 4. **Environment-Specific Compose Files**
```bash
# Development
docker-compose up -d

# Production
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up -d

# Testing
docker-compose -f docker-compose.yml -f docker-compose.test.yml up -d
```

### 5. **Resource Limits**
Always set memory and CPU limits in production to prevent resource exhaustion.

### 6. **Health Checks**
Implement health checks for automatic container restarts and load balancer integration.

### 7. **Use Alpine Images**
Use Alpine-based images (`node:18-alpine`) for smaller image sizes when possible.

### 8. **Version Pinning**
Always pin specific versions in Dockerfile and docker-compose.yml - never use `latest`.

### 9. **Multi-Stage Builds**
Use multi-stage builds to separate build dependencies from runtime dependencies.

### 10. **Logging**
Configure log rotation to prevent disk space issues:
```yaml
logging:
  driver: "json-file"
  options:
    max-size: "10m"
    max-file: "3"
```

---

## 🆘 Common Error Messages & Solutions

| Error | Solution |
|-------|----------|
| `Cannot connect to Docker daemon` | Start Docker Desktop or `sudo systemctl start docker` |
| `port is already allocated` | Stop process using port or change port mapping |
| `no space left on device` | Run `docker system prune -a` |
| `permission denied` | Add user to docker group or use `sudo` |
| `network not found` | Recreate network or restart Docker |
| `Cannot start service: driver failed` | Check volume/bind mount paths exist |
| `exec format error` | Architecture mismatch - rebuild for correct platform |
| `manifest unknown` | Image doesn't exist - check image name/tag |

---

## 📖 Additional Resources

- **Official Docker Docs:** https://docs.docker.com/
- **Docker Hub:** https://hub.docker.com/
- **Docker Compose Docs:** https://docs.docker.com/compose/
- **Dockerfile Best Practices:** https://docs.docker.com/develop/develop-images/dockerfile_best-practices/
- **Docker Cheat Sheet:** https://dockerlabs.collabnix.com/docker/cheatsheet/
- **Play with Docker:** https://labs.play-with-docker.com/ (Free practice environment)

---

## 📝 Customization Instructions

**To use this guide for your project:**

1. Replace `<container-name>` with your actual container name
2. Replace `<image-name>` with your image name
3. Replace `<service-name>` with service names from your docker-compose.yml
4. Replace `<PORT>` with your application's port number
5. Replace `<dockerhub-username>` with your Docker Hub username
6. Update environment variables section with your specific variables

---

**This is a universal Docker reference guide. Adapt placeholders to match your project specifics.**

---
layout: default
title: "Module 4: Docker Basics"
parent: Pre-Workshop Preparation
nav_order: 4
---

# 🐳 Module 4: Docker Basics

> **Duration:** 30 minutes | **Level:** Beginner | **Hands-on:** Yes

---

## 📚 What You'll Learn

By the end of this module, you'll be able to:

- [ ] Understand what Docker is and why OAI uses it
- [ ] Work with Docker images and containers
- [ ] Run containers in foreground and background
- [ ] View container logs
- [ ] Use Docker Compose for multi-container applications
- [ ] Run the OAI 5G Core using Docker

---

## 📖 Chapter 1: Understanding Docker

### What is Docker?

**Docker** packages applications with everything they need to run:
- The application code
- Runtime environment
- System libraries
- Dependencies
- Configuration files

Think of it like a **shipping container** for software - works the same everywhere.

### The Problem Docker Solves

**Without Docker:**
```
Your Computer              Workshop VM              Production Server
├─ Ubuntu 20.04           ├─ Ubuntu 22.04          ├─ Ubuntu 24.04
├─ Python 3.8             ├─ Python 3.10           ├─ Python 3.12
├─ Library v1.0           ├─ Library v2.0          ├─ Library v3.0
└─ "Works on my machine!" └─ Breaks!               └─ Different bugs!
```

**With Docker:**
```
Your Computer              Workshop VM              Production Server
└─ Docker Container       └─ Docker Container      └─ Docker Container
   ├─ Ubuntu 20.04           ├─ Ubuntu 20.04          ├─ Ubuntu 20.04
   ├─ Python 3.8             ├─ Python 3.8            ├─ Python 3.8
   ├─ Library v1.0           ├─ Library v1.0          ├─ Library v1.0
   └─ Works everywhere! ✓    └─ Works! ✓              └─ Works! ✓
```

### Docker vs Virtual Machines

**Virtual Machine:**
```
┌─────────────────────────────────┐
│  App A  │  App B  │  App C      │
│  Bins   │  Bins   │  Bins       │
│  Guest  │  Guest  │  Guest OS   │  ← Each has full OS (GBs)
│   OS    │   OS    │             │
├─────────────────────────────────┤
│       Hypervisor                │
├─────────────────────────────────┤
│       Host OS                   │
└─────────────────────────────────┘
Heavy (GBs), Slow startup (minutes)
```

**Docker Container:**
```
┌─────────────────────────────────┐
│  App A  │  App B  │  App C      │
│  Bins   │  Bins   │  Bins       │  ← Shared OS kernel
├─────────────────────────────────┤
│       Docker Engine             │
├─────────────────────────────────┤
│       Host OS                   │
└─────────────────────────────────┘
Light (MBs), Fast startup (seconds)
```

### Why OAI Uses Docker

The OAI 5G Core has multiple components:
- AMF (Access and Mobility Management)
- SMF (Session Management)
- UPF (User Plane Function)
- UDM (Unified Data Management)
- AUSF (Authentication Server)
- NRF (Network Repository Function)
- MySQL (Database)

**With Docker:**
- ✅ Each component runs in its own container
- ✅ Easy to start/stop individual components
- ✅ Consistent environment across all machines
- ✅ Easy cleanup (just remove containers)
- ✅ No conflicts between components

---

## 🎯 Chapter 2: Key Docker Concepts

### Images vs Containers

**Image** = Blueprint/Template (like Ubuntu ISO file)
- Read-only
- Contains OS, application, dependencies
- Stored on disk
- Can create many containers from one image

**Container** = Running instance (like VM running Ubuntu)
- Created from an image
- Has its own isolated environment
- Can be started, stopped, deleted
- Temporary (unless you save changes)

**Analogy:**
```
Image = Class (in programming)
Container = Object/Instance

Image = Recipe
Container = Actual cooked dish
```

### Example:
```bash
# ubuntu:22.04 is the IMAGE
docker run ubuntu:22.04 echo "Hello"
           └─────┬─────┘
                 │
    This creates a CONTAINER that:
    1. Starts
    2. Runs echo "Hello"
    3. Stops
    4. Is removed
```

### Docker Registry

**Registry** = Storage for Docker images (like GitHub for Docker)

- **Docker Hub:** Public registry (hub.docker.com)
- **GitLab Registry:** OAI stores images here
- **Private registries:** Companies host their own

**When you run:**
```bash
docker pull ubuntu:22.04
```

Docker downloads the image from Docker Hub to your computer.

---

## 🚀 Chapter 3: Basic Docker Commands

### Verify Docker Installation
```bash
# Check Docker is installed
docker --version

# Check Docker is running
docker ps

# If permission error, add yourself to docker group
sudo usermod -aG docker $USER
# Then log out and back in
```

### Working with Images
```bash
# Search for images
docker search ubuntu

# Download an image
docker pull ubuntu:22.04

# List downloaded images
docker images

# Remove an image
docker rmi ubuntu:22.04

# Get image details
docker inspect ubuntu:22.04
```

### Running Containers

**Simple run (one command):**
```bash
# Run and exit immediately
docker run ubuntu:22.04 echo "Hello Docker"

# Run with interactive terminal
docker run -it ubuntu:22.04 /bin/bash
# Now you're inside container, try:
ls
pwd
exit  # Leave container
```

**Run in background (detached):**
```bash
# Start nginx web server
docker run -d nginx

# Run with a name
docker run -d --name my-web-server nginx

# Run with port mapping
docker run -d -p 8080:80 --name webserver nginx
# Now visit: http://localhost:8080
```

**Explanation of flags:**
- `-d` = Detached (background)
- `-it` = Interactive terminal
- `-p 8080:80` = Map host port 8080 to container port 80
- `--name` = Give container a name
- `--rm` = Remove container after it stops

### Managing Containers
```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# Stop a container
docker stop container-id
docker stop my-web-server

# Start a stopped container
docker start my-web-server

# Restart a container
docker restart my-web-server

# Remove a container
docker rm my-web-server

# Remove running container (force)
docker rm -f my-web-server

# Remove all stopped containers
docker container prune
```

### Viewing Container Logs
```bash
# View logs
docker logs container-id

# Follow logs (like tail -f)
docker logs -f container-id

# Last 50 lines
docker logs --tail 50 container-id

# Logs with timestamps
docker logs -t container-id
```

### Executing Commands in Running Containers
```bash
# Start a container
docker run -d --name myubuntu ubuntu:22.04 sleep 3600

# Execute command in running container
docker exec myubuntu ls /

# Get interactive shell
docker exec -it myubuntu /bin/bash
# You're inside! Type 'exit' to leave

# The container keeps running even after you exit
```

---

## 🎯 Chapter 4: Docker Networking

### Container Networks

Containers can communicate through Docker networks.
```bash
# List networks
docker network ls

# Create a network
docker network create my-network

# Run container on specific network
docker run -d --name web --network my-network nginx

# Inspect network
docker network inspect my-network

# Remove network
docker network rm my-network
```

### Port Mapping
```bash
# Map host port to container port
docker run -d -p 8080:80 nginx
#              └─┬─┘ └┬┘
#                │    └── Container port
#                └─────── Host port

# Map to random available port
docker run -d -P nginx

# See which port was assigned
docker ps
```

**Example:** OAI AMF listens on port 38412 in container
```bash
docker run -d -p 38412:38412 oai-amf
# Now accessible at localhost:38412
```

---

## 🎯 Chapter 5: Docker Compose

### What is Docker Compose?

**Problem:** OAI 5G Core has 7+ containers that need to:
- Start in correct order
- Connect to same network
- Share configuration
- All started with one command

**Solution:** Docker Compose - define everything in one YAML file

### Example: Simple docker-compose.yml
```yaml
version: '3.8'

services:
  web:
    image: nginx
    ports:
      - "8080:80"
  
  database:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: password
    volumes:
      - db-data:/var/lib/mysql

volumes:
  db-data:
```

### Docker Compose Commands
```bash
# Start all services (in folder with docker-compose.yml)
docker-compose up

# Start in background
docker-compose up -d

# View running services
docker-compose ps

# View logs
docker-compose logs

# Follow logs of specific service
docker-compose logs -f web

# Stop all services
docker-compose stop

# Stop and remove containers
docker-compose down

# Stop, remove containers, and remove volumes
docker-compose down -v

# Restart specific service
docker-compose restart web

# Rebuild images and start
docker-compose up --build
```

### OAI Docker Compose Example

For the workshop, OAI provides a docker-compose file:
```yaml
version: '3.8'

services:
  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: linux
      MYSQL_DATABASE: oai_db
    volumes:
      - dbdata:/var/lib/mysql

  oai-nrf:
    image: oaisoftwarealliance/oai-nrf:latest
    environment:
      NRF_INTERFACE_NAME_FOR_SBI: eth0
    depends_on:
      - mysql

  oai-amf:
    image: oaisoftwarealliance/oai-amf:latest
    environment:
      AMF_INTERFACE_NAME_FOR_NGAP: eth0
    depends_on:
      - oai-nrf
      - mysql

  # ... more services (SMF, UPF, etc.)

volumes:
  dbdata:
```

**To use:**
```bash
# Start OAI 5G Core
docker-compose -f docker-compose-basic-nrf.yaml up -d

# Check status
docker-compose -f docker-compose-basic-nrf.yaml ps

# View AMF logs
docker-compose -f docker-compose-basic-nrf.yaml logs -f oai-amf

# Stop everything
docker-compose -f docker-compose-basic-nrf.yaml down
```

---

## 🛠️ Chapter 6: Useful Docker Commands

### Resource Monitoring
```bash
# See resource usage (CPU, memory)
docker stats

# For specific container
docker stats container-name

# Disk usage
docker system df
```

### Container Information
```bash
# Detailed container info
docker inspect container-name

# Get container IP address
docker inspect container-name | grep IPAddress

# View container processes
docker top container-name
```

### Cleanup
```bash
# Remove stopped containers
docker container prune

# Remove unused images
docker image prune

# Remove unused volumes
docker volume prune

# Remove unused networks
docker network prune

# Remove everything unused (BE CAREFUL!)
docker system prune -a

# See what will be removed first
docker system prune -a --dry-run
```

### Copying Files
```bash
# Copy from container to host
docker cp container-name:/path/in/container /path/on/host

# Copy from host to container
docker cp /path/on/host container-name:/path/in/container
```

---

## 🧪 Hands-On Exercise

### Task 1: Run Your First Container (3 min)
```bash
# Pull Ubuntu image
docker pull ubuntu:22.04

# List images
docker images

# Run interactive Ubuntu container
docker run -it --name my-ubuntu ubuntu:22.04 /bin/bash

# Inside container, try:
ls
cat /etc/os-release
exit

# List all containers
docker ps -a

# Remove container
docker rm my-ubuntu
```

**Expected:** Successfully ran Ubuntu container

---

### Task 2: Run Web Server (5 min)
```bash
# Run nginx web server
docker run -d -p 8080:80 --name webserver nginx

# Check it's running
docker ps

# Test in browser: http://localhost:8080
# Or use curl:
curl http://localhost:8080

# View logs
docker logs webserver

# See resource usage
docker stats webserver
# Press Ctrl+C to exit stats

# Stop and remove
docker stop webserver
docker rm webserver
```

**Expected:** Web server accessible at localhost:8080

---

### Task 3: Container Communication (5 min)
```bash
# Create network
docker network create test-net

# Run container 1 (nginx)
docker run -d --name web --network test-net nginx

# Run container 2 (ubuntu)
docker run -it --name client --network test-net ubuntu:22.04

# Inside client container:
apt update
apt install -y curl
curl http://web
# You should see nginx welcome page!
exit

# Cleanup
docker stop web client
docker rm web client
docker network rm test-net
```

**Expected:** Containers can communicate by name

---

### Task 4: Docker Compose Practice (10 min)
```bash
# Create test directory
mkdir ~/docker-test
cd ~/docker-test

# Create docker-compose.yml
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  web:
    image: nginx
    ports:
      - "8080:80"
  
  app:
    image: python:3.9
    command: python -m http.server 8000
    ports:
      - "8000:8000"
EOF

# Start services
docker-compose up -d

# Check status
docker-compose ps

# Test both services
curl http://localhost:8080
curl http://localhost:8000

# View logs
docker-compose logs

# Follow logs of web service
docker-compose logs -f web
# Press Ctrl+C to stop following

# Stop services
docker-compose down

# Cleanup
cd ~
rm -rf ~/docker-test
```

**Expected:** Multi-container application works

---

### Task 5: Practice OAI Workflow (5 min)

Simulate the OAI workflow:
```bash
# Create test setup
mkdir ~/oai-test
cd ~/oai-test

# Create simple compose file (simulating OAI)
cat > docker-compose.yml << 'EOF'
version: '3.8'

services:
  database:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: linux
      MYSQL_DATABASE: test_db
    ports:
      - "3306:3306"
  
  app:
    image: nginx
    depends_on:
      - database
    ports:
      - "8080:80"
EOF

# Start (like starting OAI Core)
docker-compose up -d

# Check all services running
docker-compose ps

# View database logs
docker-compose logs database

# View app logs
docker-compose logs app

# Check resource usage
docker stats --no-stream

# Stop (like stopping OAI Core)
docker-compose down

# Cleanup
cd ~
rm -rf ~/oai-test
```

**Expected:** Comfortable with Docker Compose workflow

---

## ✅ Self-Check Quiz

<details>
<summary>❓ What's the difference between a Docker image and a container?</summary>

**Answer:** 
- **Image** = Template/blueprint (like a class), read-only, stored on disk
- **Container** = Running instance from an image (like an object), has its own state, can be started/stopped
</details>

<details>
<summary>❓ What does `docker run -d` do?</summary>

**Answer:** Runs container in **detached** mode (background), so it doesn't block your terminal.
</details>

<details>
<summary>❓ What does `-p 8080:80` mean?</summary>

**Answer:** Maps **host port 8080** to **container port 80**. Access container's port 80 via localhost:8080 on your host.
</details>

<details>
<summary>❓ How do you view logs of a running container?</summary>

**Answer:** `docker logs container-name` or `docker logs -f container-name` to follow logs in real-time.
</details>

<details>
<summary>❓ What does `docker-compose down` do?</summary>

**Answer:** Stops and removes all containers defined in docker-compose.yml. Add `-v` to also remove volumes.
</details>

<details>
<summary>❓ How do you execute a command in a running container?</summary>

**Answer:** `docker exec container-name command` or `docker exec -it container-name /bin/bash` for interactive shell.
</details>

<details>
<summary>❓ Why does OAI use Docker?</summary>

**Answer:** 
- Multiple components (AMF, SMF, UPF, etc.) need isolation
- Consistent environment across machines
- Easy to start/stop individual components
- Simple cleanup
- No dependency conflicts
</details>

<details>
<summary>❓ What does `docker ps` show?</summary>

**Answer:** Lists **running** containers. Use `docker ps -a` to see all containers including stopped ones.
</details>

---

## 🎓 Summary

### Essential Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `docker pull` | Download image | `docker pull ubuntu:22.04` |
| `docker images` | List images | `docker images` |
| `docker run` | Create and start container | `docker run -d nginx` |
| `docker ps` | List running containers | `docker ps` |
| `docker ps -a` | List all containers | `docker ps -a` |
| `docker logs` | View container logs | `docker logs container-name` |
| `docker exec` | Execute in container | `docker exec -it name bash` |
| `docker stop` | Stop container | `docker stop container-name` |
| `docker rm` | Remove container | `docker rm container-name` |
| `docker-compose up` | Start services | `docker-compose up -d` |
| `docker-compose down` | Stop services | `docker-compose down` |
| `docker-compose ps` | List services | `docker-compose ps` |
| `docker-compose logs` | View logs | `docker-compose logs -f` |

### Key Concepts

- ✅ Docker packages apps with all dependencies
- ✅ Images are templates, containers are running instances
- ✅ Containers are isolated and lightweight
- ✅ Docker Compose manages multi-container apps
- ✅ Port mapping connects container ports to host
- ✅ Networks allow container-to-container communication
- ✅ Volumes persist data beyond container lifetime

### OAI Usage

- ✅ OAI 5G Core runs in Docker containers
- ✅ Each network function (AMF, SMF, UPF) is a separate container
- ✅ Docker Compose starts all components together
- ✅ You'll use `docker-compose up -d` to start OAI Core
- ✅ You'll use `docker logs` to debug issues
- ✅ You'll use `docker-compose down` to stop everything

---

## 📚 Additional Resources

**Learn More:**
- [Docker Official Tutorial](https://docs.docker.com/get-started/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Play with Docker](https://labs.play-with-docker.com/) - Online playground

**Docker Hub:**
- Browse images: [hub.docker.com](https://hub.docker.com)
- OAI images: [DockerHub OAI](https://hub.docker.com/u/oaisoftwarealliance)

**Best Practices:**
- Name your containers (`--name`)
- Use specific image tags (`:22.04` not `:latest`)
- Clean up regularly (`docker system prune`)
- Use Docker Compose for multi-container apps
- Check logs when debugging (`docker logs -f`)
- Monitor resources (`docker stats`)

**Common Patterns:**
```bash
# Development workflow
docker-compose up -d          # Start
docker-compose logs -f app    # Watch logs
# Make changes
docker-compose restart app    # Restart one service
docker-compose down          # Stop all

# Debugging workflow
docker ps                    # Find container
docker logs container-name   # Check logs
docker exec -it container bash  # Get shell inside
# Investigate
exit
```

---

## 🚀 What's Next

You now understand Docker! This is crucial for the workshop because:

- ✅ OAI 5G Core runs entirely in Docker
- ✅ You'll start/stop services with docker-compose
- ✅ You'll view logs to debug issues
- ✅ You'll understand the architecture better

**Next module:** C Compilation - learn how to build software from source code.

---

**Module Complete!** ✅

[⬅️ Previous: Module 3](module-3-git) | [Next: Module 5 →](module-5-c-compilation)

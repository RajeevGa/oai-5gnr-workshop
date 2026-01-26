---
layout: default
title: "Module 4: Docker Basics"
parent: Pre-Workshop Preparation
nav_order: 4
---

# 🐳 Module 4: Docker Basics

> **Duration:** 20 minutes | **Level:** Beginner | **Hands-on:** Yes

---

## 📚 What You'll Learn

By the end of this module, you'll be able to:

- [ ] Understand what Docker is and why it's useful
- [ ] Install Docker
- [ ] Run containers in background
- [ ] View container logs
- [ ] Use Docker Compose to start/stop multi-container applications

---

## 🎯 How to Use This Module

Follow along with the examples as you read. You'll install Docker and practice the essential commands. At the end, you'll complete exercises to test your understanding.

**Keep your terminal open throughout!**

---

## 📖 Chapter 1: What is Docker?

### The Problem

Ever had software work on one computer but not another? Different versions of libraries, different operating systems, missing dependencies...

### The Solution

**Docker** packages applications with everything they need:
- The application code
- All dependencies
- System libraries
- Configuration

Think of it like a **shipping container** for software - pack it once, run it anywhere.

### Docker vs Virtual Machines

You might have used virtual machines before. Docker is similar but much lighter:

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

**Key differences:**
- VMs include entire OS (gigabytes) - Docker shares host OS (megabytes)
- VMs take minutes to start - Docker starts in seconds
- You can run many more containers than VMs on the same computer

### Why You Need Docker

In the workshop, you'll run multiple network components (databases, servers, applications). Docker makes this easy:
- ✅ Start everything with one command
- ✅ Stop everything with one command
- ✅ Each component runs isolated (no conflicts)
- ✅ Lightweight and fast
- ✅ Easy cleanup

---

## 🔧 Chapter 2: Installing Docker

### Install Docker

```bash
sudo apt update
sudo apt install -y docker.io docker-compose
```

Verify installation:

```bash
docker --version
docker-compose --version
```

### Configure Permissions

Add yourself to the docker group so you don't need `sudo`:

```bash
sudo usermod -aG docker $USER
```

**Important:** Log out and log back in for this to take effect.

For now, test with sudo:

```bash
sudo docker run hello-world
```

You should see "Hello from Docker!" - installation successful!

---

## 📦 Chapter 3: Docker Basics

### Key Concepts

**Image** = Blueprint/Template
- Like a recipe or class definition
- Read-only

**Container** = Running instance  
- Like the actual dish or object
- Created from an image

### Essential Commands

**Download an image:**
```bash
docker pull nginx
```

**Run a container in background:**
```bash
docker run -d --name webserver nginx
```

The `-d` flag means "detached" (runs in background).

**List running containers:**
```bash
docker ps
```

**View container logs:**
```bash
docker logs webserver
```

Follow logs in real-time:
```bash
docker logs -f webserver
```

Press `Ctrl+C` to stop following.

**Stop a container:**
```bash
docker stop webserver
```

**Remove a container:**
```bash
docker rm webserver
```

That's it! These are the main commands you'll use.

---

## 🎼 Chapter 4: Docker Compose

### What is Docker Compose?

When you have multiple containers that need to work together, Docker Compose lets you:
- Define them all in one file (`docker-compose.yml`)
- Start them all with one command
- Stop them all with one command

### Example Compose File

Create a test directory:

```bash
mkdir ~/compose-test
cd ~/compose-test
```

Create `docker-compose.yml`:

```bash
nano docker-compose.yml
```

Add this:

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
```

Save and exit.

### Using Docker Compose

**Start all services:**
```bash
docker-compose up -d
```

The `-d` runs everything in background.

**Check status:**
```bash
docker-compose ps
```

**View logs:**
```bash
docker-compose logs
```

View logs of one service:
```bash
docker-compose logs web
```

Follow logs:
```bash
docker-compose logs -f web
```

**Stop all services:**
```bash
docker-compose down
```

This stops and removes all containers.

**Clean up:**
```bash
cd ~
rm -rf ~/compose-test
```

---

## 💻 Hands-On Exercises

Practice the essential Docker skills!

### Exercise 1: Run Your First Container

**Problem:** Start and manage a simple container.

**Requirements:**
- Pull the nginx image
- Run nginx in the background with the name "test-web"
- Verify it's running
- View its logs
- Stop and remove it

<details markdown="1">
<summary>💡 Hint</summary>

Use `docker pull`, `docker run -d --name`, `docker ps`, `docker logs`, `docker stop`, and `docker rm`.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
docker pull nginx
docker run -d --name test-web nginx
docker ps
docker logs test-web
docker stop test-web
docker rm test-web
```
</details>

---

### Exercise 2: Working with Logs

**Problem:** Practice viewing container logs in different ways.

**Requirements:**
- Run an nginx container named "webserver"
- View all logs
- View only the last 10 lines
- Follow the logs in real-time (then press Ctrl+C to stop)
- Clean up the container

<details markdown="1">
<summary>💡 Hint</summary>

Use `docker logs`, `docker logs --tail 10`, and `docker logs -f`.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
docker run -d --name webserver nginx
docker logs webserver
docker logs --tail 10 webserver
docker logs -f webserver
# Press Ctrl+C
docker stop webserver
docker rm webserver
```
</details>

---

### Exercise 3: Docker Compose Basics

**Problem:** Use Docker Compose to manage multiple containers.

**Requirements:**
- Create a directory called `my-test`
- Create a `docker-compose.yml` file with two services:
  - nginx on port 8080
  - mysql with root password "testpass"
- Start the services
- Check they're running
- View the logs
- Stop and remove everything

<details markdown="1">
<summary>💡 Hint</summary>

Use `mkdir`, `nano`, then `docker-compose up -d`, `docker-compose ps`, `docker-compose logs`, `docker-compose down`.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
mkdir ~/my-test
cd ~/my-test
nano docker-compose.yml
```

Content:
```yaml
version: '3.8'

services:
  web:
    image: nginx
    ports:
      - "8080:80"
  
  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: testpass
```

Then:
```bash
docker-compose up -d
docker-compose ps
docker-compose logs
docker-compose down
cd ~
rm -rf ~/my-test
```
</details>

---

### Exercise 4: Following Service Logs

**Problem:** Monitor a specific service's logs using Docker Compose.

**Requirements:**
- Create a simple docker-compose.yml with nginx and mysql
- Start the services
- Follow the nginx logs in real-time
- In another terminal, generate traffic: `curl http://localhost:8080`
- Observe the logs update
- Stop following (Ctrl+C) and clean up

<details markdown="1">
<summary>💡 Hint</summary>

Use `docker-compose logs -f service-name`. Open two terminals - one for logs, one for curl.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

Terminal 1:
```bash
mkdir ~/log-test
cd ~/log-test
# Create docker-compose.yml (same as Exercise 3)
docker-compose up -d
docker-compose logs -f web
```

Terminal 2:
```bash
curl http://localhost:8080
curl http://localhost:8080
```

You'll see logs appear in Terminal 1. Press Ctrl+C, then:
```bash
docker-compose down
cd ~
rm -rf ~/log-test
```
</details>

---

## ✅ Self-Check Quiz

<details markdown="1">
<summary>❓ What's the difference between a Docker image and a container?</summary>

**Answer:** Image is the template (blueprint), container is a running instance from that image.
</details>

<details markdown="1">
<summary>❓ What does `docker run -d` do?</summary>

**Answer:** Runs container in detached mode (background), so it doesn't block your terminal.
</details>

<details markdown="1">
<summary>❓ How do you view logs of a running container?</summary>

**Answer:** `docker logs container-name` or `docker logs -f container-name` for real-time.
</details>

<details markdown="1">
<summary>❓ What does `docker-compose up -d` do?</summary>

**Answer:** Starts all services defined in docker-compose.yml in the background.
</details>

<details markdown="1">
<summary>❓ What does `docker-compose down` do?</summary>

**Answer:** Stops and removes all containers defined in docker-compose.yml.
</details>

<details markdown="1">
<summary>❓ Why use Docker Compose instead of running containers individually?</summary>

**Answer:** It's easier to manage multiple related containers - start all, stop all, configure all in one file.
</details>

---

## 🎓 Summary

### Commands You'll Actually Use

| Command | Purpose |
|---------|---------|
| `docker ps` | List running containers |
| `docker logs -f name` | Follow container logs |
| `docker stop name` | Stop container |
| `docker-compose up -d` | Start all services |
| `docker-compose ps` | List services |
| `docker-compose logs -f` | Follow logs |
| `docker-compose down` | Stop all services |

### Key Takeaways

- ✅ Docker runs applications in isolated containers
- ✅ Docker Compose manages multiple containers together
- ✅ Use `-d` to run in background
- ✅ Use `docker logs -f` to watch what's happening
- ✅ `docker-compose up -d` starts everything
- ✅ `docker-compose down` stops everything

### What You Need for the Workshop

You now know enough Docker to:
1. Start the workshop applications
2. Check if they're running
3. View logs when troubleshooting
4. Stop everything when done

That's all you need!

---

## 📚 Additional Resources

**If you want to learn more:**
- [Docker Official Tutorial](https://docs.docker.com/get-started/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)

**But remember:** You don't need to be a Docker expert for the workshop. The basics you learned here are sufficient!

---

**Module Complete!** ✅

[⬅️ Previous: Module 3](module-3-git) | [Next: Module 5 →](module-5-next-topic)

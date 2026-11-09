# Day 29 – Docker Basics

> **90 Days of DevOps – Personal Learning Notes**
>
> Today I started learning Docker, a foundation of modern containerized application deployment.
>
> **Main goals:**
> - Understand containers and why we need them
> - Understand Containers vs Virtual Machines
> - Learn Docker architecture
> - Set up Docker Desktop with WSL 2
> - Run my first Docker container
> - Run Nginx and Ubuntu containers
> - Practise container management commands

---

# Task 1 – What is Docker?

## 1. What is a Container?

A **container** is a lightweight, isolated environment used to package and run an application along with everything it needs, such as:

- Application code
- Runtime
- Libraries
- Dependencies
- Configuration

Containers share the host machine's OS kernel, making them generally lighter and faster to start than virtual machines.

## 2. Why Do We Need Containers?

Containers help solve a common problem:

> "It works on my machine, but it doesn't work on another machine."

With containers, an application and its dependencies are packaged together. This helps provide a consistent environment across:

- Developer machines
- Testing environments
- CI/CD pipelines
- Cloud servers
- Kubernetes clusters

### Important Container Characteristics

- Lightweight
- Fast startup
- Portable
- Isolated
- Reproducible
- Easy to scale

---

# Containers vs Virtual Machines

| Feature | Containers | Virtual Machines |
|---|---|---|
| Virtualization | OS-level | Hardware-level |
| Kernel | Share host kernel | Each VM has its own OS/kernel |
| Size | Usually smaller | Usually larger |
| Startup | Usually fast | Usually slower |
| Resource usage | Lower overhead | Higher overhead |
| Isolation | Process-level isolation | Stronger full-OS isolation |
| Best suited for | Microservices, CI/CD | Full OS environments, legacy applications |

### Simple Example

A virtual machine is like renting an entire house.

A container is like renting an isolated apartment inside a building.

Each apartment is isolated, but the building's basic infrastructure is shared.

---

# Docker Architecture

Docker follows a client-server architecture.

```text
                 Docker Registry
                   (Docker Hub)
                       |
                       | Pull / Push
                       v
+------------------------------------------+
|               Docker Host                |
|                                          |
|  Docker Client  ----->  Docker Daemon    |
|   (docker CLI)            (dockerd)      |
|                              |           |
|                              v           |
|                        Docker Engine     |
|                              |           |
|                  +-----------+---------+ |
|                  |           |         | |
|                Image     Container  Network
|                              |           |
|                              v           |
|                         Application      |
+------------------------------------------+
```

## 1. Docker Client

The Docker client is the command-line tool used to communicate with Docker.

Examples:

```bash
docker ps
docker images
docker run nginx
```

## 2. Docker Daemon

The Docker daemon (`dockerd`) runs in the background and manages Docker objects such as:

- Images
- Containers
- Networks
- Volumes

## 3. Docker Image

A Docker image is a read-only template used to create containers.

Examples:

```text
nginx
ubuntu
redis
mysql
```

## 4. Docker Container

A container is an instance created from a Docker image.

```text
Docker Image
     |
     | docker run
     v
Docker Container
     |
     v
Running Application
```

One image can be used to create multiple containers.

## 5. Docker Registry

A registry stores and distributes Docker images.

The most popular public registry is Docker Hub.

Example:

```bash
docker pull nginx
```

This downloads an image from a registry.

---

# Task 2 – Install and Verify Docker

## My Environment

- Operating System: Windows 11
- Linux Environment: WSL 2
- Linux Distribution: Ubuntu 24.04
- Container Platform: Docker Desktop
- Docker CLI Version: 29.8.1
- Docker Desktop Version: 4.93.0

## 1. Initial Docker Integration Issue

Initially, when I executed:

```bash
docker --version
```

I received this error:

```text
The command 'docker' could not be found in this WSL 2 distro.

We recommend to activate the WSL integration
in Docker Desktop settings.
```

### Solution

I opened Docker Desktop and followed these steps:

1. Opened Docker Desktop Settings.
2. Navigated to Resources.
3. Opened WSL Integration.
4. Enabled integration for Ubuntu-24.04.
5. Applied the changes and restarted Docker Desktop.

After enabling WSL integration, Docker commands worked in my Ubuntu terminal.

## 2. Verify Docker Installation

Check Docker CLI version:

```bash
docker --version
```

My output:

```text
Docker version 29.8.1, build 4a63305
```

Check Docker client and server:

```bash
docker version
```

Check Docker Engine information:

```bash
docker info
```

These commands help verify the Docker installation and connection to the Docker Engine.

## 3. Run the Hello World Container

Command:

```bash
docker run hello-world
```

### What happened?

Initially, Docker displayed:

```text
Unable to find image 'hello-world:latest' locally
```

This means the image was not available locally.

Docker then downloaded the image from Docker Hub.

Important output:

```text
Pulling from library/hello-world
Pull complete
Download complete
```

Finally:

```text
Hello from Docker!

This message shows that your installation
appears to be working correctly.
```

### What happens when this command runs?

1. Docker checks whether the image exists locally.
2. If it does not exist, Docker pulls it from Docker Hub.
3. Docker creates a container from the image.
4. The container starts.
5. The application prints its message.
6. The container exits after completing its task.

### Docker workflow

```text
docker run hello-world
        |
        v
Docker Client
        |
        v
Docker Daemon
        |
        v
Check Local Image
        |
        v
Image Missing?
        |
        v
Pull from Docker Hub
        |
        v
Create Container
        |
        v
Run Application
        |
        v
Container Exits
```

---

# Task 3 – Run Real Containers

## 1. Run an Nginx Container

Nginx is a web server commonly used to serve websites and work as a reverse proxy.

Command:

```bash
docker run -d --name day29-nginx -p 8080:80 nginx
```

### Explanation

**`-d`**

Runs the container in detached mode, meaning it runs in the background.

**`--name day29-nginx`**

Assigns a custom name to the container.

**`-p 8080:80`**

Maps the host port to the container port.

**`nginx`**

Specifies the Docker image.

### Port Mapping

```text
Host Port 8080
      |
      v
Container Port 80
      |
      v
Nginx Web Server
```

The format is:

```bash
-p HOST_PORT:CONTAINER_PORT
```

### Access Nginx in Browser

Open:

```text
http://localhost:8080
```

My browser displayed:

```text
Welcome to nginx!

If you see this page, nginx is successfully
installed and working.
```

This confirmed that my Nginx container was running and accessible through the mapped port.

## 2. Verify Running Containers

Command:

```bash
docker ps
```

My container details:

| Field | Value |
|---|---|
| Container name | day29-nginx |
| Image | nginx |
| Status | Up |
| Host port | 8080 |
| Container port | 80 |

## 3. Run an Ubuntu Container

Command:

```bash
docker run -it --name day29-ubuntu ubuntu bash
```

### Explanation

| Option | Meaning |
|---|---|
| `-i` | Keeps standard input open |
| `-t` | Allocates a terminal |
| `--name` | Assigns a custom name |
| `ubuntu` | Ubuntu image |
| `bash` | Starts Bash shell |

Together, `-it` allows interactive use of the container.

Docker downloaded the Ubuntu image because it was not available locally.

### Explore Ubuntu Container

Inside the container, I executed:

```bash
cat /etc/os-release
```

This displays operating system information.

My container reported:

```text
PRETTY_NAME="Ubuntu 26.04.1 LTS"
VERSION_ID="26.04"
```

Notice that this container's Ubuntu version is different from my WSL Ubuntu 24.04 installation.

Check hostname:

```bash
hostname
```

Check current directory:

```bash
pwd
```

Output:

```text
/
```

List files:

```bash
ls -la
```

This displayed directories such as:

```text
bin
boot
dev
etc
home
lib
media
```

These are part of the container's Linux filesystem.

Exit the container:

```bash
exit
```

The container stopped after its main Bash process exited.

## 4. List Running Containers

Command:

```bash
docker ps
```

This displays only currently running containers.

During my practice, I saw:

- `day29-nginx`
- `lenovo-n8n-1`

The Nginx container was running alongside my existing n8n container.

## 5. List All Containers

Command:

```bash
docker ps -a
```

The `-a` option displays both running and stopped containers.

My practice showed containers including:

| Container | Image | Status |
|---|---|---|
| day29-ubuntu | ubuntu | Exited (0) |
| day29-nginx | nginx | Up |
| objective_leakey | hello-world | Exited (0) |
| flamboyant_payne | n8n-ffmpeg | Exited (0) |
| lenovo-n8n-1 | n8n-ffmpeg | Up |

Docker automatically generated the name `objective_leakey` for my hello-world container because I did not specify a custom name.

### What does Exited (0) mean?

It means the container's main process completed successfully and returned exit code 0.

---

# Task 4 – Explore Docker

## 1. Detached Mode

Command:

```bash
docker run -d --name day29-nginx -p 8080:80 nginx
```

The `-d` flag starts the container in the background.

Without detached mode, Docker attaches the terminal to the container's main process.

With detached mode:

```text
Terminal
   |
   +---- Docker Container
              |
              +---- Nginx running in background
```

Check running containers:

```bash
docker ps
```

## 2. Give a Container a Custom Name

Example:

```bash
docker run -d --name web-server nginx
```

Instead of Docker generating a random name, the container will be called:

```text
web-server
```

My actual container name:

```text
day29-nginx
```

This makes container management easier.

For example:

```bash
docker stop day29-nginx
docker start day29-nginx
docker logs day29-nginx
```

## 3. Map a Port

My command:

```bash
docker run -d --name day29-nginx -p 8080:80 nginx
```

Port mapping follows:

```text
-p HOST_PORT:CONTAINER_PORT
```

Therefore:

```text
-p 8080:80
```

means:

```text
localhost:8080
       |
       v
Container Port 80
       |
       v
Nginx
```

## 4. Check Container Logs

Command:

```bash
docker logs day29-nginx
```

To display only the last 20 lines:

```bash
docker logs --tail 20 day29-nginx
```

### Important Nginx Log Output

My output included:

```text
Configuration complete; ready for start up
nginx/1.31.8
start worker processes
```

This confirmed that Nginx initialized successfully.

### Browser Request

My logs also showed:

```text
GET / HTTP/1.1" 200 896
```

Meaning:

- `GET` – Browser requested a resource.
- `/` – Requested the root webpage.
- `200` – HTTP success status.
- `896` – Response body size in bytes.

### Favicon 404

I also noticed:

```text
GET /favicon.ico HTTP/1.1" 404
```

This happened because the browser requested a favicon file that was not present at that location.

The Nginx welcome page was still working successfully.

## 5. Run a Command Inside a Running Container

Execute a command inside the Nginx container:

```bash
docker exec day29-nginx nginx -v
```

My output:

```text
nginx version: nginx/1.31.8
```

Open an interactive shell:

```bash
docker exec -it day29-nginx sh
```

Inside the container, I executed:

```bash
hostname
pwd
ls
```

My hostname output:

```text
53db471adbe9
```

My current directory:

```text
/
```

Exit:

```bash
exit
```

### Important Difference

| Command | Purpose |
|---|---|
| `docker run` | Creates and starts a new container |
| `docker exec` | Runs a command in an existing running container |
| `docker logs` | Displays container logs |

---

# Important Docker Commands Learned

## Docker Information

```bash
docker --version
docker version
docker info
```

## Images

```bash
docker images
docker pull nginx
docker rmi nginx
```

## Containers

```bash
docker ps
docker ps -a
docker run nginx
docker start <container>
docker stop <container>
docker restart <container>
docker rm <container>
```

## Interactive Containers

```bash
docker run -it ubuntu bash
docker exec -it <container> bash
```

For images without Bash:

```bash
docker exec -it <container> sh
```

## Detached Containers

```bash
docker run -d nginx
```

## Port Mapping

```bash
docker run -d -p 8080:80 nginx
```

## Naming Containers

```bash
docker run -d --name my-container nginx
```

## Logs

```bash
docker logs <container>
docker logs --tail 20 <container>
docker logs -f <container>
```

## Container Inspection

```bash
docker inspect <container>
```

---

# Docker Command Cheat Sheet

| Command | Purpose |
|---|---|
| `docker --version` | Check Docker version |
| `docker info` | Show Docker Engine information |
| `docker images` | List local images |
| `docker pull <image>` | Download an image |
| `docker run <image>` | Create and run a container |
| `docker run -d <image>` | Run container in background |
| `docker run -it <image>` | Run interactively |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker stop <container>` | Stop a container |
| `docker start <container>` | Start a stopped container |
| `docker restart <container>` | Restart a container |
| `docker rm <container>` | Remove a container |
| `docker logs <container>` | View container logs |
| `docker exec -it <container> bash` | Open shell inside a container |
| `docker inspect <container>` | Inspect container details |
| `docker rmi <image>` | Remove an image |

---

# What I Learned Today

Today I learned that Docker provides a lightweight way to package and run applications in isolated environments.

The most important concepts I learned were:

```text
Docker Image
     |
     v
Docker Container
     |
     v
Running Application
```

I also learned how Docker uses a client, daemon, images, containers, and registries to manage containerized applications.

### Commands I Practised

```bash
docker --version
docker version
docker info
docker run hello-world
docker run -d
docker run -it
docker ps
docker ps -a
docker logs
docker exec
docker stop
```

---

# Why Docker Matters for DevOps

Docker is one of the core technologies used in modern DevOps.

It is commonly used with:

- CI/CD pipelines
- Microservices
- Kubernetes
- Cloud platforms
- Application deployment
- Automated testing
- Infrastructure automation

Learning Docker is an important step toward working with technologies such as:

```text
Docker
   ↓
Docker Compose
   ↓
CI/CD
   ↓
Kubernetes
   ↓
Cloud
   ↓
Production DevOps
```

---

# Screenshots

Add the following screenshots to the Day 29 submission:

- [ ] Docker version output
- [ ] Docker Desktop WSL integration
- [ ] `docker run hello-world`
- [ ] Nginx container running
- [ ] Nginx welcome page in browser
- [ ] Ubuntu container in interactive mode
- [ ] `docker ps`
- [ ] `docker ps -a`
- [ ] Nginx container logs
- [ ] `docker exec` session
- [ ] Container stop and removal verification

---

# Submission

File location:

```text
2026/day-29/day-29-docker-basics.md
```

Commit and push:

```bash
git add 2026/day-29/day-29-docker-basics.md
git commit -m "Add Day 29 Docker basics"
git push
```

---

# Day 29 Learning Summary

> Today I started learning Docker and successfully ran my first container using the `hello-world` image.
>
> I also explored Nginx and Ubuntu containers, accessed the Nginx welcome page through port mapping, inspected container logs, and executed commands inside a running container.
>
> My main takeaway is that Docker makes it easier to package and run applications in consistent, isolated environments.
>
> **Next step:** Learn more about Docker images, Dockerfiles, volumes, networking, and Docker Compose.

#90DaysOfDevOps #DevOpsKaJosh #TrainWithShubham
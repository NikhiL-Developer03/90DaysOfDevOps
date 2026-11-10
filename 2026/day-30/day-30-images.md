# Day 30 – Docker Images & Container Lifecycle

> **90 Days of DevOps – Personal Learning Notes**
>
> **Today's Goal:** Understand how Docker images and containers work, explore image layers, and practise the complete container lifecycle.
>
> **Topics Covered:**
> - Docker images and image management
> - Ubuntu vs Alpine image comparison
> - Docker image layers and caching
> - Container lifecycle
> - Working with running containers
> - Container inspection
> - Docker cleanup and disk usage

---

# Task 1 – Docker Images

## 1. What is a Docker Image?

A Docker image is a read-only template used to create Docker containers.

It contains the application code, dependencies, libraries, configuration, and instructions required to run an application.

For example:

- Nginx image – Used to create Nginx web server containers.
- Ubuntu image – Used to create Ubuntu Linux containers.
- Alpine image – Used to create lightweight Linux containers.

### Image vs Container

| Docker Image | Docker Container |
|---|---|
| Read-only template | Instance created from an image |
| Used to create containers | Runs an application |
| Can be shared and reused | Has its own runtime state |
| Example: `nginx` | Example: `day30-nginx` |

```text
        Docker Image
             |
             | docker run
             |
       +-----+-----+
       |           |
       v           v
   Container 1  Container 2
       |           |
       v           v
   Application  Application
```

One Docker image can create multiple containers.

## 2. Pull Docker Images

The `docker pull` command downloads images from a Docker registry.

### Pull Nginx

```bash
docker pull nginx
```

### Pull Ubuntu

```bash
docker pull ubuntu
```

### Pull Alpine

```bash
docker pull alpine
```

**Command explanation:**

```text
docker  → Docker command-line tool
pull    → Downloads an image
nginx   → Name of the image
```

## 3. List Docker Images

Command:

```bash
docker images
```

This displays the images available on the local machine.

Important columns:

| Column | Meaning |
|---|---|
| REPOSITORY | Image name |
| TAG | Image version or label |
| IMAGE ID | Unique image identifier |
| CREATED | Image creation time |
| SIZE | Reported image size |

Alternative command:

```bash
docker image ls
```

### My Image Size Comparison

*Fill in these values after running `docker images`.*

| Image | Size on my machine |
|---|---|
| nginx | To be recorded |
| ubuntu | To be recorded |
| alpine | To be recorded |

## 4. Ubuntu vs Alpine

### Why is Alpine smaller than Ubuntu?

**Alpine Linux**

- Minimal Linux distribution.
- Contains fewer default packages.
- Designed to be lightweight.
- Useful for applications that need a small base image.

**Ubuntu**

- General-purpose Linux distribution.
- Includes a larger collection of packages and utilities.
- Provides a familiar environment for many Linux applications.
- Usually has a larger image size than Alpine.

```text
Alpine
   ↓
Minimal packages
   ↓
Smaller image

Ubuntu
   ↓
More default packages and utilities
   ↓
Larger image
```

The smallest image is not automatically the best choice. Application compatibility, security updates, dependencies, and debugging requirements also matter.

## 5. Inspect a Docker Image

Command:

```bash
docker image inspect nginx
```

This displays detailed information about an image.

Information available includes:

- Image ID
- Operating system
- CPU architecture
- Image configuration
- Environment variables
- Exposed ports
- Entrypoint
- Default command
- Image layers and metadata

For a shorter output, use:

```bash
docker image inspect nginx --format '{{.Os}}/{{.Architecture}}'
```

This displays the operating system and architecture.

## 6. Remove a Docker Image

Command:

```bash
docker rmi <image-name>
```

Example:

```bash
docker rmi alpine
```

This removes the specified image if Docker allows it.

**Important:** Docker may refuse to remove an image if a container is using it. Check the dependent containers before removing an image.

---

# Task 2 – Docker Image Layers

## 1. What Are Docker Image Layers?

Docker images are built from multiple filesystem layers.

Each filesystem-changing instruction in a Dockerfile can create a layer.

Example Dockerfile:

```dockerfile
FROM ubuntu
RUN apt-get update
COPY app.py /app/
```

Conceptual structure:

```text
        Docker Image
             |
      +--------------+
      |  COPY layer  |
      +--------------+
             |
      +--------------+
      |   RUN layer  |
      +--------------+
             |
      +--------------+
      | Ubuntu layer |
      +--------------+
```

Docker combines these layers to create the final image.

## 2. View Image History

Command:

```bash
docker image history nginx
```

This displays the history of the image's build instructions and associated layer information.

Important columns:

| Column | Meaning |
|---|---|
| IMAGE | Image or layer identifier |
| CREATED | When the layer was created |
| CREATED BY | Instruction that created the layer |
| SIZE | Size associated with the layer |
| COMMENT | Additional information |

## 3. Why Do Some Layers Show 0B?

Some Dockerfile instructions mainly change image configuration or metadata rather than adding filesystem content.

Examples:

```dockerfile
CMD ["nginx", "-g", "daemon off;"]
EXPOSE 80
ENV APP_ENV=production
ENTRYPOINT ["nginx"]
```

These may appear as `0B` in image history.

A `0B` entry does not necessarily mean the instruction has no effect. It means it did not add measurable filesystem content in that history entry.

## 4. Why Does Docker Use Layers?

### 1. Reusability

Different images can share common base layers.

### 2. Caching

Docker can reuse unchanged build layers.

### 3. Faster Builds

When only a later Dockerfile instruction changes, earlier cached layers may be reused.

### 4. Storage Efficiency

Shared image layers do not need to be downloaded and stored separately for every image.

### Example

```text
Image A                 Image B
   |                       |
Application A           Application B
   |                       |
Shared Ubuntu Layer     Shared Ubuntu Layer
```

Both images can reuse the same base layer.

---

# Task 3 – Docker Container Lifecycle

A Docker container passes through different states during its lifetime.

## 1. Container Lifecycle Overview

```text
        Create
           |
           v
        Created
           |
           | Start
           v
        Running
           |
      +----+-----+
      |          |
    Pause       Stop
      |          |
      v          v
   Paused      Exited
      |
      | Unpause
      v
   Running
      |
   Restart
      |
   Running
      |
    Kill
      |
    Exited
      |
    Remove
      |
   Deleted
```

## 2. Create a Container Without Starting It

Command:

```bash
docker create --name day30-nginx nginx
```

The `docker create` command creates a container but does not start it.

Check:

```bash
docker ps -a
```

The container should show a `Created` state.

## 3. Start the Container

Command:

```bash
docker start day30-nginx
```

This starts an existing stopped or created container.

Verify:

```bash
docker ps
```

## 4. Pause a Container

Command:

```bash
docker pause day30-nginx
```

This temporarily suspends the processes inside the container.

Verify:

```bash
docker ps
```

The status should show:

```text
Up ... (Paused)
```

## 5. Unpause a Container

Command:

```bash
docker unpause day30-nginx
```

This resumes the suspended processes.

Verify:

```bash
docker ps
```

## 6. Stop a Container

Command:

```bash
docker stop day30-nginx
```

Docker sends a termination signal and allows the container to stop gracefully before forcing termination if necessary.

Verify:

```bash
docker ps -a
```

The container should show an `Exited` state.

## 7. Restart a Container

Command:

```bash
docker restart day30-nginx
```

This stops and starts the container again.

Verify:

```bash
docker ps
```

## 8. Kill a Container

Command:

```bash
docker kill day30-nginx
```

This sends a kill signal to the container's main process, normally terminating it immediately.

Verify:

```bash
docker ps -a
```

The container should show an exited state.

## 9. Remove a Container

Command:

```bash
docker rm day30-nginx
```

This removes the stopped container.

Verify:

```bash
docker ps -a
```

The container should no longer appear.

### Important: Stop vs Kill

| `docker stop` | `docker kill` |
|---|---|
| Attempts graceful shutdown | Sends a kill signal by default |
| Allows time for cleanup | Terminates immediately |
| Preferred for normal shutdown | Used when immediate termination is needed |

**Note:** Removing a container does not automatically remove its Docker image.

---

# Task 4 – Working with Running Containers

## 1. Run Nginx in Detached Mode

Command:

```bash
docker run -d --name day30-nginx -p 8081:80 nginx
```

Explanation:

| Option | Purpose |
|---|---|
| `-d` | Runs in the background |
| `--name` | Assigns a custom name |
| `-p 8081:80` | Maps host port 8081 to container port 80 |
| `nginx` | Image used to create the container |

Access it from the browser:

```text
http://localhost:8081
```

## 2. View Container Logs

Command:

```bash
docker logs day30-nginx
```

This displays logs generated by the container's main process.

Display only the last 20 lines:

```bash
docker logs --tail 20 day30-nginx
```

## 3. Follow Logs in Real Time

Command:

```bash
docker logs -f day30-nginx
```

The `-f` option means follow.

It keeps displaying new log entries as they are generated.

Press:

```text
Ctrl + C
```

to exit log-following mode. This does not normally stop the container.

## 4. Enter a Running Container

Command:

```bash
docker exec -it day30-nginx sh
```

This opens an interactive shell inside the running Nginx container.

Explore the filesystem:

```bash
pwd
ls -la
hostname
```

Exit:

```bash
exit
```

## 5. Execute a Single Command Inside a Container

Command:

```bash
docker exec day30-nginx nginx -v
```

This checks the Nginx version without opening an interactive shell.

Another example:

```bash
docker exec day30-nginx df -h
```

This displays filesystem usage from inside the container.

## 6. Inspect a Container

Command:

```bash
docker inspect day30-nginx
```

This returns detailed information about the container.

Important information includes:

- Container ID
- Container name
- Image ID
- Current status
- IP address
- Network configuration
- Port bindings
- Mounts
- Environment variables
- Restart policy
- Entrypoint and command

### Find Container IP Address

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' day30-nginx
```

### Find Port Mappings

```bash
docker port day30-nginx
```

### Find Mount Information

```bash
docker inspect -f '{{json .Mounts}}' day30-nginx
```

### Important Difference: Exposed vs Published Port

An image may expose a port internally, but that does not automatically make it accessible through a host port.

Example:

```text
80/tcp
```

means the container exposes port 80.

A mapping such as:

```text
8081:80
```

means host port 8081 forwards to container port 80.

---

# Task 5 – Docker Cleanup

Docker can use disk space for:

- Images
- Containers
- Volumes
- Build cache

Before cleaning, always inspect what is using storage.

## 1. Check Running Containers

```bash
docker ps
```

## 2. Stop All Running Containers

Challenge command:

```bash
docker stop $(docker ps -q)
```

Explanation:

- `docker ps -q` lists IDs of running containers.
- `$(...)` passes those IDs to `docker stop`.

**Safety warning:** This stops every running container, including existing applications. Inspect the list first and stop only containers you own or are authorised to stop.

## 3. Remove Stopped Containers

Command:

```bash
docker container prune
```

Docker asks for confirmation before removing stopped containers.

**Important:** This removes all stopped containers, not just the containers created for this challenge.

## 4. Remove Unused Images

Command:

```bash
docker image prune
```

This removes dangling images.

To remove all images not used by existing containers:

```bash
docker image prune -a
```

Use `-a` carefully because images you may want to use again can be deleted.

## 5. Check Docker Disk Usage

Command:

```bash
docker system df
```

This displays disk usage for:

- Images
- Containers
- Local volumes
- Build cache

For more detailed information:

```bash
docker system df -v
```

## 6. Docker System Prune

Command:

```bash
docker system prune
```

This removes unused containers, networks, dangling images, and build cache.

**Warning:** Review the confirmation prompt carefully. Do not use aggressive cleanup options such as `--volumes` unless you have checked that the volumes are not needed.

---

# Important Docker Commands Cheat Sheet

## Image Management

```bash
docker pull <image>
docker images
docker image ls
docker image inspect <image>
docker image history <image>
docker rmi <image>
docker image prune
```

## Container Lifecycle

```bash
docker create <image>
docker start <container>
docker pause <container>
docker unpause <container>
docker stop <container>
docker restart <container>
docker kill <container>
docker rm <container>
```

## Running Containers

```bash
docker run -d <image>
docker ps
docker ps -a
docker logs <container>
docker logs -f <container>
docker exec -it <container> sh
docker exec <container> <command>
docker inspect <container>
docker port <container>
```

## Cleanup and Storage

```bash
docker stop $(docker ps -q)
docker container prune
docker image prune
docker system df
docker system df -v
docker system prune
```

---

# Key Learnings

## 1. Image vs Container

An image is a read-only template, while a container is an instance created from that image.

## 2. Image Layers

Docker images consist of multiple layers that support reuse, caching, and storage efficiency.

## 3. Container Lifecycle

Understanding container states helps manage and troubleshoot containerized applications.

## 4. Docker Logs

Logs help identify application startup issues, errors, and requests.

## 5. Docker Exec

`docker exec` allows commands to run inside an existing running container.

## 6. Docker Inspect

`docker inspect` provides detailed information about container configuration and runtime state.

## 7. Docker Storage

Images, containers, volumes, and build cache all contribute to Docker disk usage.

---

# Practical Results

*Update this section as each task is completed.*

| Task | Status | Actual observation |
|---|---|---|
| Pull Nginx, Ubuntu, Alpine | Pending | To be recorded |
| Compare image sizes | Pending | To be recorded |
| Inspect image | Pending | To be recorded |
| View image history | Pending | To be recorded |
| Create and start container | Pending | To be recorded |
| Pause and unpause | Pending | To be recorded |
| Stop and restart | Pending | To be recorded |
| Kill and remove | Pending | To be recorded |
| View and follow logs | Pending | To be recorded |
| Execute commands | Pending | To be recorded |
| Inspect container | Pending | To be recorded |
| Docker cleanup | Pending | To be recorded |

---

# Screenshots Checklist

- [ ] Pull Nginx image
- [ ] Pull Ubuntu image
- [ ] Pull Alpine image
- [ ] List Docker images and sizes
- [ ] Compare Ubuntu and Alpine
- [ ] Inspect Docker image
- [ ] View Docker image history
- [ ] Create container
- [ ] Start container
- [ ] Pause container
- [ ] Unpause container
- [ ] Stop container
- [ ] Restart container
- [ ] Kill container
- [ ] Remove container
- [ ] View container logs
- [ ] Follow logs in real time
- [ ] Execute commands inside container
- [ ] Inspect container IP, ports, and mounts
- [ ] Check Docker disk usage

---

# Day 30 – Final Learning Summary

Today I focused on understanding Docker images and container lifecycle management.

The main concepts I am learning are:

```text
Docker Images
      ↓
Image Layers
      ↓
Docker Containers
      ↓
Container Lifecycle
      ↓
Logs and Inspection
      ↓
Cleanup and Storage
```

The goal is not just to remember Docker commands, but to understand how images are built, how containers behave, how to inspect them, and how to manage resources safely.

**Next step:** Continue building practical Docker skills and prepare for Dockerfiles, volumes, networking, and Docker Compose.

#90DaysOfDevOps #DevOpsKaJosh #TrainWithShubham
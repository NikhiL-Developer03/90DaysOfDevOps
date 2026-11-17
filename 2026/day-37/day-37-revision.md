# Day 37 – Docker Revision

## Objective

Revise Docker concepts and commands from Days 29–36 to strengthen practical understanding and identify topics that need more practice.

---

## 1. Self-Assessment

| Skill | Status |
|---|---|
| Run interactive and detached containers | Can do |
| List, stop and remove containers | Can do |
| Explain image layers and caching | Can do |
| Write a Dockerfile | Can do |
| Explain CMD vs ENTRYPOINT | Can do |
| Build and tag images | Can do |
| Use named volumes | Can do |
| Use bind mounts | Can do |
| Create custom networks | Can do |
| Write Compose files | Can do |
| Use `.env` with Compose | Can do |
| Write multi-stage Dockerfiles | Can do |
| Push images to Docker Hub | Can do |
| Use healthchecks and `depends_on` | Can do |

---

## 2. Quick-Fire Questions and Answers

### 1. What is the difference between an image and a container?

**Docker Image:** An image is a packaged, immutable template used to create containers.

**Docker Container:** A container is an instance created from an image with its own runtime state.

Example:

```bash
docker pull nginx
docker run -d --name my-nginx nginx
```

Here, `nginx` is the image and `my-nginx` is the container.

### 2. What happens to data inside a container when you remove it?

Data stored only in the container's writable layer is lost when the container is removed.

However, data stored in Docker volumes or bind mounts persists independently.

Example:

```bash
docker volume create mydata
docker run -d --name my-container -v mydata:/data nginx
```

The volume can preserve data even after the container is removed.

### 3. How do two containers on the same custom network communicate?

Containers connected to the same user-defined network can communicate using their container or service names.

Docker provides internal DNS resolution for these names.

Example:

```bash
docker network create mynetwork

docker run -d --name web --network mynetwork nginx

docker run -it --name test --network mynetwork alpine sh
```

Inside the Alpine container:

```bash
ping web
```

### 4. What does `docker compose down -v` do differently from `docker compose down`?

**Without `-v`:**

```bash
docker compose down
```

Removes containers and networks created by the Compose project.

**With `-v`:**

```bash
docker compose down -v
```

Also removes the named and anonymous volumes declared by the Compose project.

**Important:** Use `-v` carefully because it can delete persistent application data.

### 5. Why are multi-stage builds useful?

Multi-stage builds separate the application build environment from the final runtime environment.

Benefits:

- Smaller Docker images.
- Build tools are not included in the final image.
- Fewer unnecessary components.
- Reduced attack surface.

Example:

```dockerfile
FROM golang:1.25-alpine AS builder

WORKDIR /app

COPY main.go .

RUN CGO_ENABLED=0 go build -o myapp main.go

FROM scratch

COPY --from=builder /app/myapp /myapp

ENTRYPOINT ["/myapp"]
```

### 6. What is the difference between COPY and ADD?

| COPY | ADD |
|---|---|
| Copies files and directories into the image | Copies files and supports additional features |
| Preferred for normal file copying | Can automatically extract local archives |
| Simple and predictable | Has additional behavior, including URL support |

**Best practice:** Prefer `COPY` for normal file operations.

### 7. What does `-p 8080:80` mean?

It maps the host port `8080` to the container port `80`.

Example:

```bash
docker run -d -p 8080:80 nginx
```

You can access Nginx using:

```text
http://localhost:8080
```

### 8. How do you check how much disk space Docker is using?

Use:

```bash
docker system df
```

It displays Docker disk usage, including:

- Images
- Containers
- Local volumes
- Build cache

---

## 3. Important Commands for Revision

### Container Commands

```bash
docker run -it ubuntu bash
docker run -d nginx
docker ps
docker ps -a
docker stop CONTAINER
docker start CONTAINER
docker rm CONTAINER
docker exec -it CONTAINER sh
docker logs CONTAINER
```

### Image Commands

```bash
docker images
docker pull nginx
docker build -t my-app:v1 .
docker tag my-app:v1 USER/REPO:v1
docker push USER/REPO:v1
docker rmi IMAGE
```

### Volume Commands

```bash
docker volume create mydata
docker volume ls
docker volume inspect mydata
```

### Network Commands

```bash
docker network create mynetwork
docker network ls
docker network inspect mynetwork
docker network connect mynetwork CONTAINER
```

### Docker Compose Commands

```bash
docker compose up -d
docker compose ps
docker compose logs -f
docker compose build
docker compose down
```

---

## 4. Weak Areas Revisited

Two areas to revisit regularly:

### 1. Docker Networking

Focus on:

- Default bridge network.
- Custom bridge networks.
- Container-to-container communication.
- DNS resolution using container names.
- Port mapping.

### 2. Multi-Stage Builds

Focus on:

- Builder and runtime stages.
- `FROM ... AS builder`.
- `COPY --from=builder`.
- Minimal runtime images.
- Comparing single-stage and multi-stage image sizes.

**Hands-on practice is more valuable than memorizing commands.**

---

## 5. Key Learnings

- Docker images are templates; containers are their running instances.
- Volumes and bind mounts help preserve data.
- Custom networks allow containers to communicate.
- Docker Compose simplifies multi-container application management.
- Multi-stage builds reduce unnecessary image size.
- Docker Hub helps distribute and share Docker images.
- Docker healthchecks help monitor service health.

---

## Day 37 Completion

- [x] Self-assessment checklist
- [x] Quick-fire questions and answers
- [x] Docker command revision
- [x] Identified two areas for regular practice

**Main focus:** Understand Docker concepts and practise them through real commands rather than simply memorizing syntax.

#90DaysOfDevOps #DevOpsKaJosh #TrainWithShubham
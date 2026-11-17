# Docker Cheat Sheet

A quick reference guide for commonly used Docker commands and Dockerfile instructions.

## 1. Containers

| Command | Purpose |
|---|---|
| `docker run IMAGE` | Create and start a container |
| `docker run -it IMAGE sh` | Run a container interactively |
| `docker run -d IMAGE` | Run a container in detached mode |
| `docker run -d -p 8080:80 IMAGE` | Run container with port mapping |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers |
| `docker stop CONTAINER` | Stop a container |
| `docker start CONTAINER` | Start a stopped container |
| `docker restart CONTAINER` | Restart a container |
| `docker rm CONTAINER` | Remove a container |
| `docker exec -it CONTAINER sh` | Open a shell inside a running container |
| `docker logs CONTAINER` | View container logs |
| `docker logs -f CONTAINER` | Follow container logs |
| `docker inspect CONTAINER` | Inspect container configuration |

## 2. Images

| Command | Purpose |
|---|---|
| `docker images` | List downloaded images |
| `docker pull IMAGE` | Download an image |
| `docker build -t NAME:TAG .` | Build an image |
| `docker tag IMAGE USER/REPO:TAG` | Tag an image |
| `docker push USER/REPO:TAG` | Push an image to Docker Hub |
| `docker rmi IMAGE` | Remove an image |
| `docker image inspect IMAGE` | Inspect image details |
| `docker image history IMAGE` | View image layers and build history |

## 3. Volumes

| Command | Purpose |
|---|---|
| `docker volume create NAME` | Create a named volume |
| `docker volume ls` | List volumes |
| `docker volume inspect NAME` | Inspect a volume |
| `docker volume rm NAME` | Remove a volume |
| `docker run -v mydata:/data IMAGE` | Attach a named volume |
| `docker run -v /host/path:/container/path IMAGE` | Create a bind mount |

**Important:** Volumes help preserve data even when containers are removed.

## 4. Networks

| Command | Purpose |
|---|---|
| `docker network create NAME` | Create a custom network |
| `docker network ls` | List networks |
| `docker network inspect NAME` | Inspect network details |
| `docker network connect NET CONTAINER` | Connect a container to a network |
| `docker network disconnect NET CONTAINER` | Disconnect a container from a network |
| `docker network rm NAME` | Remove a network |

**Important:** Containers on the same user-defined network can communicate using container names.

## 5. Docker Compose

| Command | Purpose |
|---|---|
| `docker compose up` | Start services |
| `docker compose up -d` | Start services in detached mode |
| `docker compose down` | Stop and remove containers and networks created by Compose |
| `docker compose ps` | Show service status |
| `docker compose logs` | View service logs |
| `docker compose logs -f` | Follow service logs |
| `docker compose build` | Build services |
| `docker compose up -d --build` | Build and start services |
| `docker compose restart` | Restart services |
| `docker compose config` | Validate and display Compose configuration |
| `docker compose down -v` | Remove Compose-created named and anonymous volumes too |

## 6. Environment Variables

| Command / Syntax | Purpose |
|---|---|
| `docker run -e NAME=value IMAGE` | Pass an environment variable |
| `docker run --env-file .env IMAGE` | Load variables from a file |
| `env_file: .env` | Load environment variables in Compose |
| `${DB_PASSWORD}` | Reference a variable in Compose |
| `docker compose config` | Check the resolved Compose configuration |

Example `.env` file:

```env
DB_NAME=mydb
DB_USER=myuser
DB_PASSWORD=mypassword
```

**Important:** Never upload real passwords, tokens, or secrets to GitHub.

## 7. Docker Cleanup

| Command | Purpose |
|---|---|
| `docker system df` | Check Docker disk usage |
| `docker image prune` | Remove dangling images |
| `docker container prune` | Remove stopped containers |
| `docker volume prune` | Remove unused volumes |
| `docker network prune` | Remove unused networks |
| `docker system prune` | Remove unused Docker data |
| `docker system prune -a` | Remove unused images and other unused resources |

**Warning:** Cleanup commands can permanently remove data and resources. Inspect what will be affected before running them. Avoid `--volumes` unless you intentionally want to remove unused volumes.

## 8. Dockerfile Instructions

| Instruction | Purpose |
|---|---|
| `FROM` | Select the base image |
| `RUN` | Execute commands during image building |
| `COPY` | Copy files into the image |
| `ADD` | Copy files and support additional sources such as local archives |
| `WORKDIR` | Set the working directory |
| `EXPOSE` | Document the intended container port |
| `CMD` | Set the default command |
| `ENTRYPOINT` | Set the main executable |
| `ENV` | Set environment variables |
| `ARG` | Define build-time variables |
| `USER` | Set the user for subsequent instructions and runtime |
| `VOLUME` | Declare a mount point |
| `LABEL` | Add image metadata |

### Basic Dockerfile Example

```dockerfile
FROM ubuntu:24.04

WORKDIR /app

COPY . .

RUN apt-get update && apt-get install -y curl

EXPOSE 8080

CMD ["bash"]
```

## 9. Multi-Stage Builds

Example:

```dockerfile
# Build stage
FROM golang:1.25-alpine AS builder

WORKDIR /src

COPY main.go .

RUN CGO_ENABLED=0 go build -o myapp main.go

# Runtime stage
FROM scratch

COPY --from=builder /src/myapp /myapp

ENTRYPOINT ["/myapp"]
```

| Instruction | Purpose |
|---|---|
| `FROM ... AS builder` | Create a named build stage |
| `COPY --from=builder` | Copy files from another stage |
| `FROM scratch` | Start with an empty base image |

**Benefit:** Keeps build tools and dependencies out of the final runtime image.

## 10. Important Docker Concepts

| Concept | Explanation |
|---|---|
| Image | An immutable template used to create containers |
| Container | An isolated instance of an image |
| Image layers | Read-only filesystem layers that form an image |
| Layer caching | Reuses unchanged build layers to speed up builds |
| Volume | Persistent storage managed by Docker |
| Bind mount | Mounts a host file or directory into a container |
| Network | Allows containers to communicate |
| Port mapping | Connects a host port to a container port |
| Docker Hub | A registry for storing and sharing Docker images |
| Multi-stage build | Separates the build environment from the runtime image |

## 11. Important Command Examples

**Run Nginx:**

```bash
docker run -d --name my-nginx -p 8080:80 nginx
```

**Enter a running container:**

```bash
docker exec -it my-nginx sh
```

**Check container logs:**

```bash
docker logs my-nginx
```

**Build a custom image:**

```bash
docker build -t my-app:v1 .
```

**Push an image to Docker Hub:**

```bash
docker push USERNAME/REPOSITORY:TAG
```

**Check Docker disk usage:**

```bash
docker system df
```

---

## Quick Revision

- `docker run` → Create and start a container.
- `docker ps` → Check running containers.
- `docker images` → List images.
- `docker exec` → Execute a command inside a running container.
- `docker logs` → Check container logs.
- `docker volume` → Manage persistent storage.
- `docker network` → Manage container networking.
- `docker compose` → Manage multi-container applications.
- `docker build` → Build an image.
- `docker push` → Upload an image.
- `docker pull` → Download an image.

**Purpose:** Keep this cheat sheet as a quick reference while practising Docker and preparing for DevOps interviews.

#90DaysOfDevOps #DevOpsKaJosh #TrainWithShubham

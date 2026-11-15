# Day 35 – Multi-Stage Builds & Docker Hub

## Objective

Understand the problem with large Docker images, implement multi-stage builds, compare image sizes, and publish an optimized Docker image to Docker Hub.

## Application

For this challenge, I created a simple Go HTTP application.

**File: `main.go`**

```go
package main

import (
    "fmt"
    "net/http"
)

func home(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintln(w, "Hello from Day 35 - Multi-Stage Docker Build!")
}

func main() {
    http.HandleFunc("/", home)
    fmt.Println("Server running on port 8080")
    http.ListenAndServe(":8080", nil)
}
```

The application runs an HTTP server on port `8080`.

---

## Task 1: The Problem with Large Images

### Single-Stage Dockerfile

Created `Dockerfile.single`:

```dockerfile
FROM golang:1.25

WORKDIR /app

COPY main.go .

RUN go build -o myapp main.go

EXPOSE 8080

CMD ["./myapp"]
```

### Build the image

```bash
docker build -f Dockerfile.single -t day35-go:single .
```

### Check image size

```bash
docker images day35-go
```

**Actual result:**

- Disk usage: `1.37 GB`
- Content size: `330 MB`

### Why is the image large?

The single-stage image contains the Go compiler, development tools, build dependencies, and the compiled application.

However, the application only needs its compiled binary to run.

This creates an unnecessarily large production image.

---

## Task 2: Multi-Stage Build

Multi-stage builds separate the application build environment from the final runtime environment.

### Multi-Stage Dockerfile

Created `Dockerfile.multi`:

```dockerfile
# Stage 1: Build the Go application
FROM golang:1.25-alpine AS builder

WORKDIR /src

COPY main.go .

RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-s -w" -o myapp main.go


# Stage 2: Run the application
FROM scratch

COPY --from=builder /src/myapp /myapp

EXPOSE 8080

ENTRYPOINT ["/myapp"]
```

### Understanding the stages

**Stage 1 – Builder**

- Uses `golang:1.25-alpine`.
- Copies the Go source code.
- Compiles the application into a binary.

**Stage 2 – Runtime**

- Uses `scratch`, an empty base image.
- Copies only the compiled binary from the builder stage.
- Does not include the Go compiler or build environment.

### Build the image

```bash
docker build -f Dockerfile.multi -t day35-go:multi .
```

### Check image size

```bash
docker images day35-go
```

**Actual result:**

- Disk usage: `7.99 MB`
- Content size: `2.4 MB`

### Image Size Comparison

| Image | Disk Usage | Content Size |
|---|---:|---:|
| Single-stage | 1.37 GB | 330 MB |
| Multi-stage | 7.99 MB | 2.4 MB |

The multi-stage image is approximately **99.4% smaller by disk usage**.

### Why is the multi-stage image smaller?

Because the final image contains only the compiled Go binary.

The Go compiler, development tools, and build environment remain in the builder stage and are not copied into the final image.

This helps create smaller and more efficient Docker images.

### Test the multi-stage container

```bash
docker run -d \
  --name day35-go-multi-test \
  -p 8082:8080 \
  day35-go:multi
```

Check the container:

```bash
docker ps --filter name=day35-go-multi-test
```

Test the application:

```bash
curl http://localhost:8082
```

**Expected output:**

```text
Hello from Day 35 - Multi-Stage Docker Build!
```

**Result:** Application successfully ran inside the minimal `scratch` image.

---

## Task 3: Push to Docker Hub

Created a Docker Hub account and authenticated from the Ubuntu terminal.

**Docker Hub Username:** `100xnikhil`

### Login

```bash
docker login -u 100xnikhil
```

Successful authentication:

```text
Login Succeeded
```

### Tag the image

```bash
docker tag day35-go:multi 100xnikhil/day35-go:1.0
```

### Push the image

```bash
docker push 100xnikhil/day35-go:1.0
```

**Result:** Successfully pushed the image to Docker Hub.

### Pull the image

```bash
docker pull 100xnikhil/day35-go:1.0
```

Verified output:

```text
Status: Image is up to date for 100xnikhil/day35-go:1.0
```

The image was successfully retrieved or verified against Docker Hub from the same machine.

### Docker Hub Repository

**Repository URL:**

https://hub.docker.com/r/100xnikhil/day35-go

**Published image:**

```text
100xnikhil/day35-go:1.0
```

---

## Task 4: Docker Hub Repository Management

Completed the following activities:

- Created and published the Docker Hub repository.
- Added a repository description.
- Added a README explaining the project and image usage.
- Explored the Tags section.
- Verified the `1.0` image tag.
- Successfully pulled the specific `1.0` tag.

### Understanding Docker Image Tags

Docker tags help identify different versions of an image.

Example:

```text
100xnikhil/day35-go:1.0
100xnikhil/day35-go:2.0
100xnikhil/day35-go:latest
```

**Version-specific tag:** `1.0` identifies a particular version.

**Latest tag:** `latest` is simply a tag name. It does not automatically guarantee that the image is the newest version.

*Note: Publishing and comparing a `latest` tag has not yet been tested.*

---

## Task 5: Docker Image Best Practices

The challenge covers these important practices:

1. Use minimal base images such as Alpine instead of unnecessarily large base images.
2. Avoid running applications as root. Use a dedicated non-root user.
3. Combine related `RUN` commands using `&&` to reduce unnecessary layers.
4. Use specific image tags instead of relying on `latest`.
5. Keep build dependencies out of the final runtime image.

**Status:** In progress. The additional best-practice Dockerfile and its image-size comparison are still pending.

---

## Important Docker Commands Learned

| Command | Purpose |
|---|---|
| `docker build` | Build a Docker image |
| `docker images` | List Docker images |
| `docker tag` | Create another image tag |
| `docker login` | Authenticate with Docker Hub |
| `docker push` | Upload an image to Docker Hub |
| `docker pull` | Download an image from Docker Hub |
| `docker run` | Create and start a container |
| `docker ps` | List running containers |
| `docker inspect` | Inspect container or image details |

---

## Key Learnings

- Single-stage builds can produce unnecessarily large images.
- Multi-stage builds separate build-time and runtime environments.
- The `COPY --from=builder` instruction copies artifacts from another build stage.
- `scratch` provides an empty runtime base image.
- Docker Hub is a registry for storing and sharing Docker images.
- Docker tags help manage image versions.
- Non-root users improve container security.

## Day 35 Progress

- [x] Task 1 – Build a single-stage Docker image.
- [x] Task 2 – Build and compare a multi-stage Docker image.
- [x] Task 3 – Create Docker Hub account, login, push and pull.
- [x] Task 4 – Add repository description and README; verify image tag.
- [ ] Task 5 – Complete Docker image best practices and rebuild.

**Docker Hub Repository:** https://hub.docker.com/r/100xnikhil/day35-go

**Project files:**

- `main.go`
- `Dockerfile.single`
- `Dockerfile.multi`
- `Dockerfile.bestpractice` (to be added after completing Task 5)

---

**Challenge:** #90DaysOfDevOps #DevOpsKaJosh #TrainWithShubham
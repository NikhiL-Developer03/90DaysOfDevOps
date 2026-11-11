# Day 31 – Dockerfile: Build Your Own Images

> **90 Days of DevOps – Personal Learning Notes**

**Today's Goal:** Understand how to create custom Docker images using Dockerfiles.

## Topics Covered

- Introduction to Dockerfiles
- Creating a custom Ubuntu image
- Building and running custom images
- Important Dockerfile instructions
- CMD vs ENTRYPOINT
- Building a custom Nginx web server image
- Port mapping
- `.dockerignore`
- Docker build context
- Docker layer caching
- Dockerfile best practices

---

# Task 1 – Your First Dockerfile

## 1. What is a Dockerfile?

A Dockerfile is a text file containing instructions that Docker uses to build a custom Docker image.

Instead of downloading an existing image and manually configuring it every time, we can write instructions in a Dockerfile and build our own image.

**Example workflow:**

```text
Dockerfile
    |
    | docker build
    v
Docker Image
    |
    | docker run
    v
Docker Container
```

## 2. Create a Project Directory

Create a directory for our first custom image.

```bash
mkdir my-first-image
```

Move inside the directory:

```bash
cd my-first-image
```

Create a Dockerfile:

```bash
touch Dockerfile
```

Project structure:

```text
my-first-image/
└── Dockerfile
```

## 3. Write Your First Dockerfile

Open the Dockerfile:

```bash
nano Dockerfile
```

Add the following instructions:

```dockerfile
FROM ubuntu

WORKDIR /app

RUN apt-get update && \
    apt-get install curl -y

CMD ["echo", "Hello from my custom image!"]
```

Save the file.

### Explanation of Each Instruction

**1. FROM**

```dockerfile
FROM ubuntu
```

- Defines the base image.
- Every Dockerfile needs a starting image.
- Here, we use Ubuntu as the base image.

**2. WORKDIR**

```dockerfile
WORKDIR /app
```

- Sets the working directory inside the image.
- Subsequent instructions use this directory as their working directory unless another is specified.
- Docker creates the directory if it doesn't exist.

**3. RUN**

```dockerfile
RUN apt-get update && \
    apt-get install curl -y
```

- Executes commands during image building.
- `apt-get update` updates the Ubuntu package index.
- `apt-get install curl -y` installs curl.
- `&&` runs the installation only if the update succeeds.
- `\` continues the instruction onto another line.

**4. CMD**

```dockerfile
CMD ["echo", "Hello from my custom image!"]
```

- Defines the default command when the container starts.
- Here, the container prints a custom message.
- This uses Docker's exec-form syntax.

## 4. Build Your Custom Image

Run:

```bash
docker build -t my-ubuntu:v1 .
```

Explanation:

| Part | Meaning |
|---|---|
| `docker build` | Builds an image |
| `-t` | Assigns a name and tag |
| `my-ubuntu` | Image name |
| `v1` | Image tag/version |
| `.` | Current directory is the build context |

The build context is the directory Docker can access for files used by the build.

## 5. Verify the Image

```bash
docker images
```

Look for:

```text
my-ubuntu    v1
```

## 6. Run Your Custom Image

```bash
docker run --name ubuntu-cont my-ubuntu:v1
```

Expected output:

```text
Hello from my custom image!
```

This verifies that the `CMD` instruction runs when the container starts.

## 7. Verify curl Installation

Run:

```bash
docker run --rm my-ubuntu:v1 curl --version
```

Explanation:

- `--rm` automatically removes the container after it exits.
- `curl --version` checks whether curl is installed.
- The command supplied at runtime replaces the default CMD.

---

# Task 2 – Important Dockerfile Instructions

Dockerfiles use different instructions to define how an image should be created and how a container should behave.

## 1. FROM

Defines the base image.

```dockerfile
FROM ubuntu
```

Another example:

```dockerfile
FROM nginx:alpine
```

## 2. RUN

Executes commands during image building.

```dockerfile
RUN apt-get update && apt-get install curl -y
```

The result of filesystem-changing RUN instructions becomes part of the image.

## 3. COPY

Copies files from the Docker build context into the image.

```dockerfile
COPY index.html .
```

Example:

```text
Host machine                 Docker image
------------                 ------------
index.html       ------->    /app/index.html
```

The source file must be available in the build context and not excluded by `.dockerignore`.

## 4. WORKDIR

Sets the working directory inside the image.

```dockerfile
WORKDIR /app
```

Any subsequent relative paths use this directory.

## 5. EXPOSE

Documents the port that the application listens on inside the container.

```dockerfile
EXPOSE 80
```

Important: `EXPOSE` does not publish the port to your host machine.

To publish a port, use:

```bash
docker run -d -p 8080:80 nginx
```

Here:

- `8080` – Host machine port.
- `80` – Container port.

## 6. CMD

Defines the default command when a container starts.

```dockerfile
CMD ["echo", "Hello"]
```

A command provided at runtime can replace the default CMD.

---

# Task 3 – CMD vs ENTRYPOINT

Both instructions define what happens when a container starts, but they behave differently.

## 1. Understanding CMD

Example Dockerfile:

```dockerfile
FROM ubuntu

CMD ["echo", "hello"]
```

Build the image:

```bash
docker build -t cmd-demo .
```

Run it:

```bash
docker run --rm cmd-demo
```

Expected output:

```text
hello
```

Now provide a custom command:

```bash
docker run --rm cmd-demo echo "Hello Docker"
```

Expected output:

```text
Hello Docker
```

**What happened?**

The runtime command replaced the default CMD.

## 2. Understanding ENTRYPOINT

Create another Dockerfile:

```bash
nano Dockerfile.entrypoint
```

Add:

```dockerfile
FROM ubuntu

ENTRYPOINT ["echo"]
```

Build the image:

```bash
docker build -t ubuntu-entrypoint -f Dockerfile.entrypoint .
```

Run it:

```bash
docker run --rm ubuntu-entrypoint
```

It runs `echo` without additional text.

Now pass an argument:

```bash
docker run --rm ubuntu-entrypoint "Hello from ENTRYPOINT"
```

Expected output:

```text
Hello from ENTRYPOINT
```

**What happened?**

- `ENTRYPOINT` defines the main executable.
- Arguments supplied to `docker run` are appended to the ENTRYPOINT command.

## 3. CMD vs ENTRYPOINT Comparison

| CMD | ENTRYPOINT |
|---|---|
| Defines a default command | Defines the main executable |
| Runtime command can replace it | Runtime arguments are normally appended |
| Useful for default behavior | Useful for fixed application behavior |
| Can provide default arguments | Can receive runtime arguments |

**Simple takeaway:**

- Use CMD when you want to provide a default command that users can easily replace.
- Use ENTRYPOINT when you want the container to behave like a specific executable.

---

# Task 4 – Build a Simple Web App Image

Now we will create a simple static website and serve it using Nginx.

## 1. Create an HTML File

Create:

```bash
touch index.html
```

Open it:

```bash
nano index.html
```

Add:

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Docker Website</title>
</head>
<body>
    <h1>Hello from my Docker website!</h1>
    <p>This website is running inside an Nginx container.</p>
</body>
</html>
```

Save the file.

## 2. Create Dockerfile.nginx

```bash
nano Dockerfile.nginx
```

Add:

```dockerfile
FROM nginx:alpine

WORKDIR /usr/share/nginx/html

COPY index.html .

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

### Explanation

**FROM nginx:alpine**

Uses the lightweight Alpine-based Nginx image.

**WORKDIR /usr/share/nginx/html**

Sets the Nginx website directory as the working directory.

**COPY index.html .**

Copies our HTML file into the Nginx website directory.

**EXPOSE 80**

Documents the container's HTTP port.

**CMD**

Starts Nginx in the foreground.

## 3. Build the Custom Nginx Image

```bash
docker build -t my-website:v1 -f Dockerfile.nginx .
```

Explanation:

- `-t` assigns the image name and tag.
- `-f` specifies the Dockerfile to use.
- `.` specifies the current build context.

## 4. Verify the Image

```bash
docker images
```

Look for:

```text
my-website    v1
```

## 5. Run the Website

```bash
docker run -d --name my-website-cont -p 8080:80 my-website:v1
```

Explanation:

| Option | Meaning |
|---|---|
| `-d` | Runs the container in the background |
| `--name` | Assigns a container name |
| `-p 8080:80` | Maps host port 8080 to container port 80 |
| `my-website:v1` | Custom image |

Open your browser:

```text
http://localhost:8080
```

You should see your custom HTML website.

## 6. Verify the Running Container

```bash
docker ps
```

Check container logs:

```bash
docker logs my-website-cont
```

---

# Task 5 – .dockerignore

## 1. What is .dockerignore?

A `.dockerignore` file tells Docker which files and directories to exclude from the build context.

It helps to:

- Reduce the build context size.
- Avoid sending unnecessary files.
- Keep sensitive files such as `.env` out of the build context.
- Improve build performance.

## 2. Create .dockerignore

Inside your project directory:

```bash
nano .dockerignore
```

Add:

```text
node_modules
.git
*.md
.env
```

### Explanation

| Entry | Purpose |
|---|---|
| `node_modules` | Excludes Node.js dependencies |
| `.git` | Excludes Git repository data |
| `*.md` | Excludes Markdown files |
| `.env` | Excludes environment configuration files |

## 3. Understand the Build Context

Example:

```bash
docker build -t my-website:v1 -f Dockerfile.nginx .
```

The final `.` represents the current directory.

Docker uses this directory as the build context.

Files excluded by `.dockerignore` are not sent as part of that context.

**Important:** `.dockerignore` does not remove files from your computer. It only controls what is included in the Docker build context.

It also does not automatically remove files already copied into an image by another build step.

---

# Task 6 – Docker Build Cache and Optimization

## 1. What is Docker Build Cache?

Docker uses caching to avoid repeating unchanged build steps.

When Docker builds an image, it can reuse previously built layers if the relevant instruction and its inputs have not changed.

Example:

```dockerfile
FROM ubuntu

RUN apt-get update && apt-get install curl -y

COPY application.txt /app/

CMD ["echo", "Docker cache example"]
```

If only `application.txt` changes, Docker may reuse the earlier base-image and package-installation layers.

## 2. Observe Docker Build Cache

Build your image:

```bash
docker build -t cache-demo .
```

Build it again without changing anything:

```bash
docker build -t cache-demo .
```

Look for output similar to:

```text
CACHED
```

This means Docker reused a cached build step.

## 3. Why Does Instruction Order Matter?

Dockerfile instructions are processed in sequence.

When an earlier layer changes, later layers may need to be rebuilt.

For example:

```dockerfile
FROM ubuntu

RUN apt-get update && apt-get install curl -y

COPY application.txt /app/

CMD ["echo", "Hello"]
```

If the application file changes, Docker can potentially reuse the package-installation layer.

This is why frequently changing instructions should generally be placed later in the Dockerfile.

---

# Dockerfile Best Practices

## 1. Choose a Suitable Base Image

```dockerfile
FROM nginx:alpine
```

Choose an image that meets your application's compatibility, security, and maintenance requirements.

## 2. Combine Related Commands

Instead of:

```dockerfile
RUN apt-get update
RUN apt-get install curl -y
```

Use:

```dockerfile
RUN apt-get update && apt-get install curl -y
```

This can reduce unnecessary layers and keeps package installation together.

## 3. Use .dockerignore

Exclude files that are not needed during image building.

```text
node_modules
.git
*.md
.env
```

## 4. Optimize Instruction Order

Place stable instructions before frequently changing instructions.

This can help Docker reuse cached layers.

## 5. Use Exec Form for CMD and ENTRYPOINT

Example:

```dockerfile
CMD ["echo", "Hello Docker"]
```

Exec form makes the executable and its arguments explicit.

---

# Important Docker Commands Cheat Sheet

## Create and Manage Files

```bash
mkdir my-first-image
cd my-first-image
touch Dockerfile
nano Dockerfile
```

## Build Images

```bash
docker build -t my-ubuntu:v1 .
docker build -t ubuntu-entrypoint -f Dockerfile.entrypoint .
docker build -t my-website:v1 -f Dockerfile.nginx .
```

## Run Containers

```bash
docker run --rm my-ubuntu:v1

docker run --rm ubuntu-entrypoint "Hello Docker"

docker run -d --name my-website-cont -p 8080:80 my-website:v1
```

## Manage Images

```bash
docker images
docker image ls
docker image inspect my-ubuntu:v1
docker image tag nginx-server my-website:v1
```

## Manage Containers

```bash
docker ps
docker ps -a
docker logs my-website-cont
docker exec -it my-website-cont sh
docker stop my-website-cont
docker rm my-website-cont
```

---

# Key Learnings

## 1. Dockerfile

A Dockerfile contains instructions used to create a custom Docker image.

## 2. FROM

Defines the base image.

## 3. RUN

Executes commands during image building.

## 4. COPY

Copies files from the build context into the image.

## 5. WORKDIR

Sets the working directory inside the image.

## 6. EXPOSE

Documents the port used by the application. It does not publish the port.

## 7. CMD

Defines the default command executed when the container starts.

## 8. ENTRYPOINT

Defines the main executable for the container.

## 9. .dockerignore

Excludes unnecessary files from the Docker build context.

## 10. Docker Build Cache

Allows Docker to reuse unchanged build steps and improve build performance.

---

# Practical Results

Update this table after completing each practical task.

| Task | Status | Actual observation |
|---|---|---|
| Create first Dockerfile | Pending | To be recorded |
| Build Ubuntu custom image | Pending | To be recorded |
| Run custom Ubuntu image | Pending | To be recorded |
| Verify curl installation | Pending | To be recorded |
| Practise Dockerfile instructions | Pending | To be recorded |
| Understand CMD vs ENTRYPOINT | Pending | To be recorded |
| Build custom Nginx image | Pending | To be recorded |
| Access website through browser | Pending | To be recorded |
| Create .dockerignore | Pending | To be recorded |
| Verify build context | Pending | To be recorded |
| Observe Docker build cache | Pending | To be recorded |

---

# Screenshots Checklist

- [ ] Create project directory and Dockerfile
- [ ] Build custom Ubuntu image
- [ ] Run custom Ubuntu container
- [ ] Verify curl installation
- [ ] Practise CMD
- [ ] Practise ENTRYPOINT
- [ ] Build custom Nginx image
- [ ] Run Nginx website container
- [ ] Access website in browser
- [ ] Create `.dockerignore`
- [ ] Verify Docker build context
- [ ] Observe Docker build cache

---

# Final Learning Summary

Day 31 focuses on moving from running existing Docker images to creating custom images using Dockerfiles.

The main workflow is:

```text
Write Dockerfile
       |
       v
docker build
       |
       v
Custom Docker Image
       |
       v
docker run
       |
       v
Running Container
```

The most important lesson is understanding how Dockerfile instructions work together to create a reusable image.

I also learned how CMD and ENTRYPOINT control container startup behavior, how Nginx can serve a custom HTML website, and how `.dockerignore` and build caching can make image builds more efficient.

**Next Step:** Continue learning Docker volumes, networking, and Docker Compose.

#90DaysOfDevOps #DevOpsKaJosh #TrainWithShubham
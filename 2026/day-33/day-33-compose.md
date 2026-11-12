# Day 33 – Docker Compose: Multi-Container Basics

## Objective

The goal of Day 33 was to understand Docker Compose and learn how to manage application containers using a single YAML configuration file.

Docker Compose helps us define and run multiple containers, networks, volumes, and environment variables together.

---

## Task 1 – Install & Verify Docker Compose

Docker Compose was already available through Docker Desktop and the Docker CLI integration with WSL Ubuntu.

### Check Docker Compose Version

```bash
docker compose version
```

**My Output:**

```text
Docker Compose version v5.5.1
```

### Check Docker Version

```bash
docker --version
```

**My Output:**

```text
Docker version 29.8.1, build 4a63305
```

**Learning:** Docker Compose is available and ready to use on my machine.

---

## Task 2 – First Compose File (Nginx)

### Step 1: Create Project Directory

```bash
mkdir -p ~/day33-docker/compose-basics
cd ~/day33-docker/compose-basics
```

### Step 2: Create docker-compose.yml

```yaml
services:
  nginx:
    image: nginx:latest
    ports:
      - "8081:80"
```

### Understanding the Configuration

| Instruction | Purpose |
|---|---|
| `services` | Defines application services |
| `nginx` | Name of the service |
| `image` | Specifies the Docker image |
| `ports` | Maps host and container ports |
| `8081:80` | Host port 8081 maps to container port 80 |

### Step 3: Validate the Configuration

```bash
docker compose config
```

This command checks and displays the resolved Compose configuration.

### Step 4: Start Nginx

```bash
docker compose up -d
```

The `-d` option runs containers in detached mode (background).

### Step 5: Verify Running Services

```bash
docker compose ps
```

**My Practical Result:**

```text
NAME                     IMAGE          STATUS         PORTS
compose-basics-nginx-1   nginx:latest   Up             0.0.0.0:8081->80/tcp
```

### Step 6: Access Nginx

Open the following URL in a browser:

```text
http://localhost:8081
```

The Nginx welcome page should appear.

### Step 7: Stop and Remove the Application

```bash
docker compose down
```

**My Output:**

```text
Container compose-basics-nginx-1 Removed
Network compose-basics_default Removed
```

Verify:

```bash
docker compose ps
```

No services remained in the Compose project.

### Troubleshooting: Port Already Allocated

Initially, I tried using:

```yaml
ports:
  - "8080:80"
```

Docker returned:

```text
Bind for 0.0.0.0:8080 failed: port is already allocated
```

I checked running containers:

```bash
docker ps --format "table {{.Names}}\t{{.Ports}}"
```

I discovered that another container, `objective_meninsky`, was already using host port `8080`.

Instead of stopping that container, I changed the Nginx host port to `8081`.

**Key Learning:** Only one process or container can bind to a particular host IP and port combination at a time.

---

## Task 3 – WordPress + MySQL

**Status: Pending practical execution**

The objective is to run WordPress and MySQL together using Docker Compose.

### Application Architecture

```text
Browser
   |
   | Port 8082
   v
WordPress Container
   |
   | db:3306
   v
MySQL Container
   |
   v
Named Volume
```

### Important Concepts

- WordPress: Runs the website.
- MySQL: Stores website data.
- Network: Allows WordPress and MySQL to communicate.
- Named Volume: Keeps database data persistent.

### Database Connection

WordPress connects to MySQL using the Compose service name.

Example:

```yaml
WORDPRESS_DB_HOST: db:3306
```

Here, `db` is the MySQL service name.

Compose automatically creates a default network for services in the same project.

**Data Persistence:** A named volume stores MySQL data independently of the container lifecycle.

**Verification planned:**
1. Start WordPress and MySQL.
2. Open WordPress in the browser.
3. Complete the initial setup.
4. Run `docker compose down`.
5. Start the application again.
6. Verify whether the WordPress data is still available.

---

## Task 4 – Useful Docker Compose Commands

| Command | Purpose |
|---|---|
| `docker compose up` | Create and start services |
| `docker compose up -d` | Start services in the background |
| `docker compose ps` | View project services and their status |
| `docker compose logs` | View logs of all services |
| `docker compose logs -f` | Follow logs in real time |
| `docker compose logs -f db` | Follow logs of a specific service |
| `docker compose stop` | Stop services without removing containers |
| `docker compose start` | Start previously stopped services |
| `docker compose down` | Stop and remove containers and default network |
| `docker compose up -d --build` | Build/rebuild images and start services |

**Note:** `--build` is useful when your Compose configuration includes a `build:` section and you have changed the application image or Dockerfile. It is not needed just because an external image such as `nginx:latest` is used.

---

## Task 5 – Environment Variables

**Status: Pending practical execution**

Environment variables help us manage application configuration without hardcoding every value in the Compose file.

### Method 1: Define Variables Directly

Example:

```yaml
services:
  app:
    image: nginx:latest
    environment:
      APP_ENV: development
      APP_PORT: "8080"
```

### Method 2: Use a .env File

Create a file named `.env`:

```env
APP_ENV=development
APP_PORT=8080
```

Reference these variables in the Compose file:

```yaml
services:
  app:
    image: nginx:latest
    environment:
      APP_ENV: ${APP_ENV}
      APP_PORT: ${APP_PORT}
```

Verify the resolved configuration:

```bash
docker compose config
```

**Security Reminder:** Never commit real passwords, API keys, or secrets to a public GitHub repository. Add `.env` to `.gitignore` and provide a `.env.example` file with placeholder values.

---

## Key Learnings

1. Docker Compose allows us to manage containers using a YAML file.
2. `docker compose up` creates and starts application services.
3. `docker compose down` removes containers and the default network.
4. Compose automatically creates a network for services in a project.
5. Containers can communicate using service names.
6. Named volumes help preserve data beyond container removal.
7. Environment variables make application configuration easier.
8. Port mapping connects host ports to container ports.

---

## Practical Progress

- [x] Task 1 – Install & Verify Docker Compose
- [x] Task 2 – First Compose File (Nginx)
- [ ] Task 3 – WordPress + MySQL
- [ ] Task 4 – Compose Commands
- [ ] Task 5 – Environment Variables

---

## Conclusion

Today I learned how Docker Compose simplifies container management.

Instead of manually creating and managing containers and networks, we can define our application in a YAML file and manage it with simple commands.

I also encountered a host port conflict and solved it by identifying the container using port 8080 and changing my Nginx host port to 8081.


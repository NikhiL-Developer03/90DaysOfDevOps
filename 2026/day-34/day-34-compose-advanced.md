# Day 34 – Docker Compose: Real-World Multi-Container Apps

## Objective

Build a real-world, three-service application using Docker Compose.

The application includes:
- Python Flask – Web Application
- PostgreSQL – Database
- Redis – Cache
- Docker Compose – Container Orchestration

## Project Architecture

```text
                  Flask Web Application
                         (Python)
                            |
                +-----------+-----------+
                |                       |
                v                       v
            PostgreSQL                Redis
             Database                 Cache
                |                       |
                +----------+------------+
                           |
                     Docker Network
                       app-network
```

## Project Structure

```text
day34-docker/
│
├── app/
│   ├── app.py
│   ├── Dockerfile
│   └── requirements.txt
│
└── docker-compose.yml
```

## Task 1: Build Your Own App Stack

### 1. Flask Application

Created a simple Flask application that connects to PostgreSQL and Redis.

The application:
- Connects to PostgreSQL using `psycopg2`.
- Executes a simple SQL query.
- Connects to Redis using the Redis Python library.
- Stores and retrieves a message from Redis.
- Displays connection results in the browser.

### 2. Requirements File

`requirements.txt`

```text
Flask
psycopg2-binary
redis
```

### 3. Custom Dockerfile

Created a Dockerfile to build the Flask application.

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

### 4. Build Custom Image

```bash
docker build -t day34-flask:v1 .
```

Verified the image:

```bash
docker images day34-flask
```

**Result:** Successfully created the custom Flask image.

### 5. Test Flask Container

```bash
docker run -d --name day34-flask-test -p 5001:5000 day34-flask:v1
```

Accessed the application:

```text
http://localhost:5001
```

Successfully tested the Flask application in the browser.

---

## Task 2: depends_on & Healthchecks

Configured healthchecks for PostgreSQL and Redis.

### PostgreSQL Healthcheck

```yaml
healthcheck:
  test: ["CMD-SHELL", "pg_isready -U day34user -d day34db"]
  interval: 5s
  timeout: 3s
  retries: 5
```

### Redis Healthcheck

```yaml
healthcheck:
  test: ["CMD", "redis-cli", "ping"]
  interval: 5s
  timeout: 3s
  retries: 5
```

### depends_on Configuration

```yaml
depends_on:
  db:
    condition: service_healthy
  cache:
    condition: service_healthy
```

**What I learned:**

- `depends_on` controls service startup dependencies.
- `service_healthy` waits for the dependency's healthcheck to pass.
- A running container does not necessarily mean the application inside it is ready.

### Verification

```bash
docker compose ps
```

```bash
docker inspect --format='{{.Name}} -> {{.State.Health.Status}}' day34-postgres day34-redis
```

**Result:** PostgreSQL and Redis successfully reported `healthy`.

Also verified Flask logs:

```bash
docker compose logs web
```

The application successfully served an HTTP request with status `200`.

---

## Task 3: Restart Policies

Configured restart policies for all three services.

| Service | Restart Policy |
|---|---|
| PostgreSQL | `always` |
| Redis | `always` |
| Flask | `on-failure` |

### Restart Policy Configuration

For PostgreSQL and Redis:

```yaml
restart: always
```

For Flask:

```yaml
restart: on-failure
```

### Verify Restart Policies

```bash
docker inspect --format='{{.Name}} -> Restart Policy: {{.HostConfig.RestartPolicy.Name}}' day34-postgres day34-redis day34-web
```

### Practical Experiment

Forcefully stopped PostgreSQL:

```bash
docker kill day34-postgres
```

Checked all containers, including stopped containers:

```bash
docker ps -a --filter name=day34-postgres
```

Inspected the container state:

```bash
docker inspect --format='Status={{.State.Status}} | ExitCode={{.State.ExitCode}} | RestartCount={{.RestartCount}}' day34-postgres
```

Observed:

```text
Status=exited
ExitCode=137
RestartCount=0
```

Manually started PostgreSQL again:

```bash
docker start day34-postgres
```

Verified its health:

```bash
docker compose ps
```

**What I learned:**

- `always` is configured to restart a container after it stops, subject to Docker's restart-policy behavior.
- `on-failure` restarts a container when its process exits with a non-zero status.
- `docker kill` sends a forceful termination signal.
- Exit code `137` indicates termination by SIGKILL.
- `docker start` manually starts a stopped container.
- `RestartCount` helps identify automatic restarts.

**Experiment status:** Restart policies were configured and inspected. The automatic restart behavior was not conclusively demonstrated because the manually killed container remained stopped during the experiment.

---

## Task 4: Custom Dockerfiles in Compose

Used `build` in Docker Compose instead of relying only on a pre-built application image.

```yaml
web:
  build: ./app
```

### Application Code Update

Modified the Flask homepage to display:

```text
Hello from Day 34 Docker Compose - Version 2!
Updated using Docker Compose rebuild.
```

### Rebuild and Restart

```bash
docker compose up -d --build
```

**Result:**

- Docker rebuilt the custom Flask image.
- The updated web container started successfully.
- PostgreSQL and Redis remained healthy.
- Verified the updated application in the browser.

**What I learned:** Docker Compose can rebuild an application image and recreate its service after code changes using one command.

---

## Task 5: Named Networks & Volumes

Configured an explicit bridge network, a named database volume, and service labels.

### 1. Custom Network

```yaml
networks:
  app-network:
    driver: bridge
```

Verified:

```bash
docker network inspect day34-docker_app-network
```

Observed network details:

| Property | Value |
|---|---|
| Network | `day34-docker_app-network` |
| Driver | `bridge` |
| Subnet | `172.20.0.0/16` |
| Gateway | `172.20.0.1` |

Connected containers:

- Flask: `172.20.0.4`
- PostgreSQL: `172.20.0.3`
- Redis: `172.20.0.2`

**Learning:** Containers on the same Docker network can communicate using service names such as `db` and `cache`.

### 2. Named Volume

Configured persistent PostgreSQL storage:

```yaml
volumes:
  - postgres_data:/var/lib/postgresql/data
```

Declared the named volume:

```yaml
volumes:
  postgres_data:
```

Verified:

```bash
docker volume inspect day34-docker_postgres_data
```

**Learning:** Named volumes store data outside the container's writable filesystem and allow database data to survive container recreation.

### 3. Labels

Added custom labels to services:

```yaml
labels:
  project: "day34"
  service: "flask-web"
```

Verified:

```bash
docker inspect --format='{{json .Config.Labels}}' day34-web
```

**Learning:** Labels help organize, identify, filter, and manage Docker resources.

---

## Task 6: Scaling (Bonus)

Attempted to scale the Flask web application to three replicas.

### Initial Configuration Issue

The original web service used:

```yaml
container_name: day34-web

ports:
  - "5001:5000"
```

For scaling, removed the fixed `container_name` and host port mapping from the web service.

**Why?**

- Multiple replicas cannot share the same fixed container name.
- Multiple containers cannot bind the same host port simultaneously.

### Scale the Application

```bash
docker compose up -d --scale web=3
```

### Verify Replicas

```bash
docker compose ps
```

Observed three running Flask containers:

```text
day34-docker-web-1
day34-docker-web-2
day34-docker-web-3
```

Also verified:

```bash
docker compose ps web
```

**Result:** Successfully started three Flask replicas.

**What I learned:**

- Docker Compose can scale services using `--scale`.
- Each replica runs in its own container.
- Fixed host port mappings create conflicts when multiple replicas try to use the same port.
- A reverse proxy or load balancer, such as Nginx, is useful for distributing traffic among replicas.

Note: The three replicas were running on the internal container port `5000`. A load balancer was not configured in this exercise.

---

## Bonus Practical: PostgreSQL Volume Persistence

Performed an additional experiment to verify that database data survives container recreation.

### Step 1: Create a Test Table

```bash
docker exec day34-postgres psql -U day34user -d day34db -c "CREATE TABLE IF NOT EXISTS day34_test (id SERIAL PRIMARY KEY, message TEXT);"
```

### Step 2: Insert Test Data

```bash
docker exec day34-postgres psql -U day34user -d day34db -c "INSERT INTO day34_test (message) VALUES ('Docker volume persistence test');"
```

### Step 3: Verify Data

```bash
docker exec day34-postgres psql -U day34user -d day34db -c "SELECT * FROM day34_test;"
```

Observed:

```text
 id |            message
----+--------------------------------
  1 | Docker volume persistence test
(1 row)
```

### Step 4: Recreate PostgreSQL Container

```bash
docker compose up -d --force-recreate db
```

### Step 5: Verify Data Again

```bash
docker exec day34-postgres psql -U day34user -d day34db -c "SELECT * FROM day34_test;"
```

The inserted record was still present after the PostgreSQL container was recreated.

**Result:** Successfully verified named volume persistence.

---

## Important Docker Compose Commands

| Command | Purpose |
|---|---|
| `docker compose config` | Validate Compose configuration |
| `docker compose up -d` | Start services in the background |
| `docker compose up -d --build` | Build images and start services |
| `docker compose ps` | List project containers |
| `docker compose logs web` | View Flask logs |
| `docker compose up -d --scale web=3` | Scale web service to three replicas |
| `docker compose down` | Stop and remove project containers and network |
| `docker compose up -d --force-recreate db` | Recreate the database container |

**Important:** Avoid `docker compose down -v` when you want to preserve named database volumes.

---

## Key Learnings

1. How to build a multi-container application using Docker Compose.
2. How Flask communicates with PostgreSQL and Redis.
3. How healthchecks verify service readiness.
4. How `depends_on` with `service_healthy` controls startup dependencies.
5. How Docker restart policies are configured.
6. How to build and rebuild custom Docker images.
7. How to configure explicit bridge networks.
8. How named volumes preserve database data.
9. How Docker labels organize resources.
10. How to scale a service to multiple replicas.
11. Why fixed host port mappings cause scaling conflicts.
12. Why production applications need proper load balancing.

## Final Status

- [x] Task 1 – Three-service application
- [x] Task 2 – Healthchecks and dependencies
- [ ] Task 3 – Complete automatic restart-policy experiment
- [x] Task 4 – Custom Dockerfile and rebuild
- [x] Task 5 – Networks, volumes and labels
- [x] Task 6 – Scale web application to three replicas
- [x] Bonus – Verify PostgreSQL volume persistence

## Conclusion

Today I built a multi-container application using Docker Compose with Flask, PostgreSQL, and Redis.

I practiced service dependencies, healthchecks, custom Docker images, restart policies, networking, named volumes, labels, and scaling.

I also verified that PostgreSQL data survives container recreation when stored in a named volume.

This practical exercise helped me understand how multiple services work together in a containerized application.

**Day 34 – Docker Compose: Real-World Multi-Container Apps**

#90DaysOfDevOps #DevOpsKaJosh #TrainWithShubham #Docker #DockerCompose #PostgreSQL #Redis #Python #Flask #Containerization #DevOpsLearning
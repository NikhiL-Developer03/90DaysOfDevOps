# Day 39 – CI/CD Concepts

## Objective

Understand why CI/CD exists, how Continuous Integration, Continuous Delivery, and Continuous Deployment work, and how automated pipelines help software teams deliver applications reliably.

---

## 1. Why CI/CD Exists

Imagine a team of five developers working on the same project and manually deploying code to production.

Several problems can occur:

- **Human errors:** Developers may forget deployment steps or configure something incorrectly.
- **Environment differences:** Code may work on one developer's machine but fail on another server.
- **Broken builds:** Changes from different developers may conflict.
- **Slow feedback:** Bugs may be discovered late in the development process.
- **Deployment bottlenecks:** Manual deployments take time and require coordination.
- **Difficult rollbacks:** Recovering from a failed deployment can be complicated.

### What does "It works on my machine" mean?

It means an application works in one developer's environment but fails in another environment because of differences in dependencies, configurations, operating systems, or software versions.

CI/CD helps reduce these problems through automation, consistent processes, and faster feedback.

---

## 2. CI vs CD

### Continuous Integration (CI)

Continuous Integration means developers frequently integrate their code changes into a shared repository.

Each change can automatically trigger a pipeline that builds the application and runs tests to identify errors early.

**Example:**

A developer pushes code to GitHub. GitHub Actions automatically checks out the code, installs dependencies, runs tests, and reports whether the changes pass.

### Continuous Delivery (CD)

Continuous Delivery keeps the application in a deployable state.

The pipeline automatically builds, tests, and prepares the application for release. Production deployment may still require manual approval.

**Example:**

After successful testing, the pipeline creates a release artifact and waits for approval before deploying it to production.

### Continuous Deployment (CD)

Continuous Deployment automatically deploys changes to production whenever they pass all required automated checks.

No manual production approval is required for each successful deployment.

**Example:**

A developer merges code into the main branch. After all automated checks pass, the pipeline automatically deploys the new application version to production.

### Difference Between CI, Continuous Delivery, and Continuous Deployment

| Practice | Main Purpose |
|---|---|
| Continuous Integration | Automatically build and test code changes |
| Continuous Delivery | Keep the application ready for release |
| Continuous Deployment | Automatically release successful changes to production |

---

## 3. Pipeline Anatomy

A CI/CD pipeline contains several important components.

| Component | Purpose |
|---|---|
| Trigger | Event that starts a workflow |
| Stage | Logical phase such as build, test, or deploy |
| Job | Unit of work executed by a runner |
| Step | Individual command or action inside a job |
| Runner | Machine that executes the job |
| Artifact | Output produced by a job |

### Understanding Each Component

**Trigger:** Starts the pipeline when an event occurs, such as a Git push, pull request, or manual workflow execution.

**Stage:** Groups related activities, such as building, testing, and deploying an application.

**Job:** Contains a collection of steps that perform a particular task.

**Step:** A single command or reusable action executed inside a job.

**Runner:** The machine or environment that executes the workflow jobs.

**Artifact:** A file or package produced by a job, such as a compiled application or test report.

---

## 4. Example CI/CD Pipeline

### Scenario

A developer pushes application code to GitHub. The application is tested, built into a Docker image, and deployed to a staging server.

### Pipeline Diagram

```text
       Developer
           |
           v
      Git Push
           |
           v
    GitHub Repository
           |
           v
    Pipeline Trigger
           |
           v
    +--------------+
    |  Stage 1     |
    |  Build       |
    |  Checkout    |
    |  Install     |
    +--------------+
           |
           v
    +--------------+
    |  Stage 2     |
    |  Test        |
    |  Run Tests   |
    +--------------+
           |
           v
    +--------------+
    |  Stage 3     |
    |  Docker Build|
    |  Create Image|
    +--------------+
           |
           v
    +--------------+
    |  Stage 4     |
    |  Deployment  |
    |  Staging     |
    +--------------+
           |
           v
    Application Running
    on Staging Server
```

### Pipeline Explanation

1. Developer pushes code to GitHub.
2. GitHub triggers the CI/CD workflow.
3. The build stage checks out the source code and installs dependencies.
4. The test stage runs automated tests.
5. The Docker build stage creates a container image.
6. The deployment stage deploys the application to the staging server.

---

## 5. Open-Source Workflow Review

**Repository:** GitHub Actions Zero to Hero

**Repository URL:**

https://github.com/TrainWithShubham/github-actions-zero-to-hero

**Workflow file:**

`.github/workflows/cicd.yml`

The repository contains a Flask application with GitHub Actions workflows covering CI/CD and DevSecOps concepts.

### Observations

- **Trigger:** `workflow_dispatch`
- **Input:** `environment` with `dev`, `prod`, and `staging` choices.
- **Jobs:** `code`, `build`, `test`, and `deploy`.
- **Runner:** `ubuntu-latest`.
- **Job dependencies:** `build` needs `code`; `test` needs `build`; `deploy` needs both `build` and `test`.
- **Condition:** `deploy` runs only when `inputs.environment == 'dev'`.
- **Main purpose:** Demonstrates a basic CI/CD pipeline with job dependencies, manual inputs, and conditional deployment.

### Workflow Diagram

```text
   Manual Workflow Trigger
             |
             v
           Code
             |
             v
           Build
             |
             v
           Test
             |
             v
       Deploy (dev only)
```

### Important GitHub Actions Concepts

**`workflow_dispatch`:** Allows a workflow to be started manually.

**`needs`:** Defines job dependencies and execution order.

**`if`:** Runs a job or step only when a specified condition is satisfied.

**`runs-on`:** Specifies the runner environment, such as `ubuntu-latest`.

---

## 6. Important CI/CD Tools

| Tool | Purpose |
|---|---|
| GitHub Actions | Automates workflows directly in GitHub |
| Jenkins | Automation server for CI/CD |
| GitLab CI/CD | Built-in CI/CD automation in GitLab |
| CircleCI | Cloud-based CI/CD platform |

**Important:** CI/CD is a software delivery practice, not a specific tool. These platforms provide ways to implement automated pipelines.

---

## 7. Key Learnings

1. Continuous Integration helps developers detect problems early by automatically building and testing code.
2. Continuous Delivery keeps applications ready for release, while Continuous Deployment automatically releases successful changes to production.
3. A CI/CD pipeline automates repeatable software delivery activities.
4. Triggers, stages, jobs, steps, runners, and artifacts are important pipeline components.
5. GitHub Actions uses YAML workflow files to define automated workflows.
6. A failed pipeline is useful because it identifies problems before changes move further through the delivery process.

---

## Day 39 Completion Checklist

- [x] Task 1 – Understand problems with manual deployments.
- [x] Task 2 – Understand CI, Continuous Delivery, and Continuous Deployment.
- [x] Task 3 – Understand pipeline anatomy.
- [x] Task 4 – Create a text-based CI/CD pipeline diagram.
- [x] Task 5 – Document an open-source workflow based on the supplied workflow review.

**Main takeaway:** CI/CD helps teams automate software integration, testing, and deployment to deliver applications more consistently and reliably.

#90DaysOfDevOps #DevOpsKaJosh #TrainWithShubham
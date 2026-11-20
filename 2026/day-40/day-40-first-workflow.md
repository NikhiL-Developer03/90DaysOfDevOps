# Day 40 – First GitHub Actions Workflow

## Objective

Create and execute my first GitHub Actions workflow to understand how GitHub automates tasks whenever code is pushed to a repository.

## 1. What is GitHub Actions?

GitHub Actions is a CI/CD automation platform provided by GitHub.

It helps automate tasks such as:
- Building applications
- Running automated tests
- Executing shell commands
- Building Docker images
- Deploying applications

A workflow can automatically start when an event occurs, such as pushing code to GitHub.

## 2. Repository Setup

**Repository name:** `github-actions-practice`

**Workflow file location:**

```text
.github/
└── workflows/
    └── hello.yml
```

GitHub automatically detects workflow files stored inside `.github/workflows/`.

## 3. First GitHub Actions Workflow

**File:** `.github/workflows/hello.yml`

```yaml
name: Hello GitHub Actions

on:
  push:

jobs:
  greet:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Say hello
        run: echo "Hello from GitHub Actions!"

      - name: Current date
        run: date

      - name: Branch
        run: echo "Branch: ${{ github.ref_name }}"

      - name: List files
        run: ls -la

      - name: Runner OS
        run: echo "OS: $RUNNER_OS"
```

### Understanding the Workflow

**1. Workflow Name**

```yaml
name: Hello GitHub Actions
```

Defines the workflow name displayed in the GitHub Actions interface.

**2. Trigger – `on`**

```yaml
on:
  push:
```

Defines the event that starts the workflow.

Here, every push to the repository triggers the workflow.

**3. Jobs**

```yaml
jobs:
  greet:
```

Defines the jobs that the workflow executes.

Here, `greet` is the name of our job.

**4. Runner – `runs-on`**

```yaml
runs-on: ubuntu-latest
```

Specifies the machine environment where the job will execute.

Here, GitHub provides an Ubuntu runner.

**5. Steps**

```yaml
steps:
```

Defines the individual operations performed inside a job.

Steps normally execute sequentially.

**6. Uses**

```yaml
uses: actions/checkout@v4
```

Uses an existing GitHub Action.

`actions/checkout` checks out the repository code onto the runner so subsequent steps can access its files.

**7. Run**

```yaml
run: echo "Hello from GitHub Actions!"
```

Executes a shell command on the runner.

**8. Step Name**

```yaml
- name: Say hello
```

Provides a readable name for a step. It appears in the GitHub Actions interface.

## 4. Additional Workflow Commands

### Print Current Date and Time

```yaml
- name: Current date
  run: date
```

The `date` command displays the current date and time on the runner.

### Print Branch Name

```yaml
- name: Branch
  run: echo "Branch: ${{ github.ref_name }}"
```

`${{ github.ref_name }}` is a GitHub Actions context expression that provides the name of the branch or tag associated with the workflow run.

### List Repository Files

```yaml
- name: List files
  run: ls -la
```

Displays files and directories in the runner's current working directory.

### Print Runner Operating System

```yaml
- name: Runner OS
  run: echo "OS: $RUNNER_OS"
```

`RUNNER_OS` is a GitHub-provided environment variable that identifies the runner operating system.

## 5. Understanding Workflow Execution

The workflow follows this sequence:

```text
Developer pushes code
        |
        v
GitHub Repository
        |
        v
Push Event Trigger
        |
        v
GitHub Actions Workflow
        |
        v
Greet Job
        |
        v
Ubuntu Runner
        |
        v
Checkout Repository
        |
        v
Execute Shell Commands
        |
        v
Workflow Result
```

## 6. Failure Experiment

To understand how GitHub Actions handles errors, an intentional failure can be introduced.

Example:

```yaml
- name: Intentional failure
  run: exit 1
```

### What does `exit 1` mean?

- `exit 0` means the command completed successfully.
- `exit 1` means the command exited with a failure status.

### Expected Behaviour

1. The workflow starts.
2. The intentional failure step executes.
3. The step is marked as failed.
4. The job and workflow normally become unsuccessful.
5. Subsequent steps are skipped by default.

### Fixing the Failure

Remove the intentional failure or correct the faulty command, commit the change and push again.

The new workflow run should succeed if all required steps pass.

### How to Read a Failed Pipeline

In GitHub Actions:

1. Open the repository.
2. Select the Actions tab.
3. Open the failed workflow run.
4. Select the failed job.
5. Find the step marked with a red cross.
6. Read the error message in its logs.

A failed pipeline helps identify problems before changes proceed further.

## 7. Screenshot – Workflow Execution

Add your actual GitHub Actions screenshot here after completing the successful workflow run.

```text
[Insert screenshot of successful green workflow run]
```

The screenshot should show:
- Workflow name
- Successful run status
- Green checkmark
- Job execution details

## 8. Key Learnings

1. GitHub Actions automates development and deployment tasks.
2. Workflow files are stored in `.github/workflows/`.
3. The `on` key defines workflow triggers.
4. The `jobs` key defines the work to execute.
5. The `runs-on` key selects the runner environment.
6. The `steps` key defines individual operations.
7. The `uses` key calls reusable GitHub Actions.
8. The `run` key executes shell commands.
9. GitHub provides built-in variables and context expressions.
10. Failed workflows help identify errors through execution logs.

## 9. Challenge Checklist

- [ ] Create public repository `github-actions-practice`.
- [ ] Clone repository locally.
- [ ] Create `.github/workflows/`.
- [ ] Create `hello.yml`.
- [ ] Configure workflow to trigger on push.
- [ ] Create the `greet` job.
- [ ] Configure the Ubuntu runner.
- [ ] Add checkout and greeting steps.
- [ ] Push code and inspect the Actions tab.
- [ ] Add date, branch, file listing and OS steps.
- [ ] Perform intentional failure experiment.
- [ ] Fix the failure and verify successful execution.
- [ ] Capture screenshot of successful workflow.
- [ ] Complete this documentation.

## 10. Conclusion

Day 40 introduced my first GitHub Actions workflow.

I learned how a workflow is triggered by a GitHub event, how jobs execute on runners, and how steps perform individual tasks.

I also explored GitHub-provided variables and how to identify failures through workflow execution logs.

This is my first practical step towards building automated CI/CD pipelines.

---

**Challenge:** #90DaysOfDevOps  
**Day:** 40  
**Topic:** First GitHub Actions Workflowg
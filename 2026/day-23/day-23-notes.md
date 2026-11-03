# Day 23 – Git Branching & Working with GitHub

## Objective

Today I practiced Git branching workflows, switching between branches using both modern and classic commands, and pushing code to a remote GitHub repository.

I also practiced fetching vs. pulling remote changes, connecting local repositories to GitHub via SSH, and understanding the differences between cloning and forking.

---

# Task 1 – Understanding Branches

## 1. What is a branch in Git?

A branch is a lightweight, movable pointer to a specific commit in Git history.

It represents an independent line of development, allowing work on features, bug fixes, or experiments in total isolation from the primary codebase.

For example:

```text
main
 |
 └── feature-1
```

---

## 2. Why do we use branches?

Committing everything directly to `main` makes the codebase unstable and complicates collaboration.

Branches allow:
- Developing features or fixes without affecting the main working code.
- Keeping the `main` branch clean and production-ready.
- Allowing multiple team members to work on separate tasks simultaneously without conflicts.

---

## 3. What is HEAD?

`HEAD` is a reference pointer in Git that indicates the branch or commit currently checked out in the working directory.

For example:

```text
HEAD -> feature-1
```

This indicates that `feature-1` is the currently active branch.

---

## 4. What happens when switching branches?

When switching branches, Git updates the files in the working directory to match the snapshot of the selected branch.

Commits and files created on one branch remain isolated and are not visible in the working directory when switching to another branch until merged.

---

# Task 2 – Branching Commands — Hands-On

## 1. Check Existing Branches

I used:

```bash
git branch
```

### Observation

This listed all local branches in the repository, with an asterisk marking the active branch.

---

## 2. Create a New Branch (`feature-1`)

I created the branch using:

```bash
git branch feature-1
```

### Observation

A new branch pointer named `feature-1` was created based on the current commit.

---

## 3. Switch to `feature-1`

I switched branches using:

```bash
git checkout feature-1
```

*(or using `git switch feature-1`)*

### Observation

`HEAD` moved to `feature-1`, and the working tree updated to match the branch state.

---

## 4. Create and Switch in a Single Command (`feature-2`)

I used:

```bash
git switch -c feature-2
```

*(or using legacy syntax: `git checkout -b feature-2`)*

### Observation

A new branch named `feature-2` was created and checked out in one step.

---

## 5. `git switch` vs `git checkout`

- `git checkout` is a legacy, multi-purpose command used for switching branches, restoring working tree files, and checking out specific historical commits.
- `git switch` is a dedicated command introduced in Git 2.23 specifically to navigate between and create branches, providing a cleaner and safer syntax.

---

## 6. Commit on `feature-1` (Isolated from `main`)

I switched to `feature-1` and committed changes:

```bash
git switch feature-1
echo "Feature 1 work" >> feature.txt
git add feature.txt
git commit -m "Add feature 1"
```

### Observation

The commit was recorded on `feature-1`, advancing its pointer independently of `main`.

---

## 7. Switch Back to `main` and Verify Isolation

```bash
git switch main
git log --oneline
ls
```

### Observation

`feature.txt` is not present in the working directory, and the commit from `feature-1` does not exist in the history of `main`.

---

## 8. Delete a Branch

I deleted `feature-2` using:

```bash
git branch -d feature-2
```

### Observation

The branch reference was removed safely.

---

# Task 3 – Connect & Push to GitHub

## Check Existing Remote

```bash
git remote -v
```

This showed the remotes configured for the local repository.

---

## Add GitHub Remote

I configured the remote repository using SSH:

```bash
git remote add origin git@github.com:<username>/devops-git-practice.git
```

### Remote

```text
origin
```

### GitHub Repository

```text
git@github.com:<username>/devops-git-practice.git
```

---

## Verify Remote

```bash
git remote -v
```

### Observation

The `origin` remote was successfully linked to the GitHub repository.

---

## Push `main` Branch to GitHub

```bash
git push -u origin main
```

### Observation

The local `main` branch was pushed to GitHub, and upstream tracking was established.

---

## Push `feature-1` Branch to GitHub

```bash
git push -u origin feature-1
```

### Observation

The `feature-1` branch was pushed to the remote repository and is now visible on GitHub.

---

# Origin vs Upstream

## Origin

`origin` is the default name Git gives to the remote repository from which a repository was cloned or configured.

```text
origin -> personal repository on GitHub
```

## Upstream

`upstream` is the conventional name used in a fork-based workflow to track the original, central repository that was forked.

```text
upstream -> original parent repository
origin   -> personal fork on GitHub
local    -> local machine working repository
```

---

# Task 4 – Pull from GitHub (Fetch vs Pull)

## 1. Edit Directly on GitHub

A commit was made directly to a file using the GitHub web interface to simulate remote changes.

---

## 2. Pull Changes Locally

I retrieved the changes using:

```bash
git pull origin main
```

### Observation

The remote changes were fetched and automatically integrated into the local working directory.

---

## 3. Difference Between `git fetch` and `git pull`

### `git fetch`

I practiced:

```bash
git fetch origin main
```

`git fetch` downloads information and commits from the remote repository without automatically merging them into the active local branch.

```text
git fetch
    ↓
Download remote changes to origin/main
    ↓
No automatic merge
```

### `git pull`

I practiced:

```bash
git pull origin main
```

`git pull` runs `git fetch` and immediately performs a `git merge` to integrate remote changes into the current branch.

```text
git pull
    ↓
Fetch remote changes
    ↓
Integrate/Merge into local branch
```

---

# Task 5 – Clone vs Fork

## 1. Clone a Public Repository

```bash
git clone https://github.com/<owner>/<repo-name>.git
```

## 2. Fork and Clone

1. Forked the repository directly on GitHub.
2. Cloned the personal fork locally:

```bash
git clone git@github.com:<username>/<repo-name>.git
```

---

## Clone vs Fork Differences

- **Fork:** A server-side copy of a repository hosted entirely on GitHub under your personal account.
- **Clone:** A full local copy of a Git repository downloaded to your local machine.

---

## When to Clone vs Fork

- **Clone Directly:** When working on personal repositories or within a team where direct write permissions are granted.
- **Fork First:** When contributing to open-source or third-party repositories where direct push permissions are restricted. Changes are committed to the fork and submitted via a Pull Request (PR).

---

## Keeping a Fork in Sync with the Original Repo

### 1. Add Original Repo as `upstream`

```bash
git remote add upstream https://github.com/<original-owner>/<repo-name>.git
```

### 2. Fetch Latest Changes from Upstream

```bash
git fetch upstream
```

### 3. Merge Upstream Changes into Local `main`

```bash
git switch main
git merge upstream/main
```

### 4. Push Updated `main` to Personal Fork (`origin`)

```bash
git push origin main
```

---

# SSH Commands Practiced

```bash
ssh-keygen

ls -la ~/.ssh

cat ~/.ssh/id_ed25519.pub
```

These commands were used to generate and view the SSH key required for authenticating Git operations with GitHub.

---

# Git Commands Practiced

## Remote

```bash
git remote -v

git remote add origin <repository-url>

git remote add upstream <repository-url>
```

## Branches

```bash
git branch

git branch feature-1

git checkout feature-1

git switch feature-1

git switch -c feature-2

git branch -d feature-2
```

## Push

```bash
git push -u origin main

git push -u origin feature-1
```

## Pull & Fetch

```bash
git fetch origin main

git pull origin main

git merge origin/main
```

## Clone

```bash
git clone <repository-url>
```

---

# Commands Added to `git-commands.md`

The following commands should be added to the reference file:

```bash
git remote -v

git remote add origin <repository-url>

git remote add upstream <repository-url>

git branch

git branch <branch-name>

git switch <branch-name>

git switch -c <branch-name>

git checkout <branch-name>

git checkout -b <branch-name>

git branch -d <branch-name>

git push -u origin <branch-name>

git fetch origin <branch-name>

git pull origin <branch-name>

git merge <branch-name>

git clone <repository-url>
```

---

# What I Learned

### 1. Git Branching

I practiced creating, switching, and isolating work on branches using:

```bash
git switch -c feature-1
```

Branches allow safe and modular feature development without compromising the stability of `main`.

### 2. GitHub Remote and SSH

I connected my local repository to GitHub via SSH and pushed multiple branches to the remote server.

### 3. Fetch vs Pull vs Merge

I learned that `git fetch` safely inspects changes from a remote without altering local files, whereas `git pull` combines fetching and merging in one step.

### 4. Clone vs Fork

I understood that forking creates a cloud copy on GitHub for open-source contribution, while cloning creates a local copy for active development.

---

# Day 23 Summary

The main workflow practiced today:

```text
Create Branch (git switch -c)
       ↓
Make Changes & Commit (git commit)
       ↓
Push to Remote (git push -u origin)
       ↓
Fetch / Pull Changes
       ↓
Merge
```
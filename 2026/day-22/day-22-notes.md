# Day 22 – Introduction to Git: Your First Repository

## Objective

Today I practiced the basics of Git by configuring Git, creating my first local repository, learning essential Git commands, staging and committing changes, and viewing commit history.

## Task 1 – Install and Configure Git

### Check Git Version

```bash
git --version
```

Checks whether Git is installed and displays its version.

### Check Git Username

```bash
git config --global user.name
```

Displays the username configured for Git commits.

### Check Git Email

```bash
git config --global user.email
```

Displays the email configured for Git commits.

### View Git Configuration

```bash
git config --global --list
```

Displays the global Git configuration.

My Git identity is configured as:

- Username: `NikhiL-Developer03`
- Email: `nikhil.developer03@gmail.com`

## Task 2 – Create a Git Project

### Create the Project Directory

```bash
mkdir devops-git-practice
```

Creates a new directory for Git practice.

### Enter the Directory

```bash
cd devops-git-practice
```

Moves into the project directory.

### Initialize Git

```bash
git init
```

Initializes the current directory as a Git repository and creates the hidden `.git/` directory.

### Check Repository Status

```bash
git status
```

Displays the current branch and the status of files, including untracked, modified, and staged files.

### Explore the `.git/` Directory

```bash
ls -la
ls -la .git
```

The `-a` option displays hidden files and directories.

The `.git/` directory stores the internal information Git needs to manage the repository, including its configuration, objects, references, and history.

**Important:** Do not delete `.git/` unless you intentionally want to remove the local repository metadata and history.

## Task 3 – Create a Git Commands Reference

I created a file named:

```text
git-commands.md
```

This file contains the commands I practiced, organized into categories:

- Setup and Configuration
- Repository Setup
- Basic Workflow
- Viewing Changes
- Viewing Commit History
- File and Directory Commands
- Branching Commands

I will continue updating this file as I learn more Git commands.

## Task 4 – Stage and Commit Changes

### Check Repository Status

```bash
git status
```

Checks which files are untracked, modified, staged, or unchanged.

### Stage a File

```bash
git add git-commands.md
```

Stages the current contents of `git-commands.md` for the next commit.

### View Unstaged Changes

```bash
git diff
```

Displays changes in tracked files that have not yet been staged.

### View Staged Changes

```bash
git diff --staged
```

Displays changes that are staged and ready to be committed.

### Commit the Changes

```bash
git commit -m "Add initial Git commands reference"
```

Creates a commit containing the staged changes.

### View Commit History

```bash
git log
```

Displays detailed commit history, including commit IDs, authors, dates, and messages.

## Task 5 – Build Commit History

After creating the initial commit, I updated `git-commands.md` with additional commands and created more commits.

### Commit 1 – Initial Reference

```bash
git commit -m "Add initial Git commands reference"
```

Created the initial version of my Git commands reference.

### Commit 2 – File and Directory Commands

```bash
git commit -m "Add file and directory commands"
```

Recorded additional commands for working with files and directories.

### Commit 3 – Branching Commands

```bash
git commit -m "Add Git branching commands"
```

Added commands for listing, creating, and switching branches.

### Commit 4 – Git History and Inspection

```bash
git commit -m "Add Git history and inspection commands"
```

Added useful commands for inspecting commits, branches, and changes.

### View Compact Commit History

```bash
git log --oneline
```

Displays each commit on a single line, making the history easier to review.

My practice repository contains multiple commits, each with a descriptive message.

## Task 6 – Understanding the Git Workflow

### 1. What is the difference between `git add` and `git commit`?

`git add` stages changes that I want to include in the next commit.

`git commit` saves the staged changes as a snapshot in the repository's history.

Example:

```bash
git add git-commands.md
git commit -m "Update Git commands reference"
```

### 2. What does the staging area do?

The staging area allows me to select which changes should be included in the next commit.

Git does not automatically commit every change because I may want to review changes and group only specific updates into one commit.

The workflow is:

```text
Working Directory
       |
    git add
       |
 Staging Area
       |
   git commit
       |
   Repository
```

### 3. What information does `git log` show?

The `git log` command displays the repository's commit history, including:

- Commit ID
- Author
- Date
- Commit message

I practiced these commands:

```bash
git log
git log --oneline
```

The first displays detailed history, while the second provides a compact view.

### 4. What is the `.git/` folder?

The `.git/` directory is created when I initialize a repository using `git init`.

It stores Git's internal repository information and metadata.

If I delete this directory, the project files may remain, but the local Git repository metadata, branch information, and commit history are removed unless they have been backed up elsewhere.

### 5. What is the difference between the working directory, staging area, and repository?

**Working Directory**

The location where I create and modify project files.

Example: `git-commands.md`

**Staging Area**

The area where I select changes for the next commit using:

```bash
git add git-commands.md
```

**Repository**

The Git-managed history containing committed snapshots and related metadata.

The complete workflow is:

```text
Working Directory
       |
    git add
       |
 Staging Area
       |
   git commit
       |
   Repository
```

## Important Git Commands I Practiced

| Command | Purpose |
|---|---|
| `git --version` | Checks the installed Git version. |
| `git config --global --list` | Displays global Git configuration. |
| `mkdir devops-git-practice` | Creates the project directory. |
| `cd devops-git-practice` | Enters the project directory. |
| `git init` | Initializes a Git repository. |
| `ls -la` | Lists files, including hidden entries. |
| `git status` | Checks repository and file status. |
| `git diff` | Shows unstaged changes to tracked files. |
| `git add git-commands.md` | Stages the file's current contents. |
| `git diff --staged` | Shows staged changes. |
| `git commit -m "message"` | Records staged changes in a commit. |
| `git log` | Shows detailed commit history. |
| `git log --oneline` | Shows compact commit history. |
| `git branch` | Lists local branches. |
| `git switch -c feature-notes` | Creates and switches to a new branch. |
| `git show` | Displays details of a commit. |

## What I Learned

1. Git tracks project changes through commits.
2. The staging area lets me choose which changes go into a commit.
3. `git status` helps me understand the current state of my repository.
4. `git diff` and `git diff --staged` help me review changes before committing.
5. `git log` and `git log --oneline` help me inspect commit history.
6. Clear commit messages make project history easier to understand.
7. The `git-commands.md` file will serve as my personal reference as I continue learning Git.

## Day 22 Summary

Today I created my first local Git repository, configured Git, created a commands reference, practiced staging and committing changes, and built a history of multiple commits.

My basic Git workflow is:

```text
Initialize Repository
        |
   Edit Files
        |
   git status
        |
    git diff
        |
     git add
        |
 git diff --staged
        |
    git commit
        |
     git log
```

I will continue updating `git-commands.md` during upcoming Git practice days as I learn new commands and workflows.

# Day 26 – GitHub CLI: Manage GitHub from Your Terminal

## Objective

Today I learned how to use GitHub CLI (`gh`) to manage GitHub directly from the terminal.

GitHub CLI allows developers to create repositories, manage issues, create pull requests, and interact with GitHub Actions without constantly switching to the browser.

---

# Task 1 – Install and Authenticate

## 1. Check GitHub CLI Version

```bash
gh --version
```

**Purpose:** Checks whether GitHub CLI is installed and displays its version.

**My Output:**

```text
gh version 2.45.0
```

## 2. Authenticate with GitHub

```bash
gh auth login
```

**Purpose:** Starts the GitHub authentication process.

During authentication, I selected:

- GitHub.com
- SSH as the Git protocol
- My existing SSH public key
- Browser-based authentication

**Note:** My WSL environment could not automatically open the browser. I manually opened the authentication URL in my Windows browser and completed the process.

## 3. Check Authentication Status

```bash
gh auth status
```

**Purpose:** Shows whether I am authenticated and which GitHub account is active.

## 4. Verify the Active Account

```bash
gh api user --jq .login
```

**Output:**

```text
NikhiL-Developer03
```

## Authentication Methods Supported by GitHub CLI

GitHub CLI supports:

1. **Browser-based authentication:** Opens a browser/device authentication flow to authorize GitHub CLI.
2. **Personal Access Token (PAT):** Uses a GitHub token to authenticate.

Token environment variables such as `GH_TOKEN` and `GITHUB_TOKEN` can also provide credentials, especially in automation and CI/CD environments.

**Important:** SSH and HTTPS are Git communication protocols. They are not separate GitHub CLI authentication methods.

---

# Task 2 – Working with Repositories

## 1. Create a GitHub Repository

```bash
gh repo create my-test-repo --public --add-readme
```

**Purpose:**
- Creates a new GitHub repository.
- `--public` makes the repository publicly accessible.
- `--add-readme` creates a README file.

**My Practice:** Created a temporary repository:

```bash
gh repo create day26-gh-cli-practice --public --add-readme
```

## 2. Clone a Repository

```bash
gh repo clone OWNER/REPOSITORY
```

**Purpose:** Clones a GitHub repository directly using GitHub CLI instead of manually using `git clone`.

Example:

```bash
gh repo clone NikhiL-Developer03/day26-gh-cli-practice
```

## 3. View Repository Details

```bash
gh repo view
```

**Purpose:** Displays information about the current repository.

For a specific repository:

```bash
gh repo view OWNER/REPOSITORY
```

## 4. List Repositories

```bash
gh repo list
```

**Purpose:** Lists repositories associated with my GitHub account.

To limit the number of results:

```bash
gh repo list --limit 50
```

## 5. Open Repository in Browser

```bash
gh repo view --web
```

**Purpose:** Opens the current repository in a web browser.

**My Observation:** WSL could not automatically open the browser because `xdg-open` was unavailable. I could manually open the repository URL in Windows.

## 6. Delete a Repository

```bash
gh repo delete OWNER/REPOSITORY
```

**Purpose:** Deletes a GitHub repository.

I deleted my temporary practice repository:

```bash
gh repo delete NikhiL-Developer03/day26-gh-cli-practice
```

**Important:** Repository deletion is destructive. Always verify the repository name before confirming deletion.

---

# Task 3 – GitHub Issues

I practiced creating, listing, viewing, and closing issues using GitHub CLI.

Practice repository:

`NikhiL-Developer03/devops-git-practice`

## 1. Create an Issue

```bash
gh issue create \
  --repo NikhiL-Developer03/devops-git-practice \
  --title "Improve Git command documentation" \
  --body "The Git command reference needs additional explanations and examples for beginners." \
  --label documentation
```

**Purpose:** Creates an issue with a title, description, and label.

## 2. List Open Issues

```bash
gh issue list --repo OWNER/REPOSITORY
```

**Purpose:** Lists open issues in a repository.

To explicitly filter open issues:

```bash
gh issue list --repo OWNER/REPOSITORY --state open
```

## 3. View an Issue

```bash
gh issue view ISSUE_NUMBER --repo OWNER/REPOSITORY
```

**Purpose:** Displays details of a specific issue.

**My Observation:** I encountered a GitHub GraphQL Projects (classic) deprecation error with the normal issue-view command.

I used this alternative:

```bash
gh issue view 1 --repo NikhiL-Developer03/devops-git-practice \
  --json number,title,body,state,labels
```

This returned the issue information in JSON format.

## 4. Close an Issue

```bash
gh issue close 1 \
  --repo NikhiL-Developer03/devops-git-practice \
  --comment "Completed the Git command documentation practice."
```

**Purpose:** Closes an issue and adds a closing comment.

## How Can `gh issue` Be Used in Automation?

GitHub CLI can be used in shell scripts to automate issue management.

Examples:

- Automatically create an issue when a monitoring script detects a problem.
- List open issues for a daily report.
- Close an issue after a task has been completed.
- Retrieve issue information using JSON for further processing.

Example:

```bash
gh issue list --state open --json number,title
```

---

# Task 4 – Pull Requests

A pull request (PR) allows developers to propose changes from one branch and merge them into another branch after reviewing the changes.

I practiced the complete pull request workflow using GitHub CLI.

Practice repository:

`NikhiL-Developer03/devops-git-practice`

## 1. Create a Feature Branch

```bash
git checkout -b feature/gh-cli-practice
```

**Purpose:** Creates a new feature branch and switches to it.

## 2. Create a File and Add Content

```bash
echo "GitHub CLI practice" >> gh-cli-practice.md
```

**Purpose:** Creates or appends content to a file.

## 3. Stage and Commit Changes

```bash
git add gh-cli-practice.md
git commit -m "Add GitHub CLI practice notes"
```

**Purpose:**
- `git add` stages the changes.
- `git commit` saves the changes in the local Git repository.

## 4. Push the Feature Branch

```bash
git push -u origin feature/gh-cli-practice
```

**Purpose:** Pushes the feature branch to the remote repository.

The `-u` option sets the upstream tracking branch.

## 5. Create a Pull Request

```bash
gh pr create
```

Another option:

```bash
gh pr create --fill
```

**Purpose:** Creates a pull request directly from the terminal.

The `--fill` option uses commit information to populate the PR title and description.

### My Practical PR Exercise

I created a clean feature branch based on the latest remote `main` branch:

```bash
git fetch origin

git switch -c feature/gh-cli-practice-clean origin/main
```

I transferred my practice commit to this clean branch:

```bash
git cherry-pick 354e26c
```

**Why did I do this?**

My original branch contained unrelated commits. I created a clean branch from `origin/main` and cherry-picked only the GitHub CLI practice commit so that the pull request would contain only the intended changes.

I pushed the clean branch:

```bash
git push -u origin feature/gh-cli-practice-clean
```

Then created the pull request:

```bash
gh pr create \
  --base main \
  --head feature/gh-cli-practice-clean \
  --title "Add GitHub CLI practice notes" \
  --body "Add GitHub CLI practice notes and document repository, issue, and pull request workflows as part of Day 26."
```

**Important:**
- `--base main`: Destination branch.
- `--head feature/gh-cli-practice-clean`: Source branch.
- `--title`: Pull request title.
- `--body`: Pull request description.

My pull request:

https://github.com/NikhiL-Developer03/devops-git-practice/pull/2

## 6. List Open Pull Requests

```bash
gh pr list --repo OWNER/REPOSITORY
```

**Purpose:** Lists open pull requests in a repository.

## 7. View Pull Request Details

```bash
gh pr view 2 --repo NikhiL-Developer03/devops-git-practice \
  --json number,title,state,mergeable,headRefName,baseRefName,url
```

**Purpose:** Displays pull request information in JSON format.

My output confirmed:

- State: `OPEN`
- Mergeable: `MERGEABLE`
- Base branch: `main`
- Head branch: `feature/gh-cli-practice-clean`

## 8. Check Pull Request Status

```bash
gh pr checks 2 --repo NikhiL-Developer03/devops-git-practice
```

**My Output:**

```text
no checks reported on the 'feature/gh-cli-practice-clean' branch
```

**Observation:** No automated status checks were configured for this practice branch.

## 9. Review Pull Request Changes

```bash
gh pr diff 2 --repo NikhiL-Developer03/devops-git-practice
```

**Purpose:** Displays the changes included in a pull request.

I verified that my PR added only the intended file:

`gh-cli-practice.md`

## 10. Check Reviewers

```bash
gh pr view 2 --repo NikhiL-Developer03/devops-git-practice \
  --json reviewRequests,reviewDecision
```

**Purpose:** Displays requested reviewers and the review decision.

My output showed:

```json
{
  "reviewDecision": "",
  "reviewRequests": []
}
```

No reviewers were requested, and no review decision was recorded.

## 11. Merge a Pull Request

```bash
gh pr merge 2 --repo NikhiL-Developer03/devops-git-practice --merge
```

**My Output:**

```text
✓ Merged pull request #2 (Add GitHub CLI practice notes)
```

The pull request was successfully merged into `main`.

### Merge Methods Supported by `gh pr merge`

**1. Merge commit**

```bash
gh pr merge PR_NUMBER --merge
```

Creates a merge commit and preserves the branch history.

**2. Squash merge**

```bash
gh pr merge PR_NUMBER --squash
```

Combines all pull request commits into a single commit.

**3. Rebase merge**

```bash
gh pr merge PR_NUMBER --rebase
```

Replays the pull request commits on top of the base branch.

## 12. Verify the Merge

```bash
git fetch origin
git log origin/main --oneline -5
```

**Purpose:** Fetches the latest remote changes and displays recent commits from the remote `main` branch.

My output included:

```text
1fa1fdb Merge pull request #2 from NikhiL-Developer03/feature/gh-cli-practice-clean
```

This confirmed that my pull request was merged into the remote `main` branch.

**My Observation:** `gh pr view` also displayed a GraphQL Projects (classic) deprecation error. However, the merge succeeded, and the Git commit history confirmed it.

## How Would I Review Someone Else's Pull Request?

I can use these commands:

```bash
gh pr list
gh pr view PR_NUMBER
gh pr diff PR_NUMBER
gh pr checks PR_NUMBER
```

I can also check out a pull request locally:

```bash
gh pr checkout PR_NUMBER
```

To submit a review:

```bash
gh pr review PR_NUMBER --approve
gh pr review PR_NUMBER --request-changes
gh pr review PR_NUMBER --comment
```

These commands help developers inspect changes, check automated tests, and provide feedback before merging.

---

# Task 5 – GitHub Actions & Workflows (Pending)

**Status:** Not completed yet.

## 1. List Workflows

```bash
gh workflow list --repo OWNER/REPOSITORY
```

**Purpose:** Lists GitHub Actions workflows available in a repository.

## 2. List Workflow Runs

```bash
gh run list --repo OWNER/REPOSITORY --limit 5
```

**Purpose:** Displays recent GitHub Actions workflow runs.

## 3. View a Specific Workflow Run

```bash
gh run view RUN_ID --repo OWNER/REPOSITORY
```

**Purpose:** Displays details about a particular workflow run.

## How Can `gh run` and `gh workflow` Help in CI/CD?

- Monitor automated build and deployment workflows.
- Identify failed workflow runs.
- Inspect workflow execution details.
- Check the status of automated tests.
- Trigger workflows when required.

**Learning:** GitHub CLI can help DevOps engineers monitor and manage CI/CD operations directly from the terminal.

*Practical command execution and observations are still pending.*

---

# Task 6 – Useful GitHub CLI Tricks (Pending)

**Status:** Not completed yet.

## 1. GitHub API

```bash
gh api repos/OWNER/REPOSITORY
```

**Purpose:** Makes requests to the GitHub REST API directly from the terminal.

Example:

```bash
gh api user --jq .login
```

This retrieves the authenticated GitHub username.

## 2. GitHub Gist

```bash
gh gist create FILE
```

**Purpose:** Creates a GitHub Gist from a local file.

A Gist is useful for sharing small code snippets, scripts, and configuration examples.

## 3. GitHub Releases

List releases:

```bash
gh release list
```

Create a release:

```bash
gh release create TAG
```

**Purpose:** Helps manage software releases and versioned project distributions.

## 4. GitHub Aliases

List aliases:

```bash
gh alias list
```

Create an alias:

```bash
gh alias set ALIAS 'COMMAND'
```

**Purpose:** Creates shortcuts for frequently used GitHub CLI commands.

## 5. Search GitHub Repositories

```bash
gh search repos "github cli" --limit 5
```

**Purpose:** Searches GitHub repositories directly from the terminal.

*Practical exploration of these commands is still pending.*

---

# Useful GitHub CLI Commands Reference

| Command | Purpose |
|---|---|
| `gh --version` | Check GitHub CLI version |
| `gh auth login` | Authenticate with GitHub |
| `gh auth status` | Check authentication status |
| `gh api user --jq .login` | Verify active account |
| `gh repo create` | Create a repository |
| `gh repo clone` | Clone a repository |
| `gh repo view` | View repository details |
| `gh repo list` | List repositories |
| `gh repo view --web` | Open repository in browser |
| `gh repo delete` | Delete a repository |
| `gh issue create` | Create an issue |
| `gh issue list` | List issues |
| `gh issue view` | View an issue |
| `gh issue close` | Close an issue |
| `gh pr create` | Create a pull request |
| `gh pr list` | List pull requests |
| `gh pr view` | View a pull request |
| `gh pr diff` | View PR changes |
| `gh pr checks` | Check PR status |
| `gh pr checkout` | Check out a PR locally |
| `gh pr review` | Review a pull request |
| `gh pr merge` | Merge a pull request |
| `gh workflow list` | List workflows |
| `gh run list` | List workflow runs |
| `gh run view` | View workflow run details |
| `gh api` | Access GitHub API |
| `gh gist create` | Create a Gist |
| `gh release list` | List releases |
| `gh release create` | Create a release |
| `gh alias list` | List aliases |
| `gh alias set` | Create an alias |
| `gh search repos` | Search repositories |

---

# Git vs GitHub CLI

| Git | GitHub CLI (`gh`) |
|---|---|
| Version control system | Command-line interface for GitHub |
| `git add` | `gh issue create` |
| `git commit` | `gh pr create` |
| `git push` | `gh pr merge` |
| `git log` | `gh pr list` |
| Manages local and remote Git history | Manages GitHub features such as issues and PRs |

**Key Difference:** Git manages version control and repository history, while GitHub CLI provides terminal access to GitHub-specific features.

---

# What I Learned

1. GitHub CLI allows me to perform GitHub operations directly from the terminal.
2. I learned how to authenticate my GitHub account using `gh auth login`.
3. I practiced creating, cloning, viewing, and deleting repositories.
4. I learned how to create, list, view, and close GitHub issues.
5. I created a feature branch, pushed my changes, and opened a pull request using GitHub CLI.
6. I inspected PR changes, checked mergeability, and merged my pull request.
7. I learned that `gh` commands can be used in shell scripts to automate GitHub operations.
8. I understood how GitHub CLI can support DevOps workflows and CI/CD automation.

---

# Day 26 Progress

- [x] Task 1 – Install and authenticate GitHub CLI
- [x] Task 2 – Working with repositories
- [x] Task 3 – GitHub Issues
- [x] Task 4 – Pull Requests
- [ ] Task 5 – GitHub Actions and workflows
- [ ] Task 6 – Useful GitHub CLI tricks
- [ ] Update `git-commands.md`
- [ ] Push Day 26 notes to my fork

---

# Final Reflection

GitHub CLI makes it possible to manage GitHub directly from the terminal.

During this practice, I created a repository, managed an issue, created a pull request, reviewed the changes, and successfully merged the PR without performing the main operations manually in the GitHub browser.

This is useful for DevOps engineers because GitHub CLI commands can be integrated into shell scripts, automation tasks, and CI/CD workflows.

**Day 26: Tasks 1–4 completed. Tasks 5–6 and submission updates pending.**

#90DaysOfDevOps #DevOpsKaJosh #TrainWithShubham #GitHubCLI #Git #GitHub #DevOps #Linux #CloudComputing #Automation #CICD
# Day 25 – Git Reset vs Revert & Branching Strategies

## Objective

Today I learned how to **undo mistakes** in Git using `reset` and `revert`, and I studied the **branching strategies** that real engineering teams use. I practiced in my `devops-git-practice` repository and documented what I actually observed.

> **Repository note:** My default branch is `master`.

---

# Task 1 – Git Reset

## Create three commits (A, B, C)

```bash
touch file-prac.txt
git add .
git commit -m "first commit"

echo "first line" >> file-prac.txt
git add .
git commit -m "second commit"

echo "second line" >> file-prac.txt
git add .
git commit -m "third commit"
```

Check the history:

```bash
git log --oneline
```

---

## `git reset --soft`

```bash
git reset --soft d26c685
git status
```

**Observation**

- `HEAD` moved back one commit.
- The changes from the removed commit stayed **staged** (ready to commit).

I recommitted them:

```bash
git commit -m "after soft commit"
```

---

## `git reset --mixed`

```bash
git reset --mixed d26c685
git status
```

**Observation**

- `HEAD` moved back one commit.
- The changes became **unstaged**.
- The files were still modified, so no work was lost.

I staged and committed again:

```bash
git add .
git commit -m "after mixed commmit"
```

---

## `git reset --hard`

```bash
git reset --hard d26c685
git status
git log --oneline
```

**Observation**

- `HEAD` moved back one commit.
- Staged changes and working-tree changes were both **discarded**.
- The file went back to exactly how it was at that commit.

---

## Reset comparison

| Mode | HEAD | Staging area | Working directory |
|---|---|---|---|
| `--soft` | Moves | Keeps changes staged | Keeps changes |
| `--mixed` (default) | Moves | Unstages changes | Keeps changes |
| `--hard` | Moves | Clears | Discards changes |

## Answers

**What is the difference between `--soft`, `--mixed`, and `--hard`?**
All three move `HEAD` (and the branch) back to the chosen commit. They differ in what happens to the changes from the removed commits:
- `--soft`: changes stay staged.
- `--mixed`: changes stay in the files but are unstaged.
- `--hard`: changes are thrown away completely.

**Which one is destructive and why?**
`--hard`. It deletes uncommitted work from both the staging area and the working directory, and Git cannot bring back uncommitted changes. (The commits themselves can often still be recovered with `git reflog`, see below.)

**When would I use each one?**
- `--soft`: I want to redo a commit, for example fix the message or combine the last few commits into one.
- `--mixed`: I committed too early and want to re-select which files go into the commit.
- `--hard`: I want to completely throw away local experiments and return to a clean known state.

**Should I ever use `git reset` on commits that are already pushed?**
No. Reset rewrites history, so teammates who already pulled those commits would have a different history than the remote. For pushed commits, use `git revert`. The only exception is a private branch that nobody else uses, and even then it needs a force push (`git push --force-with-lease`).

## Safety net: `git reflog`

`git reflog` records every place `HEAD` has been, even after a `--hard` reset.

```bash
git reflog
git reset --hard HEAD@{1}      # go back to where HEAD was before the reset
```

This is how I can recover commits that look "lost" after a hard reset.

---

# Task 2 – Git Revert

## Create three commits (X, Y, Z)

```bash
touch revert.txt
git add .
git commit -m "x"

touch revert1.txt
git add .
git commit -m "y"

touch revert2.txt
git add .
git commit -m "z"
```

History:

```bash
git log --oneline
```

---

## Revert the middle commit (Y)

```bash
git revert 6eac539
git log --oneline
```

**Observation**

- Commit **Y** is **still in the history**.
- Git created a **new commit** (`Revert "y"`) that undoes the changes made by Y.
- Commits X and Z were untouched.

## Answers

**How is `git revert` different from `git reset`?**
`reset` moves the branch pointer backwards and removes commits from the branch history. `revert` leaves history alone and adds a new commit that applies the opposite of an earlier commit.

**Why is revert safer for shared branches?**
Revert only adds a commit, so nobody's existing history changes. Teammates can pull normally with no conflicts caused by rewritten history, and the full audit trail (what was changed, and that it was undone) remains visible.

**When would I use revert vs reset?**
- Revert: the bad commit is already pushed or shared (for example a bug reached `main`).
- Reset: the commits are only on my local machine and I want to clean them up before pushing.

---

# Task 3 – Reset vs Revert Summary

| | `git reset` | `git revert` |
|---|---|---|
| **What it does** | Moves `HEAD` and the branch pointer back to an earlier commit | Creates a new commit that undoes the changes of a chosen commit |
| **Removes commit from history?** | Yes, the commits disappear from the branch history | No, the original commit stays; a new revert commit is added |
| **Safe for shared/pushed branches?** | No, it rewrites history and needs a force push | Yes, it only adds history |
| **When to use** | Cleaning up local, unpushed commits | Undoing a commit that is already pushed or shared |

---

# Task 4 – Branching Strategies

## 1. GitFlow

**How it works:**
There are two long-lived branches: `main` (production code) and `develop` (integration of the next release). Work happens on short-lived branches:
- `feature/*` branch from `develop` and merge back into `develop`.
- `release/*` branch from `develop` to stabilise a release, then merge into `main` and back into `develop`.
- `hotfix/*` branch from `main` to fix production bugs, then merge into both `main` and `develop`.

**Flow:**

```text
main     ●─────────────────●───────────●────────▶   (tagged releases)
          \               / \         /
release    \         ●───●   \       /
            \       /         \     /
develop      ●──●──●──●────────●───●──────────────▶
              \  /  \  /
feature        ●●    ●●
hotfix                    ●──● (from main, merged to main + develop)
```

**Used in:** Products with scheduled, versioned releases (mobile apps, desktop software, enterprise software that supports several versions).

**Pros:**
- Clear structure and roles for every branch.
- Good for planned releases and supporting multiple versions.
- Hotfixes are isolated from ongoing development.

**Cons:**
- Many branches to manage, which is complex.
- Long-lived branches cause big, painful merges.
- Slows down continuous delivery.

---

## 2. GitHub Flow

**How it works:**
One main branch (`main`) that is always deployable. For any change: create a branch from `main`, commit, open a Pull Request, get review and passing checks, merge into `main`, then deploy.

**Flow:**

```text
main     ●──────────●──────────●──────▶
          \        / \        /
feature    ●──●──●    ●──●──●
           (PR + review)  (PR + review)
```

**Used in:** Web apps and SaaS products with continuous delivery, and many open-source projects.

**Pros:**
- Simple and easy to learn.
- Works well with pull requests, code review and CI/CD.
- Fast release cycle.

**Cons:**
- Not suited to managing several release versions at once.
- `main` must always be stable, so it needs good automated tests.
- Less structure for larger or regulated releases.

---

## 3. Trunk-Based Development

**How it works:**
Everyone integrates into a single branch (the "trunk", usually `main`) very frequently, at least daily. Branches, if used at all, live only hours or a day or two. Unfinished work is hidden behind **feature flags** so it can be merged without being visible to users.

**Flow:**

```text
Developer A ─┐
Developer B ─┼──► main ──► CI/CD ──► production
Developer C ─┘
(tiny, short-lived branches, merged within a day)
```

**Used in:** Teams with strong CI/CD and automated testing that deploy many times per day (large tech companies).

**Pros:**
- Very few merge conflicts because changes are small and frequent.
- Fastest feedback and delivery.
- Simple branch model.

**Cons:**
- Needs strong automated tests and CI discipline.
- Needs feature flags to hide unfinished work.
- A mistake on trunk affects everyone quickly.

---

## Comparison

| | GitFlow | GitHub Flow | Trunk-Based |
|---|---|---|---|
| Long-lived branches | `main` + `develop` | `main` | `main` only |
| Branch lifetime | Days to weeks | Hours to days | Hours |
| Release style | Scheduled | Continuous | Continuous, very frequent |
| Complexity | High | Low | Low (needs discipline) |

## Answers

**Which strategy for a startup shipping fast?**
**GitHub Flow** (or trunk-based once CI is strong). It is simple, supports quick pull request reviews, and lets the team deploy `main` whenever something is ready.

**Which strategy for a large team with scheduled releases?**
**GitFlow.** Release branches let the team stabilise a version while new features continue on `develop`, and hotfix branches let them patch production without disturbing that work.

**Which strategy does a favourite open-source project use?**
I checked the **Kubernetes** repository (`github.com/kubernetes/kubernetes`). Development happens on the default branch through pull requests (mostly from forks), and separate `release-1.x` branches are used for each released version. Fixes are backported to those release branches by cherry-picking, which connects back to what I learned on Day 24.

> ✏️ Before submitting, open the repo yourself, click the branch dropdown and look at the branch names, so this answer comes from what you actually see. You can also check React or the Linux kernel.

---

# Task 5 – Git Commands Reference Update

I updated `git-commands.md` to cover Days 22 to 25 (setup, basic workflow, branching, remote, merging and rebasing, stash and cherry-pick, reset and revert). The new commands from today:

```bash
# Reset
git reset --soft <commit>
git reset --mixed <commit>
git reset --hard <commit>

# Revert
git revert <commit>

# Safety net
git reflog
```

---

# Commands Practiced Today

```bash
git reset --soft <commit>
git reset --mixed <commit>
git reset --hard <commit>
git revert <commit>
git reflog
git log --oneline
git status
```

---

# Mistakes I Made

| Mistake | Fix |
|---|---|
| `git commmit` | `git commit` |
| `git log --online` | `git log --oneline` |
| `git staus` | `git status` |

These errors helped me remember the correct command names.

---

# Key Takeaways

1. **Reset** rewrites local history; **Revert** preserves history by creating a new commit.
2. `--soft`, `--mixed`, and `--hard` differ in how they treat the staging area and working tree. `--hard` is the only one that destroys work.
3. Never rewrite shared history with `git reset`; use `git revert` instead.
4. `git reflog` can recover commits that seem lost after a reset.
5. Choose a branching strategy to fit the team: GitFlow for scheduled releases, GitHub Flow for simple continuous delivery, Trunk-Based for very frequent integration with strong CI.

---

# Practice Summary

| Task | Status |
|---|---|
| Reset (`--soft`, `--mixed`, `--hard`) | Completed |
| Revert | Completed |
| Reset vs Revert comparison | Completed |
| Branching strategies review | Completed |
| Git command reference update | Completed |

**Day 25 completed.**

`#90DaysOfDevOps` `#DevOpsKaJosh` `#TrainWithShubham`
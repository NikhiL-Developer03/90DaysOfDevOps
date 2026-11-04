# Day 24 – Advanced Git: Merge, Rebase, Stash & Cherry Pick

## Objective

Today I practiced how branches come back together (merge, rebase, squash), how to save unfinished work (stash), and how to copy a single commit to another branch (cherry-pick).

Useful command for every task:

```bash
git log --oneline --graph --all
```

---

# Task 1: Git Merge

## 1. Fast-forward merge

```bash
git switch main
git switch -c feature-login
echo "login line 1" >> login.txt && git add . && git commit -m "login: add line 1"
echo "login line 2" >> login.txt && git add . && git commit -m "login: add line 2"
git switch main
git merge feature-login
```

**My output:**

```text
<paste the output of git merge feature-login here>
```

**Observation:** `<Was it Fast-forward or a merge commit? Why?>`

## 2. Merge commit (diverged branches)

```bash
git switch -c feature-signup
echo "signup 1" >> signup.txt && git add . && git commit -m "signup: add file"
echo "signup 2" >> signup.txt && git add . && git commit -m "signup: add line"
git switch main
echo "main change" >> main-notes.txt && git add . && git commit -m "main: add notes"
git merge feature-signup
```

**My output:**

```text
<paste output and git log --oneline --graph --all here>
```

**Observation:** `<What was different this time?>`

## 3. Intentional merge conflict

```bash
echo "line from main" > conflict.txt && git add . && git commit -m "main: conflict.txt"
git switch -c feature-conflict HEAD~1
echo "line from feature" > conflict.txt && git add . && git commit -m "feature: conflict.txt"
git switch main
git merge feature-conflict
```

Git stops with a conflict. Open `conflict.txt`, you will see:

```text
<<<<<<< HEAD
line from main
=======
line from feature
>>>>>>> feature-conflict
```

Fix it by keeping the content you want and deleting the marker lines, then:

```bash
git add conflict.txt
git commit -m "Resolve conflict in conflict.txt"
```

**My output:**

```text
<paste the CONFLICT message here>
```

## Answers

**What is a fast-forward merge?**
It happens when the target branch has no new commits of its own since the feature branch was created. Git just moves the branch pointer forward, and no new commit is created.

**When does Git create a merge commit?**
When both branches have new commits (they have diverged). Git combines the two histories and records the result in a new commit with two parents.

**What is a merge conflict?**
It happens when both branches change the same part of the same file (or one deletes what the other edits) and Git cannot decide which version to keep. You resolve it manually, `git add` the file, and commit.

---

# Task 2: Git Rebase

```bash
git switch main
git switch -c feature-dashboard
echo "d1" >> dash.txt && git add . && git commit -m "dashboard 1"
echo "d2" >> dash.txt && git add . && git commit -m "dashboard 2"
echo "d3" >> dash.txt && git add . && git commit -m "dashboard 3"

git switch main
echo "main moved ahead" >> main-ahead.txt && git add . && git commit -m "main: moved ahead"

git switch feature-dashboard
git log --oneline --graph --all      # BEFORE rebase
git rebase main
git log --oneline --graph --all      # AFTER rebase
```

**Graph before rebase:**

```text
<paste here>
```

**Graph after rebase:**

```text
<paste here>
```

**Observation:** `<Compare the history with the merge graphs from Task 1.>`

## Answers

**What does rebase do to my commits?**
It takes the commits unique to my branch and replays them one by one on top of the target branch's latest commit. The replayed commits are brand new commits with new hashes (`D` becomes `D'`).

**How is the history different from a merge?**

```text
Merge:    A---B---C---M          Rebase:   A---B---C---D'---E'
               \     /
                D---E
```

Merge keeps the real branching and adds a merge commit. Rebase gives a straight, linear history with no merge commit.

**Why never rebase commits that are pushed and shared?**
Rebase rewrites history by creating new commit hashes. Teammates who already have the old commits will see their history and the remote history disagree, which leads to duplicate commits, painful conflicts and the need for force-push.

**Rebase vs merge?**
- Rebase: to update my own local feature branch with the latest `main` and keep history clean, before it is shared.
- Merge: for shared branches, or when I want to preserve the true history of how work was integrated.

---

# Task 3: Squash Merge vs Regular Merge

## Squash merge

```bash
git switch main
git switch -c feature-profile
echo "p1" >> profile.txt && git add . && git commit -m "profile: create"
echo "typo fix" >> profile.txt && git add . && git commit -m "profile: fix typo"
echo "format" >> profile.txt && git add . && git commit -m "profile: formatting"
echo "p4" >> profile.txt && git add . && git commit -m "profile: add field"
echo "p5" >> profile.txt && git add . && git commit -m "profile: final tweak"

git switch main
git merge --squash feature-profile
git status                      # changes are staged, nothing committed yet
git commit -m "Add profile feature"
git log --oneline
```

**My output:**

```text
<paste git log --oneline here>
```

**Commits added to main:** `<number>`

## Regular merge

```bash
git switch main
git switch -c feature-settings
echo "s1" >> settings.txt && git add . && git commit -m "settings 1"
echo "s2" >> settings.txt && git add . && git commit -m "settings 2"
echo "s3" >> settings.txt && git add . && git commit -m "settings 3"

git switch main
echo "extra" >> extra.txt && git add . && git commit -m "main: extra"   # forces a merge commit
git merge feature-settings
git log --oneline --graph --all
```

**My output:**

```text
<paste graph here>
```

## Answers

**What does squash merging do?**
It takes all the changes from the feature branch and stages them as one set of changes on the target branch. I then make a single commit. The original commits are not brought over.

**Squash vs regular merge?**
- Squash: feature branches with many tiny or messy commits (typo fixes, "wip"), when I want one clean commit per feature on `main`.
- Regular merge: when the individual commits are meaningful and I want to keep the full history.

**Trade-off of squashing?**
`main` is cleaner, but I lose the detailed step-by-step history, individual authorship per commit, and the ability to bisect or revert at a fine-grained level. The feature branch also is not shown as merged in the graph.

---

# Task 4: Git Stash

```bash
git switch -c feature-stash-demo
echo "base" >> work.txt && git add . && git commit -m "work: base"

echo "unfinished change" >> work.txt        # do NOT commit
git switch main                            # try to switch
```

**What happened when I tried to switch?**

```text
<paste the error here, if Git blocked it>
```

> Note: Git only blocks the switch if the other branch has a different version of the changed file. If it does not block, edit a file that differs between the two branches.

```bash
git stash push -m "WIP: unfinished change"
git switch main
echo "urgent fix" >> urgent.txt && git add . && git commit -m "urgent fix"
git switch feature-stash-demo
git stash pop
```

## Multiple stashes

```bash
echo "change 1" >> work.txt && git stash push -m "stash one"
echo "change 2" >> work.txt && git stash push -m "stash two"
git stash list
git stash apply stash@{1}      # apply a specific one, keeps it in the list
git stash drop stash@{1}       # remove it manually
```

**My `git stash list` output:**

```text
<paste here>
```

## Answers

**`git stash pop` vs `git stash apply`?**
- `pop`: applies the stash and removes it from the stash list.
- `apply`: applies the stash but keeps it in the list, so I can reuse it on another branch.

If `pop` hits a conflict, the stash is kept.

**When would I use stash in a real workflow?**
When I am halfway through a feature and a teammate or production needs an urgent fix on another branch. I stash my work, fix the issue, come back and `pop`. It is also handy for pulling the latest changes when I have uncommitted edits.

---

# Task 5: Cherry-Pick

```bash
git switch main
git switch -c feature-hotfix
echo "fix 1" >> hotfix.txt && git add . && git commit -m "hotfix 1"
echo "fix 2" >> hotfix2.txt && git add . && git commit -m "hotfix 2"
echo "fix 3" >> hotfix3.txt && git add . && git commit -m "hotfix 3"
git log --oneline                # copy the hash of "hotfix 2"

git switch main
git cherry-pick <hash-of-hotfix-2>
git log --oneline
```

> Tip: use a different file for each commit (as above) so cherry-pick applies cleanly. If all three commits edit the same file, the second commit may depend on the first and cause a conflict.

**My output:**

```text
<paste git log --oneline from main here>
```

**Observation:** `<Only one commit was added. Its hash is different from the original. Why?>`

If a conflict happens:

```bash
# fix the file
git add <file>
git cherry-pick --continue      # or --abort to cancel
```

## Answers

**What does cherry-pick do?**
It takes the changes from one specific commit and applies them as a new commit on my current branch, without merging the rest of that branch. The new commit has a different hash.

**When would I use it in a real project?**
- Applying a bug fix from a feature branch to `main` or a release branch.
- Backporting a fix to an older release.
- Rescuing one useful commit from an abandoned branch.

**What can go wrong?**
- Conflicts if the target branch differs in the same code.
- Missing dependencies: the commit may rely on earlier commits that were not picked.
- Duplicate commits: the same change exists twice with different hashes, which can cause confusing conflicts when the branches are merged later.
- History becomes harder to follow.

---

# Commands Practiced

```bash
# Merge
git merge <branch>
git merge --squash <branch>

# Rebase
git rebase <branch>

# Stash
git stash
git stash push -m "message"
git stash list
git stash pop
git stash apply stash@{n}
git stash drop stash@{n}

# Cherry-pick
git cherry-pick <commit-hash>
git cherry-pick --continue
git cherry-pick --abort

# History
git log --oneline --graph --all
```

---

# Mistakes and Lessons

| Mistake | What I learned |
|---|---|
| Ran `git rebase main` when my default branch was named `master` (`fatal: invalid upstream`) | Always check the real branch name with `git branch` before rebasing or merging. |
| Tried `git stash pop <commit-hash>` | Stashes are referenced as `stash@{0}`, `stash@{1}`, not by commit hash. Use `git stash list` to see them. |
| Ran `git cherry-pick <hash>` again while a cherry-pick conflict was still in progress | Fix the file, `git add` it, then run `git cherry-pick --continue` (or `--abort` to cancel). |
| Left conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) in the file and committed | Always remove the markers and check the file before `git add`. |
| Used `git merge --squash` and thought it was finished | Squash only stages the changes. I still have to run `git commit` myself. |
| Tried to switch branches with uncommitted changes and Git refused | Commit or `git stash` first, then switch. |
| Typos such as `git cherr-pick` or `git log --online` | Git commands are exact. Read the error, spot the typo, and retype. |

---

# Merge vs Rebase vs Squash vs Stash vs Cherry-Pick

| Operation | Purpose |
|---|---|
| `git merge` | Combine two branch histories, keeping the real history |
| `git rebase` | Replay my commits on a new base for a linear history |
| `git merge --squash` | Turn many commits into one new commit on the target |
| `git stash` | Temporarily save uncommitted work |
| `git cherry-pick` | Copy one specific commit to the current branch |

---

# Day 24 Summary

Key takeaway: Git has a different tool for each situation. I use **merge** to combine branches and keep the real history, **rebase** to keep my own branch linear and up to date, **squash** to turn many small commits into one clean commit, **stash** to save unfinished work before switching branches, and **cherry-pick** to copy a single commit. The command that made everything clear was `git log --oneline --graph --all`, because it shows how branches split, merge and move.

```text
Combine branches          -> MERGE
Keep history linear       -> REBASE
Many small commits -> one -> SQUASH
Save unfinished work      -> STASH
Take one specific commit  -> CHERRY-PICK
```
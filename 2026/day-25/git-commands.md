# Git Commands Reference (Days 22–25)

## 1. Setup & Config

```bash
git --version                                   # check installed version
git config --global user.name "Your Name"       # set username
git config --global user.email "you@example.com" # set email
git config --list                               # view configuration
```

## 2. Basic Workflow

```bash
git init                      # create a new repository
git status                    # show state of files
git status --short            # compact status
git add <file>                # stage a file
git add .                     # stage everything in the current directory
git commit -m "message"       # save staged changes
git diff                      # unstaged changes
git diff --staged             # staged changes
git diff HEAD                 # working tree vs latest commit
git log                       # full history
git log --oneline             # compact history
git log --oneline --graph --all   # visual branch history
git show HEAD                 # inspect the latest commit
git restore <file>            # discard unstaged changes in a file (careful!)
git ls-files                  # list tracked files
```

## 3. Branching

```bash
git branch                    # list branches
git branch <name>             # create a branch
git switch <name>             # switch to a branch
git switch -c <name>          # create and switch
git checkout <name>           # older way to switch
git checkout -b <name>        # older way to create and switch
git branch -d <name>          # delete a merged branch
git branch -D <name>          # force delete
git branch -M main            # rename current branch to main
```

## 4. Remote

```bash
git remote -v                             # list remotes
git remote add origin <url>               # add a remote
git remote add upstream <url>             # add the original repo of a fork
git push -u origin <branch>               # push and set upstream tracking
git push origin <branch>                  # push
git fetch origin                          # download changes, do not merge
git pull origin <branch>                  # fetch + merge
git clone <url>                           # copy a repository locally
# Fork = GitHub feature (button on GitHub), then clone your fork
```

Keep a fork in sync:

```bash
git fetch upstream
git switch main
git merge upstream/main
git push origin main
```

## 5. Merging & Rebasing

```bash
git merge <branch>            # merge a branch into the current one
git merge --squash <branch>   # stage all changes as one (then git commit)
git merge --abort             # cancel a merge in progress
git rebase <branch>           # replay current branch commits on top of <branch>
git rebase --abort            # cancel a rebase
```

Resolve a conflict:

```bash
# edit the file, remove <<<<<<< ======= >>>>>>> markers
git add <file>
git commit
```

## 6. Stash & Cherry-Pick

```bash
git stash                         # save uncommitted work
git stash push -m "message"       # save with a message
git stash list                    # list stashes
git stash pop                     # apply latest stash and remove it
git stash apply stash@{n}         # apply a stash and keep it
git stash drop stash@{n}          # delete a stash

git cherry-pick <hash>            # apply one commit to the current branch
git cherry-pick --continue        # after fixing a conflict
git cherry-pick --abort           # cancel
```

## 7. Reset & Revert

```bash
git reset --soft <commit>     # move HEAD, keep changes staged
git reset --mixed <commit>    # move HEAD, keep changes unstaged (default)
git reset --hard <commit>     # move HEAD, discard all changes (destructive)
git revert <commit>           # new commit that undoes a commit (safe for shared branches)
git reflog                    # history of where HEAD has been (recovery tool)
```

## Quick Rules

- Pushed/shared commits: use `git revert`, not `git reset`.
- Never rebase commits that others already have.
- `git reset --hard` destroys uncommitted work. Check `git status` first.
- `git reflog` can often recover commits after a mistake.
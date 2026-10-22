# Day 11 – File Ownership Challenge (`chown` & `chgrp`)

## Objective
Learn how Linux file ownership works and practice changing file owners and groups with `chown`, `chgrp`, and recursive ownership changes.

## Task 1 – Understanding Ownership

### List files
```bash
ls -l
```

Example format:
```text
-rw-r--r-- 1 ubuntu ubuntu 120 Oct 22 notes.txt
```

The columns include permissions, link count, **owner**, **group**, size, modification date/time, and filename.

- **Owner:** the user account that owns the file.
- **Group:** the group associated with the file; group permissions apply to users who belong to that group.

### Observation
The owner identifies the user account that owns a file. The group identifies the associated group used when evaluating group permissions.

## Task 2 – Basic `chown` Operations

### Create a file and check its current owner
```bash
touch devops-file.txt
ls -l devops-file.txt
```

### Change owner to `tokyo`, then `berlin`
```bash
sudo chown tokyo devops-file.txt
ls -l devops-file.txt

sudo chown berlin devops-file.txt
ls -l devops-file.txt
```

### Observation
The file's owner changes first to `tokyo` and then to `berlin`. The group is not explicitly changed by these commands.

## Task 3 – Basic `chgrp` Operations

### Create a file and inspect it
```bash
touch team-notes.txt
ls -l team-notes.txt
```

### Create a group and change the file's group
This version uses `money-heist`, matching the supplied reference notes.

```bash
sudo groupadd money-heist
sudo chgrp money-heist team-notes.txt
ls -l team-notes.txt
```

### Observation
The file's group ownership is changed to `money-heist`.

> Note: The original challenge specifies `heist-team`, while the reference notes use `money-heist`. Use the same group name consistently. If you already created `money-heist`, do not create a different group unless you intend to.

## Task 4 – Change Owner and Group Together

### Create the configuration file
```bash
touch project-config.yaml
```

### Change both owner and group
```bash
sudo chown professor:money-heist project-config.yaml
ls -l project-config.yaml
```

### Create the application logs directory and change ownership
```bash
mkdir app-logs
sudo chown berlin:money-heist app-logs
ls -ld app-logs
```

`ls -ld` displays the directory itself, including its owner and group.

### Observation
The owner and group are set together using the `owner:group` syntax.

## Task 5 – Recursive Ownership

### Create the directory structure and files
```bash
mkdir -p heist-project/vault
mkdir -p heist-project/plans

touch heist-project/vault/gold.txt
touch heist-project/plans/strategy.conf
```

### Create the group
```bash
sudo groupadd planners
```
If the group already exists, continue.

### Change ownership recursively
```bash
sudo chown -R professor:planners heist-project/
```

- `-R` means recursive.
- The change applies to `heist-project/` and the files and subdirectories inside it.

### Verify
```bash
ls -lR heist-project/
ls -ld heist-project/
```

### Observation
The main directory, subdirectories, and files should show `professor` as owner and `planners` as group.

## Task 6 – Practice Challenge

### Create groups
```bash
sudo groupadd vault-team
sudo groupadd tech-team
```
If either group already exists, continue.

### Create the directory and files
```bash
mkdir -p bank-heist

touch bank-heist/access-codes.txt
touch bank-heist/blueprints.pdf
touch bank-heist/escape-plan.txt
```

### Set ownership for each file
```bash
sudo chown tokyo:vault-team bank-heist/access-codes.txt
sudo chown berlin:tech-team bank-heist/blueprints.pdf
sudo chown nairobi:vault-team bank-heist/escape-plan.txt
```

### Verify
```bash
ls -l bank-heist/
```

Expected ownership:

| File | Owner | Group |
|---|---|---|
| `access-codes.txt` | `tokyo` | `vault-team` |
| `blueprints.pdf` | `berlin` | `tech-team` |
| `escape-plan.txt` | `nairobi` | `vault-team` |

### Troubleshooting note
Linux filenames must match exactly. If you create `acces-codes.txt` but run `chown` on `access-codes.txt`, Linux reports that the file cannot be found. Check spelling with `ls -l bank-heist/`, or rename the file with `mv` so its name matches.

## Key Commands Reference

| Command | Purpose |
|---|---|
| `ls -l FILE` | View file permissions, owner, and group |
| `ls -ld DIRECTORY` | View the directory itself and its ownership |
| `sudo chown USER FILE` | Change the owner |
| `sudo chgrp GROUP FILE` | Change the group |
| `sudo chown USER:GROUP FILE` | Change both owner and group |
| `sudo chown -R USER:GROUP DIRECTORY/` | Change ownership recursively |
| `sudo chown :GROUP FILE` | Change only the group using `chown` |

## Commands Used – Combined Reference

```bash
ls -l

touch devops-file.txt
sudo chown tokyo devops-file.txt
sudo chown berlin devops-file.txt
ls -l devops-file.txt

touch team-notes.txt
sudo groupadd money-heist
sudo chgrp money-heist team-notes.txt
ls -l team-notes.txt

touch project-config.yaml
sudo chown professor:money-heist project-config.yaml
mkdir app-logs
sudo chown berlin:money-heist app-logs
ls -ld app-logs

mkdir -p heist-project/vault
mkdir -p heist-project/plans
touch heist-project/vault/gold.txt
touch heist-project/plans/strategy.conf
sudo groupadd planners
sudo chown -R professor:planners heist-project/
ls -lR heist-project/

sudo groupadd vault-team
sudo groupadd tech-team
mkdir -p bank-heist
touch bank-heist/access-codes.txt
touch bank-heist/blueprints.pdf
touch bank-heist/escape-plan.txt
sudo chown tokyo:vault-team bank-heist/access-codes.txt
sudo chown berlin:tech-team bank-heist/blueprints.pdf
sudo chown nairobi:vault-team bank-heist/escape-plan.txt
ls -l bank-heist/
```

## Verification Checklist
- ✅ Identified owner and group columns using `ls -l`
- ✅ Changed `devops-file.txt` owner to `tokyo`, then `berlin`
- ✅ Changed `team-notes.txt` group to `money-heist`
- ✅ Changed owner and group for `project-config.yaml`
- ✅ Changed owner and group for `app-logs/`
- ✅ Applied recursive ownership to `heist-project/`
- ✅ Set the specified ownership on all files in `bank-heist/`
- ✅ Captured screenshots showing ownership changes

## Key Learnings
1. Every file and directory has an owner and an associated group.
2. `chown` changes ownership, while `chgrp` changes group ownership.
3. `chown USER:GROUP FILE` changes both at once, and `chown -R` applies changes recursively to a directory tree.
4. `ls -l` and `ls -ld` help verify the results.

## Why This Matters for DevOps
Correct ownership is important for application deployments, shared team directories, container file access, CI/CD pipeline artifacts, and log management.

## Conclusion
This challenge practiced viewing and changing Linux file ownership, changing groups, setting owner and group together, and applying ownership recursively. These are useful Linux administration skills for managing access to files and directories on servers.



# Day 09 – Linux User & Group Management Challenge

## Objective

Practice Linux user and group management by creating users, assigning groups, configuring shared directories, and verifying permissions.

## Task 1 – Create Users

### Create users

```bash
sudo useradd -m tokyo
sudo useradd -m berlin
sudo useradd -m professor
```

### Set passwords

```bash
sudo passwd tokyo
sudo passwd berlin
sudo passwd professor
```

### Verify users and home directories

```bash
grep -E 'tokyo|berlin|professor' /etc/passwd
ls -l /home
```

**What to check:** Each user should appear in `/etc/passwd`, and their home directories should exist under `/home`.

## Task 2 – Create Groups

### Create groups

```bash
sudo groupadd developers
sudo groupadd admins
```

### Verify groups

```bash
grep -E 'developers|admins' /etc/group
```

**What to check:** Both `developers` and `admins` should appear in `/etc/group`.

## Task 3 – Assign Users to Groups

### Add users to groups

```bash
sudo usermod -aG developers tokyo
sudo usermod -aG developers,admins berlin
sudo usermod -aG admins professor
```

### Verify memberships

```bash
groups tokyo
groups berlin
groups professor
```

Or use:

```bash
id tokyo
id berlin
id professor
```

**Expected membership:**
- `tokyo` → `developers`
- `berlin` → `developers`, `admins`
- `professor` → `admins`

## Task 4 – Create a Shared Directory

### Create the directory

```bash
sudo mkdir -p /opt/dev-project
```

### Set the group owner

```bash
sudo chgrp developers /opt/dev-project
```

### Set permissions

```bash
sudo chmod 775 /opt/dev-project
```

### Verify ownership and permissions

```bash
ls -ld /opt/dev-project
```

Example format:

```text
drwxrwxr-x 2 root developers 4096 ... /opt/dev-project
```

### Test file creation as each user

```bash
sudo -u tokyo touch /opt/dev-project/tokyo-file.txt
sudo -u berlin touch /opt/dev-project/berlin-file.txt
```

Verify:

```bash
ls -l /opt/dev-project
```

**Observation:** Both users should be able to create files in the shared directory.

## Task 5 – Create a Team Workspace

### Create user and set password

```bash
sudo useradd -m nairobi
sudo passwd nairobi
```

### Create the group

```bash
sudo groupadd project-team
```

### Add users to the group

```bash
sudo usermod -aG project-team nairobi
sudo usermod -aG project-team tokyo
```

### Create the workspace

```bash
sudo mkdir -p /opt/team-workspace
sudo chgrp project-team /opt/team-workspace
sudo chmod 775 /opt/team-workspace
```

### Verify

```bash
groups nairobi
groups tokyo
ls -ld /opt/team-workspace
```

### Test file creation

```bash
sudo -u nairobi touch /opt/team-workspace/project.txt
ls -l /opt/team-workspace
```

**Observation:** Nairobi should be able to create a file inside the team workspace.

## Quick Command Reference

| Command | Purpose |
|---|---|
| `useradd -m USER` | Create a user and home directory |
| `passwd USER` | Set or change a user's password |
| `groupadd GROUP` | Create a group |
| `usermod -aG GROUP USER` | Add a user to a supplementary group |
| `groups USER` | Show a user's group memberships |
| `id USER` | Show user ID, primary group, and supplementary groups |
| `chgrp GROUP PATH` | Change the group owner of a file or directory |
| `chmod 775 PATH` | Give owner and group full permissions; others read and execute |
| `sudo -u USER COMMAND` | Run a command as another user |
| `ls -ld PATH` | Show a directory's permissions and ownership |
| `ls -l PATH` | List files with permissions and ownership |

## Understanding `chmod 775`

The permission value `775` means:

- **Owner (`7`)**: read, write, execute
- **Group (`7`)**: read, write, execute
- **Others (`5`)**: read, execute

For directories, execute permission allows a user to enter/traverse the directory. Write permission allows creating or removing directory entries, subject to other permission rules.

## Verification Checklist

- [ ] Created users: `tokyo`, `berlin`, `professor`, `nairobi`
- [ ] Created groups: `developers`, `admins`, `project-team`
- [ ] Assigned users to the intended groups
- [ ] Created `/opt/dev-project` and `/opt/team-workspace`
- [ ] Set group ownership on each directory
- [ ] Applied `775` permissions
- [ ] Tested file creation as `tokyo`, `berlin`, and `nairobi`

## Key Learnings

- Linux users and groups help organize accounts and control access.
- `useradd -m` creates a user with a home directory.
- `usermod -aG` adds supplementary group membership without replacing existing supplementary groups.
- `chgrp` sets a directory's group owner.
- `chmod 775` gives the owner and group read/write/execute access, while others receive read/execute access.
- `sudo -u` helps test permissions as another user without starting a separate login session.

## Conclusion

This challenge practiced creating Linux users and groups, assigning group memberships, setting shared-directory ownership and permissions, and testing access by creating files as different users. These are foundational Linux administration skills for managing collaborative access to files and directories.

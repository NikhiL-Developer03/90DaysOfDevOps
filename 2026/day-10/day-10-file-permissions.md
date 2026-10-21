# Day 10 – File Permissions & File Operations Challenge

## Objective
Practice creating and reading files in Linux, understanding permission bits, changing permissions with `chmod`, and testing how permissions affect file operations.

## Task 1 – Create Files

### Create an empty file
```bash
touch devops.txt
```

### Create `notes.txt` with content
```bash
echo "Linux file permissions are important." > notes.txt
echo "DevOps engineers work with configuration files." >> notes.txt
echo "Practice Linux commands every day." >> notes.txt
```
`>` creates or overwrites a file. `>>` appends content.

### Create a shell script
```bash
vim script.sh
```
Add this content:
```bash
#!/bin/bash
echo "Hello DevOps"
```
Save and exit Vim with `Esc`, then type `:wq` and press Enter.

### Verify
```bash
ls -l devops.txt notes.txt script.sh
```
New files commonly start with `rw-r--r--` (`644`), depending on the system's default `umask`. Use your actual `ls -l` output as the source of truth.

## Task 2 – Read Files

```bash
cat notes.txt
vim -R script.sh
head -n 5 /etc/passwd
tail -n 5 /etc/passwd
```

- `cat` displays the whole file.
- `vim -R` opens a file in read-only mode.
- `head -n 5` displays the first five lines.
- `tail -n 5` displays the last five lines.

The contents of `/etc/passwd` vary between systems.

## Task 3 – Understand Permissions

Check the current permissions:
```bash
ls -l devops.txt notes.txt script.sh
```

A permission string such as `-rw-r--r--` starts with a file-type character (`-` for a regular file, `d` for a directory), followed by three permission groups: owner, group, and others.

| Permission | Numeric value | Meaning |
|---|---:|---|
| `r` | 4 | Read |
| `w` | 2 | Write |
| `x` | 1 | Execute |

Examples:
- `644` → owner: read/write; group: read; others: read.
- `640` → owner: read/write; group: read; others: no permissions.
- `755` → owner: read/write/execute; group and others: read/execute.

## Task 4 – Modify Permissions

### Make `script.sh` executable
```bash
chmod +x script.sh
ls -l script.sh
./script.sh
```
Expected output:
```text
Hello DevOps
```

### Remove write permission from `devops.txt` for everyone
```bash
chmod a-w devops.txt
ls -l devops.txt
```
`a-w` removes write permission for owner, group, and others.

### Set `notes.txt` to `640`
```bash
chmod 640 notes.txt
ls -l notes.txt
```
Owner gets read/write, group gets read, and others get no permissions.

### Create `project/` with `755`
```bash
mkdir -p project
chmod 755 project
ls -ld project
```
`ls -ld` displays the directory itself.

## Task 5 – Test Permissions

### Try appending to the read-only file
```bash
echo "Testing" >> devops.txt
```
If you are the file owner and write permission has been removed, the shell should report a permission error. Exact wording varies. Test as your normal user; a privileged user may still be able to write.

### Try running a script without execute permission
```bash
chmod -x script.sh
ls -l script.sh
./script.sh
```
A typical result is `Permission denied`. Restore execute permission when needed:
```bash
chmod +x script.sh
```

## Commands Used

```bash
touch devops.txt
echo "Linux file permissions are important." > notes.txt
echo "DevOps engineers work with configuration files." >> notes.txt
echo "Practice Linux commands every day." >> notes.txt
vim script.sh
ls -l devops.txt notes.txt script.sh
cat notes.txt
vim -R script.sh
head -n 5 /etc/passwd
tail -n 5 /etc/passwd
chmod +x script.sh
./script.sh
chmod a-w devops.txt
chmod 640 notes.txt
mkdir -p project
chmod 755 project
ls -ld project
echo "Testing" >> devops.txt
chmod -x script.sh
./script.sh
```

## Files and Directory Created
- `devops.txt`
- `notes.txt`
- `script.sh`
- `project/`

## Key Learnings
1. `touch` creates an empty file, while `echo` can write or append text.
2. `cat`, `head`, and `tail` help inspect file contents; `vim -R` opens a file read-only.
3. Linux permissions control read, write, and execute access for owner, group, and others. `chmod` changes permissions using symbolic or numeric notation.

## Conclusion
This challenge provided practice with everyday Linux file operations and permission management. Understanding `chmod`, permission values, and access errors is an important foundation for working with Linux servers and DevOps tools.

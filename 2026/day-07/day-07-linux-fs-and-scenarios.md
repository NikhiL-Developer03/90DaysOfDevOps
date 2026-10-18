# Day 07 – Linux File System Hierarchy & Scenario Practice

## Part 1: Linux File System Hierarchy

Linux does not use drive letters like `C:` or `D:`. Everything starts from the root directory `/`.

| Directory | Purpose | I would use this when... |
|---|---|---|
| `/` | Starting point of the entire Linux file system. | I need to navigate to another directory. |
| `/home` | Home directories for normal users. | I need to access user files. |
| `/root` | Home directory of the root (administrator) user. | I need to understand where root's personal files are stored. |
| `/etc` | System-wide configuration files. | I need to check a system or service configuration. |
| `/var/log` | Many system and application log files. | I need to investigate errors. |
| `/tmp` | Temporary files. | I need a temporary place for testing. |
| `/bin` | Essential commands; on many modern systems it links to `/usr/bin`. | I need to understand where essential commands are located. |
| `/usr/bin` | Many installed commands and programs. | I need to locate an installed command. |
| `/opt` | Often used for optional or third-party applications. | I need to find software installed outside standard locations. |

### Inspect the directories

Run these commands and note one or two entries you actually see. Results vary between systems.

```bash
ls -l /
ls -l /home
ls -l /root
ls -l /etc
ls -l /var/log
ls -l /tmp
ls -l /bin
ls -l /usr/bin
ls -l /opt
```

Note: `ls -l /root` may show `Permission denied` as a normal user.

### Hands-on practice

**Find the five largest entries under `/var/log`:**

```bash
du -sh /var/log/* 2>/dev/null | sort -h | tail -5
```

**Check the hostname:**

```bash
cat /etc/hostname
```

**List your home directory, including hidden files:**

```bash
ls -la ~
```

`~` means your current user's home directory.

---

## Part 2: Scenario-Based Practice

Troubleshooting flow: **check → inspect details/logs → identify the cause → make a change only when you understand the issue → verify.**

### Scenario 1: A service will not start

**Step 1 — Check status**

```bash
systemctl status myapp
```

Why: Shows whether the service is active, failed, or missing.

**Step 2 — Check recent logs**

```bash
journalctl -u myapp -n 50
```

Why: Looks for service error messages.

**Step 3 — Check whether it starts at boot**

```bash
systemctl is-enabled myapp
```

Why: Checks whether it is enabled to start automatically.

**Step 4 — Verify after investigating**

```bash
systemctl status myapp
```

Do not restart or change a service until you have investigated the cause and decided it is safe. In WSL, `systemctl` may not work if systemd is not running.

### Scenario 2: High CPU usage

**Step 1 — View live processes**

```bash
top
```

Why: Shows processes and changing CPU usage. Press `q` to quit.

**Step 2 — List the top CPU-consuming processes**

```bash
ps aux --sort=-%cpu | head -10
```

Why: Shows the first 10 process entries sorted by CPU usage.

**Step 3 — Inspect one process**

```bash
ps -fp <PID>
```

Replace `<PID>` with the actual process ID.

Remember: Do not kill a process just because its CPU usage is high. First understand what it does.

### Scenario 3: Find Docker service logs

**Step 1 — Check status**

```bash
systemctl status docker
```

**Step 2 — Read the latest 50 log lines**

```bash
journalctl -u docker -n 50
```

**Step 3 — Follow new logs live**

```bash
journalctl -u docker -f
```

Press `Ctrl+C` to stop following logs.

`journalctl -u` filters logs for a systemd service. These commands require systemd and the Docker service to be available.

### Scenario 4: A script shows “Permission denied”

Example path: `/home/user/backup.sh`

**Step 1 — Check permissions**

```bash
ls -l /home/user/backup.sh
```

Look for `x`, which means execute permission.

**Step 2 — Add execute permission for the owner**

```bash
chmod u+x /home/user/backup.sh
```

**Step 3 — Verify permissions**

```bash
ls -l /home/user/backup.sh
```

**Step 4 — Try running the script**

```bash
/home/user/backup.sh
```

Use the real path on your system. If the error continues, check the file path, script contents, and directory permissions. Avoid changing permissions more broadly than necessary.

---

## Key Takeaways

- `/` is the starting point of the Linux file system.
- `/etc` contains configuration files; `/var/log` is useful for troubleshooting.
- `top` and `ps` help identify processes using CPU.
- `systemctl` checks service status; `journalctl` reads systemd service logs.
- `ls -l` helps inspect permissions; `chmod` changes them.
- Understand the likely cause before changing or stopping something.


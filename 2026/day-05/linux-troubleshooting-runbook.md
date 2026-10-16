# Day 05 – Linux Troubleshooting Runbook

**Goal:** Practise checking a Linux system before making changes.

**Environment:** Ubuntu on WSL (Windows). Some commands may behave differently on a full Linux server. Record your own output; do not copy another person's results.

## 1. System Information

| Command | What it does |
|---|---|
| `uname -a` | Shows kernel and system information. |
| `cat /etc/os-release` | Shows the Linux distribution and version. |
| `whoami` | Shows the current username. |
| `uptime` | Shows how long the system has been running and its load average. |

## 2. File and Directory Checks

| Command | What it does |
|---|---|
| `pwd` | Shows the current directory. |
| `ls -lah` | Lists files, including hidden files, with readable sizes. |
| `mkdir -p /tmp/runbook-demo` | Creates a temporary practice directory. Safe to rerun. |
| `cp /etc/hosts /tmp/runbook-demo/hosts-copy` | Copies a system text file into the practice directory. |
| `ls -l /tmp/runbook-demo` | Confirms the copied file exists. |
| `cat /etc/hosts` | Displays the hosts file; does not change it. |

## 3. CPU, Processes, and Memory

| Command | What it does |
|---|---|
| `top` | Shows live processes and CPU/memory use. Press `q` to exit. |
| `ps -ef` | Shows a snapshot of running processes. |
| `free -h` | Shows RAM and swap usage in readable units. |
| `ps -eo pid,pcpu,pmem,comm --sort=-pcpu | head` | Shows processes using the most CPU. |

**What to look for:** Is a process using unusually high CPU? Is memory available? A high number by itself does not always mean there is a problem; compare it with normal behaviour.

## 4. Disk Space and Logs

| Command | What it does |
|---|---|
| `df -h` | Shows disk space used and available on mounted filesystems. |
| `du -sh /var/log` | Shows the total size of the log directory. |
| `du -sh /tmp/runbook-demo` | Shows the size of your practice directory. |
| `ls -lh /var/log` | Lists log files and their sizes. |

**What to look for:** Check the `Use%` column in `df -h`. If a filesystem is nearly full, identify what is using space before deleting anything. Do not delete system logs just to free space.

## 5. Network Troubleshooting

| Command | What it does |
|---|---|
| `ip addr` | Shows network interfaces and IP addresses. |
| `ip route` | Shows the routing table and default route. |
| `ss -tulpn` | Lists listening TCP/UDP ports; some process details may require `sudo`. |
| `getent hosts google.com` | Checks whether the system can resolve a hostname to an address. |
| `ping -c 4 google.com` | Sends four test packets. A failed ping alone does not prove the internet is down because ping may be blocked. |
| `curl -I --max-time 10 https://example.com` | Checks whether an HTTPS website responds with headers. Requires `curl` and network access. |

If a command is missing, record the message instead of installing tools immediately.

## 6. Check a Service and Its Logs

Try the SSH service if it exists in your environment:

| Command | What it does |
|---|---|
| `systemctl status ssh` | Shows SSH service status. On some systems the service is named `sshd`. |
| `systemctl list-units --type=service --state=running` | Lists services currently reported as running. |
| `journalctl -u ssh -n 20 --no-pager` | Shows the latest 20 SSH log entries. |
| `journalctl -p warning -n 20 --no-pager` | Shows recent warning-level or more serious system messages. |

**WSL note:** Some WSL setups do not run `systemd` or an SSH service. If you see an error such as “System has not been booted with systemd,” record it; do not assume your Ubuntu installation is broken. You can still practise the other sections.

## 7. A Simple Troubleshooting Method

1. **Identify the symptom:** What is slow, failing, or unavailable?
2. **Check system resources:** Run `top`, `free -h`, and `df -h`.
3. **Check the relevant area:** Use network commands for network issues, or service status and logs for service issues.
4. **Read the error:** Note the exact error message and when it happened.
5. **Make one change at a time:** Avoid changing several things together.
6. **Test again:** Run the same check to see whether the result changed.
7. **Record the result:** Write down the cause if known, the action taken, and the outcome.

## 8. If the Problem Gets Worse

- Stop and record the new error message.
- Check service status and logs before trying a fix.
- Avoid deleting files or changing permissions without understanding them.
- Do not restart SSH on a remote server unless you understand the risk; it can interrupt remote access.
- In a work environment, follow the team's incident process and ask for help when needed.

## 9. My Practice Results

Fill this section with output from **your own Ubuntu WSL terminal**.

- **Date:**
- **Ubuntu version:**
- **Current username (`whoami`):**
- **CPU/process observation (`top` or `ps -ef`):**
- **Memory observation (`free -h`):**
- **Disk observation (`df -h`):**
- **Network test (`getent hosts`, `ping`, or `curl`):**
- **Service checked (if available):**
- **Important error or log message (if any):**
- **What I learned:**

## 10. Quick Command Checklist

- [ ] System information checked
- [ ] Practice directory and copied file verified
- [ ] Processes and memory checked
- [ ] Disk space checked
- [ ] Network checked
- [ ] Service status/logs checked if available
- [ ] Findings recorded using my actual output

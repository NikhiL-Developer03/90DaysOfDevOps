## #90DaysOfDevOps – Day 03


## 1. File System Commands

| Command | What it does |
|---|---|
| `pwd` | Shows the full path of the folder you are currently in. |
| `ls` | Lists files and folders in the current directory. |
| `ls -la` | Lists all files, including hidden files, with details such as permissions and size. |
| `cd <directory>` | Moves into the directory you specify. Example: `cd Documents` |
| `cd ..` | Moves up one directory. |
| `cd ~` | Moves to your home directory. |
| `mkdir <name>` | Creates a new directory. Example: `mkdir practice` |
| `rmdir <name>` | Removes an empty directory. |
| `touch <file>` | Creates an empty file, or updates its modification time if it already exists. |
| `cp <source> <destination>` | Copies a file to a destination. Example: `cp notes.txt notes-backup.txt` |
| `mv <source> <destination>` | Moves a file or renames it. Example: `mv old.txt new.txt` |
| `rm <file>` | Deletes a file. |
| `rm -r <directory>` | Deletes a directory and its contents recursively. **Check the path carefully before using it.** |
| `cat <file>` | Prints a file's contents in the terminal. Best for short files. |
| `less <file>` | Opens a file for page-by-page reading. Press `q` to quit. |
| `head <file>` | Shows the first 10 lines of a file by default. |
| `tail <file>` | Shows the last 10 lines of a file by default. Useful for checking recent log entries. |
| `find . -name "<file>"` | Searches for a matching filename in the current directory and its subdirectories. Replace `<file>` with the name or pattern. |

---

## 2. Process Management Commands

A **process** is a running program. These commands help you see and manage running programs.

| Command | What it does |
|---|---|
| `ps` | Shows processes associated with the current terminal. |
| `ps -ef` | Lists processes running on the system. |
| `top` | Shows live process and system-resource usage. Press `q` to quit. |
| `kill <PID>` | Requests that a process stop. Replace `<PID>` with its process ID. |
| `kill -9 <PID>` | Force-stops a process. Use only if a normal stop does not work, because it does not allow the process to shut down cleanly. |
| `jobs` | Lists jobs started from the current shell that are running or stopped in the background. |
| `bg` | Resumes a stopped job in the background. |
| `fg` | Brings a background job to the foreground. |

### Background job basics

- Add `&` to the end of a command to start it in the background, for example: `sleep 60 &`
- Use `jobs` to see jobs started by your current shell.
- Use `fg` to bring a job back to the foreground.
- Use `bg` to resume a stopped job in the background.

---

## 3. Networking Troubleshooting Commands

Networking commands help you check connectivity, find your IP address, test websites, and troubleshoot DNS.

| Command | What it does |
|---|---|
| `ping google.com` | Checks whether a host responds over the network. Press `Ctrl + C` to stop it. Some hosts or networks block ping. |
| `ip addr` | Shows network interfaces and their IP addresses. |
| `curl https://example.com` | Makes an HTTP/HTTPS request and displays the response body. Useful for basic website or API checks. |
| `dig google.com` | Looks up DNS information for a domain. It may need to be installed separately on some Linux systems. |

### Basic networking checks

```bash
ip addr
ping google.com
curl https://example.com
dig google.com
```

- If `ip addr` shows no expected IP address, check the network interface or connection.
- If `ping` fails, it does not always mean the internet is down; ping may be blocked.
- If a domain does not resolve, check DNS using `dig`.
- If a website or API behaves unexpectedly, try `curl` to see whether it responds.

---

## 4. System Information Commands

Use these commands to learn about the machine and check its resources.

| Command | What it does |
|---|---|
| `hostname` | Displays the computer's hostname. |
| `uname -a` | Shows kernel and system information. |
| `uptime` | Shows how long the system has been running and gives a system-load summary. |
| `free -h` | Shows memory usage in a human-readable format. |
| `lscpu` | Displays CPU details. |
| `lsblk` | Lists storage devices and their partitions. |
| `whoami` | Shows the username of the current user. |
| `id` | Shows the current user's ID and group IDs. |

### Quick system check

```bash
hostname
whoami
uptime
free -h
lsblk
```

These commands are useful when you first connect to a Linux server or investigate a machine that seems slow.

---

## 5. Viewing Logs and Basic Troubleshooting

Logs record events from the operating system and applications. They are useful when a service fails or an application behaves unexpectedly.

| Command | What it does |
|---|---|
| `tail <file>` | Shows the last 10 lines of a file, often useful for recent log entries. |
| `less <file>` | Lets you read a log file page by page. Press `q` to exit. |
| `head <file>` | Shows the first 10 lines of a file. |
| `cat <file>` | Prints a file's contents; use for short files rather than very large logs. |

Example, if you have a log file named `app.log` in your current directory:

```bash
tail app.log
less app.log
```

Some system logs are protected and may require administrator permissions. Only use `sudo` when needed and when you understand the command.

---

## 6. Getting Help and Using the Terminal

| Command or shortcut | What it does |
|---|---|
| `man <command>` | Opens the manual page for a command. Press `q` to exit. Example: `man ls` |
| `command --help` | Often displays a short help page. Example: `ls --help` |
| `history` | Lists commands entered in the current shell's history. |
| `Ctrl + C` | Interrupts the command currently running in the terminal. |
| `Ctrl + R` | Searches previously entered commands in shell history. |
| `Tab` | Helps complete command names and file paths. |

---

## 7. Quick Revision

| If you want to... | Try this |
|---|---|
| Check where you are | `pwd` |
| List files, including hidden ones | `ls -la` |
| Create a folder | `mkdir practice` |
| Create an empty file | `touch notes.txt` |
| Read a short file | `cat notes.txt` |
| Read a long file | `less notes.txt` |
| Find a file by name | `find . -name "notes.txt"` |
| See running processes | `ps -ef` or `top` |
| Check your IP address | `ip addr` |
| Test network reachability | `ping google.com` |
| Test an HTTPS request | `curl https://example.com` |
| Check DNS | `dig google.com` |
| Check memory | `free -h` |
| Check system uptime | `uptime` |
| Get command help | `man ls` or `ls --help` |

---

## 8. Key Takeaways

- ✔ Start with common commands and practise them regularly.
- ✔ Use `pwd`, `ls`, and `cd` to navigate the file system.
- ✔ Use `ps` and `top` to inspect running processes.
- ✔ Use `ip addr`, `ping`, `curl`, and `dig` for basic network checks.
- ✔ Use `free -h`, `uptime`, and `lsblk` to inspect system resources.
- ✔ Read logs carefully when troubleshooting.
- ✔ Be extra careful with deletion commands and force-stopping processes.

## Day 03 Practice Task

1. Use `pwd` and `ls -la` to inspect your current directory.
2. Create a folder named `linux-practice`, enter it, and create a file named `notes.txt`.
3. View the file using `cat` and `less`.
4. Run `ps -ef` and `top` to inspect processes.
5. Run `ip addr`, `ping google.com`, and `curl https://example.com`.
6. Check system details with `hostname`, `uptime`, and `free -h`.
7. Use `man ls` or `ls --help` to learn more about one command.

---

**Day 03 complete:** Keep this cheat sheet nearby while practising. The goal is to understand when to use each command, not to memorise every option.

**Hashtags:** `#90DaysOfDevOps` `#DevOpsKaJosh` `#TrainWithShubham`

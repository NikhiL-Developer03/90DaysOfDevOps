# Day 02 — Linux Architecture, Processes & systemd

## 1. Linux Architecture

Linux architecture describes how hardware, the kernel, and user applications work together.

### Core Components

- **Hardware:** Physical components such as the CPU, RAM, storage, and input/output devices.
- **Kernel:** The core of Linux. It manages CPU time, memory, devices, files, and processes. Applications use system calls to request services from the kernel.
- **User Space:** The environment where applications and user-level tools run, such as browsers, editors, utilities, and shells. Applications do not normally access hardware directly; they request kernel services.
- **Shell:** A command-line interface that accepts commands and launches programs. Examples include Bash and Zsh.
- **Init system / `systemd`:** On most modern Linux distributions that use systemd, it is the first user-space process started by the kernel and normally runs as PID 1. It helps start and manage services during boot.

### Basic Request Flow

1. A user runs a command or opens an application.
2. The application requests operating-system services when needed.
3. The kernel manages access to hardware and system resources.
4. The result is returned to the application and displayed to the user.

**Remember:** Hardware provides resources, the kernel manages them, and user-space applications use them.

---

## 2. Processes in Linux

A **process** is a running instance of a program.

### Important Points

- Every process has a unique **Process ID (PID)** while it exists.
- A **parent process** can create a **child process**.
- The kernel schedules processes and decides when they can use the CPU.
- A process can run in the **foreground** (interacting with the terminal) or the **background** (running without holding up the terminal).
- A program is a set of instructions; a process is that program while it is running.

### Common Process States

| State | Name | Meaning |
|---|---|---|
| `R` | Running / Runnable | Running on the CPU or ready to run |
| `S` | Interruptible sleep | Waiting for an event; can usually be woken by a signal |
| `D` | Uninterruptible sleep | Usually waiting for an I/O operation to complete |
| `T` | Stopped | Paused, often by a signal or debugger |
| `Z` | Zombie | Finished, but its parent has not yet collected its exit status |

**Note:** These are common state labels shown by Linux process tools. The exact display can vary by command.

---

## 3. What Is `systemd`?

`systemd` is an init system and service manager used by most modern Linux distributions.

### Main Responsibilities

- Starts system services during boot.
- Starts, stops, restarts, and monitors services.
- Manages service dependencies and startup order.
- Can restart services when configured to do so.
- Collects system and service logs through the journal, which can be viewed with `journalctl`.

### Why It Matters in DevOps

- Check whether an application service is running.
- Start or restart a service during troubleshooting.
- Configure a service to start automatically at boot.
- Inspect logs when a service fails.
- Diagnose issues on Linux servers.

### Useful `systemctl` Commands

| Command | Purpose |
|---|---|
| `systemctl status nginx` | Check the status of Nginx |
| `systemctl start nginx` | Start Nginx now |
| `systemctl stop nginx` | Stop Nginx |
| `systemctl restart nginx` | Restart Nginx |
| `systemctl enable nginx` | Configure Nginx to start at boot |
| `systemctl disable nginx` | Prevent Nginx from being enabled at boot |
| `systemctl is-enabled nginx` | Check whether Nginx is enabled at boot |

Replace `nginx` with the service name on your system. Some distributions use `ssh`; others use `sshd` for the SSH service.

---

## 4. Useful Linux Commands

| Command | Purpose |
|---|---|
| `ps aux` | Show processes in a BSD-style format |
| `ps -ef` | Show processes in a full-format listing |
| `top` | Monitor processes and system resources in real time |
| `free -h` | Display memory usage in human-readable units |
| `df -h` | Display filesystem disk usage |
| `systemctl status nginx` | Check a service's status |
| `journalctl -u nginx` | View journal logs for the Nginx service |
| `kill <PID>` | Send the default termination signal to a process |
| `kill -9 <PID>` | Force-kill a process; use only when gentler termination does not work |

### Command Examples

```bash
# Display running processes
ps aux

# Monitor CPU and memory usage
top

# Check memory and disk usage
free -h
df -h

# Check a service
systemctl status nginx

# View logs for a service
journalctl -u nginx
```

**Tip:** Replace example service names and PIDs with the values from your own system. Avoid using `kill -9` as the first option because it does not allow the process to clean up.

---

## 5. Quick Interview Revision

- **What is the kernel?** The core of Linux that manages hardware and system resources.
- **What is user space?** The environment where applications and user-level tools run.
- **What is a shell?** A command interpreter that accepts commands and launches programs.
- **What is a process?** A running instance of a program.
- **What is a PID?** A process identifier used to identify a process.
- **What is a parent process?** A process that creates another process.
- **What is `systemd`?** An init system and service manager commonly used to start and manage services.
- **What is a zombie process?** A process that has finished but whose parent has not yet collected its exit status.
- **How do you check a service?** Use `systemctl status <service-name>`.
- **How do you check service logs?** Use `journalctl -u <service-name>`.

---

## 6. Key Takeaways

- ✔ Hardware consists of physical system components.
- ✔ The kernel manages CPU, memory, devices, and processes.
- ✔ User space is where applications and utilities run.
- ✔ The shell provides a command-line interface.
- ✔ A process is a running program and has a PID while it exists.
- ✔ Linux process states help explain what a process is doing.
- ✔ `systemd` manages startup and services on systemd-based distributions.
- ✔ `systemctl` manages services, while `journalctl` helps inspect logs.
- ✔ Process and service commands are essential for Linux troubleshooting in DevOps.

---

## 7. Hands-on Practice Checklist

- [ ] Run `ps aux` and identify a process PID.
- [ ] Run `top` and observe CPU and memory usage.
- [ ] Run `free -h` and `df -h`.
- [ ] Check a service with `systemctl status <service-name>`.
- [ ] Read its logs with `journalctl -u <service-name>`.
- [ ] Explain the Linux architecture and process states in your own words.


# 🚀 Day 12 – Breather & Revision (Days 01–11)

## 🎯 Goal

Take a short break from learning new concepts and reinforce the Linux fundamentals covered in **Days 01–11**. The focus today is on revision, retention, and building confidence through quick hands-on practice.

---

# 1. Process & Service Revision

## Check Running Processes

### Command

```bash
ps aux
```

### Observation

Displayed all running processes along with CPU and memory usage.

---

## Check SSH Service

### Command

```bash
systemctl status ssh
```

### Observation

Verified that the SSH service is active and running.

---

## View SSH Logs

### Command

```bash
journalctl -u ssh -n 20
```

### Observation

Reviewed recent SSH logs and confirmed there were no critical errors.

---

# 2. File Operations Revision

## Create Directory

```bash
mkdir revision-demo
```

---

## Create File

```bash
touch notes.txt
```

---

## Append Content

```bash
echo "Linux Revision Day" >> notes.txt
```

---

## Check Permissions

```bash
ls -l notes.txt
```

---

## Copy File

```bash
cp notes.txt backup.txt
```

---

## Change Permissions

```bash
chmod 755 backup.txt
```

### Observation

Successfully created, copied, and modified file permissions.

---

# 3. Cheat Sheet Refresh

## Five Commands I Would Use First During an Incident

| Command | Why |
|---------|-----|
| `ps aux` | Check running processes. |
| `top` | Monitor CPU and memory usage. |
| `systemctl status <service>` | Verify service health. |
| `journalctl -u <service>` | Review service logs. |
| `df -h` | Check available disk space. |

---

# 4. User & Group Revision

## Check User Information

```bash
id tokyo
```

---

## Check Groups

```bash
groups tokyo
```

---

## Verify Ownership

```bash
ls -l
```

### Observation

Verified user membership and file ownership successfully.

---

# Commands Practiced

```bash
ps aux

top

systemctl status ssh

journalctl -u ssh -n 20

mkdir revision-demo

touch notes.txt

echo "Linux Revision Day" >> notes.txt

cp notes.txt backup.txt

ls -l

chmod 755 backup.txt

id tokyo

groups tokyo

df -h
```

---

# Key Takeaways

- Linux command-line skills improve with regular practice.
- Service troubleshooting always begins with checking status and logs.
- File permissions and ownership are critical for system security.
- User and group management help organize secure access.
- A strong understanding of Linux fundamentals is essential before learning Docker, Kubernetes, and Cloud technologies.

---


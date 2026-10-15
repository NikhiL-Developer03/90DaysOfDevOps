# Day 04 – Linux Practice: Processes and Services

## 1. Process Checks

| Command | Purpose |
|---|---|
| `ps` | Shows processes running in my terminal. |
| `ps -ef` | Shows processes running on the system. |
| `top` | Shows running processes and CPU/memory usage. Press `q` to exit. |

## 2. Service Checks

A **service** is a program that runs in the background. I checked the `cron` service.

| Command | Purpose |
|---|---|
| `systemctl status cron` | Checks the status of the cron service. |
| `systemctl list-units --type=service` | Lists system services known to systemd. |

## 3. Log Checks

Logs help us understand what happened when a service has a problem.

| Command | Purpose |
|---|---|
| `journalctl -u cron` | Shows logs for the cron service. |
| `journalctl -u cron -n 10` | Shows the latest 10 log entries for cron. |

## 4. Simple Troubleshooting Steps

1. Use `ps` to check processes.
2. Use `top` to see CPU and memory usage.
3. Use `systemctl status cron` to check whether cron is running.
4. Use `journalctl -u cron` to look for service messages.
5. Note any errors before making changes.

## 5. My Practice Notes

- **System:** Ubuntu on WSL
- **Service checked:** `cron`
- **Process commands:** `ps`, `ps -ef`, `top`
- **Service and log commands:** Add what you ran and what you saw.
- **What I learned:** `ps` shows processes, `top` updates process information, `systemctl` checks services, and `journalctl` shows logs.

## Key Takeaways

- `ps` and `top` help check running processes.
- `systemctl status` helps check a service.
- `journalctl` helps read service logs.
- Always check the output and understand the problem before changing a service.

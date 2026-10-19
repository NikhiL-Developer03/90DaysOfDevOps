# Day 08 – Cloud Server Setup: Docker, Nginx & Web Deployment

## Objective

Set up a cloud server, connect through SSH, install Docker and Nginx, verify web access, and collect Nginx logs.

## 1. Connect to the EC2 Server

```bash
ssh -i "your-key.pem" ubuntu@<YOUR_PUBLIC_IP>
```

**What it does:** Connects securely to the Ubuntu EC2 instance.

**My observation:** I connected to my AWS EC2 Ubuntu server through SSH.

## 2. Update Ubuntu

```bash
sudo apt update
```

**What it does:** Refreshes the list of available packages.

## 3. Install and Verify Docker

```bash
sudo apt install docker.io -y
sudo systemctl enable --now docker
docker --version
sudo systemctl status docker
```

**What it does:** Installs Docker, starts it, enables it at boot, and checks its version and status.

**My observation:** I installed Docker and checked its version and service status. Record the exact version and status shown by your terminal.

## 4. Install and Verify Nginx

```bash
sudo apt install nginx -y
sudo systemctl status nginx
sudo systemctl enable nginx
```

**What it does:** Installs Nginx, checks whether it is running, and enables it to start after a reboot.

## 5. Configure the AWS Security Group

| Port | Protocol | Purpose |
|---|---|---|
| 22 | TCP | SSH access; restrict the source to your IP where possible |
| 80 | TCP | HTTP web access |
| 443 | TCP | HTTPS, only if you configure HTTPS |

**My observation:** I configured inbound HTTP access on port 80 so the Nginx page could be opened in a browser.

## 6. Test Nginx

Test on the server:

```bash
curl localhost
```

Then open this URL in your browser, replacing the placeholder with your EC2 public IP:

```text
http://<YOUR_PUBLIC_IP>
```

**My observation:** `curl localhost` returned the Nginx HTML page, and the Nginx welcome page loaded in the browser.

## 7. View Nginx Logs

```bash
sudo tail -n 20 /var/log/nginx/access.log
sudo tail -n 20 /var/log/nginx/error.log
```

The access log records HTTP requests. The error log records Nginx errors and warnings.

### What I noticed in my access log

- `200` — the request succeeded.
- `304` — the browser can use a cached copy.
- `404` — the requested resource was not found, such as `/favicon.ico`.
- `400` — the request was invalid; TLS-looking data on an HTTP port can indicate an HTTPS request was sent to port 80.
- `curl/8.18.0` with `::1` indicates a request from the server itself over IPv6 loopback.
- Automated clients and crawlers can also appear in public web-server logs.

## 8. Save the Logs to a File

```bash
sudo sh -c 'cat /var/log/nginx/access.log /var/log/nginx/error.log > /home/ubuntu/nginx-logs.txt'
```

Verify the file:

```bash
ls -lh /home/ubuntu/nginx-logs.txt
head -n 10 /home/ubuntu/nginx-logs.txt
```

This combines the current access and error logs into `nginx-logs.txt` in the Ubuntu user's home directory.

## 9. Download the Log File to Windows

Run this command in **Windows PowerShell**, not in the EC2 SSH terminal. Replace `your-key.pem` and the public IP with your actual values.

```powershell
scp -i "$HOME\Downloads\your-key.pem" ubuntu@<YOUR_PUBLIC_IP>:/home/ubuntu/nginx-logs.txt "$HOME\Downloads\nginx-logs.txt"
```

Verify the downloaded file:

```powershell
Get-Item "$HOME\Downloads\nginx-logs.txt"
```

SCP securely copies the log file from EC2 to your Windows Downloads folder over SSH.

## Commands Used

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl enable --now docker
docker --version
sudo systemctl status docker
sudo apt install nginx -y
sudo systemctl status nginx
sudo systemctl enable nginx
curl localhost
sudo tail -n 20 /var/log/nginx/access.log
sudo tail -n 20 /var/log/nginx/error.log
sudo sh -c 'cat /var/log/nginx/access.log /var/log/nginx/error.log > /home/ubuntu/nginx-logs.txt'
ls -lh /home/ubuntu/nginx-logs.txt
head -n 10 /home/ubuntu/nginx-logs.txt
```

## Challenges Faced

- The browser URL used HTTPS on port 80. Switching to `http://<YOUR_PUBLIC_IP>` matched the default Nginx HTTP configuration.
- Understanding `scp` was confusing at first. I learned that it copies a file from the remote EC2 server to my local Windows Downloads folder.

## What I Learned

- How to connect to an AWS EC2 Ubuntu server using SSH.
- How to install and verify Docker and Nginx.
- How AWS security groups control inbound web traffic.
- How to test Nginx locally with `curl` and through a browser.
- How to read Nginx access logs and understand common HTTP status codes.
- How to save logs to a text file and download it using `scp`.

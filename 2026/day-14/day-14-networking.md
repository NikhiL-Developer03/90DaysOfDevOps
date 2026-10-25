# Day 14 – Networking Fundamentals & Hands-on Checks

## Objective

Practice core networking concepts and common Linux commands used to troubleshoot IP connectivity, routing, DNS, ports, and HTTP responses.

## 1. OSI Model vs TCP/IP Model

### OSI Model – 7 Layers

| Layer | Name | Purpose | Examples |
|---|---|---|---|
| L7 | Application | Network services used by applications | HTTP, HTTPS, DNS |
| L6 | Presentation | Data format, encoding, encryption | TLS/SSL is often discussed here conceptually |
| L5 | Session | Manages communication sessions | Session management |
| L4 | Transport | Communication between applications | TCP, UDP |
| L3 | Network | IP addressing and routing | IPv4, IPv6, ICMP |
| L2 | Data Link | Communication on the local link | Ethernet, MAC address, Wi-Fi link |
| L1 | Physical | Sends bits over the physical medium | Cable, radio, fiber |

### TCP/IP Model – 4 Layers

| Layer | Purpose | Examples |
|---|---|---|
| Application | Network protocols used by applications | HTTP, HTTPS, DNS, SSH |
| Transport | Application-to-application transport | TCP, UDP |
| Internet | Addressing and routing between networks | IP, ICMP |
| Link | Local network delivery | Ethernet, Wi-Fi |

### Where common protocols fit

- **IP:** Network/Internet layer.
- **TCP and UDP:** Transport layer.
- **HTTP and HTTPS:** Application layer.
- **DNS:** Application layer.
- **Ethernet and MAC addressing:** Link/Data Link layer.

### Real example

```bash
curl https://example.com
```

This command makes an HTTPS request. Conceptually, the application protocol uses TLS encryption over TCP, which uses IP, and then the local network link carries the traffic. Actual transport details can vary (for example, HTTP/3 uses QUIC over UDP).

## 2. Identify This Machine's IP Address

```bash
hostname -I
```

Alternative:

```bash
ip addr show
```

**What it does:** Displays IP addresses assigned to the machine or its network interfaces.

**Observation to record:** Write down the IP address shown by your own terminal. A machine can have multiple interfaces and addresses.

## 3. Test Reachability

```bash
ping google.com
```

**What it does:** Sends ICMP echo requests and reports responses, latency, and packet loss when replies are received.

**Observation to record:**
- Did you receive replies?
- What latency was shown?
- What packet loss percentage was reported?

A failed ping does not always prove the host is down; ICMP may be blocked by a firewall or network policy.

## 4. Trace the Network Path

Try one of these commands, depending on which utility is installed:

```bash
traceroute google.com
```

or

```bash
tracepath google.com
```

**What it does:** Shows the route through network hops toward the destination.

**Observation to record:** Note the number of hops and any timeouts (`*`). Some routers do not respond to traceroute probes, so a timeout does not automatically mean the route is broken.

## 5. Check Listening Ports

```bash
ss -tulpn
```

**What it does:** Lists listening TCP/UDP sockets and, where permissions allow, the related processes.

**Observation to record:** Identify one listening service and its port from your actual output. For example, SSH commonly uses port 22, but your system may differ. Use `sudo ss -tulpn` if process details require elevated privileges.

## 6. Check DNS Resolution

```bash
dig google.com
```

Alternative:

```bash
nslookup google.com
```

**What it does:** Queries DNS to resolve a domain name to one or more IP addresses.

**Observation to record:** Record the resolved IP shown in your output. DNS answers can vary by location, resolver, and time.

## 7. Check an HTTP/HTTPS Response

```bash
curl -I https://google.com
```

**What it does:** Requests response headers without downloading the full page body.

**Observation to record:** Record the HTTP status line from your actual output. A 2xx status usually indicates success; a 3xx status indicates a redirect; 4xx and 5xx statuses indicate client-side/request or server-side errors, respectively. Some sites redirect to another URL.

## 8. View a Snapshot of Network Connections

```bash
netstat -an | head
```

If `netstat` is not installed, use:

```bash
ss -tan
```

**What it does:** Shows a snapshot of network sockets/connections. `head` limits the output to the first few lines; it does not count all connection states.

**Observation to record:** Look for states such as `ESTABLISHED` and `LISTEN`. The output depends on your operating system and current connections.

## 9. Mini Task – Probe a Local Port

### Step 1: Find a listening port

```bash
ss -tulpn
```

Choose a port that is actually listening on your machine. SSH commonly uses port 22, but do not assume it is present.

### Step 2: Test that port

Example for port 22:

```bash
nc -zv localhost 22
```

Replace `22` with the port you found. If `nc` is missing, install the appropriate package for your distribution or use a service-specific test such as `curl -I http://localhost:PORT` for an HTTP service.

### Step 3: Interpret the result

- **Connection succeeds:** A service is accepting connections on that local port.
- **Connection fails:** Check whether the service is running, whether it is listening on the expected address/port, and whether firewall rules are involved.

Useful checks:

```bash
systemctl status ssh
sudo ss -tulpn
```

Service names vary by distribution; some systems use `sshd` instead of `ssh`.

Firewall check examples (use the tool available on your system):

```bash
sudo ufw status
```

or

```bash
sudo firewall-cmd --list-all
```

## 10. Troubleshooting Summary

| Symptom | First useful check | What it helps determine |
|---|---|---|
| Cannot reach a host | `ping google.com` | Basic IP reachability, latency, packet loss (if ICMP is allowed) |
| Domain does not resolve | `dig google.com` or `nslookup google.com` | DNS lookup result |
| Website does not respond as expected | `curl -I https://google.com` | HTTP response/status and headers |
| Expected port is unavailable | `ss -tulpn` | Whether a local service is listening |
| Need to inspect route | `traceroute google.com` or `tracepath google.com` | Visible network hops |
| Need a connection snapshot | `ss -tan` or `netstat -an` | Current socket states |

## 11. Reflection

### Which command gives the fastest signal when something is broken?

`ping` is a quick initial check for IP reachability and latency, when the destination permits ICMP. It does not test DNS, a specific application port, or HTTP by itself.

### If DNS fails, which layer should I inspect?

DNS is an application-layer protocol. Check the resolver configuration and query results with `dig` or `nslookup`, then inspect network connectivity to the configured DNS resolver if needed.

### If HTTP 500 appears, which layer should I inspect next?

An HTTP 500 response means an HTTP server returned an internal-server-error status. Network connectivity reached an HTTP endpoint, so inspect web-server/application logs, backend dependencies, and application health.

### Two follow-up checks in a real incident

1. Check whether the required service is running:

   ```bash
   systemctl status <service-name>
   ```

2. Check whether it is listening on the expected port:

   ```bash
   sudo ss -tulpn
   ```

   If needed, check the host firewall with `sudo ufw status` or `sudo firewall-cmd --list-all`, depending on the distribution.

## 12. Commands Reference

```bash
hostname -I
ip addr show
ping google.com
traceroute google.com
tracepath google.com
ss -tulpn
dig google.com
nslookup google.com
curl -I https://google.com
netstat -an | head
ss -tan
nc -zv localhost 22
systemctl status ssh
sudo ufw status
sudo firewall-cmd --list-all
```

These are practice commands. Run only the ones supported by your environment, and record the outputs you actually see.

## 13. What I Learned

1. The OSI and TCP/IP models help explain how network communication is organized into layers.
2. Commands such as `ping`, `traceroute`, `dig`, `curl`, and `ss` each check a different part of network connectivity and troubleshooting.
3. A successful ping does not guarantee that a website or port is working; DNS, routing, ports, firewalls, and application responses need separate checks.


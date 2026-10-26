# Day 15 – Networking Concepts: DNS, IP, Subnets & Ports

## Objective

Understand the basic networking concepts used by DevOps engineers, including DNS, IP addressing, CIDR, subnetting, and ports.

## Task 1 – DNS: How Names Become IPs

When I type `google.com` in a browser, the browser needs to find the IP address associated with the domain name. A DNS resolver looks up the domain and returns IP address information. The browser then uses the resolved address to connect to Google's service. DNS lets users use easy-to-remember names instead of memorizing IP addresses.

### DNS Record Types

| Record | Purpose |
|---|---|
| **A** | Maps a domain name to an IPv4 address. |
| **AAAA** | Maps a domain name to an IPv6 address. |
| **CNAME** | Creates an alias that points one domain name to another. |
| **MX** | Specifies mail servers responsible for receiving email for a domain. |
| **NS** | Specifies authoritative name servers for a domain. |

### Check DNS Resolution

Command:

```bash
dig google.com
```

**My observed result:**
- DNS query status: `NOERROR`
- Query type: `A` (IPv4)
- A records returned: 6
- TTL shown: `107` seconds
- Query time: `47 msec`
- DNS resolver: `10.255.255.254`

**A records returned in my output:**

```text
192.178.177.100
192.178.177.101
192.178.177.113
192.178.177.102
192.178.177.138
192.178.177.139
```

**Observation:** The `dig` command successfully returned IPv4 address records for `google.com`. The TTL value was 107 seconds in the output I captured. DNS answers and TTLs can change over time.

## Task 2 – IP Addressing

### What is an IPv4 Address?

An IPv4 address is a 32-bit address used to identify a device or network interface. It is written as four decimal numbers called octets, separated by dots.

Example: `192.168.1.10`

Each octet has a value from `0` to `255`.

### Public vs Private IP

- **Private IP:** Used within private networks and not directly routable across the public internet. Example: `192.168.1.10`.
- **Public IP:** Used for internet-facing communication. Example: `8.8.8.8`.

A device using a private IP may access the internet through a router or carrier gateway that uses NAT.

### Private IPv4 Ranges

| Private Range | CIDR |
|---|---|
| `10.0.0.0 – 10.255.255.255` | `10.0.0.0/8` |
| `172.16.0.0 – 172.31.255.255` | `172.16.0.0/12` |
| `192.168.0.0 – 192.168.255.255` | `192.168.0.0/16` |

### Check My IP Addresses

Command:

```bash
ip addr show
```

**My observed result:**
- Loopback IPv4: `127.0.0.1/8`
- Additional address on `lo`: `10.255.255.254/32`
- `eth0` IPv4: `172.20.177.71/20`
- `eth0` MAC address: `00:15:5d:24:8d:5c`
- `eth0` IPv6 link-local address: `fe80::215:5dff:fe24:8d5c/64`

**Private IP identified:** `172.20.177.71/20`. This address falls within the private range `172.16.0.0 – 172.31.255.255`.

The `10.255.255.254/32` address is also within a private IPv4 range, but it appears on the loopback interface in this environment rather than as the `eth0` address.

### Understanding `/20`

The address `172.20.177.71/20` has:
- Subnet mask: `255.255.240.0`
- Network address: `172.20.176.0`
- Broadcast address: `172.20.191.255`

These values match the address and broadcast information shown by the command output.

## Task 3 – CIDR & Subnetting

### What does `/24` mean?

In `192.168.1.0/24`, `/24` means the first 24 bits identify the network portion. The remaining 8 bits are available for host addresses.

Subnet mask: `255.255.255.0`

### CIDR Table

For traditional IPv4 subnets, the network and broadcast addresses are excluded from the usual usable-host count.

| CIDR | Subnet Mask | Total IPs | Usable Hosts |
|---|---|---:|---:|
| `/24` | `255.255.255.0` | 256 | 254 |
| `/16` | `255.255.0.0` | 65,536 | 65,534 |
| `/28` | `255.255.255.240` | 16 | 14 |

### Why Do We Subnet?

Subnetting divides a larger network into smaller networks. It helps organize IP addresses, separate systems or environments, use address space efficiently, and make network management easier.

## Task 4 – Ports: The Doors to Services

### What is a Port?

A port is a logical number used to identify a service or application on a device. An IP address identifies the network interface or host, while the port identifies the service receiving the connection.

Example: `192.168.1.10:22`
- IP address: `192.168.1.10`
- Port: `22`
- Port 22 is commonly used by SSH.

### Common Ports

| Port | Service |
|---:|---|
| `22` | SSH |
| `80` | HTTP |
| `443` | HTTPS |
| `53` | DNS |
| `3306` | MySQL |
| `6379` | Redis |
| `27017` | MongoDB |

### Check Listening Ports

Command:

```bash
ss -tulpn
```

This command displays listening TCP and UDP sockets. Process details may require elevated privileges, so try `sudo ss -tulpn` if needed.

**Reference example:** The supplied reference output showed TCP port `22` listening (commonly SSH) and port `53` listening on loopback addresses (commonly a local DNS resolver). Check your own output and record at least two ports that are actually listening on your system.

**My observation:** Add the actual ports and service names from your own `ss -tulpn` output here.

## Task 5 – Putting It Together

### Scenario 1: Access an application

```bash
curl http://myapp.com:8080
```

DNS may resolve `myapp.com` to an IP address. IP networking and TCP carry the connection, port `8080` identifies the destination service, and HTTP is used at the application layer.

### Scenario 2: Application cannot reach a database

Target: `10.0.1.50:3306`

First, check whether the host responds to ping:

```bash
ping 10.0.1.50
```

Then check whether the database port is reachable:

```bash
nc -zv 10.0.1.50 3306
```

If the connection fails, check whether the database service is running and listening on port `3306`, and inspect firewall/security-group rules and network routes. A failed ping alone does not prove the host is unavailable because ICMP can be blocked.

## Commands Practiced

```bash
dig google.com
ip addr show
ss -tulpn
```

Additional commands for the database troubleshooting exercise:

```bash
ping 10.0.1.50
nc -zv 10.0.1.50 3306
```

Run the last two commands only against a lab or host you are authorized to test, and record their actual outputs if you perform the exercise.

## What I Learned

1. **DNS** translates domain names into IP address information. I used `dig google.com` to identify A records and the TTL from my own output.
2. **IP addressing and subnetting** help identify private networks and divide address space into smaller networks. I identified `172.20.177.71/20` on my Ubuntu `eth0` interface.
3. **Ports** identify services on a host. Commands such as `ss -tulpn` and `nc -zv` can help investigate whether a service is listening and reachable.

## Key Networking Flow

```text
Domain Name
     |
     v
    DNS
     |
     v
 IP Address
     |
     v
Port / Transport (TCP or UDP)
     |
     v
 Application Protocol (HTTP, SSH, etc.)
```

## Conclusion

Day 15 helped me connect DNS, IP addressing, CIDR/subnetting, and ports. These fundamentals are useful for diagnosing connectivity issues in Linux systems, cloud environments, and DevOps infrastructure.

**Hashtags:** #90DaysOfDevOps #DevOpsKaJosh #TrainWithShubham

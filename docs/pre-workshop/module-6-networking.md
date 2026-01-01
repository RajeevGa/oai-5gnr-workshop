---
layout: default
title: "Module 6: Network Basics"
parent: Pre-Workshop Preparation
nav_order: 6
---

# 🌐 Module 6: Network Basics

> **Duration:** 40 minutes | **Level:** Refresher | **Hands-on:** Yes

---

## 📚 What You'll Learn

By the end of this module, you'll be able to:

- [ ] Check your network configuration (IP, interfaces, gateway)
- [ ] Understand private vs public IP addressing
- [ ] Test network connectivity systematically
- [ ] View active services and ports
- [ ] Capture and analyze network packets with Wireshark

---

## Introduction

Modern networks rely on IP networking to connect devices across the globe - sometimes even from space! You've likely seen IP addresses while setting up your home router or connecting to campus WiFi. Starting with 4G LTE, cellular networks also adopted this all-IP approach. This module covers the IP and Linux networking basics you'll need to configure and work with networks during the workshop's hands-on sessions.


This is a quick brush-up focusing on practical Linux commands for network exploration and troubleshooting.

---

## 📝 Important Note About Your Environment

**This module uses Ubuntu Linux.** The commands and examples shown work on:
- ✅ Native Ubuntu installation
- ✅ Ubuntu in VirtualBox/VMware
- ✅ Ubuntu on cloud VMs (AWS, GCP, Azure, Oracle Cloud)

**What might differ:**

**If you're using a VM (VirtualBox/VMware):**
- Interface names might be `enp0s3`, `enp0s8` instead of `eth0`
- IP addresses depend on your VM network mode:
  - **NAT mode:** VM gets IP like `10.0.2.15`, gateway `10.0.2.2`
  - **Bridged mode:** VM gets IP from your actual network
- You might see additional virtual interfaces

**If you're on a cloud VM (AWS/GCP/Azure):**
- Interface names might be `ens3`, `ens5`, `eth0`
- IP addresses are cloud-provider specific:
  - **AWS:** Often `172.31.x.x` (private)
  - **GCP:** Often `10.128.x.x` to `10.142.x.x`
  - **Azure:** Often `10.0.x.x` or `172.16.x.x`
  - **Oracle:** Often `10.0.x.x`

**Don't worry if your output looks different!** The concepts remain the same. Focus on understanding how to read the information, not matching exact output.

---

## Chapter 1: Understanding Your Network Configuration

Every device on a network needs an identifier - the IP address. Let's explore your network configuration by examining interfaces and their addresses together.

### Exploring Network Interfaces and IP Addresses

Run this command to see all network interfaces and their configuration:
```bash
ip addr show
```

**Example output:**
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536
    inet 127.0.0.1/8 scope host lo
    inet6 ::1/128 scope host

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
    inet 192.168.1.105/24 brd 192.168.1.255 scope global eth0
    inet6 fe80::5054:ff:fe12:3456/64 scope link

3: wlan0: <BROADCAST,MULTICAST> mtu 1500
    (no IP address - not connected)

4: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
```

**Understanding the output:**

Each numbered item represents a network interface - a connection point for networking.

**1. lo (Loopback):** Always present, address is always `127.0.0.1`. Used when your computer communicates with itself.

**2. eth0 (Ethernet):** Physical wired connection. Status `UP,LOWER_UP` means interface is enabled and cable is connected. Has IP `192.168.1.105/24`.

**3. wlan0 (WiFi):** Wireless adapter. Status `DOWN` means not connected. No IP assigned.

**4. docker0:** Virtual interface created by Docker. `NO-CARRIER` means no containers connected currently.

**Interface Status:**
- `UP` - Interface enabled
- `LOWER_UP` - Physical connection active
- `DOWN` - Disabled or disconnected

**About IPv6:** You'll see `inet6` lines - these are IPv6 addresses. For this workshop, we focus on IPv4 (the `inet` lines).

### Understanding IP Addresses

Looking at eth0: `inet 192.168.1.105/24`

This is the IP address with CIDR notation. An IPv4 address has four numbers (0-255) separated by dots. The `/24` indicates network size - this network has 256 addresses (192.168.1.0 through 192.168.1.255).

Common network sizes:
- `/24` = 256 addresses (home/lab networks)
- `/16` = 65,536 addresses
- `/8` = 16 million addresses

### Private vs Public IP Addresses

The address `192.168.1.105` is a **private IP** address - only valid within your local network.

**Understanding through an example:**

Consider a university campus with 10,000 devices. Instead of obtaining 10,000 unique public IP addresses, the campus uses private addressing:

**Campus private network:**
- Building A computers: `10.50.1.1` - `10.50.1.254`
- Building B computers: `10.50.2.1` - `10.50.2.254`
- WiFi devices: `10.50.10.x`
- Campus router: `10.50.0.1` (internal), `203.45.123.89` (external public IP)

All devices use private IPs internally. When accessing the internet, the router translates private addresses to its public IP.

**Another campus can use the exact same private addresses** - no conflict because they're isolated networks.

**Reserved private IP ranges:**

| Range | Typical Use |
|-------|-------------|
| 10.0.0.0 - 10.255.255.255 | Large organizations, cloud |
| 172.16.0.0 - 172.31.255.255 | Medium networks |
| 192.168.0.0 - 192.168.255.255 | Home networks, labs |

**Public IPs** are globally unique and routable on the internet.

### Finding Your Gateway

The gateway forwards traffic between your local network and the internet:
```bash
ip route show
```

**Example output:**
```
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 scope link
```

First line shows: to reach the internet, send packets to `192.168.1.1` via `eth0`.

**Your turn:**
```bash
ip addr show
ip route show
cat /etc/resolv.conf
```

**Observe:**
- Your IP address - Is it private?
- Your gateway IP
- Which interface is active?
- DNS servers configured

---

## Chapter 2: Testing Network Connectivity

The `ping` command tests reachability. Test systematically from your computer outward:

**Test 1: Localhost**
```bash
ping -c 3 127.0.0.1
```

**Example output:**
```
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.024 ms
```

Extremely low time (0.024ms) - packets loop back internally.

**Test 2: Gateway**
```bash
ping -c 3 192.168.1.1  # Use YOUR gateway
```

**Example output:**
```
64 bytes from 192.168.1.1: icmp_seq=1 ttl=64 time=2.34 ms
```

Time ~1-2ms - packets travel over your network to router.

**Test 3: Internet**
```bash
ping -c 3 8.8.8.8
```

**Example output:**
```
64 bytes from 8.8.8.8: icmp_seq=1 ttl=117 time=12.4 ms
```

Time ~10-50ms - packets travel through router, ISP, and internet.

**Test 4: DNS**
```bash
ping -c 3 google.com
```

First line shows `google.com (142.250.192.78)` - hostname resolved to IP.

**Troubleshooting logic:**

| If This Fails | But This Works | Problem Is |
|---------------|----------------|------------|
| Test 1 | - | System networking |
| Test 2 | Test 1 | Local network (cable/WiFi) |
| Test 3 | Test 2 | Internet connection |
| Test 4 | Test 3 | DNS configuration |

### Understanding DNS

DNS translates hostnames to IP addresses:
```bash
cat /etc/resolv.conf
```

Shows your DNS servers (like `nameserver 8.8.8.8`).

**Your turn:**
```bash
ping -c 3 127.0.0.1
ping -c 3 <YOUR_GATEWAY>
ping -c 3 8.8.8.8
ping -c 3 google.com
```

**Observe:** Do all succeed? Compare latency values.

---

## Chapter 3: Ports and Services

IP addresses identify devices, ports identify specific services. Together (IP:Port) they form a socket.

### Viewing Active Services
```bash
sudo ss -tuln
```

**Example output:**
```
State    Local Address:Port
LISTEN   0.0.0.0:22
LISTEN   127.0.0.1:631
LISTEN   0.0.0.0:4000
```

**Understanding:**
- `0.0.0.0:22` - SSH listening on ALL interfaces
- `127.0.0.1:631` - Print service on localhost only
- `0.0.0.0:4000` - Jekyll on all interfaces

**Command flags:**
- `-t` TCP, `-u` UDP, `-l` listening, `-n` numeric

**With process names:**
```bash
sudo ss -tulnp
```

Shows which program uses which port:
```
LISTEN  0.0.0.0:22    users:(("sshd",pid=1234))
```

**Your turn:**
```bash
sudo ss -tuln | grep LISTEN
sudo ss -tulnp | grep LISTEN
```

**Observe:** Which ports are listening? Which programs?

---

## Chapter 4: Packet Analysis with Wireshark

You've tested connectivity with ping and viewed ports with ss. Wireshark shows you the actual packets traveling across your network.

### Installing Wireshark
```bash
sudo apt install wireshark -y
```

When asked "Should non-superusers be able to capture packets?" - select **Yes**
```bash
sudo usermod -aG wireshark $USER
```

**Log out and back in** for this to take effect.

### Capturing Traffic
```bash
wireshark
```

**To capture:**
1. Double-click your main interface (eth0, enp0s3)
2. Packets appear in real-time
3. Click red Stop button when done

**Three panels:**
- **Top:** Packet list (one line per packet)
- **Middle:** Packet details (expandable structure)
- **Bottom:** Raw bytes

### Filtering Packets

Type in filter bar:
```
icmp                    # Ping traffic only
tcp                     # TCP traffic only
ip.addr == 8.8.8.8     # Specific IP only
```

Press Enter to apply, click X to clear.

### Examining Packets

Click any packet. In middle panel, expand sections:
```
▼ Internet Protocol Version 4
  Source: 192.168.1.105
  Destination: 8.8.8.8
  
▼ Internet Control Message Protocol
  Type: 8 (Echo request)
```

This shows packet structure - protocols used and data contained.

**Your turn:**
```bash
# Start Wireshark
wireshark &

# Capture on your interface (double-click it)

# Generate traffic
ping -c 5 8.8.8.8

# Stop capture
```

**Observe:**
1. Apply filter: `icmp`
2. Count packets (5 requests + 5 replies = 10)
3. Click a request, expand layers
4. See source/destination IPs
5. Try filter: `ip.addr == 8.8.8.8`

---

## Hands-On Exercises

### Exercise 1: Network Discovery (5 min)
```bash
ip addr show
ip route show
cat /etc/resolv.conf
```

**Record:** Your IP, gateway, DNS servers, main interface name

---

### Exercise 2: Connectivity Testing (5 min)
```bash
ping -c 3 127.0.0.1
ping -c 3 <YOUR_GATEWAY>
ping -c 3 8.8.8.8
ping -c 3 google.com
```

**Observe:** All succeed? Record latency for each.

---

### Exercise 3: Interface Check (4 min)
```bash
ip link show
ip route show | grep default
```

**Observe:** Which interfaces are UP? Which carries your traffic?

---

### Exercise 4: Port Discovery (4 min)
```bash
sudo ss -tuln | grep LISTEN
sudo ss -tulnp | grep LISTEN
```

**Observe:** Which ports listening? Which programs?

---

### Exercise 5: Wireshark Packet Capture (5 min)
```bash
# Start Wireshark
wireshark &

# Capture on your interface

# Generate ping traffic
ping -c 5 8.8.8.8

# Stop capture
```

**Tasks:**
1. Apply filter: `icmp`
2. Click a request packet, expand "Internet Protocol Version 4"
3. Click a reply packet, compare with request
4. Try filter: `tcp`
5. Save capture: File → Save As → `~/network-practice.pcapng`

---

## Self-Check Quiz

<details>
<summary>❓ Q1: IP address 10.128.50.100/24 - private or public? What's the range?</summary>

**Answer:** Private (10.x.x.x range). Network: 10.128.50.0 - 10.128.50.255 (256 addresses).
</details>

<details>
<summary>❓ Q2: Difference between `ip addr show` and `ip link show`?</summary>

**Answer:** `ip link show` shows interfaces and status. `ip addr show` additionally shows IP addresses.
</details>

<details>
<summary>❓ Q3: Can ping 8.8.8.8 but not google.com. What's wrong?</summary>

**Answer:** DNS resolution failing. Internet works (reach by IP), but can't translate hostnames.
</details>

<details>
<summary>❓ Q4: What does `0.0.0.0:22` mean in ss output?</summary>

**Answer:** Service listening on ALL interfaces, port 22. Accessible from any network.
</details>

<details>
<summary>❓ Q5: Gateway is 10.0.2.2, can ping it, but not 8.8.8.8. Where's the problem?</summary>

**Answer:** Beyond local network - router has no internet or ISP issue. Local network works fine.
</details>

<details>
<summary>❓ Q6: What does Wireshark do?</summary>

**Answer:** Captures and displays network packets, showing actual data traveling across networks in real-time.
</details>

<details>
<summary>❓ Q7: How to filter only ping traffic in Wireshark?</summary>

**Answer:** Type `icmp` in filter bar and press Enter.
</details>

---

## Summary

### Commands Mastered

| Command | Purpose |
|---------|---------|
| `ip addr show` | View interfaces and IPs |
| `ip link show` | View interface status |
| `ip route show` | View gateway and routing |
| `ping` | Test connectivity |
| `ss -tuln` | View listening ports |
| `ss -tulnp` | View ports with programs |
| `cat /etc/resolv.conf` | View DNS servers |
| `wireshark` | Capture and analyze packets |

### Key Concepts

- ✓ IP addresses identify devices on networks
- ✓ Interfaces are connection points (physical or virtual)
- ✓ Private IPs (10.x, 172.16-31.x, 192.168.x) for local networks
- ✓ Public IPs are globally unique
- ✓ Gateways connect local network to internet
- ✓ CIDR notation (/24, /16) indicates network size
- ✓ Ports identify services (IP:Port = socket)
- ✓ Systematic testing isolates problems
- ✓ Wireshark visualizes actual network traffic

### What You Can Do Now

- ✓ Check network configuration quickly
- ✓ Test connectivity systematically
- ✓ View running services and ports
- ✓ Capture network packets
- ✓ Filter traffic to focus on specific protocols
- ✓ Examine packet structure
- ✓ Troubleshoot basic network issues

These skills will be used throughout the workshop for verifying setup and troubleshooting connectivity.

---

## Additional Resources

**For deeper understanding:**
- [Linux Networking Commands](https://www.tecmint.com/linux-networking-commands/)
- [Wireshark User Guide](https://www.wireshark.org/docs/wsug_html_chunked/)
- [TCP/IP Fundamentals](https://www.cloudflare.com/learning/network-layer/)

**Online tools:**
- [Subnet Calculator](https://www.subnet-calculator.com/)
- [What Is My IP?](https://whatismyipaddress.com/)

---

**Module Complete!** ✅

You now have the networking foundation needed for the workshop.

[⬅️ Previous: Module 5](module-5-c-compilation) | [Workshop Schedule →](../../workshop/)

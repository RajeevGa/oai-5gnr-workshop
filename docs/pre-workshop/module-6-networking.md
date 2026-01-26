---
layout: default
title: "Module 6: Network Basics"
parent: Pre-Workshop Preparation
nav_order: 6
---

# 🌐 Module 6: Networking Basics

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

## 🎯 How to Use This Module

Follow along with the examples as you read. You'll explore your own network configuration and practice troubleshooting commands. At the end, you'll complete exercises to test your understanding.

**Keep your terminal open throughout!**

---

## Introduction

Modern networks rely on IP networking to connect devices across the globe - sometimes even from space! You've likely seen IP addresses while setting up your home router or connecting to campus WiFi. Starting with 4G LTE, cellular networks also adopted this all-IP approach. This module covers the IP and Linux networking basics you'll need to configure and work with networks during the workshop's hands-on sessions.

This is a quick brush-up focusing on practical Linux commands for network exploration and troubleshooting.

---

## 🔍 Important Note About Your Environment

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

### Installing Network Tools

First, make sure you have the necessary tools:

```bash
sudo apt update
sudo apt install -y iproute2 net-tools
```

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

The gateway forwards traffic between your local network and the internet. Check it:

```bash
ip route show
```

**Example output:**
```
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 scope link
```

First line shows: to reach the internet, send packets to `192.168.1.1` via `eth0`.

### Finding Your DNS Servers

DNS servers translate hostnames to IP addresses:

```bash
cat /etc/resolv.conf
```

**Example output:**
```
nameserver 8.8.8.8
nameserver 8.8.4.4
```

Now let's check your configuration. Run these commands:

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

The `ping` command tests reachability. Let's test systematically from your computer outward.

### Test 1: Localhost

First, test your own computer:

```bash
ping -c 3 127.0.0.1
```

**Example output:**
```
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.024 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.031 ms
64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.028 ms
```

Extremely low time (0.024ms) - packets loop back internally. This always works if networking is functioning.

### Test 2: Gateway

Test your local network by pinging the gateway:

```bash
ping -c 3 192.168.1.1  # Use YOUR gateway from ip route show
```

**Example output:**
```
64 bytes from 192.168.1.1: icmp_seq=1 ttl=64 time=2.34 ms
```

Time ~1-2ms - packets travel over your network to router. If this fails, check your cable or WiFi connection.

### Test 3: Internet

Test internet connectivity:

```bash
ping -c 3 8.8.8.8
```

**Example output:**
```
64 bytes from 8.8.8.8: icmp_seq=1 ttl=117 time=12.4 ms
```

Time ~10-50ms - packets travel through router, ISP, and internet. This is Google's public DNS server.

### Test 4: DNS Resolution

Test both internet and DNS:

```bash
ping -c 3 google.com
```

First line shows `google.com (142.250.192.78)` - hostname was resolved to IP address before pinging.

### Troubleshooting Logic

Use this systematic approach:

| If This Fails | But This Works | Problem Is |
|---------------|----------------|------------|
| Test 1 (localhost) | - | System networking broken |
| Test 2 (gateway) | Test 1 | Local network (cable/WiFi) |
| Test 3 (internet) | Test 2 | Router or ISP issue |
| Test 4 (DNS) | Test 3 | DNS configuration |

Now try all four tests yourself:

```bash
ping -c 3 127.0.0.1
ping -c 3 <YOUR_GATEWAY>  # Replace with your gateway IP
ping -c 3 8.8.8.8
ping -c 3 google.com
```

**Observe:** Do all succeed? Compare latency values - they should increase as you go further from your computer.

---

## Chapter 3: Ports and Services

IP addresses identify devices, ports identify specific services running on those devices. Together (IP:Port) they form a socket - a unique endpoint for network communication.

### Understanding Ports

Common port numbers:
- Port 22: SSH (remote login)
- Port 80: HTTP (web)
- Port 443: HTTPS (secure web)
- Port 3306: MySQL database
- Port 5432: PostgreSQL database

### Viewing Active Services

See what's listening on your system:

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
- `0.0.0.0:22` - SSH listening on ALL interfaces (accessible from network)
- `127.0.0.1:631` - Print service on localhost only (not accessible from network)
- `0.0.0.0:4000` - Some service on all interfaces

**Command flags:**
- `-t` = TCP connections
- `-u` = UDP connections
- `-l` = Listening sockets only
- `-n` = Show numeric ports (don't resolve service names)

### Viewing with Process Names

See which program is using each port:

```bash
sudo ss -tulnp
```

**Example output:**
```
LISTEN  0.0.0.0:22    users:(("sshd",pid=1234,fd=3))
LISTEN  0.0.0.0:4000  users:(("ruby",pid=5678,fd=12))
```

Now you can see that SSH daemon (`sshd`) is using port 22, and Ruby (probably Jekyll) is using port 4000.

Try it yourself:

```bash
sudo ss -tuln | grep LISTEN
sudo ss -tulnp | grep LISTEN
```

**Observe:** 
- Which ports are listening?
- Which programs are using them?
- Are they listening on all interfaces (0.0.0.0) or just localhost (127.0.0.1)?

---

## Chapter 4: Packet Analysis with Wireshark

You've tested connectivity with ping and viewed ports with ss. Wireshark lets you see the actual packets traveling across your network - the raw data being sent and received.

### Installing Wireshark

```bash
sudo apt install -y wireshark
```

During installation, you'll be asked: "Should non-superusers be able to capture packets?"

Select **Yes**, then add yourself to the wireshark group:

```bash
sudo usermod -aG wireshark $USER
```

**Important:** Log out and log back in for this to take effect.

### Starting Wireshark

Launch Wireshark:

```bash
wireshark
```

The GUI will open showing a list of network interfaces.

### Capturing Traffic

**To start capturing:**
1. Double-click your main interface (eth0, enp0s3, or whatever you identified earlier)
2. Packets will start appearing in real-time
3. Click the red Stop button when done

**Understanding the interface:**

Wireshark has three panels:
- **Top:** Packet list (one line per packet)
- **Middle:** Packet details (expandable structure)
- **Bottom:** Raw bytes (hexadecimal)

### Filtering Packets

With many packets flying by, filters help you focus. Type in the filter bar at the top:

```
icmp
```

Press Enter. Now you only see ping traffic.

**Other useful filters:**
```
tcp                     # TCP traffic only
udp                     # UDP traffic only
ip.addr == 8.8.8.8     # Traffic to/from specific IP
tcp.port == 22         # SSH traffic only
```

Press the X button next to the filter to clear it.

### Examining Packets

Click any packet in the top panel. In the middle panel, you'll see expandable sections:

```
▼ Ethernet II
▼ Internet Protocol Version 4
  Source: 192.168.1.105
  Destination: 8.8.8.8
  
▼ Internet Control Message Protocol
  Type: 8 (Echo request)
```

Click the arrows to expand/collapse sections. This shows the layered structure of network protocols.

Let's try it. First, start Wireshark:

```bash
wireshark &
```

**Now capture some traffic:**

1. In Wireshark, double-click your main interface to start capturing
2. In your terminal, generate some ping traffic:

```bash
ping -c 5 8.8.8.8
```

3. Back in Wireshark, click the red Stop button
4. In the filter bar, type `icmp` and press Enter

**Observe:**
- You should see 10 packets (5 requests + 5 replies)
- Click a "request" packet, expand "Internet Protocol Version 4"
- See source (your IP) and destination (8.8.8.8)
- Click a "reply" packet - source and destination are swapped
- Clear the filter and try: `ip.addr == 8.8.8.8`

---

## 💻 Hands-On Exercises

Test your networking skills!

### Exercise 1: Network Configuration Discovery

**Problem:** Document your complete network configuration.

**Requirements:**
- Find your IP address and interface name
- Find your gateway IP
- Find your DNS servers
- Determine if your IP is private or public
- Identify the network size (CIDR notation)

<details markdown="1">
<summary>💡 Hint</summary>

Use `ip addr show` for IP and interface, `ip route show` for gateway, `cat /etc/resolv.conf` for DNS.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
# Get IP and interface
ip addr show

# Get gateway
ip route show | grep default

# Get DNS servers
cat /etc/resolv.conf

# Example results:
# IP: 192.168.1.105/24 (private, 10.x, 172.16-31.x, or 192.168.x)
# Interface: eth0 or enp0s3 or ens3
# Gateway: 192.168.1.1
# DNS: 8.8.8.8, 8.8.4.4
# Network size: /24 = 256 addresses
```
</details>

---

### Exercise 2: Systematic Connectivity Testing

**Problem:** Test your network connectivity using the systematic approach.

**Requirements:**
- Test localhost
- Test gateway
- Test internet (8.8.8.8)
- Test DNS resolution (google.com)
- Record the latency (time) for each test
- If any test fails, identify where the problem is

<details markdown="1">
<summary>💡 Hint</summary>

Use `ping -c 3` for each test. Look at the "time=" value in the output. Use the troubleshooting table from Chapter 2.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
# Test 1: Localhost
ping -c 3 127.0.0.1
# Expected: ~0.02ms, always works

# Test 2: Gateway (use YOUR gateway)
ping -c 3 <your-gateway-ip>
# Expected: ~1-5ms

# Test 3: Internet
ping -c 3 8.8.8.8
# Expected: ~10-50ms

# Test 4: DNS
ping -c 3 google.com
# Expected: Similar to test 3

# If test 2 fails but test 1 works: Local network problem
# If test 3 fails but test 2 works: Internet/ISP problem
# If test 4 fails but test 3 works: DNS problem
```
</details>

---

### Exercise 3: Interface Status Check

**Problem:** Determine which interfaces are active and which is carrying your traffic.

**Requirements:**
- List all network interfaces
- Identify which are UP
- Find which interface has your default route
- Check if any Docker or virtual interfaces exist

<details markdown="1">
<summary>💡 Hint</summary>

Use `ip link show` for interface status, `ip route show` for routing. Look for `UP` in status, and `default via` in routing.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
# List all interfaces with status
ip link show

# Check which interface has default route
ip route show | grep default

# Detailed address info
ip addr show

# Interface carrying traffic is the one in "default via X.X.X.X dev INTERFACE"
```
</details>

---

### Exercise 4: Port and Service Discovery

**Problem:** Identify what services are running on your system.

**Requirements:**
- List all listening TCP ports
- List all listening UDP ports
- Identify which programs are using these ports
- Find if SSH (port 22) is running
- Count how many services are listening

<details markdown="1">
<summary>💡 Hint</summary>

Use `sudo ss -tuln` for ports, add `p` flag for program names. Use `grep` to filter for specific ports or count with `wc -l`.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
# All listening ports
sudo ss -tuln | grep LISTEN

# With program names
sudo ss -tulnp | grep LISTEN

# Check if SSH is running
sudo ss -tulnp | grep :22

# Count listening services
sudo ss -tuln | grep LISTEN | wc -l

# Example: If you see 0.0.0.0:22, SSH is accessible from network
# If you see 127.0.0.1:631, that service only accepts local connections
```
</details>

---

### Exercise 5: Basic Wireshark Capture

**Problem:** Capture and analyze ping traffic using Wireshark.

**Requirements:**
- Start Wireshark and begin capturing on your main interface
- Generate 5 pings to 8.8.8.8
- Stop the capture
- Filter to show only ICMP traffic
- Count the total packets (requests + replies)
- Examine one request and one reply packet
- Save the capture file

<details markdown="1">
<summary>💡 Hint</summary>

Double-click interface to start capture. Use `icmp` filter. Expand "Internet Protocol" section in middle panel. Save with File → Save As.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
# Start Wireshark
wireshark &

# In Wireshark: Double-click your main interface

# In terminal: Generate traffic
ping -c 5 8.8.8.8

# In Wireshark:
# 1. Click red Stop button
# 2. Type "icmp" in filter bar, press Enter
# 3. Should see 10 packets (5 requests + 5 replies)
# 4. Click a request packet
# 5. In middle panel, expand "Internet Protocol Version 4"
# 6. Note: Source = your IP, Destination = 8.8.8.8
# 7. Click a reply packet
# 8. Note: Source and Destination are swapped
# 9. File → Save As → ~/ping-capture.pcapng
```
</details>

---

### Exercise 6: DNS Investigation

**Problem:** Investigate how DNS works using both command-line and Wireshark.

**Requirements:**
- Check your DNS servers
- Use Wireshark to capture DNS traffic
- In terminal, run: `ping -c 1 github.com`
- Stop capture and filter for DNS traffic (`dns`)
- Identify the DNS query and response packets
- Find what IP address github.com resolved to

<details markdown="1">
<summary>💡 Hint</summary>

DNS uses UDP port 53. In Wireshark, use filter `dns`. Look for "Standard query" and "Standard query response" packets. The IP will be in the response packet.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
# Check DNS servers
cat /etc/resolv.conf

# Start Wireshark, double-click interface

# Generate DNS traffic
ping -c 1 github.com

# In Wireshark:
# 1. Stop capture
# 2. Filter: dns
# 3. Find "Standard query" packet (your computer asking for github.com)
# 4. Find "Standard query response" packet (DNS server responding)
# 5. Click response packet
# 6. Expand "Domain Name System (response)"
# 7. Look for "github.com: type A, class IN, addr X.X.X.X"
# 8. That's the IP address!
```
</details>

---

### Exercise 7: Troubleshooting Scenario

**Problem:** Simulate and diagnose a network problem.

**Requirements:**
- Temporarily change your DNS server to an invalid one (1.2.3.4)
- Test if you can still ping 8.8.8.8
- Test if you can ping google.com
- Explain why one works and one doesn't
- Restore your original DNS configuration

<details markdown="1">
<summary>💡 Hint</summary>

Edit `/etc/resolv.conf` with sudo. Ping by IP works without DNS. Ping by hostname needs DNS. Reboot or restore the file to fix.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
# Backup original DNS config
sudo cp /etc/resolv.conf /etc/resolv.conf.backup

# Set invalid DNS
echo "nameserver 1.2.3.4" | sudo tee /etc/resolv.conf

# Test IP - WORKS
ping -c 3 8.8.8.8
# This works because it doesn't need DNS

# Test hostname - FAILS
ping -c 3 google.com
# This fails because DNS can't resolve the name

# Explanation: Internet connection is fine, but DNS is broken

# Restore DNS
sudo mv /etc/resolv.conf.backup /etc/resolv.conf

# Verify it works again
ping -c 3 google.com
```
</details>

---

## ✅ Self-Check Quiz

<details markdown="1">
<summary>❓ Q1: IP address 10.128.50.100/24 - private or public? What's the network range?</summary>

**Answer:** Private (10.x.x.x range). Network: 10.128.50.0 - 10.128.50.255 (256 addresses, /24 = 2^8).
</details>

<details markdown="1">
<summary>❓ Q2: What's the difference between `ip addr show` and `ip link show`?</summary>

**Answer:** `ip link show` shows interfaces and their status (UP/DOWN). `ip addr show` additionally shows IP addresses assigned to each interface.
</details>

<details markdown="1">
<summary>❓ Q3: Can ping 8.8.8.8 but not google.com. What's wrong?</summary>

**Answer:** DNS resolution is failing. Internet connection works (can reach by IP), but can't translate hostnames to IPs. Check `/etc/resolv.conf`.
</details>

<details markdown="1">
<summary>❓ Q4: What does `0.0.0.0:22` mean in ss output?</summary>

**Answer:** Service listening on ALL interfaces at port 22 (SSH). Accessible from any network the machine is connected to, not just localhost.
</details>

<details markdown="1">
<summary>❓ Q5: Gateway is 10.0.2.2, can ping it, but not 8.8.8.8. Where's the problem?</summary>

**Answer:** Beyond local network - gateway/router has no internet connection or ISP issue. Local network works fine (can reach gateway).
</details>

<details markdown="1">
<summary>❓ Q6: What does Wireshark do?</summary>

**Answer:** Captures and displays network packets in real-time, showing the actual data traveling across networks with full protocol details.
</details>

<details markdown="1">
<summary>❓ Q7: How do you filter only ping traffic in Wireshark?</summary>

**Answer:** Type `icmp` in the filter bar and press Enter. ICMP is the protocol used by ping.
</details>

<details markdown="1">
<summary>❓ Q8: What's the difference between private and public IP addresses?</summary>

**Answer:** Private IPs (10.x, 172.16-31.x, 192.168.x) are for local networks only, can be reused. Public IPs are globally unique and routable on the internet.
</details>

---

## 🎓 Summary

### Commands Mastered

| Command | Purpose |
|---------|---------|
| `ip addr show` | View interfaces and IP addresses |
| `ip link show` | View interface status only |
| `ip route show` | View gateway and routing table |
| `ping -c N` | Test connectivity (N packets) |
| `ss -tuln` | View listening TCP/UDP ports |
| `ss -tulnp` | View ports with program names |
| `cat /etc/resolv.conf` | View DNS server configuration |
| `wireshark` | Capture and analyze network packets |

### Key Concepts

- ✓ IP addresses identify devices on networks
- ✓ Network interfaces are connection points (physical or virtual)
- ✓ Private IPs (10.x, 172.16-31.x, 192.168.x) for local networks
- ✓ Public IPs are globally unique and internet-routable
- ✓ Gateways connect local networks to the internet
- ✓ CIDR notation (/24, /16) indicates network size
- ✓ Ports identify specific services (IP:Port = socket)
- ✓ Systematic testing isolates network problems
- ✓ Wireshark visualizes actual network traffic
- ✓ DNS translates hostnames to IP addresses

### What You Can Do Now

- ✓ Check network configuration on any Linux system
- ✓ Test connectivity systematically to isolate problems
- ✓ View running services and which ports they use
- ✓ Capture and analyze network packets
- ✓ Filter Wireshark traffic to focus on specific protocols
- ✓ Examine packet structure and understand protocols
- ✓ Troubleshoot basic network connectivity issues
- ✓ Distinguish between local network, internet, and DNS problems

These skills will be used throughout the workshop for verifying setup, testing connectivity, and troubleshooting network issues.

---

## 📚 Additional Resources

**For deeper understanding:**
- [Linux Networking Commands](https://www.tecmint.com/linux-networking-commands/)
- [Wireshark User Guide](https://www.wireshark.org/docs/wsug_html_chunked/)
- [TCP/IP Fundamentals](https://www.cloudflare.com/learning/network-layer/)

**Online tools:**
- [Subnet Calculator](https://www.subnet-calculator.com/)
- [What Is My IP?](https://whatismyipaddress.com/)

---

**Module Complete!** ✅

You now have the networking foundation needed for the workshop!

[⬅️ Previous: Module 5](module-5-c-compilation) | [Workshop Schedule →](../../workshop/)

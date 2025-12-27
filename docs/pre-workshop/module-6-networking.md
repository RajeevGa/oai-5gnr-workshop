---
layout: default
title: "Module 6: Network Basics"
parent: Pre-Workshop Preparation
nav_order: 6
---

# 🌐 Module 6: Network Basics

> **Duration:** 20 minutes | **Level:** Beginner | **Hands-on:** Yes

---

## 📚 What You'll Learn

By the end of this module, you'll be able to:

- [ ] Understand IP addresses and subnets
- [ ] Check your network configuration
- [ ] Test network connectivity
- [ ] View network interfaces
- [ ] Troubleshoot basic network issues
- [ ] Understand OAI network architecture

---

## 📖 Chapter 1: Network Fundamentals

### What is an IP Address?

An **IP address** is like a phone number for computers - it identifies devices on a network.

**IPv4 Format:**
```
192.168.1.100
 │   │   │  │
 └───┴───┴──┴─── Four numbers (0-255) separated by dots
```

### Types of IP Addresses

**Private IP addresses** (internal network):
```
10.0.0.0    - 10.255.255.255     (Large networks)
172.16.0.0  - 172.31.255.255     (Medium networks)
192.168.0.0 - 192.168.255.255    (Home/small networks)
```

**Special addresses:**
```
127.0.0.1   - Localhost (your own computer)
0.0.0.0     - All interfaces / Any address
255.255.255.255 - Broadcast (send to everyone)
```

**Public IP addresses:**
- Everything else (routable on the internet)
- Assigned by your ISP
- Example: 8.8.8.8 (Google DNS)

### Subnet Masks

A **subnet mask** defines which part is network and which part is host.

**Common subnet masks:**
```
255.255.255.0   = /24 = 256 addresses (254 usable)
255.255.0.0     = /16 = 65,536 addresses
255.0.0.0       = /8  = 16,777,216 addresses
```

**Example:**
```
IP Address:    192.168.1.100
Subnet Mask:   255.255.255.0
Network:       192.168.1.0
Your device:   192.168.1.100
Gateway:       192.168.1.1 (typically)
Broadcast:     192.168.1.255
```

### CIDR Notation

**CIDR** = Classless Inter-Domain Routing
```
192.168.1.0/24
            └── Number of network bits

/24 = 255.255.255.0   (256 addresses)
/16 = 255.255.0.0     (65,536 addresses)
/8  = 255.0.0.0       (16,777,216 addresses)
```

---

## 🖥️ Chapter 2: Network Interfaces

### What is a Network Interface?

A **network interface** is a connection point between your computer and a network.

**Types:**
- `lo` - Loopback (127.0.0.1, talks to itself)
- `eth0`, `eth1` - Ethernet (wired)
- `wlan0`, `wlan1` - WiFi (wireless)
- `enp0s3`, `ens33` - Modern naming convention
- `docker0` - Docker virtual network
- `br-xxxxx` - Network bridges

### Viewing Network Interfaces
```bash
# Modern command
ip addr show

# Or shorter
ip a

# Legacy command (still works)
ifconfig
```

**Example output:**
```
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
    inet 192.168.1.100/24 brd 192.168.1.255 scope global eth0
    inet6 fe80::a00:27ff:fe4e:66a1/64 scope link
```

**Reading the output:**
- `eth0` - Interface name
- `UP` - Interface is active
- `192.168.1.100/24` - IP address and subnet
- `mtu 1500` - Maximum transmission unit (packet size)

---

## 🔍 Chapter 3: Essential Network Commands

### Checking Your IP Address
```bash
# Show all interfaces
ip addr show

# Show specific interface
ip addr show eth0

# Show only IPv4 addresses
ip -4 addr

# Quick way to get your IP
hostname -I
```

### Checking Interface Status
```bash
# List all interfaces
ip link show

# Check if interface is UP
ip link show eth0
```

**Status indicators:**
- `UP` - Interface is enabled
- `DOWN` - Interface is disabled
- `LOWER_UP` - Physical connection is active

### Viewing Routing Table

The **routing table** tells your computer where to send network traffic.
```bash
# Show routing table
ip route show

# Or
route -n
```

**Example output:**
```
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.100
```

**Reading it:**
- `default via 192.168.1.1` - Default gateway (router)
- `dev eth0` - Use interface eth0
- `192.168.1.0/24` - Local network route

---

## 🔌 Chapter 4: Testing Connectivity

### Ping - Test Reachability

**Ping** sends packets to test if a host is reachable.
```bash
# Ping Google DNS
ping 8.8.8.8

# Ping with count
ping -c 4 8.8.8.8

# Ping by hostname
ping google.com

# Ping localhost
ping 127.0.0.1
```

**Example output:**
```
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=117 time=10.2 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=117 time=10.5 ms
```

**Reading the output:**
- `64 bytes` - Packet size
- `icmp_seq=1` - Packet sequence number
- `time=10.2 ms` - Round-trip time (latency)
- `ttl=117` - Time to live (hops remaining)

**What it means:**
- ✅ Replies received = Network is working
- ❌ No replies = Network issue or host down
- ❌ "Destination Host Unreachable" = Routing problem
- ❌ "Name or service not known" = DNS problem

### Testing Gateway
```bash
# Find your gateway
ip route | grep default

# Ping the gateway
ping -c 3 192.168.1.1
```

If gateway responds → Local network OK ✓

### Testing Internet Connectivity
```bash
# Test internet (Google DNS)
ping -c 3 8.8.8.8

# Test DNS resolution
ping -c 3 google.com
```

**Troubleshooting logic:**
```
ping 127.0.0.1      OK? → Network stack works
ping 192.168.1.1    OK? → Local network works
ping 8.8.8.8        OK? → Internet connection works
ping google.com     OK? → DNS resolution works
```

---

## 🔎 Chapter 5: Advanced Network Commands

### Traceroute - Path to Destination

**Traceroute** shows the path packets take to reach a destination.
```bash
# Install if needed
sudo apt install traceroute

# Trace route to Google
traceroute google.com

# Or use
tracepath google.com
```

**Example output:**
```
 1  192.168.1.1        1.234 ms   (your router)
 2  10.0.0.1          10.123 ms   (ISP)
 3  72.14.204.1       15.567 ms   (Google network)
 4  8.8.8.8           20.234 ms   (Google DNS)
```

### Netstat / SS - Network Statistics

**netstat** (legacy) or **ss** (modern) show network connections.
```bash
# Show listening ports
sudo ss -tuln

# Show all connections
ss -tun

# Show listening TCP ports
sudo ss -tln

# Legacy equivalent
sudo netstat -tuln
```

**Flags explained:**
- `-t` = TCP connections
- `-u` = UDP connections
- `-l` = Listening sockets
- `-n` = Numeric (don't resolve names)
- `-p` = Show process (needs sudo)

**Example output:**
```
State    Recv-Q Send-Q  Local Address:Port   Peer Address:Port
LISTEN   0      128     0.0.0.0:22           0.0.0.0:*
LISTEN   0      128     0.0.0.0:80           0.0.0.0:*
```

**Reading it:**
- `LISTEN` - Waiting for connections
- `0.0.0.0:22` - Listening on all interfaces, port 22 (SSH)
- `0.0.0.0:80` - Web server on port 80

### DNS Lookup
```bash
# Lookup IP for hostname
nslookup google.com

# Or use dig (more detailed)
dig google.com

# Reverse lookup (IP to hostname)
nslookup 8.8.8.8
```

### Testing Port Connectivity
```bash
# Check if port is open (using netcat)
nc -zv google.com 80

# Or use telnet
telnet google.com 80
```

**Use case:** Check if OAI AMF port 38412 is accessible:
```bash
nc -zv 192.168.70.132 38412
```

---

## 🏗️ Chapter 6: Network Configuration

### Viewing Current Configuration
```bash
# Complete network configuration
ip addr show

# DNS servers
cat /etc/resolv.conf

# Hostname
hostname
```

### Static vs DHCP

**DHCP** (Dynamic Host Configuration Protocol):
- Automatic IP assignment
- Gets IP from router/server
- Most common for laptops

**Static IP**:
- Manually configured
- Doesn't change
- Used for servers, network equipment
- OAI components often use static IPs

### Ubuntu Network Configuration

**Modern Ubuntu uses Netplan:**
```bash
# Configuration files location
ls /etc/netplan/

# Example config (view only, don't edit yet)
cat /etc/netplan/*.yaml
```

**Example Netplan config:**
```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: true
```

**Static IP example:**
```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.1.100/24
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
```

---

## 📡 Chapter 7: OAI Network Architecture

### OAI Network Interfaces

OAI uses multiple network interfaces for different purposes:
```
┌─────────────────────────────────────────┐
│                 5G Core                 │
│  ┌─────┐  ┌─────┐  ┌─────┐  ┌─────┐  │
│  │ AMF │  │ SMF │  │ UPF │  │ NRF │  │
│  └─────┘  └─────┘  └─────┘  └─────┘  │
│     ↑         ↑         ↑              │
│     └─────────┴─────────┴──────────────┼─── Management Network
│              N2        N3               │     (192.168.70.0/24)
└──────────────┬─────────┬───────────────┘
               │         │
            ┌──┴─────────┴──┐
            │      gNB       │
            └────────────────┘
                    ↑
                    │ NR Interface
                    │
                ┌───┴───┐
                │  UE   │
                └───────┘
```

### Typical OAI IP Addressing

**Core Network (Docker):**
```
MySQL:     192.168.70.131
NRF:       192.168.70.130
AMF:       192.168.70.132
SMF:       192.168.70.133
UPF:       192.168.70.134
Network:   192.168.70.0/24
```

**UE Network:**
```
UE IP range: 12.1.1.0/24
UE gets:     12.1.1.2, 12.1.1.3, etc.
```

**Key Interfaces:**
- **N1** - UE ↔ AMF (via gNB)
- **N2** - gNB ↔ AMF (NGAP protocol)
- **N3** - gNB ↔ UPF (user data)
- **N4** - SMF ↔ UPF (session management)

### Checking OAI Network Connectivity
```bash
# Check if you can reach AMF
ping -c 2 192.168.70.132

# Check if AMF port is open
nc -zv 192.168.70.132 38412

# Check Docker network
docker network ls
docker network inspect oai-public-net

# Check container IPs
docker inspect oai-amf | grep IPAddress
```

---

## 🛠️ Chapter 8: Troubleshooting

### Systematic Approach
```
1. Check physical connection
   └─> Cable plugged in? WiFi connected?

2. Check interface status
   └─> ip link show (interface UP?)

3. Check IP address
   └─> ip addr show (have IP?)

4. Check local network
   └─> ping gateway

5. Check internet
   └─> ping 8.8.8.8

6. Check DNS
   └─> ping google.com
```

### Common Issues and Solutions

**Issue: No IP Address**
```bash
# Check DHCP
sudo dhclient eth0

# Restart networking
sudo systemctl restart NetworkManager
```

**Issue: Can't reach gateway**
```bash
# Check routing
ip route show

# Check interface is up
ip link set eth0 up
```

**Issue: Internet works but DNS doesn't**
```bash
# Check DNS servers
cat /etc/resolv.conf

# Try different DNS
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf

# Test
ping google.com
```

**Issue: Port already in use**
```bash
# Find what's using port
sudo ss -tulpn | grep :8080

# Or
sudo lsof -i :8080

# Kill process if needed
sudo kill <PID>
```

---

## 🧪 Hands-On Exercise

### Task 1: Network Discovery (5 min)
```bash
# Find your IP address
ip addr show

# Find your gateway
ip route | grep default

# Find your DNS servers
cat /etc/resolv.conf

# Test localhost
ping -c 2 127.0.0.1

# Create summary
echo "=== My Network Configuration ==="
echo "IP Address: $(hostname -I)"
echo "Gateway: $(ip route | grep default | awk '{print $3}')"
echo "DNS: $(cat /etc/resolv.conf | grep nameserver | awk '{print $2}')"
```

**Expected:** Know your network configuration

---

### Task 2: Connectivity Testing (5 min)
```bash
# Test local network
GATEWAY=$(ip route | grep default | awk '{print $3}')
echo "Testing gateway: $GATEWAY"
ping -c 3 $GATEWAY

# Test internet
echo "Testing internet connectivity..."
ping -c 3 8.8.8.8

# Test DNS
echo "Testing DNS resolution..."
ping -c 3 google.com

# Create report
if ping -c 1 8.8.8.8 > /dev/null 2>&1; then
    echo "✓ Internet: OK"
else
    echo "✗ Internet: FAILED"
fi

if ping -c 1 google.com > /dev/null 2>&1; then
    echo "✓ DNS: OK"
else
    echo "✗ DNS: FAILED"
fi
```

**Expected:** Connectivity verified

---

### Task 3: Port Investigation (5 min)
```bash
# See what's listening
echo "=== Listening Ports ==="
sudo ss -tulpn | grep LISTEN

# Check specific port
echo "=== Checking SSH (port 22) ==="
sudo ss -tulpn | grep :22

# If Docker is running
echo "=== Docker ports ==="
docker ps --format "{{.Names}}: {{.Ports}}"

# Test connectivity to Google
echo "=== Testing port 80 to Google ==="
nc -zv google.com 80
```

**Expected:** Understand what ports are in use

---

### Task 4: Network Troubleshooting Script (5 min)
```bash
# Create diagnostic script
cat > ~/network-check.sh << 'EOF'
#!/bin/bash

echo "==================================="
echo "   Network Diagnostic Tool"
echo "==================================="
echo ""

# Interface check
echo "1. Network Interfaces:"
ip link show | grep "UP" | awk '{print "   ✓", $2}'
echo ""

# IP Address
echo "2. IP Configuration:"
ip -4 addr show | grep inet | awk '{print "   ", $2, "on", $NF}'
echo ""

# Gateway
echo "3. Default Gateway:"
ip route | grep default | awk '{print "   ", $3, "via", $5}'
echo ""

# DNS
echo "4. DNS Servers:"
cat /etc/resolv.conf | grep nameserver | awk '{print "   ", $2}'
echo ""

# Connectivity Tests
echo "5. Connectivity:"

if ping -c 1 -W 2 127.0.0.1 > /dev/null 2>&1; then
    echo "   ✓ Localhost (127.0.0.1)"
else
    echo "   ✗ Localhost FAILED"
fi

GW=$(ip route | grep default | awk '{print $3}')
if [ -n "$GW" ]; then
    if ping -c 1 -W 2 $GW > /dev/null 2>&1; then
        echo "   ✓ Gateway ($GW)"
    else
        echo "   ✗ Gateway FAILED"
    fi
fi

if ping -c 1 -W 2 8.8.8.8 > /dev/null 2>&1; then
    echo "   ✓ Internet (8.8.8.8)"
else
    echo "   ✗ Internet FAILED"
fi

if ping -c 1 -W 2 google.com > /dev/null 2>&1; then
    echo "   ✓ DNS (google.com)"
else
    echo "   ✗ DNS FAILED"
fi

echo ""
echo "==================================="
EOF

# Make executable
chmod +x ~/network-check.sh

# Run it
~/network-check.sh
```

**Expected:** Complete network diagnostic report

---

## ✅ Self-Check Quiz

<details>
<summary>❓ What is 127.0.0.1?</summary>

**Answer:** Localhost / loopback address - refers to your own computer. Used to test if network stack is working.
</details>

<details>
<summary>❓ What does `/24` mean in `192.168.1.0/24`?</summary>

**Answer:** CIDR notation indicating 24 network bits, which means subnet mask 255.255.255.0, giving 256 addresses (254 usable).
</details>

<details>
<summary>❓ What's the difference between `ping 8.8.8.8` and `ping google.com`?</summary>

**Answer:** 
- `ping 8.8.8.8` tests internet connectivity (IP address)
- `ping google.com` tests internet + DNS resolution (hostname)
</details>

<details>
<summary>❓ What does `ip addr show` display?</summary>

**Answer:** Shows all network interfaces, their status (UP/DOWN), IP addresses, and configuration.
</details>

<details>
<summary>❓ How do you find your default gateway?</summary>

**Answer:** `ip route | grep default` or `ip route show`
</details>

<details>
<summary>❓ What port does OAI AMF use for N2 interface?</summary>

**Answer:** Port 38412 (SCTP protocol)
</details>

<details>
<summary>❓ What's the typical OAI Core network range?</summary>

**Answer:** 192.168.70.0/24 (for Docker containers)
</details>

<details>
<summary>❓ How do you check if a port is open?</summary>

**Answer:** `nc -zv hostname port` or `telnet hostname port`
</details>

---

## 🎓 Summary

### Commands Mastered

| Command | Purpose | Example |
|---------|---------|---------|
| `ip addr show` | Show IP addresses | `ip addr show eth0` |
| `ip link show` | Show interfaces | `ip link show` |
| `ip route show` | Show routing table | `ip route show` |
| `ping` | Test connectivity | `ping -c 3 8.8.8.8` |
| `ss -tuln` | Show listening ports | `sudo ss -tuln` |
| `nslookup` | DNS lookup | `nslookup google.com` |
| `traceroute` | Trace network path | `traceroute google.com` |
| `nc` | Test port connectivity | `nc -zv host 80` |
| `hostname -I` | Show IP addresses | `hostname -I` |
| `cat /etc/resolv.conf` | Show DNS servers | `cat /etc/resolv.conf` |

### Key Concepts

- ✅ IP addresses identify devices on networks
- ✅ Subnet masks define network boundaries
- ✅ Gateway is the router to reach other networks
- ✅ DNS translates hostnames to IP addresses
- ✅ Interfaces are network connection points
- ✅ Ports identify specific services
- ✅ DHCP assigns IPs automatically
- ✅ Static IPs are manually configured
- ✅ Docker creates virtual networks for containers

### OAI Network Understanding

- ✅ OAI uses 192.168.70.0/24 for Core Network
- ✅ AMF listens on port 38412 (N2 interface)
- ✅ UPF handles user data on N3 interface
- ✅ UEs get IPs in 12.1.1.0/24 range
- ✅ Docker networks connect Core components
- ✅ Multiple interfaces needed for OAI deployment

---

## 📚 Additional Resources

**Learn More:**
- [Computer Networks](https://www.cloudflare.com/learning/network-layer/what-is-the-network-layer/) - Networking basics
- [TCP/IP Guide](http://www.tcpipguide.com/) - Comprehensive networking guide
- [Subnet Calculator](https://www.subnet-calculator.com/) - Calculate subnet ranges

**Troubleshooting:**
- [Linux Networking Commands](https://www.tecmint.com/linux-networking-commands/)
- [Network Troubleshooting Guide](https://www.cyberciti.biz/tips/linux-network-troubleshooting-guide.html)

**OAI Specific:**
- 3GPP 5G interfaces and protocols
- Docker networking documentation
- Network namespace concepts

**Best Practices:**
- Document your network configuration
- Use static IPs for servers/infrastructure
- Test connectivity systematically (layers 1-7)
- Monitor network performance
- Keep network diagrams updated
- Use meaningful hostnames

---

## 🚀 Congratulations!

You've completed all 6 pre-workshop modules! 🎉

### What You've Learned

- ✅ **Module 1:** Linux terminal, file operations, permissions
- ✅ **Module 2:** Package management with apt
- ✅ **Module 3:** Git version control basics
- ✅ **Module 4:** Docker containers and Compose
- ✅ **Module 5:** C compilation and build systems
- ✅ **Module 6:** Network basics and troubleshooting

### You're Ready For

- ✅ Navigating Linux confidently
- ✅ Installing required software
- ✅ Cloning and updating OAI code
- ✅ Running Docker containers
- ✅ Building OAI from source
- ✅ Understanding network connectivity
- ✅ **The 2-day OAI 5G workshop!**

---

## 🎯 Final Pre-Workshop Checklist

**System:**
- [ ] Ubuntu 22.04 LTS installed (local or cloud)
- [ ] System fully updated
- [ ] 8GB+ RAM, 50GB+ disk space

**Tools:**
- [ ] Git installed and configured
- [ ] Docker and Docker Compose working
- [ ] Build tools installed (gcc, make, cmake)
- [ ] Network tools available

**Skills:**
- [ ] Comfortable with terminal
- [ ] Can edit files with nano
- [ ] Understand file permissions
- [ ] Can clone Git repositories
- [ ] Can run Docker containers
- [ ] Can check network connectivity

**Network:**
- [ ] Internet connection working
- [ ] Can ping 8.8.8.8
- [ ] DNS resolution working
- [ ] Know your IP configuration

**Ready to:**
- [ ] Clone OAI repository
- [ ] Build OAI from source
- [ ] Run OAI Docker containers
- [ ] Debug basic issues
- [ ] Follow workshop instructions

---

## 📞 Getting Help

**Before Workshop:**
- Workshop communication group
- Email instructor
- Review module quizzes

**During Workshop:**
- Ask instructors immediately
- Help your neighbors
- Use troubleshooting guides

**After Workshop:**
- OAI community forums
- GitLab issues
- Continue learning!

---

**All Pre-Workshop Modules Complete!** ✅

See you at the workshop! 🚀

[⬅️ Previous: Module 5](module-5-c-compilation) | [🏠 Home](../../)

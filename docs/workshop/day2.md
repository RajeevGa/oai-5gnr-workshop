---
layout: default
title: "Day 2: OAI Hands-on"
parent: Workshop Schedule
nav_order: 2
---

# 📅 Day 2: OpenAirInterface Hands-on

---

## Morning Session (9:00 AM - 12:20 PM)

### 9:00 - 10:30 AM: Introduction to OAI

**Topics:**
- What is OpenAirInterface?
- OAI Software Alliance and community
- OAI ecosystem: RAN, Core, UE components
- Software architecture
- OAI vs commercial implementations
- Development environment overview
- Community resources (GitLab, Slack, documentation)

**Demo:**
- Navigate OAI GitLab repository
- Explore documentation and wiki
- Browse community discussions

---

**10:30 - 10:50 AM: ☕ BREAK**

---

### 10:50 AM - 12:20 PM: Installation & Setup

**Topics:**
- System requirements
- Installation methods (native vs Docker)
- OAI repository structure
- Build script overview
- Configuration files walkthrough

**Hands-on Lab - Part 1: Build OAI RAN (45 min)**
```bash
# Clone repository
git clone https://gitlab.eurecom.fr/oai/openairinterface5g.git
cd openairinterface5g
git checkout develop

# Install dependencies
cd cmake_targets
./build_oai -I

# Build gNB with RFsimulator
./build_oai --gNB --nrUE --rfsimulator
```

**Hands-on Lab - Part 2: Setup 5G Core (30 min)**
```bash
# Clone Core network
git clone https://gitlab.eurecom.fr/oai/cn5g/oai-cn5g-fed.git
cd oai-cn5g-fed/docker-compose

# Pull Docker images
docker-compose -f docker-compose-basic-nrf.yaml pull
```

**Checkpoints:**
- ✓ Repositories cloned
- ✓ Dependencies installed
- ✓ gNB binary created
- ✓ Core Docker images ready

---

## Evening Session (2:00 PM - 5:20 PM)

### 2:00 - 3:30 PM: Running OAI 5G Network

**Part 1: Start 5G Core (30 min)**

**Topics:**
- Docker Compose configuration
- NF startup sequence
- Database initialization

**Hands-on:**
```bash
# Start 5G Core
docker-compose -f docker-compose-basic-nrf.yaml up -d

# Verify containers
docker-compose ps
docker logs oai-amf
```

**Part 2: Start gNB (30 min)**

**Topics:**
- gNB configuration parameters
- N2 setup with AMF
- RFsimulator mode

**Hands-on:**
```bash
# Start gNB
sudo ./nr-softmodem -O <config-file> --sa --rfsim
```

**Part 3: Connect UE (30 min)**

**Topics:**
- UE configuration (IMSI, keys)
- Connection procedure observation

**Hands-on:**
```bash
# Start UE
sudo ./nr-uesoftmodem -O <config-file> --sa --rfsim

# Test connectivity
ping -I oaitun_ue1 8.8.8.8
```

**Checkpoints:**
- ✓ Core network running
- ✓ gNB connected to AMF
- ✓ UE registered successfully
- ✓ Data connectivity working

---

**3:30 - 3:50 PM: ☕ BREAK**

---

### 3:50 - 5:20 PM: Experiments & Analysis

**Part 1: Wireshark Protocol Analysis (30 min)**

**Topics:**
- Capturing 5G interfaces (N2, N3)
- Wireshark filters for 5G
- Protocol message analysis

**Hands-on:**
- Capture N2 interface traffic
- Analyze UE registration messages
- Examine PDU session establishment
- Extract QoS parameters

**Part 2: Performance Testing (20 min)**

**Hands-on:**
```bash
# Throughput test
iperf3 -s
iperf3 -c <server-ip> -B <UE-ip>

# Latency measurement
ping -I oaitun_ue1 <target>
```

**Part 3: Configuration Experiments (30 min)**

**Experiments:**

**Experiment 1: Bandwidth Variation**
- Modify gNB config: Change from 40 MHz to 100 MHz
- Measure throughput difference

**Experiment 2: Numerology Change**
- Switch subcarrier spacing: 30 kHz → 60 kHz
- Observe latency impact

**Lab Report:**
- Document configuration changes
- Record measurements
- Analyze results

**Part 4: Q&A and Next Steps (10 min)**

**Topics:**
- OAI research opportunities
- Community support resources
- Continued learning paths

---

[← Day 1](day1) | [Workshop Overview →](index)

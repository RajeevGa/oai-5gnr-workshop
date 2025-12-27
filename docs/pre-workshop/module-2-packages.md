---
layout: default
title: "Module 2: Package Management"
parent: Pre-Workshop Preparation
nav_order: 2
---

# 📦 Module 2: Package Management

> **Duration:** 20 minutes | **Level:** Beginner | **Hands-on:** Yes

---

## 📚 What You'll Learn

By the end of this module, you'll be able to:

- [ ] Understand what package managers do
- [ ] Update system packages
- [ ] Install software using `apt`
- [ ] Search for and remove packages
- [ ] Install all tools needed for the workshop

---

## 📖 Chapter 1: Understanding Package Management

### What is a Package?

A **package** is a bundle containing:
- Software application (the program)
- Dependencies (other software it needs)
- Configuration files
- Documentation

Think of it like an app on your phone, but for Linux.

### What is a Package Manager?

A **package manager** automates:
- ✅ Installing software
- ✅ Updating software
- ✅ Removing software
- ✅ Resolving dependencies (installing required libraries automatically)
- ✅ Managing versions

**Without package manager (old way):**
```
1. Search Google for software
2. Download from random website
3. Hope it's not malware
4. Manually install dependencies
5. Configure paths
6. Update manually
```

**With package manager (modern way):**
```
sudo apt install software-name
Done! ✓
```

### APT: Ubuntu's Package Manager

**APT** (Advanced Package Tool) is Ubuntu's package manager.

**Package sources:** Software comes from official Ubuntu repositories (secure, verified)

**Common commands:**
- `apt update` - Refresh list of available packages
- `apt upgrade` - Update installed packages
- `apt install` - Install new software
- `apt remove` - Uninstall software
- `apt search` - Find packages

---

## 🔄 Chapter 2: Updating Your System

### Why Update?

Regular updates provide:
- 🛡️ Security patches
- 🐛 Bug fixes
- ✨ New features
- 🔧 Better compatibility

### The Two-Step Update Process

#### Step 1: Update Package Lists
```bash
sudo apt update
```

**What it does:** Downloads information about available packages and their versions from Ubuntu's servers. Like refreshing the app store catalog.

**Expected output:**
```
Hit:1 http://archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://archive.ubuntu.com/ubuntu jammy-updates InRelease [119 kB]
...
Fetched 1,234 kB in 2s (617 kB/s)
Reading package lists... Done
Building dependency tree... Done
3 packages can be upgraded. Run 'apt list --upgradable' to see them.
```

#### Step 2: Upgrade Packages
```bash
sudo apt upgrade
```

**What it does:** Actually installs the newer versions of your installed packages.

**Expected output:**
```
Reading package lists... Done
Building dependency tree... Done
Calculating upgrade... Done
The following packages will be upgraded:
  git libssl1.1 openssh-client
3 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
Need to get 2,345 kB of archives.
After this operation, 123 kB of additional disk space will be used.
Do you want to continue? [Y/n] 
```

Type `Y` and press Enter.

### Full Upgrade (Advanced)
```bash
sudo apt full-upgrade
```

**Difference:** Can remove packages if needed to resolve dependencies. More aggressive than `upgrade`.

**Use when:** Major version updates or complex dependency changes.

### Checking Available Updates
```bash
# See what can be updated
apt list --upgradable

# Count updates available
apt list --upgradable | wc -l
```

---

## 📥 Chapter 3: Installing Software

### Basic Installation
```bash
sudo apt install package-name
```

**Example:**
```bash
# Install Git
sudo apt install git

# Install multiple packages at once
sudo apt install git curl wget

# Install without confirmation prompt
sudo apt install -y git
```

### What Happens During Installation
```
1. APT checks if package exists
2. Checks dependencies (what else is needed)
3. Downloads package and dependencies
4. Installs everything in correct order
5. Configures the software
6. Updates system records
```

### Real Example: Installing Git
```bash
sudo apt install git
```

**Output walkthrough:**
```
Reading package lists... Done          ← Checking available packages
Building dependency tree... Done        ← Figuring out dependencies
The following additional packages will be installed:
  git-man liberror-perl               ← Dependencies that will also install
Suggested packages:
  git-daemon-run git-doc              ← Optional related packages
The following NEW packages will be installed:
  git git-man liberror-perl           ← What will be installed
0 upgraded, 3 newly installed, 0 to remove
Need to get 8,234 kB of archives      ← Download size
After this operation, 42.3 MB will be used  ← Disk space needed
Do you want to continue? [Y/n]
```

Press `Y` then Enter.
```
Get:1 http://archive.ubuntu.com/ubuntu jammy/main amd64 git amd64 1:2.34.1 [3,165 kB]
...
Fetched 8,234 kB in 3s (2,745 kB/s)
Selecting previously unselected package git-man.
...
Setting up git (1:2.34.1-1ubuntu1) ...
```

### Verifying Installation
```bash
# Check if installed
git --version

# Check installation location
which git

# Show package info
apt show git
```

---

## 🔍 Chapter 4: Searching for Packages

### Finding Software
```bash
# Search by name
apt search docker

# Search for specific package
apt search ^docker$

# Case-insensitive search
apt search -n docker
```

**Output:**
```
docker.io/jammy 20.10.21-0ubuntu1 amd64
  Linux container runtime

docker-compose/jammy 1.29.2-1 all
  define and run multi-container Docker applications
```

### Getting Package Information
```bash
# Detailed info about a package
apt show git

# Show installed packages
apt list --installed

# Show specific package
apt list git
```

**Example output:**
```
Package: git
Version: 1:2.34.1-1ubuntu1
Priority: optional
Section: vcs
Maintainer: Ubuntu Developers
Installed-Size: 42.3 MB
Depends: libc6, libcurl3-gnutls, liberror-perl...
Description: fast, scalable, distributed revision control system
 Git is a version control system with an emphasis on speed...
```

---

## 🗑️ Chapter 5: Removing Software

### Uninstall Package
```bash
# Remove package (keep config files)
sudo apt remove package-name

# Remove package and config files
sudo apt purge package-name

# Remove unused dependencies
sudo apt autoremove
```

**Example:**
```bash
# Install test package
sudo apt install cowsay

# Test it
cowsay "Hello!"

# Remove it
sudo apt remove cowsay

# Clean up dependencies
sudo apt autoremove
```

### Cleaning Up
```bash
# Remove downloaded package files (safe)
sudo apt clean

# Remove old package files
sudo apt autoclean

# Remove unnecessary packages
sudo apt autoremove
```

---

## 🛠️ Chapter 6: Essential Workshop Tools

### Build Tools

These tools compile software from source code:
```bash
sudo apt install build-essential
```

**Includes:**
- `gcc` - C compiler
- `g++` - C++ compiler  
- `make` - Build automation
- Libraries and headers

**Verify:**
```bash
gcc --version
make --version
```

### Version Control
```bash
sudo apt install git
```

**Why needed:** Clone OAI repository from GitLab

**Verify:**
```bash
git --version
```

### CMake (Build System)
```bash
sudo apt install cmake
```

**Why needed:** OAI uses CMake to build

**Verify:**
```bash
cmake --version
```

### Network Tools
```bash
sudo apt install net-tools iputils-ping
```

**Provides:**
- `ifconfig` - Network interface configuration
- `ping` - Test connectivity
- `netstat` - Network statistics

**Verify:**
```bash
ifconfig
ping -c 2 8.8.8.8
```

### Additional Useful Tools
```bash
# Text editor (if not already installed)
sudo apt install nano vim

# File compression tools
sudo apt install zip unzip

# Download tools
sudo apt install wget curl

# System monitoring
sudo apt install htop

# All at once
sudo apt install -y nano vim zip unzip wget curl htop
```

---

## 🎯 Chapter 7: Installing Docker and Docker Compose

Docker will be covered in detail in Module 4, but let's install it now:
```bash
# Install Docker
sudo apt install -y docker.io

# Add your user to docker group (avoid sudo for docker commands)
sudo usermod -aG docker $USER

# Install Docker Compose
sudo apt install -y docker-compose

# Note: Log out and log back in for group changes to take effect
```

**Verify after re-login:**
```bash
docker --version
docker-compose --version
```

---

## 🔧 Chapter 8: Troubleshooting Common Issues

### Issue: "Unable to locate package"

**Problem:** Package name is wrong or repositories not updated

**Solution:**
```bash
# Update package lists
sudo apt update

# Search for correct name
apt search package-name
```

### Issue: "Package has unmet dependencies"

**Problem:** Dependency conflicts

**Solution:**
```bash
# Try fixing broken packages
sudo apt --fix-broken install

# Or
sudo apt install -f
```

### Issue: "Could not get lock"

**Problem:** Another package manager is running

**Full error:**
```
E: Could not get lock /var/lib/dpkg/lock-frontend
```

**Solution:**
```bash
# Wait for other process to finish
# Or find and kill it
ps aux | grep apt
sudo kill <process-id>

# If still stuck (use carefully!)
sudo rm /var/lib/dpkg/lock-frontend
sudo apt update
```

### Issue: "404 Not Found"

**Problem:** Repository URL is wrong or outdated

**Solution:**
```bash
# Update package lists
sudo apt update

# Change to different mirror (if needed)
sudo nano /etc/apt/sources.list
```

### Issue: Broken Packages After Update

**Solution:**
```bash
# Reconfigure packages
sudo dpkg --configure -a

# Fix broken installations
sudo apt --fix-broken install
```

---

## 🧪 Hands-On Exercise

### Task 1: System Update (3 min)
```bash
# Update package lists
sudo apt update

# Check what can be updated
apt list --upgradable

# Upgrade packages
sudo apt upgrade -y
```

**Expected:** System is up-to-date

---

### Task 2: Install Workshop Tools (5 min)
```bash
# Install essential tools
sudo apt install -y build-essential git cmake

# Verify
gcc --version
git --version
cmake --version

# Install network tools
sudo apt install -y net-tools iputils-ping

# Verify
ifconfig
ping -c 2 8.8.8.8
```

**Expected:** All tools installed and verified

---

### Task 3: Search and Install Practice (3 min)
```bash
# Search for a package
apt search tree

# Install tree (directory visualization tool)
sudo apt install -y tree

# Test it
tree ~/ -L 2

# Get package info
apt show tree

# Remove it (for practice)
sudo apt remove tree

# Clean up
sudo apt autoremove
```

**Expected:** Comfortable with search, install, remove cycle

---

### Task 4: Complete Workshop Setup (5 min)

Install everything needed for the workshop:
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install all workshop requirements
sudo apt install -y \
  build-essential \
  cmake \
  git \
  docker.io \
  docker-compose \
  net-tools \
  iputils-ping \
  nano \
  vim \
  wget \
  curl \
  htop

# Add to docker group
sudo usermod -aG docker $USER

# Verify key tools
echo "=== Verification ==="
gcc --version | head -1
git --version
cmake --version | head -1
docker --version
docker-compose --version
echo "=== Complete! ==="
```

**Expected:** All workshop tools installed

**Important:** Log out and log back in for docker group to take effect

---

## ✅ Self-Check Quiz

<details>
<summary>❓ What does `sudo apt update` do?</summary>

**Answer:** Updates the list of available packages from Ubuntu's repositories. It doesn't install or upgrade anything, just refreshes the catalog.
</details>

<details>
<summary>❓ What's the difference between `apt remove` and `apt purge`?</summary>

**Answer:** 
- `apt remove` - Uninstalls package but keeps configuration files
- `apt purge` - Uninstalls package AND removes all configuration files
</details>

<details>
<summary>❓ Why do we need `sudo` for apt commands?</summary>

**Answer:** Installing/removing software affects the entire system, not just your user account, so it requires administrator (root) privileges.
</details>

<details>
<summary>❓ What does `apt autoremove` do?</summary>

**Answer:** Removes packages that were installed as dependencies but are no longer needed by any installed software.
</details>

<details>
<summary>❓ How do you install multiple packages at once?</summary>

**Answer:** List them separated by spaces: `sudo apt install package1 package2 package3`
</details>

<details>
<summary>❓ What does the `-y` flag do in `sudo apt install -y git`?</summary>

**Answer:** Automatically answers "yes" to prompts, so installation proceeds without asking for confirmation.
</details>

<details>
<summary>❓ How can you check if a package is installed?</summary>

**Answer:** Several ways:
- `which package-name` (shows path if installed)
- `package-name --version` (shows version if installed)
- `apt list --installed | grep package-name`
</details>

---

## 🎓 Summary

### Commands Mastered

| Command | Purpose | Example |
|---------|---------|---------|
| `apt update` | Refresh package lists | `sudo apt update` |
| `apt upgrade` | Update installed packages | `sudo apt upgrade` |
| `apt install` | Install software | `sudo apt install git` |
| `apt remove` | Uninstall software | `sudo apt remove package` |
| `apt purge` | Uninstall + remove configs | `sudo apt purge package` |
| `apt search` | Find packages | `apt search docker` |
| `apt show` | Package details | `apt show git` |
| `apt list` | List packages | `apt list --installed` |
| `apt autoremove` | Remove unused dependencies | `sudo apt autoremove` |
| `apt clean` | Clear package cache | `sudo apt clean` |

### Key Concepts

- ✅ Package managers automate software installation
- ✅ Always run `apt update` before installing
- ✅ `apt upgrade` keeps your system secure and current
- ✅ Use `apt search` to find package names
- ✅ Dependencies are handled automatically
- ✅ `sudo` is required for system-wide changes
- ✅ Keep your system updated regularly

### Workshop Tools Installed

- ✅ `build-essential` - Compilers and build tools
- ✅ `git` - Version control for OAI code
- ✅ `cmake` - Build system for OAI
- ✅ `docker` & `docker-compose` - Container platform
- ✅ `net-tools` - Network utilities
- ✅ Text editors, download tools, monitoring tools

---

## 📚 Additional Resources

**Learn More:**
- [Ubuntu Packages Search](https://packages.ubuntu.com/) - Browse all available packages
- [APT User Manual](https://manpages.ubuntu.com/manpages/jammy/man8/apt.8.html)

**Package Management on Other Systems:**
- **Debian:** `apt` (same as Ubuntu)
- **Red Hat/CentOS:** `yum` or `dnf`
- **Fedora:** `dnf`
- **Arch:** `pacman`
- **macOS:** `brew` (Homebrew)

**Best Practices:**
- Update system weekly: `sudo apt update && sudo apt upgrade`
- Remove unused packages: `sudo apt autoremove`
- Clean package cache occasionally: `sudo apt clean`
- Read what packages will be installed before confirming
- Don't mix package managers (apt vs snap vs manual installs)

---

## 🚀 What's Next

You now know how to manage software on Linux! This is essential because:

- Installing OAI dependencies
- Updating tools during workshop
- Installing debugging utilities
- Managing system maintenance

**Next module:** We'll learn Git to download and manage the OAI codebase.

---

**Module Complete!** ✅

[⬅️ Previous: Module 1](module-1-linux) | [Next: Module 3 →](module-3-git)


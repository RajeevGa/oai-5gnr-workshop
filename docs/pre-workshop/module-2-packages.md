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

## 🎯 How to Use This Module

Follow along with the examples as you read. Keep your terminal open and try the commands as they're introduced. At the end, you'll complete exercises that test your understanding.

---

## 📖 Chapter 1: Understanding Package Management

### What is a Package?

A **package** is a bundle containing:
- Software application (the program itself)
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

**Without package manager (the old way):**
```
1. Search Google for software
2. Download from random website
3. Hope it's not malware
4. Manually install dependencies
5. Configure paths
6. Update manually
```

**With package manager (the modern way):**
```
sudo apt install software-name
Done! ✓
```

### APT: Ubuntu's Package Manager

**APT** (Advanced Package Tool) is Ubuntu's package manager.

**Package sources:** Software comes from official Ubuntu repositories (secure, verified packages).

**Common commands you'll learn:**
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

### Understanding the Update Process

Package management has two separate steps: updating the list of available packages, and upgrading the actual software. Let's try both.

First, update the package lists:

```bash
sudo apt update
```

**What it does:** Downloads information about available packages and their versions from Ubuntu's servers. Like refreshing your app store catalog.

You'll see output like:
```
Hit:1 http://archive.ubuntu.com/ubuntu jammy InRelease
Get:2 http://archive.ubuntu.com/ubuntu jammy-updates InRelease [119 kB]
...
Fetched 1,234 kB in 2s (617 kB/s)
Reading package lists... Done
Building dependency tree... Done
3 packages can be upgraded. Run 'apt list --upgradable' to see them.
```

The last line tells you if any updates are available. Let's check what can be updated:

```bash
apt list --upgradable
```

This shows packages with available updates. Now let's actually upgrade them:

```bash
sudo apt upgrade
```

**What it does:** Installs the newer versions of your installed packages.

You'll see:
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

Type `Y` and press Enter. The packages will download and install.

💡 **Pro tip:** You can skip the confirmation prompt with the `-y` flag:
```bash
sudo apt upgrade -y
```

---

## 📥 Chapter 3: Installing Software

### Installing Your First Package

Most Ubuntu systems don't come with `curl` pre-installed. Let's install it now. Type:

```bash
sudo apt install curl
```

**What happens:**
```
Reading package lists... Done          ← Checking available packages
Building dependency tree... Done        ← Figuring out dependencies
The following NEW packages will be installed:
  curl                                  ← What will be installed
0 upgraded, 1 newly installed, 0 to remove
Need to get 194 kB of archives         ← Download size
After this operation, 451 kB will be used  ← Disk space needed
Do you want to continue? [Y/n]
```

Press `Y` and Enter. Once installed, test it:

```bash
curl --version
```

You should see version information. Let's test it by downloading a simple text file:

```bash
curl https://www.example.com
```

This downloads and displays the HTML of example.com. Pretty neat!

### Installing Multiple Packages

You can install several packages in one command. Let's install two more useful tools:

```bash
sudo apt install wget htop
```

This installs:
- `wget` - Another download tool (alternative to curl)
- `htop` - Interactive system monitor (better than `top`)

Test them:
```bash
wget --version
htop --version
```

You can run `htop` to see a nice system monitor (press `q` to quit):
```bash
htop
```

### Verifying Installation

Check if a package is installed:

```bash
# Check version
curl --version

# Check location
which curl

# Get detailed info
apt show curl
```

The `which` command shows where the program is installed. The `apt show` command gives detailed package information.

---

## 🔍 Chapter 4: Searching for Packages

### Finding Software

Don't know the exact package name? Search for it:

```bash
apt search network
```

This returns many results. To narrow it down, search for packages with "network" in the name:

```bash
apt search ^network
```

The `^` symbol means "starts with".

Let's search for Git (a version control tool you'll need later):

```bash
apt search git
```

You'll see many results including `git`, `git-man`, `gitk`, and more. To get detailed information about a specific package before installing:

```bash
apt show git
```

This displays information like:
```
Package: git
Version: 1:2.34.1-1ubuntu1
Priority: optional
Section: vcs
Maintainer: Ubuntu Developers
Installed-Size: 42.3 MB
Depends: libc6, libcurl3-gnutls...
Description: fast, scalable, distributed revision control system
 Git is a version control system with an emphasis on speed...
```

Now you can decide if you want to install it. Let's install it:

```bash
sudo apt install git
```

Verify it's installed:

```bash
git --version
```

### Listing Installed Packages

See everything installed on your system:

```bash
apt list --installed
```

This is a long list! To search within it, use `grep`. Let's check if `git` is now installed:

```bash
apt list --installed | grep git
```

This should show `git` in the list since we just installed it.

---

## 🗑️ Chapter 5: Removing Software

### Uninstalling Packages

Now that you've installed `wget` and `htop`, let's practice removing one. Remove `htop`:

```bash
sudo apt remove htop
```

This uninstalls the package but keeps configuration files (in case you reinstall later).

To completely remove a package including its configuration, use `purge` instead:

```bash
sudo apt purge htop
```

💡 **Note:** Since we just removed it with `remove`, running `purge` now will just clean up the leftover config files.

### Cleaning Up

Over time, your system accumulates packages that were installed as dependencies but are no longer needed. Clean them up:

```bash
sudo apt autoremove
```

This removes packages that have no dependencies anymore.

You can also clear the package download cache:

```bash
sudo apt clean
```

This frees up disk space by removing downloaded package files (they're no longer needed after installation).

---

## 🔧 Chapter 6: Troubleshooting Common Issues

### "Unable to locate package"

**Problem:** The package name is wrong or your package lists are outdated.

**Solution:** Update your package lists first:
```bash
sudo apt update
apt search package-name
```

### "Package has unmet dependencies"

**Problem:** There are dependency conflicts.

**Solution:** Try fixing broken packages:
```bash
sudo apt --fix-broken install
```

### "Could not get lock"

**Full error message:**
```
E: Could not get lock /var/lib/dpkg/lock-frontend
```

**Problem:** Another package manager is already running (like Software Updater).

**Solution:** Wait for the other process to finish. You can check what's running:
```bash
ps aux | grep apt
```

If nothing should be running but you still get this error, you can remove the lock file:
```bash
sudo rm /var/lib/dpkg/lock-frontend
sudo apt update
```

⚠️ **Warning:** Only do this if you're sure no other package manager is running!

### Broken Packages After Update

**Solution:** Reconfigure packages and fix installations:
```bash
sudo dpkg --configure -a
sudo apt --fix-broken install
```

---

## 💻 Hands-On Exercises

Now test your knowledge! Try to solve these problems using what you've learned.

### Exercise 1: System Update

**Problem:** Your system needs to be updated with the latest security patches and software versions.

**Requirements:**
- Update the package lists
- Check how many packages can be upgraded
- Upgrade all packages

<details markdown="1">
<summary>💡 Hint</summary>

Use `apt update` first, then `apt list --upgradable` to check, then `apt upgrade`.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
sudo apt update
apt list --upgradable
sudo apt upgrade -y
```
</details>

---

### Exercise 2: Installing Development Tools

**Problem:** You need to install essential development tools for compiling software.

**Requirements:**
- Install the `build-essential` package (contains compilers and build tools)
- Install `git` for version control
- Install `cmake` for build configuration
- Verify each tool is installed by checking their versions

<details markdown="1">
<summary>💡 Hint</summary>

You can install multiple packages in one command. Use `--version` flag to check versions.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
sudo apt install build-essential git cmake
gcc --version
git --version
cmake --version
```
</details>

---

### Exercise 3: Installing Network Tools

**Problem:** You need network diagnostic tools for troubleshooting.

**Requirements:**
- Install `net-tools` (provides `ifconfig`)
- Install `iputils-ping` (provides `ping` - though it's usually pre-installed)
- Test `ifconfig` to see your network interfaces
- Test `ping` by pinging Google's DNS server (8.8.8.8) with 3 packets

<details markdown="1">
<summary>💡 Hint</summary>

Use `-c` flag with ping to specify packet count. Multiple packages can be installed together. Note: `ping` might already be installed.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
sudo apt install net-tools iputils-ping
ifconfig
ping -c 3 8.8.8.8
```
</details>

---

### Exercise 4: Package Search and Information

**Problem:** You've heard about a package called `htop` but want to learn more before installing.

**Requirements:**
- Search for packages related to "htop"
- Display detailed information about the `htop` package
- Check if `htop` is currently installed on your system

<details markdown="1">
<summary>💡 Hint</summary>

Use `apt search`, `apt show`, and `apt list --installed` with grep.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
apt search htop
apt show htop
apt list --installed | grep htop
```
</details>

---

### Exercise 5: Install, Use, and Remove

**Problem:** Practice the complete lifecycle of a package.

**Requirements:**
- Install the `cowsay` package (a fun program that makes an ASCII cow say things)
- Run it with: `cowsay "Hello Linux!"`
- Remove the package completely including configuration files
- Clean up any unused dependencies

<details markdown="1">
<summary>💡 Hint</summary>

Use `apt install`, then `apt purge` to remove with configs, then `apt autoremove` for dependencies.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
sudo apt install cowsay
cowsay "Hello Linux!"
sudo apt purge cowsay
sudo apt autoremove
```
</details>

---

### Exercise 6: Troubleshooting Practice

**Problem:** Simulate and fix a broken package situation.

**Requirements:**
- Try to install a non-existent package called `fake-package-xyz`
- Observe the error message
- Update your package lists
- Search for a similar package that might exist

<details markdown="1">
<summary>💡 Hint</summary>

The error will say "Unable to locate package". Use `apt update` then `apt search`.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
sudo apt install fake-package-xyz
# Error: Unable to locate package
sudo apt update
apt search fake
# No results, package doesn't exist
```
</details>

---

## ✅ Self-Check Quiz

<details markdown="1">
<summary>❓ What does `sudo apt update` do?</summary>

**Answer:** Updates the list of available packages from Ubuntu's repositories. It doesn't install or upgrade anything, just refreshes the catalog.
</details>

<details markdown="1">
<summary>❓ What's the difference between `apt remove` and `apt purge`?</summary>

**Answer:** 
- `apt remove` - Uninstalls package but keeps configuration files
- `apt purge` - Uninstalls package AND removes all configuration files
</details>

<details markdown="1">
<summary>❓ Why do we need `sudo` for apt commands?</summary>

**Answer:** Installing/removing software affects the entire system, not just your user account, so it requires administrator (root) privileges.
</details>

<details markdown="1">
<summary>❓ What does `apt autoremove` do?</summary>

**Answer:** Removes packages that were installed as dependencies but are no longer needed by any installed software.
</details>

<details markdown="1">
<summary>❓ How do you install multiple packages at once?</summary>

**Answer:** List them separated by spaces: `sudo apt install package1 package2 package3`
</details>

<details markdown="1">
<summary>❓ What does the `-y` flag do in `sudo apt install -y git`?</summary>

**Answer:** Automatically answers "yes" to prompts, so installation proceeds without asking for confirmation.
</details>

<details markdown="1">
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

---

**Module Complete!** ✅

[⬅️ Previous: Module 1](module-1-linux) | [Next: Module 3 →](module-3-git)

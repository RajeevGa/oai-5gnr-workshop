---
layout: default
title: "Module 3: Git & Version Control"
parent: Pre-Workshop Preparation
nav_order: 3
---


# 🚀 Module 3: Git and Version Control

> **Duration:** 25 minutes | **Level:** Beginner | **Hands-on:** Yes

---

## 📚 What You'll Learn

By the end of this module, you'll be able to:

- [ ] Clone the OAI repository from GitLab
- [ ] Navigate through commit history  
- [ ] Switch between branches (master ↔ develop)
- [ ] Checkout specific releases
- [ ] Pull updates to stay current

---

## 📖 Chapter 1: Why Version Control?

### The Problem

Ever worked on a project and ended up with this? 🤦‍♂️
```
MyProject/
├── config.conf
├── config_backup.conf
├── config_final.conf
├── config_final_v2.conf
├── config_REALLY_FINAL.conf
└── config_use_this_one.conf  ← Which one do I use?!
```

Version control solves this chaos by tracking every change to every file over time. Think of it like an infinite undo button with a complete history of who changed what, when, and why.

### Git in 3 Concepts

1. **Repository** = Project + Complete History
2. **Commit** = Snapshot at a point in time
3. **Branch** = Parallel version (stable vs latest)

For this workshop, you'll use Git to download the OAI code (called "cloning"), check its history, switch between stable and development versions, and pull updates when new code is released.

---

## 🔧 Chapter 2: Git Setup

### Install Git
```bash
# Install
sudo apt update && sudo apt install -y git

# Verify
git --version
```

### Configure Your Identity
```bash
# Set your name
git config --global user.name "Your Name"

# Set your email  
git config --global user.email "your.email@example.com"

# Verify
git config --list | grep user
```

---

## 🎯 Chapter 3: Working with OAI Repository

### Clone the Repository
```bash
# Navigate to home directory
cd ~

# Clone OAI repository (takes 2-5 minutes)
git clone https://gitlab.eurecom.fr/oai/openairinterface5g.git

# Enter the directory
cd openairinterface5g
```

### Check Status and History
```bash
# See current state
git status

# View last 10 commits
git log --oneline -10

# View all branches
git branch -a
```

### Switch Between Branches

OAI has two main branches:
- `master` = Stable releases
- `develop` = Latest features
```bash
# Switch to develop branch
git checkout develop

# Verify
git status

# Switch back to master
git checkout master
```

### View Release Tags
```bash
# List all tags
git tag

# Search specific versions
git tag -l "v1.5*"

# Use a specific release
git checkout v1.5.0
```

### Pull Updates
```bash
# Make sure you're on develop
git checkout develop

# Get latest changes
git pull
```

---

## 📝 Quick Check Questions

<details>
<summary>❓ What's the difference between `git clone` and `git pull`?</summary>

- **`git clone`** = Download repository for the first time (creates new folder)
- **`git pull`** = Update existing repository with latest changes
</details>

<details>
<summary>❓ What's the difference between `master` and `develop` branches?</summary>

- **`master`** = Stable, production-ready code
- **`develop`** = Latest features, still being tested
</details>

---

## 🎯 Your Workshop Workflow
```bash
# 1️⃣ Clone (once)
git clone https://gitlab.eurecom.fr/oai/openairinterface5g.git
cd openairinterface5g

# 2️⃣ Switch to development branch
git checkout develop

# 3️⃣ Check history
git log --oneline -20

# 4️⃣ When instructor announces updates
git pull

# 5️⃣ To try a stable release
git checkout v1.5.0

# 6️⃣ Return to latest
git checkout develop
```

---

## 🔬 Hands-On Exercise

### Task: Clone and Explore OAI
```bash
cd ~
git clone https://gitlab.eurecom.fr/oai/openairinterface5g.git
cd openairinterface5g

# Explore
git status
git log --oneline -10
git branch -a
git tag | head -20

# Switch branches
git checkout develop
git log --oneline -5

# Return to master
git checkout master
```

### Completion Checklist

- [ ] Successfully cloned OAI repository
- [ ] Viewed commit history with `git log`
- [ ] Listed available branches
- [ ] Switched between master and develop
- [ ] Listed available release tags

---

## 🎓 Summary

**Essential Commands:**

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `git clone <url>` | Download repository | Once, initially |
| `git status` | Check current state | Anytime |
| `git log --oneline` | View history | See what changed |
| `git branch -a` | List branches | Find versions |
| `git checkout <branch>` | Switch version | Change branch/tag |
| `git pull` | Get updates | Stay current |

---

## 📚 Additional Resources

**Want to Learn More?**

- [Pro Git Book](https://git-scm.com/book) - Free, comprehensive
- [Learn Git Branching](https://learngitbranching.js.org) - Interactive

**Topics We Didn't Cover** (not needed for workshop):
- Creating commits
- Pushing changes
- Merging branches
- Resolving conflicts

---

**Module Complete!** ✅

[⬅️ Previous: Module 2](module-2-packages) | [Next: Module 4 ➡️](module-4-docker)

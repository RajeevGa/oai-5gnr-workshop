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

- [ ] Understand what version control is and why it matters
- [ ] Clone repositories from remote servers
- [ ] Navigate through commit history
- [ ] Switch between branches
- [ ] Checkout specific releases
- [ ] Pull updates to stay current

---

## 🎯 How to Use This Module

Follow along with the examples as you read. You'll practice Git commands with a real repository as you learn. At the end, you'll complete exercises to test your understanding.

**Keep your terminal open throughout!**

---

## 📖 Chapter 1: Understanding Version Control

### The Problem Without Version Control

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

Or worse, accidentally deleted something important with no way to recover it?

### What is Version Control?

**Version control** tracks every change to every file over time. Think of it like:
- An infinite undo button
- A time machine for your code
- A complete history of who changed what, when, and why

### Git in Three Concepts

1. **Repository (repo)** - A project folder + its complete history
2. **Commit** - A snapshot of your project at a specific point in time
3. **Branch** - A parallel version of your code (like stable vs. experimental)

### What You'll Use Git For

In this workshop, you'll use Git to:
- **Clone** - Download the OAI code from the internet
- **View history** - See what changed over time
- **Switch branches** - Move between stable and development versions
- **Pull updates** - Get the latest code when new features are released

You won't be creating commits or pushing changes (that's for developers contributing to the project).

---

## 🔧 Chapter 2: Setting Up Git

### Installing Git

First, make sure Git is installed (you may have already installed it in Module 2):

```bash
sudo apt update
sudo apt install -y git
```

Verify it's installed:

```bash
git --version
```

You should see something like `git version 2.34.1`.

### Configuring Your Identity

Git needs to know who you are. Even though you won't be creating commits in this workshop, it's good practice to set this up:

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

Replace "Your Name" and the email with your actual information. Verify it worked:

```bash
git config --list | grep user
```

You should see:
```
user.name=Your Name
user.email=your.email@example.com
```

---

## 📦 Chapter 3: Cloning a Repository

### What is Cloning?

**Cloning** means downloading a complete copy of a repository (all files + entire history) from a remote server to your computer.

### Clone a Practice Repository

Let's start with a small, simple repository to practice. We'll clone a public example repository:

```bash
cd ~
git clone https://github.com/github/gitignore.git
cd gitignore
```

This downloads the repository and creates a folder called `gitignore`. Let's look inside:

```bash
ls
```

You'll see many files. This is just like any other folder, but it has a hidden `.git` folder that contains all the version history.

---

## 🔍 Chapter 4: Exploring Repository History

### Checking Repository Status

See the current state of your repository:

```bash
git status
```

You'll see something like:
```
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

This tells you:
- Which branch you're on (`main`)
- That you're up to date with the remote server
- No files have been modified

### Viewing Commit History

See the history of changes:

```bash
git log
```

This shows detailed information about each commit. Press `q` to quit.

For a more compact view:

```bash
git log --oneline
```

You'll see something like:
```
a1b2c3d Add Python gitignore
e4f5g6h Update Node.js patterns
i7j8k9l Fix typo in README
```

Each line is one commit showing:
- A short ID (like `a1b2c3d`)
- The commit message describing what changed

Let's see just the last 5 commits:

```bash
git log --oneline -5
```

### Understanding What You See

Each commit represents a snapshot of the project at that moment. Git tracks:
- What files changed
- Who made the change
- When it happened
- Why (the commit message)

---

## 🌿 Chapter 5: Working with Branches

### What are Branches?

**Branches** let you have multiple versions of your code simultaneously. Think of them like parallel universes for your project:
- `main` or `master` - Stable, production-ready code
- `develop` - Latest features being tested
- `feature-xyz` - Experimental new feature

### Viewing Available Branches

See which branches exist:

```bash
git branch -a
```

You'll see something like:
```
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
```

The `*` shows which branch you're currently on. The `remotes/origin/` entries are branches on the remote server.

### Switching Branches

If the repository has multiple branches, you can switch between them with `git checkout`:

```bash
git checkout <branch-name>
```

💡 **Note:** In newer Git versions, you can also use `git switch <branch-name>`.

Let's go back to the main branch:

```bash
git checkout main
```

Check your status again:

```bash
git status
```

---

## 🏷️ Chapter 6: Working with Tags

### What are Tags?

**Tags** mark specific commits as important, usually for releases. Tags are like bookmarks for specific versions:
- `v1.0.0` - First major release
- `v1.5.2` - Version 1.5.2
- `v2.0.0-beta` - Beta version of 2.0

### Viewing Tags

List all tags:

```bash
git tag
```

Search for specific tags:

```bash
git tag -l "v1.*"
```

This shows all tags starting with "v1.".

### Checking Out a Tag

You can checkout a specific tagged version just like a branch:

```bash
git checkout v1.5.0
```

This puts your repository in that exact state. To return to the latest version:

```bash
git checkout main
```

---

## 🔄 Chapter 7: Pulling Updates

### What is Pulling?

**Pulling** downloads the latest changes from the remote server and updates your local copy. Use this when:
- New code has been released
- You want the latest features
- You need bug fixes

### Pull the Latest Changes

First, make sure you're on the branch you want to update:

```bash
git checkout main
```

Then pull:

```bash
git pull
```

You'll see:
```
remote: Counting objects: 5, done.
remote: Compressing objects: 100% (5/5), done.
Unpacking objects: 100% (5/5), done.
From https://github.com/github/gitignore
   a1b2c3d..e4f5g6h  main     -> origin/main
Updating a1b2c3d..e4f5g6h
Fast-forward
 Python.gitignore | 2 ++
 1 file changed, 2 insertions(+)
```

This shows what was updated. If everything is already up to date:
```
Already up to date.
```

---

## 💻 Hands-On Exercises

Now practice what you've learned with real-world scenarios!

### Exercise 1: Clone and Explore a Repository

**Problem:** You need to download a repository and explore its history.

**Requirements:**
- Clone this small practice repository: `https://github.com/octocat/Hello-World.git`
- Navigate into the cloned directory
- Check the repository status
- View the last 10 commits in one-line format
- Count how many total commits exist (hint: `git log --oneline | wc -l`)

<details markdown="1">
<summary>💡 Hint</summary>

Use `git clone`, then `cd` into the folder. Use `git log --oneline` with `-10` for the last 10, or pipe to `wc -l` to count all.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
cd ~
git clone https://github.com/octocat/Hello-World.git
cd Hello-World
git status
git log --oneline -10
git log --oneline | wc -l
```
</details>

---

### Exercise 2: Understanding Branches

**Problem:** Explore the branches in a repository to understand the project structure.

**Requirements:**
- While still in the Hello-World repository, list all branches (local and remote)
- Identify which branch you're currently on
- Check how many remote branches exist

<details markdown="1">
<summary>💡 Hint</summary>

Use `git branch -a` to see all branches. The current branch has a `*` next to it. Remote branches start with `remotes/origin/`.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
git branch -a
# Current branch has * next to it
# Count remote branches: git branch -r | wc -l
```
</details>

---

### Exercise 3: Time Travel with Commits

**Problem:** View the repository at different points in its history.

**Requirements:**
- View the full commit history (not just one-line)
- Note the commit ID (hash) of the very first commit
- Look at a specific commit in detail using `git show <commit-id>`
- Return to the latest version

<details markdown="1">
<summary>💡 Hint</summary>

Use `git log` without flags for full details. The first commit is at the bottom. Use `git show` with the commit ID. Press `q` to exit viewers.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
git log
# Note the first commit ID (at bottom)
git show <commit-id>
# Press 'q' to exit
git checkout main
```
</details>

---

### Exercise 4: Working with Tags

**Problem:** Find and checkout a specific release version.

**Requirements:**
- Clone the Git repository: `https://github.com/git/git.git` (yes, Git's own source code!)
- Navigate into the directory
- List all available tags
- Find tags for version 2.30.x (hint: use `-l` with a pattern)
- Checkout tag `v2.30.0`
- Verify you're on that tag with `git status`
- Return to the master branch

<details markdown="1">
<summary>💡 Hint</summary>

Use `git tag` to list all, `git tag -l "v2.30*"` to filter. Use `git checkout v2.30.0` to switch to that tag.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
cd ~
git clone https://github.com/git/git.git
cd git
git tag | head -20
git tag -l "v2.30*"
git checkout v2.30.0
git status
git checkout master
```
</details>

---

### Exercise 5: Keeping Up to Date

**Problem:** Simulate getting the latest updates from a repository.

**Requirements:**
- Make sure you're on the master branch
- Pull the latest changes
- View the last 5 commits to see what's new
- Check if any new tags were added

<details markdown="1">
<summary>💡 Hint</summary>

Use `git checkout master`, then `git pull`, then explore with `git log` and `git tag`.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
git checkout master
git pull
git log --oneline -5
git tag | tail -10
```
</details>

---

### Exercise 6: Repository Investigation

**Problem:** You need to gather information about a repository for documentation.

**Requirements:**
- In any cloned repository, find:
  - The name of the current branch
  - The commit ID of the latest commit
  - The author and date of the most recent commit
  - How many files are in the repository root

<details markdown="1">
<summary>💡 Hint</summary>

Use `git branch` for branch, `git log -1` for latest commit details, `ls` for files.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
git branch
# Shows current branch with *
git log -1
# Shows latest commit with full details
ls | wc -l
# Counts files in current directory
```
</details>

---

### Exercise 7: Cleanup

**Problem:** Remove the practice repositories to free up space.

**Requirements:**
- Navigate to your home directory
- Remove the practice repositories: `gitignore`, `Hello-World`, and `git`
- Verify they're deleted

<details markdown="1">
<summary>⚠️ Warning</summary>

Make sure you're removing the correct directories. Use `rm -rf` carefully!
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
cd ~
rm -rf gitignore Hello-World git
ls
# Verify the folders are gone
```
</details>

---

## ✅ Self-Check Quiz

<details markdown="1">
<summary>❓ What's the difference between `git clone` and `git pull`?</summary>

**Answer:** 
- `git clone` - Downloads a repository for the first time (creates new folder)
- `git pull` - Updates an existing repository with the latest changes
</details>

<details markdown="1">
<summary>❓ What does `git status` tell you?</summary>

**Answer:** Shows your current branch, whether you have uncommitted changes, and if you're up to date with the remote server.
</details>

<details markdown="1">
<summary>❓ What's the difference between `master`/`main` and `develop` branches?</summary>

**Answer:** 
- `master`/`main` - Stable, production-ready code
- `develop` - Latest features, still being tested
</details>

<details markdown="1">
<summary>❓ How do you see the last 10 commits in a compact format?</summary>

**Answer:** `git log --oneline -10`
</details>

<details markdown="1">
<summary>❓ What are tags used for?</summary>

**Answer:** Tags mark specific commits as important releases or versions (like v1.0.0, v2.5.1).
</details>

<details markdown="1">
<summary>❓ How do you switch to a different branch?</summary>

**Answer:** `git checkout branch-name` or `git switch branch-name` (newer syntax)
</details>

<details markdown="1">
<summary>❓ What happens when you checkout a tag?</summary>

**Answer:** Your repository switches to the exact state it was in at that tagged commit. You enter "detached HEAD" state.
</details>

---

## 🎓 Summary

### Commands Mastered

| Command | Purpose | Example |
|---------|---------|---------|
| `git clone <url>` | Download repository | `git clone https://github.com/user/repo.git` |
| `git status` | Check current state | `git status` |
| `git log` | View commit history | `git log --oneline -10` |
| `git branch -a` | List all branches | `git branch -a` |
| `git checkout <name>` | Switch branch/tag | `git checkout develop` |
| `git tag` | List tags | `git tag -l "v1.*"` |
| `git pull` | Get latest updates | `git pull` |

### Key Concepts

- ✅ Version control tracks all changes to your project over time
- ✅ Clone downloads a repository once; pull updates it
- ✅ Commits are snapshots of your project
- ✅ Branches are parallel versions (stable vs. development)
- ✅ Tags mark important releases
- ✅ `git status` is your friend - use it often!

---

## 📚 Additional Resources

**Want to Learn More?**

- [Pro Git Book](https://git-scm.com/book) - Free, comprehensive guide
- [Learn Git Branching](https://learngitbranching.js.org) - Interactive visual tutorial
- [Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)

**Topics We Didn't Cover** (not needed for this workshop):
- Creating commits (`git add`, `git commit`)
- Pushing changes (`git push`)
- Creating branches (`git branch`)
- Merging branches (`git merge`)
- Resolving conflicts
- Rebasing

---

**Module Complete!** ✅

[⬅️ Previous: Module 2](module-2-packages) | [Next: Module 4 ➡️](module-4-docker)

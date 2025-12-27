---
layout: default
title: "Module 1: Linux Essentials"
parent: Pre-Workshop Preparation
nav_order: 1
---

# 🐧 Module 1: Linux Essentials

> **Duration:** 45 minutes | **Level:** Beginner | **Hands-on:** Yes

---

## 📚 What You'll Learn

By the end of this module, you'll be able to:

- [ ] Navigate the Linux file system confidently
- [ ] Create, view, edit, and delete files
- [ ] Understand and modify file permissions
- [ ] Use text editors (nano)
- [ ] Execute basic commands needed for the workshop

---

## 📖 Chapter 1: Getting Started with Terminal

### Why Learn Linux?

OpenAirInterface runs on Linux (specifically Ubuntu). The workshop requires you to use the terminal to:
- Clone code repositories
- Install software packages
- Edit configuration files
- Run OAI components
- Debug issues

Think of the terminal as a powerful text-based interface where you type commands instead of clicking buttons. Once you learn the basics, it's often faster and more precise than using a graphical interface.

### Opening the Terminal

**Keyboard shortcut:** Press `Ctrl + Alt + T`

**Or:** Click Activities → Search "Terminal" → Click Terminal icon

You'll see something like:
```
username@hostname:~$
```

This is your **command prompt**, showing:
- `username` - Your login name
- `hostname` - Computer name
- `~` - Current location (home directory)
- `$` - Regular user (vs `#` for root)

---

## 🗂️ Chapter 2: File System Navigation

### Understanding Linux File System

Linux organizes files in a tree structure starting from root (`/`):
```
/                    (root - top of everything)
├── home/            (user home directories)
│   └── username/    (your home folder)
├── etc/             (configuration files)
├── usr/             (user programs)
├── var/             (variable data, logs)
└── tmp/             (temporary files)
```

### Essential Navigation Commands

#### pwd (Print Working Directory)
Shows where you are right now.
```bash
pwd
# Output: /home/username
```

#### ls (List)
Shows what's in current folder.
```bash
# Basic list
ls

# Detailed list
ls -l

# Include hidden files (starting with .)
ls -a

# Human-readable sizes
ls -lh

# Combine options
ls -lah
```

**Output explanation:**
```
drwxr-xr-x  2 user group 4096 Dec 26 10:00 Documents
-rw-r--r--  1 user group  156 Dec 25 14:30 file.txt
```
- First character: `d` = directory, `-` = file
- Next 9 characters: permissions
- Numbers: links, owner, group, size
- Date and name

#### cd (Change Directory)
Move to different folders.
```bash
# Go to Documents
cd Documents

# Go back to home
cd ~
# or just
cd

# Go up one level
cd ..

# Go up two levels
cd ../..

# Go to previous directory
cd -

# Go to root
cd /
```

**Paths:**
- **Absolute path:** Starts from root `/home/username/Documents`
- **Relative path:** From current location `Documents/file.txt`
- **Home shortcut:** `~/Documents` = `/home/username/Documents`

---

## 📄 Chapter 3: Working with Files and Directories

### Creating
```bash
# Create empty file
touch myfile.txt

# Create directory
mkdir myfolder

# Create nested directories
mkdir -p project/configs/test
```

### Viewing File Contents
```bash
# Show entire file
cat myfile.txt

# Show with page-by-page viewing
less myfile.txt
# (Press 'q' to quit, Space for next page)

# First 10 lines
head myfile.txt

# Last 10 lines
tail myfile.txt

# Follow file updates (useful for logs)
tail -f logfile.txt
```

### Copying
```bash
# Copy file
cp source.txt destination.txt

# Copy to directory
cp file.txt ~/Documents/

# Copy directory (recursive)
cp -r folder1/ folder2/

# Copy with verbose output
cp -v file.txt backup.txt
```

### Moving/Renaming
```bash
# Rename file
mv oldname.txt newname.txt

# Move file
mv file.txt ~/Documents/

# Move and rename
mv old.txt ~/Documents/new.txt
```

### Deleting
```bash
# Delete file
rm myfile.txt

# Delete directory (recursive)
rm -r myfolder/

# Delete without confirmation (be careful!)
rm -rf folder/

# Delete empty directory
rmdir emptyfolder/
```

⚠️ **Warning:** `rm -rf` is permanent! No recycle bin in terminal.

---

## 🔒 Chapter 4: File Permissions

### Understanding Permissions

Every file has permissions for three groups:
- **Owner (u)** - User who owns the file
- **Group (g)** - Group of users
- **Others (o)** - Everyone else

Three types of permissions:
- **r (read)** = 4 - Can read file
- **w (write)** = 2 - Can modify file
- **x (execute)** = 1 - Can run file as program

### Reading Permission String
```bash
ls -l script.sh
# -rwxr-xr-x 1 user group 156 Dec 26 10:00 script.sh
#  ───────── 
#  │││││││││
#  │││││││└┴─ Others: r-x (read, execute)
#  │││└┴┴─── Group: r-x (read, execute)
#  └┴┴───── Owner: rwx (read, write, execute)
```

### Changing Permissions

**Numeric method:**
```bash
# rwx = 4+2+1 = 7
# rw- = 4+2   = 6
# r-x = 4+1   = 5
# r-- = 4     = 4

# Make file read/write for owner, read-only for others
chmod 644 file.txt

# Make script executable by everyone
chmod 755 script.sh

# Full permissions (avoid this!)
chmod 777 file.txt
```

**Symbolic method:**
```bash
# Add execute permission for owner
chmod u+x script.sh

# Remove write permission for group
chmod g-w file.txt

# Add read for everyone
chmod a+r file.txt

# Set exact permissions
chmod u=rwx,g=rx,o=r script.sh
```

### Common Permission Patterns

| Code | Permissions | Use Case |
|------|-------------|----------|
| 644 | rw-r--r-- | Regular files |
| 755 | rwxr-xr-x | Scripts, executables |
| 700 | rwx------ | Private files |
| 600 | rw------- | Private config files |

### Using sudo

Some operations require administrator (root) privileges:
```bash
# Install software (needs root)
sudo apt install package-name

# Edit system files (needs root)
sudo nano /etc/config-file

# Run command as root
sudo command
```

**When to use sudo:**
- Installing software
- Modifying system files
- Accessing protected resources

**When NOT to use sudo:**
- Regular file operations in your home folder
- Running scripts that don't need system access

---

## ✏️ Chapter 5: Text Editing with Nano

### Why Nano?

Nano is a beginner-friendly text editor in the terminal. You'll use it to edit configuration files during the workshop.

### Opening Nano
```bash
# Create/edit a file
nano myfile.txt

# Edit existing file
nano /path/to/file.txt
```

### Basic Navigation

- **Arrow keys:** Move cursor
- **Page Up/Down:** Scroll
- **Home/End:** Start/end of line
- **Ctrl+A:** Start of line
- **Ctrl+E:** End of line

### Essential Commands

All commands shown at bottom of screen (`^` means Ctrl):
```
^O  Save (WriteOut)     → Press Ctrl+O, then Enter
^X  Exit                → Press Ctrl+X
^K  Cut line            → Press Ctrl+K
^U  Paste (Uncut)       → Press Ctrl+U
^W  Search              → Press Ctrl+W
^G  Help                → Press Ctrl+G
```

### Common Workflow
```bash
# 1. Open file
nano config.txt

# 2. Make changes with arrow keys and typing

# 3. Save: Ctrl+O, then Enter

# 4. Exit: Ctrl+X
```

If you try to exit with unsaved changes, Nano asks:
```
Save modified buffer?
 Y  Yes
 N  No
^C Cancel
```

---

## 🔧 Chapter 6: Essential Commands for Workshop

### Searching
```bash
# Find files by name
find . -name "*.txt"
find /home -name "config*"

# Search inside files
grep "search_term" file.txt
grep -r "search_term" /path/to/folder/

# Case-insensitive search
grep -i "text" file.txt
```

### Getting Help
```bash
# Manual page
man ls
man chmod

# Quick help
ls --help
cp --help

# Which command to use?
apropos "search term"
```

### Checking System Info
```bash
# Disk space
df -h

# Folder size
du -sh folder/

# Memory usage
free -h

# CPU info
lscpu

# Number of CPU cores
nproc
```

### Useful Shortcuts
```bash
# Clear screen
clear
# or: Ctrl+L

# Stop current command
Ctrl+C

# Logout
exit
# or: Ctrl+D

# Command history
history

# Rerun previous command
!!

# Previous command
↑ (up arrow)
```

---

## 🧪 Hands-On Exercise

### Task 1: File System Navigation (5 min)
```bash
# 1. Check where you are
pwd

# 2. List contents
ls -lah

# 3. Go to home
cd ~

# 4. Create test structure
mkdir -p workshop/day1/configs
mkdir -p workshop/day2/logs

# 5. Verify
ls -R workshop/

# 6. Navigate
cd workshop/day1/configs
pwd

# 7. Go back to home
cd ~
```

**Expected:** You can navigate directories confidently.

---

### Task 2: File Operations (5 min)
```bash
# 1. Create file
cd ~/workshop/day1
echo "Configuration for OAI" > config.txt

# 2. View content
cat config.txt

# 3. Copy file
cp config.txt config-backup.txt

# 4. Check
ls -l

# 5. Edit file
nano config.txt
# Add line: "timeout = 30"
# Save: Ctrl+O, Enter
# Exit: Ctrl+X

# 6. View changes
cat config.txt

# 7. Move file
mv config-backup.txt ../day2/

# 8. Verify
ls
ls ../day2/
```

**Expected:** Comfortable creating, editing, copying, moving files.

---

### Task 3: Permissions (5 min)
```bash
# 1. Create script
cd ~/workshop
cat > test-script.sh << 'EOF'
#!/bin/bash
echo "Hello from script!"
EOF

# 2. Check permissions
ls -l test-script.sh

# 3. Try to run (will fail)
./test-script.sh

# 4. Make executable
chmod +x test-script.sh

# 5. Check permissions again
ls -l test-script.sh

# 6. Run successfully
./test-script.sh

# 7. Practice permission codes
chmod 755 test-script.sh
ls -l test-script.sh

chmod 644 test-script.sh
ls -l test-script.sh
```

**Expected:** Understand permissions, can make files executable.

---

### Task 4: Searching and Help (5 min)
```bash
# 1. Find all .txt files
find ~/workshop -name "*.txt"

# 2. Search inside files
grep "timeout" ~/workshop/day1/config.txt

# 3. Get help for ls
man ls
# (Press 'q' to quit)

# 4. Quick help
ls --help | head -20
```

**Expected:** Can find files and get command help.

---

### Task 5: Cleanup
```bash
# Remove test folder
rm -rf ~/workshop
```

---

## ✅ Self-Check Quiz

<details>
<summary>❓ What command shows your current directory?</summary>

**Answer:** `pwd` (Print Working Directory)
</details>

<details>
<summary>❓ How do you make a file executable?</summary>

**Answer:** `chmod +x filename` or `chmod 755 filename`
</details>

<details>
<summary>❓ What does `ls -lah` do?</summary>

**Answer:** Lists files in long format (`-l`), including hidden files (`-a`), with human-readable sizes (`-h`)
</details>

<details>
<summary>❓ How do you go to your home directory?</summary>

**Answer:** `cd ~` or just `cd` or `cd $HOME`
</details>

<details>
<summary>❓ What's the difference between `rm file.txt` and `rm -rf folder/`?</summary>

**Answer:** `rm file.txt` removes a file. `rm -rf folder/` removes a directory and all its contents recursively (`-r`) and forcefully without confirmation (`-f`).
</details>

<details>
<summary>❓ In nano, how do you save and exit?</summary>

**Answer:** Save: `Ctrl+O`, Enter. Exit: `Ctrl+X`
</details>

<details>
<summary>❓ What does `sudo` do?</summary>

**Answer:** Runs a command with administrator (root) privileges
</details>

<details>
<summary>❓ What does `755` mean in permissions?</summary>

**Answer:** 
- 7 (rwx) - Owner can read, write, execute
- 5 (r-x) - Group can read and execute
- 5 (r-x) - Others can read and execute
</details>

---

## 🎓 Summary

### Commands Mastered

| Command | Purpose | Example |
|---------|---------|---------|
| `pwd` | Show current directory | `pwd` |
| `ls` | List files | `ls -lah` |
| `cd` | Change directory | `cd ~/Documents` |
| `mkdir` | Create directory | `mkdir project` |
| `touch` | Create empty file | `touch file.txt` |
| `cat` | View file | `cat file.txt` |
| `nano` | Edit file | `nano file.txt` |
| `cp` | Copy | `cp file.txt backup.txt` |
| `mv` | Move/rename | `mv old.txt new.txt` |
| `rm` | Delete | `rm file.txt` |
| `chmod` | Change permissions | `chmod +x script.sh` |
| `sudo` | Run as admin | `sudo apt install` |
| `find` | Find files | `find . -name "*.txt"` |
| `grep` | Search in files | `grep "text" file.txt` |
| `man` | Manual/help | `man ls` |

### Key Concepts

- ✅ Linux file system is a tree starting from `/`
- ✅ `~` is shortcut for home directory
- ✅ Permissions control who can read/write/execute files
- ✅ `chmod` changes permissions (644, 755, etc.)
- ✅ `sudo` runs commands as administrator
- ✅ Nano is your text editor for config files
- ✅ Tab completion saves typing

### You're Ready For

- ✅ Navigating to OAI installation directories
- ✅ Creating folders for workshop files
- ✅ Editing OAI configuration files
- ✅ Making scripts executable
- ✅ Viewing log files
- ✅ Basic troubleshooting

---

## 📚 Additional Resources

**Practice More:**
- [Linux Journey](https://linuxjourney.com/) - Interactive tutorials
- [OverTheWire: Bandit](https://overthewire.org/wargames/bandit/) - Command line game

**Quick References:**
- [Linux Command Cheat Sheet](https://www.linuxtrainingacademy.com/linux-commands-cheat-sheet/)
- [Chmod Calculator](https://chmod-calculator.com/)

**If You Want to Go Deeper:**
- [The Linux Command Line](http://linuxcommand.org/tlcl.php) - Free book
- Advanced topics: shell scripting, process management, system administration

---

**Module Complete!** ✅

You now have the Linux skills needed for the workshop!

[Next: Module 2: Package Management →](module-2-packages)


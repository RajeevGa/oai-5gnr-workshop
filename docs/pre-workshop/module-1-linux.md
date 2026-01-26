---
layout: default
title: "Module 1: Linux Essentials"
parent: Pre-Workshop Preparation
nav_order: 1
---

# 🧩 Module 1: Linux Essentials

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

## 🎯 How to Use This Module

Keep your terminal open as you read through each chapter. You'll practice commands as you learn them, building your skills progressively. At the end, you'll complete exercises that test what you've learned.

**Ready? Let's open your terminal now with `Ctrl + Alt + T`**

---

## 📖 Chapter 1: Getting Started with Terminal

### Why Learn Linux?

OpenAirInterface runs on Linux (specifically Ubuntu). In the workshop you will use the terminal to:
- Clone code repositories
- Install software packages
- Edit configuration files
- Run OAI components

While this module provides a basic introduction, we strongly encourage you to invest additional time in learning Linux. Linux skills are invaluable across the tech industry.

### Understanding the Command Prompt

When you open your terminal, you'll see something like:
```
username@hostname:~$
```

This is your **command prompt**, showing:
- `username` - Your login name
- `hostname` - Computer name
- `~` - Current location (home directory)
- `$` - Regular user (vs `#` for root)

Let's try your first command. Type this and press Enter:

```bash
pwd
```

You should see something like `/home/username`. This is your current location in the file system. The command `pwd` stands for "Print Working Directory" - it tells you where you are.

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

**Key concepts:**
- **Absolute path:** Starts from root `/home/username/Documents`
- **Relative path:** From current location `Documents/file.txt`
- **Home shortcut:** `~` equals `/home/username`

### Seeing What's Around You

Now let's look at what files and folders are in your current location. Type:

```bash
ls
```

You'll see a list of files and directories. Now try this to get more information:

```bash
ls -l
```

This shows a detailed list with file sizes, dates, and permissions. Let's add two more useful options:

```bash
ls -lah
```

This shows:
- `-l`: Long format with details
- `-a`: All files, including hidden ones (starting with `.`)
- `-h`: Human-readable file sizes (KB, MB instead of bytes)

Notice files starting with `.`? Those are hidden configuration files. The `-a` flag reveals them.

**Understanding the output:**
```
drwxr-xr-x  2 user group 4096 Dec 26 10:00 Documents
-rw-r--r--  1 user group  156 Dec 25 14:30 file.txt
```
- First character: `d` = directory, `-` = file
- Next 9 characters: permissions (we'll cover this later)
- Numbers: owner, group, size (in bytes)
- Date and name

### Moving Around

Let's explore the file system. First, go to the root directory:

```bash
cd /
```

Your prompt should change to show `/`. Now list what's there:

```bash
ls
```

You'll see system directories like `home`, `etc`, `usr`. Now return to your home directory:

```bash
cd ~
```

Or simply:

```bash
cd
```

Both commands take you home. Check where you are:

```bash
pwd
```

**Other navigation shortcuts:**
```bash
cd ..        # Go up one level
cd ../..     # Go up two levels
cd -         # Go to previous directory
```

Let's practice. If you have a `Documents` folder, go there:

```bash
cd Documents
pwd
```

Then go back:

```bash
cd ..
```

---

## 📄 Chapter 3: Working with Files and Directories

### Creating Your First Files and Folders

Let's create a practice area. Type these commands:

```bash
mkdir ~/linux-practice
cd ~/linux-practice
pwd
```

You've just created a folder called `linux-practice` in your home directory and moved into it. Now create a file:

```bash
touch welcome.txt
```

Verify it exists:

```bash
ls -l
```

You should see `welcome.txt` with a size of 0 bytes - it's empty. Let's add some content:

```bash
echo "Welcome to Linux!" > welcome.txt
```

The `>` operator writes text to a file (overwriting existing content). View what you just wrote:

```bash
cat welcome.txt
```

Now add another line without overwriting:

```bash
echo "This is my first file." >> welcome.txt
```

The `>>` operator appends (adds to the end). Check the result:

```bash
cat welcome.txt
```

### More Ways to View Files

For longer files, `cat` isn't ideal. Try creating a file with multiple lines:

```bash
echo "Line 1" > longfile.txt
echo "Line 2" >> longfile.txt
echo "Line 3" >> longfile.txt
echo "Line 4" >> longfile.txt
echo "Line 5" >> longfile.txt
```

Now try different viewing commands:

```bash
head -n 3 longfile.txt    # First 3 lines
tail -n 2 longfile.txt    # Last 2 lines
less longfile.txt         # Scrollable view (press 'q' to quit)
```

### Copying and Organizing Files

Let's make a backup of your welcome file:

```bash
cp welcome.txt welcome-backup.txt
ls -l
```

You now have two copies. Rename the backup:

```bash
mv welcome-backup.txt backup.txt
ls
```

The `mv` command moves OR renames files. Let's create a folder and move the backup there:

```bash
mkdir backups
mv backup.txt backups/
ls backups/
```

Your backup is now organized in its own folder.

### Creating Nested Directories

Need to create several levels of folders at once? Use the `-p` flag:

```bash
mkdir -p project/configs/test
ls -R project/
```

The `-R` flag shows directory contents recursively (including subdirectories).

### Deleting Files

Remove the longfile you created:

```bash
rm longfile.txt
ls
```

To remove a directory with contents:

```bash
rm -r backups/
ls
```

⚠️ **Be careful!** `rm` is permanent. There's no recycle bin. The `-f` flag forces deletion without asking for confirmation - use it cautiously.

---

## 🔒 Chapter 4: File Permissions

### Understanding the Permission System

Every file in Linux has permissions that control who can read, write, or execute it. Let's look at permissions for your welcome file:

```bash
ls -l welcome.txt
```

You'll see something like:
```
-rw-r--r-- 1 user group 42 Dec 26 10:00 welcome.txt
```

The permission string `-rw-r--r--` breaks down into:
- First `-`: File type (- = file, d = directory)
- Next 3 `rw-`: Owner permissions (read, write, no execute)
- Next 3 `r--`: Group permissions (read only)
- Last 3 `r--`: Others permissions (read only)

Three permission types:
- **r (read)** = 4 - Can read/view file
- **w (write)** = 2 - Can modify file
- **x (execute)** = 1 - Can run file as program

### Making Files Executable

Let's create a simple script:

```bash
echo '#!/bin/bash' > test.sh
echo 'echo "Hello from script!"' >> test.sh
```

Check its permissions:

```bash
ls -l test.sh
```

Probably shows `-rw-r--r--` (not executable). Try to run it:

```bash
./test.sh
```

You'll get a "Permission denied" error. Let's fix that:

```bash
chmod +x test.sh
```

Check permissions again:

```bash
ls -l test.sh
```

Now you'll see `-rwxr-xr-x`. The `x` was added. Run it:

```bash
./test.sh
```

Success! You've made your first executable script.

### Using Numeric Permissions

You can also set permissions with numbers. Each permission has a value:
- r (read) = 4
- w (write) = 2  
- x (execute) = 1

Add them up for each group:
- `rwx` = 4+2+1 = 7
- `rw-` = 4+2 = 6
- `r-x` = 4+1 = 5
- `r--` = 4

Try these commands and watch the permissions change:

```bash
chmod 644 welcome.txt
ls -l welcome.txt

chmod 755 test.sh
ls -l test.sh

chmod 600 welcome.txt
ls -l welcome.txt
```

**Common patterns:**
- `644` (rw-r--r--): Regular files - owner can edit, others can read
- `755` (rwxr-xr-x): Scripts - owner can edit/run, others can run
- `700` (rwx------): Private files - only owner can access
- `600` (rw-------): Private configs - only owner can read/write

### When You Need Administrator Access

Some operations require administrator (root) privileges. Try this:

```bash
ls /root
```

You'll get "Permission denied". To run commands with admin privileges, use `sudo`:

```bash
sudo ls /root
```

You'll be asked for your password. After entering it, the command runs with elevated privileges.

💡 **When to use sudo:**
- Installing software packages
- Editing system configuration files
- Accessing protected directories

**When NOT to use sudo:**
- Working with files in your home directory
- Running your own scripts
- Regular daily tasks

Using `sudo` unnecessarily can be dangerous - you could accidentally break system files.

---

## ✏️ Chapter 5: Text Editing with Nano

### Why You Need a Text Editor

Configuration files are just text files. You'll edit many of them during the workshop. If you are familiar with other termimal based text editors like vim and emacs you can use them!
Let's create and edit a configuration file. Type:

```bash
nano server.conf
```

Nano opens with a blank file. Notice the commands listed at the bottom (where `^` means Ctrl).

Type this content:

```
port=8080
host=localhost
timeout=30
```

Now save the file:
1. Press `Ctrl+O` (WriteOut)
2. Press `Enter` to confirm the filename
3. Press `Ctrl+X` to exit

Verify your changes:

```bash
cat server.conf
```

Let's edit it again:

```bash
nano server.conf
```

Use arrow keys to move around. Add a new line at the end:

```
debug=true
```

**Navigation shortcuts:**
- Arrow keys: Move cursor
- `Ctrl+A`: Jump to start of line
- `Ctrl+E`: Jump to end of line
- Page Up/Down: Scroll

**Editing shortcuts:**
- `Ctrl+K`: Cut entire line
- `Ctrl+U`: Paste (uncut)
- `Ctrl+W`: Search for text
- `Ctrl+G`: Show help screen

Save and exit with `Ctrl+O`, Enter, then `Ctrl+X`.

If you try to exit without saving, nano will ask:
```
Save modified buffer?
 Y  Yes
 N  No
^C Cancel
```

Press `Y` to save, `N` to discard changes, or `Ctrl+C` to cancel and keep editing.

---

## 🔧 Chapter 6: Essential Commands for Workshop

### Finding Files

Need to locate a file? Use `find`:

```bash
find ~/linux-practice -name "*.txt"
```

This searches your practice folder for all `.txt` files. The `*` is a wildcard matching any characters.

Find files starting with "test":

```bash
find ~/linux-practice -name "test*"
```

### Searching Inside Files

Looking for specific content? Use `grep`:

```bash
grep "timeout" server.conf
```

This shows lines containing "timeout". Search all files in a directory:

```bash
grep -r "localhost" ~/linux-practice
```

The `-r` flag means recursive (search subdirectories too). Case-insensitive search:

```bash
grep -i "TIMEOUT" server.conf
```

### Getting Help

Stuck on a command? Every command has documentation:

```bash
man ls
```

This opens the manual page for `ls`. Use arrow keys to scroll, press `q` to quit.

For a quicker summary:

```bash
ls --help
```

Not sure what command to use? Search by topic:

```bash
apropos "search"
```

### Checking System Resources

Useful commands for monitoring your system:

```bash
df -h              # Disk space
du -sh ~/linux-practice   # Folder size
free -h            # Memory usage
nproc              # Number of CPU cores
```

### Useful Keyboard Shortcuts

While working in the terminal:
- `Ctrl+L` or type `clear`: Clear the screen
- `Ctrl+C`: Stop the current running command
- `Ctrl+D` or type `exit`: Close terminal
- `↑` (up arrow): Recall previous commands
- `Tab`: Auto-complete file/folder names (very useful!)

Try tab completion. Type:

```bash
cd ~/lin
```

Then press `Tab`. It should auto-complete to `linux-practice/`.

---

## 💻 Hands-On Exercises

Now it's your turn! These exercises test what you've learned. Try to solve them without looking at the solutions first. Each exercise includes hints if you get stuck.

### Exercise 1: Creating Directory Structures

**Problem:** Create a project directory with organized subdirectories.

**Requirements:**
- Create a folder called `my-project` in your home directory
- Inside it, create three subdirectories: `data`, `documents`, and `output`
- Verify the structure was created correctly by listing the contents

<details markdown="1">
<summary>💡 Hint</summary>

You can use `mkdir -p` to create nested directories in one command. The syntax is `mkdir -p parent/{child1,child2,child3}`.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

One-line approach:
```bash
mkdir -p ~/my-project/{data,documents,output}
ls ~/my-project
```

Or create them one by one:
```bash
mkdir ~/my-project
cd ~/my-project
mkdir data documents output
ls
```
</details>

---

### Exercise 2: Working with Text Files

**Problem:** Create and populate a configuration file.

**Requirements:**
- Navigate to the `data` folder you created
- Create a file named `settings.conf`
- Add these three lines to the file:
  ```
  username=student
  server=localhost
  port=8080
  ```
- View the file contents to verify

<details markdown="1">
<summary>💡 Hint</summary>

You can use `nano` to create and edit the file, or use `echo` with `>` and `>>` to write lines.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

Using nano:
```bash
cd ~/my-project/data
nano settings.conf
# Type the three lines
# Save: Ctrl+O, Enter
# Exit: Ctrl+X
cat settings.conf
```

Using echo:
```bash
cd ~/my-project/data
echo "username=student" > settings.conf
echo "server=localhost" >> settings.conf
echo "port=8080" >> settings.conf
cat settings.conf
```
</details>

---

### Exercise 3: Copying and Moving Files

**Problem:** Create a backup and reorganize files.

**Requirements:**
- Make a copy of `settings.conf` named `settings-backup.conf`
- Move the backup file to the `documents` folder
- List files in both `data` and `documents` folders to verify

<details markdown="1">
<summary>💡 Hint</summary>

Use `cp` for copying and `mv` for moving. Use `../documents/` to refer to the documents folder from the data folder.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
cd ~/my-project/data
cp settings.conf settings-backup.conf
mv settings-backup.conf ../documents/
ls
ls ../documents/
```
</details>

---

### Exercise 4: Creating an Executable Script

**Problem:** Write a script that displays system information.

**Requirements:**
- Navigate to the `output` folder
- Create a file named `info.sh`
- Add a bash shebang (`#!/bin/bash`) as the first line
- Add commands to display:
  - A message "System Information:"
  - Current directory path
  - List of files in the parent directory
- Make the script executable
- Run the script

<details markdown="1">
<summary>💡 Hint</summary>

Use `nano` to create the script. Use `echo` for messages, `pwd` for current directory, and `ls ..` for parent directory contents. Make it executable with `chmod +x`.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
cd ~/my-project/output
nano info.sh
```

In nano, type:
```bash
#!/bin/bash
echo "System Information:"
pwd
echo "Parent directory contents:"
ls -lh ..
```

Save and exit, then:
```bash
chmod +x info.sh
./info.sh
```
</details>

---

### Exercise 5: Managing Permissions

**Problem:** Set appropriate permissions on your configuration file.

**Requirements:**
- Navigate to the `data` folder
- Set permissions on `settings.conf` so that:
  - Owner can read and write
  - Group can only read
  - Others have no access
- Verify the permissions are correct

<details markdown="1">
<summary>💡 Hint</summary>

Calculate the numeric permission: Owner needs read(4) + write(2) = 6. Group needs read(4) = 4. Others need nothing = 0. So the code is 640.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
cd ~/my-project/data
chmod 640 settings.conf
ls -l settings.conf
```

You should see: `-rw-r-----`

Alternative using symbolic notation:
```bash
chmod u=rw,g=r,o= settings.conf
```
</details>

---

### Exercise 6: Searching for Files and Content

**Problem:** Locate specific files and search for content within them.

**Requirements:**
- Find all `.conf` files in the `my-project` directory and its subdirectories
- Search for the word "localhost" in all files within the `data` folder
- Display the first 3 lines of your script file

<details markdown="1">
<summary>💡 Hint</summary>

Use `find` with the `-name` option for file patterns. Use `grep` with `-r` for recursive search. Use `head -n` to show specific number of lines.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
find ~/my-project -name "*.conf"
grep -r "localhost" ~/my-project/data/
head -n 3 ~/my-project/output/info.sh
```
</details>

---

### Exercise 7: Cleanup

**Problem:** Remove the practice directories you created.

**Requirements:**
- Remove both the `linux-practice` and `my-project` directories
- Verify they're gone by checking your home directory

<details markdown="1">
<summary>⚠️ Warning</summary>

Be very careful with `rm -rf`. Always double-check the path before pressing Enter! This command permanently deletes files.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
rm -rf ~/linux-practice
rm -rf ~/my-project
ls ~
```

The folders should no longer appear in the listing.
</details>

---

## ✅ Self-Check Quiz

Test your understanding:

<details markdown="1">
<summary>❓ What command shows your current directory?</summary>

**Answer:** `pwd` (Print Working Directory)
</details>

<details markdown="1">
<summary>❓ How do you make a file executable?</summary>

**Answer:** `chmod +x filename` or `chmod 755 filename`
</details>

<details markdown="1">
<summary>❓ What does `ls -lah` do?</summary>

**Answer:** Lists files in long format (`-l`), including hidden files (`-a`), with human-readable sizes (`-h`)
</details>

<details markdown="1">
<summary>❓ How do you go to your home directory?</summary>

**Answer:** `cd ~` or just `cd` or `cd $HOME`
</details>

<details markdown="1">
<summary>❓ What's the difference between `>` and `>>`?</summary>

**Answer:** `>` overwrites the file with new content. `>>` appends to the end of the file without erasing existing content.
</details>

<details markdown="1">
<summary>❓ In nano, how do you save and exit?</summary>

**Answer:** Save: `Ctrl+O`, Enter. Exit: `Ctrl+X`
</details>

<details markdown="1">
<summary>❓ What does `sudo` do?</summary>

**Answer:** Runs a command with administrator (root) privileges. Use it carefully!
</details>

<details markdown="1">
<summary>❓ What does `755` mean in permissions?</summary>

**Answer:** 
- 7 (rwx) - Owner can read, write, execute
- 5 (r-x) - Group can read and execute
- 5 (r-x) - Others can read and execute
</details>

---

## 🎓 Command Reference

Quick reference of commands you've mastered:

| Command | Purpose | Example |
|---------|---------|---------|
| `pwd` | Show current directory | `pwd` |
| `ls` | List files | `ls -lah` |
| `cd` | Change directory | `cd ~/Documents` |
| `mkdir` | Create directory | `mkdir -p parent/child` |
| `touch` | Create empty file | `touch file.txt` |
| `echo` | Output text | `echo "text" > file.txt` |
| `cat` | View file | `cat file.txt` |
| `nano` | Edit file | `nano file.txt` |
| `cp` | Copy | `cp file.txt backup.txt` |
| `mv` | Move/rename | `mv old.txt new.txt` |
| `rm` | Delete | `rm file.txt` |
| `chmod` | Change permissions | `chmod 755 script.sh` |
| `sudo` | Run as admin | `sudo apt install` |
| `find` | Find files | `find . -name "*.txt"` |
| `grep` | Search in files | `grep "text" file.txt` |
| `man` | Manual/help | `man ls` |

---

## 📚 Additional Resources

Want to deepen your Linux knowledge?

- [Linux Journey](https://linuxjourney.com/) - Interactive tutorials
- [OverTheWire: Bandit](https://overthewire.org/wargames/bandit/) - Command line game
- [Linux Command Cheat Sheet](https://www.linuxtrainingacademy.com/linux-commands-cheat-sheet/)
- [The Linux Command Line](http://linuxcommand.org/tlcl.php) - Free book

---

**Module Complete!** ✅

You now have the Linux skills needed for the workshop! Next, you'll learn about package management in Ubuntu.

[Next: Module 2: Package Management →](module-2-packages)

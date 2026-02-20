---
layout: default
title: "Module 5: C Compilation"
parent: Pre-Workshop Preparation
nav_order: 5
---

# ⚙️ Module 5: C Compilation Basics

> **Duration:** 45 minutes | **Level:** Refresher | **Hands-on:** Yes

---

## 📚 What You'll Learn

By the end of this module, you'll be able to:

- [ ] Compile C programs with GCC
- [ ] Understand the compilation process (preprocessing, compilation, assembly, linking)
- [ ] Build multi-file projects
- [ ] Use Makefiles for build automation
- [ ] Work with CMake and Ninja
- [ ] Create and use shared libraries (.so files)
- [ ] Debug programs with GDB

---

## 🎯 How to Use This Module

Follow along with the examples as you read. You'll write and compile C programs as you learn. At the end, you'll complete exercises to test your understanding.

**Keep your terminal open throughout!**

---

## Introduction

C has been the foundation of systems programming for over five decades - from operating systems to network stacks to telecommunications infrastructure. Its direct hardware access, minimal runtime overhead, and predictable performance make it the natural choice for domains where timing precision and resource efficiency are critical: embedded systems, IoT devices, real-time communications, and telecom protocols.

What makes OpenAirInterface notable is its use of C on general-purpose computers with open-source tools. You can build and experiment with 5G technology using a standard Linux machine, GCC, and common development tools - making cellular technology accessible for learning and experimentation.

This module covers the basic Linux C development workflow - from compiling simple programs to understanding how complex projects are built using GCC, Make, and CMake.

---

## Chapter 1: From Source to Executable

Before we start compiling C programs, you need to create source code files. On Linux, you'll use text editors - terminal-based editors like nano, vim, or emacs are essential when working with remote systems or cloud VMs where graphical interfaces aren't available.

### Verifying GCC Installation

Check if the C compiler is installed:

```bash
gcc --version
```

If you see version information, you're ready. If not:

```bash
sudo apt update
sudo apt install build-essential
gcc --version
```

### Creating Your First C Program

Let's create a workspace and your first program:

```bash
mkdir ~/c-learning
cd ~/c-learning
nano hello.c
```

Type the following code:

```c
#include <stdio.h>

int main() {
    printf("Hello from C!\n");
    return 0;
}
```

Save and exit (`Ctrl+O`, Enter, `Ctrl+X`).

### Compiling the Program

Now compile it:

```bash
gcc hello.c -o hello
```

This creates an executable file named `hello` from your source code `hello.c`.

### Running the Program

```bash
./hello
```

**Output:**
```
Hello from C!
```

Great! You've compiled and run your first C program.

### The Four Stages of Compilation

When you ran `gcc hello.c -o hello`, the compiler performed four stages behind the scenes. Let's see each one:

**Stage 1: Preprocessing**

```bash
gcc -E hello.c -o hello.i
wc -l hello.c hello.i
```

**Example output:**
```
    5 hello.c
  847 hello.i
```

The preprocessor processes `#include <stdio.h>` by inserting the entire header file. Your 5-line program expanded to 847 lines with all the included headers! You can look at it:

```bash
nano hello.i
```

**Stage 2: Compilation to Assembly**

```bash
gcc -S hello.c -o hello.s
nano hello.s
```

The compiler translates C into assembly language. You'll see instructions like `movq`, `call`, `ret` - assembly code for your CPU architecture.

**Stage 3: Assembly to Machine Code**

```bash
gcc -c hello.c -o hello.o
file hello.o
```

**Output:**
```
hello.o: ELF 64-bit LSB relocatable, x86-64
```

The assembler converted assembly into binary machine code. This object file contains machine code but isn't yet executable.

**Stage 4: Linking**

```bash
gcc hello.o -o hello
./hello
```

The linker combines your object file with system libraries (where `printf` actually lives) to create the final executable.

**Why understand these stages?**

Build error messages reference specific stages:
- "Syntax error" → Compilation stage
- "Undefined reference" → Linking stage
- "Cannot find header" → Preprocessing stage

Knowing this helps you diagnose build failures quickly.

### Important GCC Options

Always enable warnings to catch potential bugs:

```bash
gcc -Wall hello.c -o hello
```

Use `-Wall` to catch potential bugs. Let's see an example. Create `warn.c`:

```bash
nano warn.c
```

```c
#include <stdio.h>

int main() {
    int x = 5;      // unused
    int y;          // uninitialized
    printf("%d\n", y);
    return 0;
}
```

Compile with warnings:

```bash
gcc -Wall warn.c -o warn
```

**Output:**
```
warn.c:4:9: warning: unused variable 'x'
warn.c:6:20: warning: 'y' is used uninitialized
```

It's good practice to fix these warnings, as they often indicate real bugs.

---

## Chapter 2: Multi-File Projects

Real projects split code across multiple files for organization, maintainability, and team collaboration. Let's build a calculator program with separate modules.

### Project Structure

We'll create:
- `calculator.h` - Function declarations (interface)
- `operations.c` - Function implementations
- `calculator.c` - Main program

Create the project directory:

```bash
mkdir ~/calculator-project
cd ~/calculator-project
```

### Creating the Header File

Create `calculator.h`:

```bash
nano calculator.h
```

```c
#ifndef CALCULATOR_H
#define CALCULATOR_H

// Function declarations
int add(int a, int b);
int subtract(int a, int b);
int multiply(int a, int b);

#endif
```

**Header files** declare what functions exist - they're the interface other files use. The `#ifndef` guard prevents multiple inclusion if this header is included by several files.

### Creating the Implementation

Create `operations.c`:

```bash
nano operations.c
```

```c
#include "calculator.h"

int add(int a, int b) {
    return a + b;
}

int subtract(int a, int b) {
    return a - b;
}

int multiply(int a, int b) {
    return a * b;
}
```

This file **implements** the functions declared in the header.

### Creating the Main Program

Create `calculator.c`:

```bash
nano calculator.c
```

```c
#include <stdio.h>
#include "calculator.h"

int main() {
    printf("10 + 5 = %d\n", add(10, 5));
    printf("10 - 5 = %d\n", subtract(10, 5));
    printf("10 * 5 = %d\n", multiply(10, 5));
    return 0;
}
```

### Compiling Multi-File Projects

**Method 1: All at once**

```bash
gcc operations.c calculator.c -o calc
./calc
```

**Output:**
```
10 + 5 = 15
10 - 5 = 5
10 * 5 = 50
```

**Method 2: Separate compilation**

```bash
# Compile each .c file to .o (object file)
gcc -c operations.c -o operations.o
gcc -c calculator.c -o calculator.o

# Link all .o files together
gcc operations.o calculator.o -o calc

# Run
./calc
```

Both methods produce identical results, but separate compilation has advantages.

**Why separate compilation matters:**

Imagine you modify only `calculator.c` in a 100-file project. With separate compilation:
1. Recompile only `calculator.c` to `calculator.o` (fast - one file)
2. Keep existing `.o` files for unchanged files (no recompilation)
3. Re-link all `.o` files (fast)

Result: Building takes seconds instead of minutes.

---

## Chapter 3: Build Automation with Make

For projects with many files, typing gcc commands repeatedly is tedious and error-prone. Make automates the build process.

### The Problem

Imagine a project with 50 source files:
```bash
gcc -c file1.c -o file1.o
gcc -c file2.c -o file2.o
gcc -c file3.c -o file3.o
# ... repeat 47 more times
gcc file1.o file2.o file3.o ... file50.o -o program
```

And if you modify `file5.c`, which other files need recompiling? You'd have to track dependencies manually.

### The Solution: Makefile

A Makefile describes how to build your project and what depends on what. Make reads it and handles the rest.

Create a Makefile:

```bash
nano Makefile
```

Type the following:

```makefile
# Variables
CC = gcc
CFLAGS = -Wall -O2

# Default target
all: calc

# How to build calc
calc: operations.o calculator.o
	$(CC) operations.o calculator.o -o calc

# How to build object files
operations.o: operations.c calculator.h
	$(CC) $(CFLAGS) -c operations.c

calculator.o: calculator.c calculator.h
	$(CC) $(CFLAGS) -c calculator.c

# Clean up
clean:
	rm -f *.o calc
```

⚠️ **Important:** The indented lines under targets must use TAB, not spaces!

**Makefile syntax:**
```makefile
target: dependencies
	command
```

**Understanding the example:**
- `calc` depends on `operations.o` and `calculator.o`
- `operations.o` depends on `operations.c` and `calculator.h`
- If `calculator.h` changes, both `.o` files need rebuilding (they both include it)

### Using Make

Build everything:

```bash
make
```

Run:

```bash
./calc
```

Clean build artifacts:

```bash
make clean
```

Rebuild from scratch:

```bash
make clean
make
```

### Make's Intelligence: Incremental Builds

Let's see Make's power. Modify `operations.c` to add a new function:

```bash
nano operations.c
```

Add at the end:

```c
int divide(int a, int b) {
    if (b != 0) return a / b;
    return 0;
}
```

Now rebuild:

```bash
make
```

**What Make does:**
1. Checks timestamps: `operations.c` is newer than `operations.o`
2. Recompiles only `operations.c` to `operations.o`
3. `calculator.c` unchanged - skips recompiling
4. Re-links to create `calc`

**Output:**
```
gcc -Wall -O2 -c operations.c
gcc operations.o calculator.o -o calc
```

Notice: Only `operations.c` recompiled! For large projects, this saves enormous time.

---

## Chapter 4: Modern Build Systems - CMake and Ninja

Makefiles work well but have limitations - they're platform-specific (Linux Makefile differs from Windows) and become complex for large projects. CMake addresses this.

### What is CMake?

CMake is a build system generator - it creates Makefiles (or other build files) for you. Write one `CMakeLists.txt` and CMake generates appropriate build files for any platform.

```
CMakeLists.txt → CMake → Makefile → Make → Executable
  (platform-    (generates) (platform-  (builds)
   independent)            specific)
```

### Creating CMakeLists.txt

Create a CMakeLists.txt file:

```bash
nano CMakeLists.txt
```

```cmake
cmake_minimum_required(VERSION 3.10)
project(Calculator)

# Use C11 standard
set(CMAKE_C_STANDARD 11)

# Create executable from source files
add_executable(calc
   operations.c
   calculator.c
)
```

This is simpler than a Makefile - just list your sources, CMake figures out dependencies automatically.

### Using CMake

Create a separate build directory and use CMake:

```bash
# Create separate build directory
mkdir build
cd build

# Generate Makefiles
cmake ..

# Build
make

# Run
./calc
```

**The build directory approach (out-of-source build):**
- All build artifacts go in `build/`
- Source directory stays clean
- Easy cleanup: `rm -rf build`
- Can have multiple builds: `build-debug/`, `build-release/`

### Ninja: Faster Builds

Make works, but for large projects (with hundreds of files), Ninja is significantly faster.

Install Ninja:

```bash
sudo apt install ninja-build
```

Use with CMake:

```bash
cd ~/calculator-project
mkdir build-ninja
cd build-ninja

# Generate Ninja files instead of Makefiles
cmake -G Ninja ..

# Build with Ninja
ninja

# Clean
ninja clean

# Build with Ninja
ninja

# Run
./calc
```

**Why Ninja?**
- Designed for speed (2-3x faster than Make on large projects)
- Better parallelization across CPU cores
- Minimal overhead
- Used by large projects: Google Chrome, Android, LLVM

When you build complex projects during the workshop, using Ninja will significantly reduce compilation time.

---

## Chapter 5: Shared Libraries

When you compile programs, library code can be included statically (copied into executable) or dynamically (loaded at runtime). Understanding shared libraries is important because many projects use them extensively.

### Static vs Dynamic Linking

**Static linking:**
- Library code copied into your executable
- Larger executable size
- No external dependencies at runtime
- File extension: `.a` (archive)

**Dynamic linking:**
- Library loaded at runtime
- Smaller executable size
- Multiple programs share one library copy in memory
- Library can be updated independently
- File extension: `.so` (shared object)

**Example scenario:**

You have 10 programs all using math functions:

**Without shared library:**
```
program1 (1.5 MB) = your code (500 KB) + library (1 MB)
program2 (1.5 MB) = your code (500 KB) + library (1 MB)
...
program10 (1.5 MB) = your code (500 KB) + library (1 MB)
Total: 15 MB
```

**With shared library:**
```
program1 (500 KB) = your code only
program2 (500 KB) = your code only
...
program10 (500 KB) = your code only
libmath.so (1 MB) = library (shared by all)
Total: 6 MB
```

Plus, all 10 programs share one copy in RAM when running.

### Creating a Shared Library

Let's convert our calculator operations into a shared library:

**Step 1: Compile with Position-Independent Code**

```bash
cd ~/calculator-project
gcc -fPIC -c operations.c -o operations.o
```

The `-fPIC` flag generates position-independent code - required for shared libraries because they can be loaded at any memory address.

**Step 2: Create the shared library**

```bash
gcc -shared operations.o -o liboperations.so
```

Now you have `liboperations.so` - a shared library other programs can use.

**Step 3: Link your program against it**

```bash
gcc calculator.c -o calc -L. -loperations -Wl,-rpath,.
```

**Flags explained:**
- `-L.` - Look in current directory for libraries
- `-loperations` - Link with liboperations.so (lib prefix and .so suffix added automatically)
- `-Wl,-rpath,.` - Tell program where to find the library at runtime

**Step 4: Run the program**

```bash
./calc
```

Works the same as before, but now uses external shared library!

### Viewing Library Dependencies

See what libraries your program depends on:

```bash
ldd calc
```

**Example output:**
```
liboperations.so => ./liboperations.so (0x00007f8a2b3c4000)
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f8a2b1e0000)
```

This shows your program depends on:
- `liboperations.so` - Your library (in current directory)
- `libc.so.6` - System C library (contains printf, malloc, etc.)

At runtime, the dynamic linker loads these libraries before your program starts.

---

## Chapter 6: Debugging with GDB

When programs don't behave as expected, you need to investigate what's happening. GDB (GNU Debugger) lets you step through code, inspect variables, and find bugs.

### Installing GDB

```bash
sudo apt install gdb
```

### Creating a Program with a Bug

Let's create a program with an intentional bug:

```bash
cd ~/calculator-project
nano buggy.c
```

```c
#include <stdio.h>

int divide(int a, int b) {
    return a / b;  // Bug: what if b is 0?
}

int main() {
    int result;
    
    result = divide(10, 2);
    printf("10 / 2 = %d\n", result);
    
    result = divide(10, 0);  // This will crash!
    printf("10 / 0 = %d\n", result);
    
    return 0;
}
```

### Compiling with Debug Symbols

To use GDB effectively, compile with `-g`:

```bash
gcc -g buggy.c -o buggy
```

The `-g` flag includes debugging information - variable names, line numbers, function names.

### Running in GDB

Start GDB:

```bash
gdb ./buggy
```

You're now in GDB. The prompt shows `(gdb)`.

Run the program:

```
(gdb) run
```

**Output:**
```
10 / 2 = 5

Program received signal SIGFPE, Arithmetic exception.
0x000055555555516a in divide (a=10, b=0) at buggy.c:4
4           return a / b;
```

GDB caught the crash and shows:
- Signal: `SIGFPE` (floating point exception - division by zero)
- Function: `divide`
- Parameters: `a=10, b=0`
- Line: `buggy.c:4`

### Basic GDB Commands

**View source around the crash:**

```
(gdb) list
```

Shows code context around line 4.

**Examine variables:**

```
(gdb) print a
$1 = 10
(gdb) print b
$2 = 0
```

Now you see the problem - `b` is 0!

**See call stack:**

```
(gdb) backtrace
```

**Output:**
```
#0  divide (a=10, b=0) at buggy.c:4
#1  0x00005555555551a8 in main () at buggy.c:13
```

Shows: `main` called `divide`, which is where it crashed.

### Setting Breakpoints

Instead of waiting for crashes, pause execution at specific points:

```
(gdb) quit          # Exit current session
gdb ./buggy         # Start fresh

# Set breakpoint at line 13 (before the crash)
(gdb) break 13
Breakpoint 1 at 0x...: file buggy.c, line 13.

# Run
(gdb) run
```

Program starts and pauses at line 13:

```
Breakpoint 1, main () at buggy.c:13
13          result = divide(10, 0);
```

Now you can inspect BEFORE the crash:

```
(gdb) print result
$1 = 5

# Step into the divide function
(gdb) step

# Now inside divide, check parameters
(gdb) print a
$2 = 10
(gdb) print b
$3 = 0
```

You've identified the problem before it crashes!

Exit GDB:

```
(gdb) quit
```

### Essential GDB Commands

| Command | Shortcut | Purpose |
|---------|----------|---------|
| `run` | `r` | Start program |
| `break <line>` | `b <line>` | Pause at line number |
| `continue` | `c` | Resume execution |
| `next` | `n` | Execute next line (don't enter functions) |
| `step` | `s` | Execute next line (enter functions) |
| `print <var>` | `p <var>` | Show variable value |
| `list` | `l` | Show source code |
| `backtrace` | `bt` | Show call stack |
| `quit` | `q` | Exit GDB |

**When to use GDB:**
- Program crashes (segmentation fault, arithmetic exception)
- Unexpected behavior (check variable values)
- Understanding code flow (step through execution)

---

## 💻 Hands-On Exercises

Now test your C compilation skills!

### Exercise 1: Complete Compilation Workflow

**Problem:** Practice the entire compilation process from source to executable.

**Requirements:**
- Create a new C program `greet.c` that:
  - Takes no command-line arguments
  - Prints "Welcome to C programming!"
  - Returns 0
- Compile it with warnings enabled
- Run the executable
- Clean up by removing the executable

<details markdown="1">
<summary>💡 Hint</summary>

Create file with nano, use `gcc -Wall`, run with `./`, remove with `rm`.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
cd ~/c-learning
nano greet.c
```

Content:
```c
#include <stdio.h>

int main() {
    printf("Welcome to C programming!\n");
    return 0;
}
```

Then:
```bash
gcc -Wall greet.c -o greet
./greet
rm greet
```
</details>

---

### Exercise 2: Understanding Compilation Stages

**Problem:** Observe each stage of compilation.

**Requirements:**
- Use your `hello.c` file from earlier
- Generate the preprocessed file (`.i`)
- Generate the assembly file (`.s`)
- Generate the object file (`.o`)
- Link the object file to create executable
- Count lines in the preprocessed file
- View a few lines of the assembly file

<details markdown="1">
<summary>💡 Hint</summary>

Use `-E` for preprocessing, `-S` for assembly, `-c` for object file. Use `wc -l` to count lines, `head` to view first lines.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
cd ~/c-learning
gcc -E hello.c -o hello.i
gcc -S hello.c -o hello.s
gcc -c hello.c -o hello.o
gcc hello.o -o hello
wc -l hello.i
head -20 hello.s
```
</details>

---

### Exercise 3: Multi-File Project from Scratch

**Problem:** Build a temperature converter with multiple files.

**Requirements:**
- Create a header file `temp.h` with declarations for:
  - `float celsius_to_fahrenheit(float c)`
  - `float fahrenheit_to_celsius(float f)`
- Create `temp.c` implementing these functions
- Create `main.c` that converts 100°C to Fahrenheit and 212°F to Celsius
- Compile using separate compilation method
- Run and verify output

<details markdown="1">
<summary>💡 Hint</summary>

Formula: F = C × 9/5 + 32, C = (F - 32) × 5/9. Use `gcc -c` for each .c file, then link .o files.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
mkdir ~/temp-converter
cd ~/temp-converter
nano temp.h
```

temp.h:
```c
#ifndef TEMP_H
#define TEMP_H

float celsius_to_fahrenheit(float c);
float fahrenheit_to_celsius(float f);

#endif
```

```bash
nano temp.c
```

temp.c:
```c
#include "temp.h"

float celsius_to_fahrenheit(float c) {
    return c * 9.0 / 5.0 + 32.0;
}

float fahrenheit_to_celsius(float f) {
    return (f - 32.0) * 5.0 / 9.0;
}
```

```bash
nano main.c
```

main.c:
```c
#include <stdio.h>
#include "temp.h"

int main() {
    printf("100°C = %.2f°F\n", celsius_to_fahrenheit(100));
    printf("212°F = %.2f°C\n", fahrenheit_to_celsius(212));
    return 0;
}
```

Compile:
```bash
gcc -c temp.c -o temp.o
gcc -c main.c -o main.o
gcc temp.o main.o -o converter
./converter
```
</details>

---

### Exercise 4: Makefile Creation

**Problem:** Create a Makefile for the temperature converter project.

**Requirements:**
- Create a Makefile with:
  - Variables for CC and CFLAGS
  - Target `all` that builds `converter`
  - Target `clean` that removes .o files and executable
  - Proper dependencies
- Test `make`, `make clean`, and rebuild

<details markdown="1">
<summary>💡 Hint</summary>

Remember TAB indentation for commands. Define dependencies based on which files include which headers.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
cd ~/temp-converter
nano Makefile
```

Content:
```makefile
CC = gcc
CFLAGS = -Wall

all: converter

converter: temp.o main.o
	$(CC) temp.o main.o -o converter

temp.o: temp.c temp.h
	$(CC) $(CFLAGS) -c temp.c

main.o: main.c temp.h
	$(CC) $(CFLAGS) -c main.c

clean:
	rm -f *.o converter
```

Test:
```bash
make
./converter
make clean
make
```
</details>

---

### Exercise 5: CMake Build System

**Problem:** Convert the temperature converter to use CMake.

**Requirements:**
- Create CMakeLists.txt for the project
- Create a build directory
- Generate build files with CMake
- Build the project
- Run the executable
- Clean up by removing the build directory

<details markdown="1">
<summary>💡 Hint</summary>

Use `cmake_minimum_required`, `project`, and `add_executable`. Create build directory, run cmake from inside it.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
cd ~/temp-converter
nano CMakeLists.txt
```

Content:
```cmake
cmake_minimum_required(VERSION 3.10)
project(TempConverter)

set(CMAKE_C_STANDARD 11)

add_executable(converter
   temp.c
   main.c
)
```

Build:
```bash
mkdir build
cd build
cmake ..
make
./converter
cd ..
rm -rf build
```
</details>

---

### Exercise 6: Shared Library Creation

**Problem:** Convert temperature functions into a shared library.

**Requirements:**
- Compile `temp.c` with -fPIC to create `temp.o`
- Create shared library `libtemp.so`
- Compile `main.c` linking against the shared library
- Use `ldd` to verify dependencies
- Run the program

<details markdown="1">
<summary>💡 Hint</summary>

Use `-fPIC` for position-independent code, `-shared` to create .so, `-L.` and `-ltemp` to link, `-Wl,-rpath,.` for runtime path.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
cd ~/temp-converter
gcc -fPIC -c temp.c -o temp.o
gcc -shared temp.o -o libtemp.so
gcc main.c -o converter -L. -ltemp -Wl,-rpath,.
ldd converter
./converter
```
</details>

---

### Exercise 7: Debugging with GDB

**Problem:** Debug a program with a logical error.

**Requirements:**
- Create a program that calculates factorial but has a bug
- Compile with debug symbols
- Use GDB to find the bug by:
  - Setting a breakpoint
  - Stepping through the function
  - Printing variable values
- Identify what's wrong (don't fix it, just identify)

<details markdown="1">
<summary>💡 Hint</summary>

Create factorial function with loop, compile with `-g`, use `break`, `step`, and `print` in GDB.
</details>

<details markdown="1">
<summary>✅ Solution</summary>

```bash
cd ~/c-learning
nano factorial.c
```

Content (with intentional bug):
```c
#include <stdio.h>

int factorial(int n) {
    int result = 0;  // Bug: should be 1
    for (int i = 1; i <= n; i++) {
        result *= i;
    }
    return result;
}

int main() {
    printf("5! = %d\n", factorial(5));
    return 0;
}
```

Debug:
```bash
gcc -g factorial.c -o factorial
gdb ./factorial
(gdb) break factorial
(gdb) run
(gdb) next
(gdb) print result
(gdb) next
(gdb) print result
# Observe that result stays 0!
(gdb) quit
```

The bug: `result` initialized to 0 instead of 1, so multiplication always gives 0.
</details>

---

### Exercise 8: Cleanup

**Problem:** Clean up all practice projects.

**Requirements:**
- Remove the following directories:
  - `~/c-learning`
  - `~/calculator-project`
  - `~/temp-converter`
- Verify they're gone

<details markdown="1">
<summary>✅ Solution</summary>

```bash
rm -rf ~/c-learning
rm -rf ~/calculator-project
rm -rf ~/temp-converter
ls ~
```
</details>

---

## ✅ Self-Check Quiz

<details markdown="1">
<summary>❓ Q1: What are the four stages of compilation?</summary>

**Answer:** Preprocessing (handle #include, #define), Compilation (C to assembly), Assembly (assembly to machine code), Linking (combine object files with libraries).
</details>

<details markdown="1">
<summary>❓ Q2: What's the difference between .c, .h, and .o files?</summary>

**Answer:** 
- `.c` - Source code (implementation)
- `.h` - Header file (declarations/interface)
- `.o` - Object file (compiled machine code, not yet linked)
</details>

<details markdown="1">
<summary>❓ Q3: Why always use `-Wall` flag?</summary>

**Answer:** Enables compiler warnings to catch potential bugs like unused variables, uninitialized variables, type mismatches. Warnings often indicate real problems.
</details>

<details markdown="1">
<summary>❓ Q4: What advantage does separate compilation provide?</summary>

**Answer:** For large projects, only modified files need recompiling. Unchanged files keep their existing .o files, saving significant compilation time.
</details>

<details markdown="1">
<summary>❓ Q5: What does `make clean` do?</summary>

**Answer:** Removes build artifacts (.o files, executables) as defined in Makefile's clean target. Allows fresh rebuild.
</details>

<details markdown="1">
<summary>❓ Q6: Why create a separate `build/` directory with CMake?</summary>

**Answer:** Keeps source directory clean (all build artifacts in build/), easy cleanup (rm -rf build), can have multiple build configurations (debug, release) simultaneously.
</details>

<details markdown="1">
<summary>❓ Q7: What's Ninja and why use it?</summary>

**Answer:** Ninja is a build tool designed for speed - significantly faster than Make for large projects (2-3x), better CPU parallelization.
</details>

<details markdown="1">
<summary>❓ Q8: What are shared libraries (.so files)?</summary>

**Answer:** Shared libraries are code modules loaded at runtime, reducing executable size and enabling code sharing between programs. Multiple programs can share one library in memory.
</details>

<details markdown="1">
<summary>❓ Q9: Why compile with `-g` flag when using GDB?</summary>

**Answer:** Includes debug symbols (variable names, line numbers, function names) so GDB can show meaningful information instead of just memory addresses.
</details>

<details markdown="1">
<summary>❓ Q10: What does `ldd` command show?</summary>

**Answer:** Lists shared library dependencies of an executable - which .so files the program needs to run and where they're located.
</details>

---

## 🎓 Summary

### Commands Mastered

| Command | Purpose |
|---------|---------|
| `gcc file.c -o output` | Compile C program |
| `gcc -Wall` | Enable warnings |
| `gcc -g` | Include debug symbols |
| `gcc -O2` | Optimize code |
| `gcc -c` | Compile to object file |
| `gcc -fPIC` | Position-independent code (for shared libs) |
| `gcc -shared` | Create shared library |
| `make` | Build using Makefile |
| `make clean` | Remove build artifacts |
| `cmake ..` | Generate build files |
| `ninja` | Build with Ninja (fast) |
| `ldd` | Show library dependencies |
| `gdb` | Debug programs |

### Key Concepts

- ✓ Compilation has 4 stages: preprocessing, compilation, assembly, linking
- ✓ GCC is the standard C compiler on Linux
- ✓ Header files (.h) declare, source files (.c) implement
- ✓ Object files (.o) are compiled but not linked
- ✓ Makefiles automate builds and enable incremental compilation
- ✓ CMake generates platform-independent build files
- ✓ Ninja provides faster builds for large projects
- ✓ Shared libraries (.so) enable modularity and code reuse
- ✓ GDB helps find bugs by inspecting running programs
- ✓ Always compile with `-Wall` for warnings, `-g` for debugging

---

## 📚 Additional Resources

**C Programming:**
- [The C Programming Language](https://en.wikipedia.org/wiki/The_C_Programming_Language) by Kernighan & Ritchie
- [Learn C](https://www.learn-c.org/) - Interactive tutorials

**Build Tools:**
- [GCC Documentation](https://gcc.gnu.org/onlinedocs/)
- [GNU Make Manual](https://www.gnu.org/software/make/manual/)
- [CMake Tutorial](https://cmake.org/cmake/help/latest/guide/tutorial/)
- [Ninja Build](https://ninja-build.org/)

**Debugging:**
- [GDB Tutorial](https://www.gdbtutorial.com/)
- [Debugging with GDB](https://sourceware.org/gdb/current/onlinedocs/gdb/)

**Shared Libraries:**
- [Shared Libraries Guide](https://tldp.org/HOWTO/Program-Library-HOWTO/shared-libraries.html)

---

**Module Complete!** ✅

[⬅️ Previous: Module 4](module-4-docker) | [Next: Module 6 →](module-6-networking)

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
- [ ] Understand OAI project organization

---

## Introduction
C has been the foundation of systems programming for over five decades - from operating systems to network stacks to telecommunications infrastructure. Its direct hardware access, minimal runtime overhead, and predictable performance make it the natural choice for domains where timing precision and resource efficiency are critical: embedded systems, IoT devices, real-time communications, and telecom protocols.

What makes OpenAirInterface notable is its use of C on general-purpose  with open-source tools. You can build and experiment with 5G technology using a standard Linux machine, GCC, and common development tools - making cellular technology accessible for learning and experimentation.

This module covers the basic Linux C development workflow - from compiling simple programs to understanding how complex projects like OAI are built using GCC, Make, and CMake.

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
```bash
mkdir ~/c-learning
cd ~/c-learning
# Create hello.c using your favorite text editor (nano, vim, emacs)
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

Save and exit.

### Compiling the Program
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

### The Four Stages of Compilation

When you ran `gcc hello.c -o hello`, the compiler performed four stages. Let's see each one:

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

The preprocessor processes `#include <stdio.h>` by inserting the entire header file. Your 5-line program expanded to 847 lines with all the included headers!You can take a lookat it in the text editor.

**Stage 2: Compilation to Assembly**
```bash
gcc -S hello.c -o hello.s
```

The compiler translates C into assembly language. View it:
```bash
#Use your favorite text editor
nano hello.s
```

You'll see instructions like `movq`, `call`, `ret` - assembly code for your CPU architecture.

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

**Enable warnings:**
```bash
gcc -Wall hello.c -o hello
```

Use `-Wall` to catch potential bugs. Let's see an example.

Create `warn.c`:
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

Its good practice to fix these warnings, as they often indicate real bugs.

---

## Chapter 2: Multi-File Projects

Real projects split code across multiple files for organization, maintainability, and team collaboration. Let's build a calculator program with separate modules.

### Project Structure

We'll create:
- `calculator.h` - Function declarations (interface)
- `calculator.c` - Function implementations
- `main.c` - Main program

### Creating the Header File
```bash
mkdir ~/calculator-project
cd ~/calculator-project
```
Create **calculator.h** file with the  following code:

```bash

#ifndef CALCULATOR_H
#define CALCULATOR_H

// Function declarations
int add(int a, int b);
int subtract(int a, int b);
int multiply(int a, int b);

#endif

```

**Header files** declare what functions exist - they're the interface other files use.

The `#ifndef` guard prevents multiple inclusion if this header is included by several files.

### Creating the Implementation

Create **operations.c** file with the  following code:
```bash
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

### Creating the main Program

Create **calculator.c** file with the  following code:
```bash
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

**Your turn:**

Create the calculator project and try both compilation methods. 

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

Create a Makefile

```bash
#Use your favorite text editor
vi Makefile
```
and type the following:

```bash
# Variables
CC = gcc
CFLAGS = -Wall -O2

# Default target
all: calc

# How to build calc
calc: operations.o calculator.o
	$(CC) operations.o calculator.o  -o calc

# How to build object files
operations.o: operations.c calculator.h
	$(CC) $(CFLAGS) -c operations.c

calculator.o: calculator.c calculator.h
	$(CC) $(CFLAGS) -c calculator.c

# Clean up
clean:
	rm -f *.o calc
```

**Makefile syntax:**
```makefile
target: dependencies
	command
```

**Understanding the example:**
- `calc` depends on `oprations.o` and `calculator.o`
- `operations.o` depends on `operations.c` and `calculator.h`
- If `calculator.h` changes, both `.o` files need rebuilding (they both include it)

### Using Make
```bash
# Build everything
make

# Run
./calc

# Clean build artifacts
make clean

# Rebuild from scratch
make clean
make
```

### Make's Intelligence: Incremental Builds

Let's see Make's power. Modify `operations.c`:
```bash
# Add a new function
int divide(int a, int b);

int divide(int a, int b) {
    if (b != 0) return a / b;
    return 0;
}

# Rebuild
make
```

**What Make does:**
1. Checks timestamps: `operations.c` is newer than `operations.o`
2. Recompiles only `operations.c` to `operations.o`
3. `calculator.c` unchanged - skips recompiling
4. Re-links to create `calc`

**Output:**
```
gcc -Wall -O2 -c calculator.c
gcc operations.o calculator.o -o calc
```

Notice: Only `operations.c` recompiled! For large projects, this saves enormous time.

**Your turn:**

Create the Makefile, build the project, modify a file, and rebuild. Observe that Make intelligently recompiles only what changed.

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
Create **CMakeLists.txt** file and type the following:
```bash
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

Make works, but for large projects (like OAI with hundreds of files), Ninja is significantly faster.

**Install Ninja:**
```bash
sudo apt install ninja-build
```

**Use with CMake:**
```bash
# Generate Ninja files instead of Makefiles
cmake -G Ninja ..

# Build with Ninja
ninja

# Clean
ninja clean
```

**Why Ninja?**
- Designed for speed (2-3x faster than Make on large projects)
- Better parallelization across CPU cores
- Minimal overhead
- Used by large projects: Google Chrome, Android, LLVM

**When you build OAI during the workshop, using Ninja will significantly reduce compilation time** (from ~20 minutes to ~10 minutes on a 4-core system).

**Your turn:**
```bash
# Try both build tools
mkdir build-make && cd build-make
cmake ..
time make  # Note the time

cd ..
mkdir build-ninja && cd build-ninja
cmake -G Ninja ..
time ninja  # Compare the time

# Both produce same executable
```

---

## Chapter 5: Shared Libraries

When you compile programs, library code can be included statically (copied into executable) or dynamically (loaded at runtime). Understanding shared libraries is important because OAI uses them extensively.

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

### Why OAI Uses Shared Libraries

OAI is highly modular. Different components compile as shared libraries:

**OAI shared libraries:**
- `librfsimulator.so` - Software radio simulator (no hardware needed)
- `libcoding.so` - Channel coding functions (LDPC, Polar codes)
- `liboai_eth_transpro.so` - Ethernet transport layer
- `libparams_libconfig.so` - Configuration file parsing


### Creating a Shared Library

Let's convert our calculator into a shared library:

**Step 1: Compile with Position-Independent Code**
```bash
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

**Your turn:**

Create a shared library from calculator.c and link your program against it. Use `ldd` to verify dependencies.

---

## Chapter 6: Debugging with GDB

When programs don't behave as expected, you need to investigate what's happening. GDB (GNU Debugger) lets you step through code, inspect variables, and find bugs.

### Installing GDB
```bash
sudo apt install gdb
```

### Creating a Program with a Bug
Create a file **buggy.c** and and enter the following:

```bash
cd ~/calculator-project
vi buggy.c
```
```bash
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
```bash
gdb ./buggy
```

You're now in GDB. The prompt shows `(gdb)`.

**Run the program:**
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
#1  0x00005555555551a8 in main () at buggy.c:12
```

Shows: `main` called `divide`, which is where it crashed.

### Setting Breakpoints

Instead of waiting for crashes, pause execution at specific points:
```
(gdb) quit          # Exit current session
gdb ./buggy         # Start fresh

# Set breakpoint at line 12 (before the crash)
(gdb) break 12
Breakpoint 1 at 0x...: file buggy.c, line 12.

# Run
(gdb) run
```

Program starts and pauses at line 12:
```
Breakpoint 1, main () at buggy.c:12
12          result = divide(10, 0);
```

**Now you can inspect BEFORE the crash:**
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


**When to use GDB during the workshop:**
- Program crashes (segmentation fault, arithmetic exception)
- Unexpected behavior (check variable values)
- Understanding code flow (step through execution)

**Your turn:**
```bash
# Create buggy.c
# Compile with -g
gcc -g buggy.c -o buggy

# Debug it
gdb ./buggy
(gdb) run              # See crash
(gdb) backtrace        # See where it happened
(gdb) print b          # Check variables
(gdb) quit

# Fix the bug and verify
```

---

## Chapter 7: OAI Project Organization

When you work with OAI during the workshop, you'll encounter a large codebase organized into modules. Understanding the structure helps you navigate it.

### Repository Structure
```
openairinterface5g/
├── cmake_targets/          ← Build system
│   ├── build_oai          ← Main build script
│   ├── CMakeLists.txt     ← CMake configuration
│   └── ran_build/
│       └── build/         ← Compiled binaries appear here
│
├── openair1/              ← Physical layer (PHY)
│   └── (modulation, coding, OFDM, FFT, etc.)
│
├── openair2/              ← Data link layers
│   └── (MAC scheduling, RLC, PDCP)
│
├── openair3/              ← RRC and Network layers
│   └── (RRC, NAS protocols)
│
├── common/                ← Shared utilities
│   └── (logging, utilities, data structures)
│
├── targets/               ← Configuration files
│   └── PROJECTS/          ← Config templates
│
└── executables/           ← Main programs
```


### Build Outputs

After building (which you'll do in the workshop), binaries appear in:
```
cmake_targets/ran_build/build/
├── nr-softmodem           ← 5G gNB executable
├── nr-uesoftmodem         ← 5G UE executable
│
├── librfsimulator.so      ← RF simulator library
├── libcoding.so           ← Channel coding
├── libparams_libconfig.so ← Config parser
└── (many other .so files) ← Modular components
```

**During the workshop, you'll use provided build scripts that handle all of this.** This chapter just gives you awareness of the organization and where to find things.

**Your turn:**

Just understand the structure. When you clone OAI in the workshop, you'll recognize this organization and know where binaries appear after building.

---


---

## Self-Check Quiz

<details>
<summary>❓ Q1: What are the four stages of compilation?</summary>

**Answer:** Preprocessing (handle #include, #define), Compilation (C to assembly), Assembly (assembly to machine code), Linking (combine object files with libraries).
</details>

<details>
<summary>❓ Q2: What's the difference between .c, .h, and .o files?</summary>

**Answer:** 
- `.c` - Source code (implementation)
- `.h` - Header file (declarations/interface)
- `.o` - Object file (compiled machine code, not yet linked)
</details>

<details>
<summary>❓ Q3: Why always use `-Wall` flag?</summary>

**Answer:** Enables compiler warnings to catch potential bugs like unused variables, uninitialized variables, type mismatches. Warnings often indicate real problems.
</details>

<details>
<summary>❓ Q4: What advantage does separate compilation provide?</summary>

**Answer:** For large projects, only modified files need recompiling. Unchanged files keep their existing .o files, saving significant compilation time.
</details>

<details>
<summary>❓ Q5: What does `make clean` do?</summary>

**Answer:** Removes build artifacts (.o files, executables) as defined in Makefile's clean target. Allows fresh rebuild.
</details>

<details>
<summary>❓ Q6: Why create a separate `build/` directory with CMake?</summary>

**Answer:** Keeps source directory clean (all build artifacts in build/), easy cleanup (rm -rf build), can have multiple build configurations (debug, release) simultaneously.
</details>

<details>
<summary>❓ Q7: What's Ninja and why use it?</summary>

**Answer:** Ninja is a build tool designed for speed - significantly faster than Make for large projects (2-3x), better CPU parallelization. Used by OAI for faster compilation.
</details>

<details>
<summary>❓ Q8: What are shared libraries (.so files) and why does OAI use them?</summary>

**Answer:** Shared libraries are code modules loaded at runtime, reducing executable size and enabling code sharing. OAI uses them for modularity - can swap RF backends (simulator vs hardware) by loading different .so files without recompiling.
</details>

<details>
<summary>❓ Q9: Why compile with `-g` flag when using GDB?</summary>

**Answer:** Includes debug symbols (variable names, line numbers, function names) so GDB can show meaningful information instead of just memory addresses.
</details>

<details>
<summary>❓ Q10: What does `ldd` command show?</summary>

**Answer:** Lists shared library dependencies of an executable - which .so files the program needs to run and where they're located.
</details>

---

## Summary

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


### What You Can Do Now

- ✓ Compile C programs and understand each stage
- ✓ Build multi-file projects
- ✓ Use Make for automated builds
- ✓ Use CMake and Ninja for modern projects
- ✓ Create and use shared libraries
- ✓ Debug programs with GDB
- ✓ Navigate large codebases like OAI

These skills prepare you for building and debugging OAI during the workshop.

---

## Additional Resources

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

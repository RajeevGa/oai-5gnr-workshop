---
layout: default
title: "Module 5: C Compilation"
parent: Pre-Workshop Preparation
nav_order: 5
---

# ⚙️ Module 5: C Compilation Basics

> **Duration:** 25 minutes | **Level:** Beginner | **Hands-on:** Yes

---

## 📚 What You'll Learn

By the end of this module, you'll be able to:

- [ ] Understand why C is used for telecommunications
- [ ] Compile simple C programs with GCC
- [ ] Understand the compilation process
- [ ] Use Makefiles for automation
- [ ] Work with CMake basics
- [ ] Understand how OAI is built

---

## 📖 Chapter 1: Why C for Telecommunications?

### What is C?

**C** is a programming language created in 1972. Despite being over 50 years old, it's still widely used for:
- Operating Systems (Linux, Windows kernel)
- Embedded Systems (routers, IoT devices)
- Telecommunications (5G, networking equipment)
- Performance-critical applications

### Why OAI Uses C

**Performance:**
- C compiles to highly efficient machine code
- Direct memory access and hardware control
- No garbage collection overhead
- Critical for real-time 5G processing

**Low-Level Control:**
- Direct hardware access
- Memory management control
- Suitable for embedded systems
- Works on resource-constrained devices

**Industry Standard:**
- Telecommunications industry uses C/C++
- Interoperability with existing systems
- Well-understood by engineers
- Mature tooling and libraries

**Portability:**
- Works on x86, ARM, and other architectures
- Can run on servers, embedded devices, SDRs
- Cross-platform compatibility

### You Don't Need to Write C

**For this workshop:**
- ✅ You'll compile C code (OAI)
- ✅ You'll run build commands
- ✅ You'll understand error messages
- ❌ You won't write C code

**Think of it like:**
- Using a microwave (workshop) vs Building a microwave (C programming)
- You need to know which buttons to press, not how it works internally

---

## 🔨 Chapter 2: The GCC Compiler

### What is a Compiler?

A **compiler** translates human-readable code into machine code:
```
C Source Code          Compiler          Executable
(hello.c)         →    [GCC]      →     (hello)
Human readable         Process           Machine code
```

**GCC** (GNU Compiler Collection) is the most common C compiler on Linux.

### Installing GCC
```bash
# Install build tools (includes GCC)
sudo apt install build-essential

# Verify installation
gcc --version
```

**Expected output:**
```
gcc (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0
```

### Simple Compilation Example

**Step 1: Create a C program**
```bash
# Create hello.c
cat > hello.c << 'EOF'
#include <stdio.h>

int main() {
    printf("Hello from C!\n");
    return 0;
}
EOF
```

**Step 2: Compile it**
```bash
# Compile hello.c to create executable 'hello'
gcc hello.c -o hello

# The -o flag specifies output filename
```

**Step 3: Run it**
```bash
# Make it executable (if needed)
chmod +x hello

# Run the program
./hello
```

**Output:**
```
Hello from C!
```

---

## 🔄 Chapter 3: The Compilation Process

### Four Stages of Compilation
```
Source Code → Preprocessing → Compilation → Assembly → Linking → Executable
 (hello.c)        (.i)           (.s)         (.o)              (hello)
```

#### Stage 1: Preprocessing
- Handles `#include`, `#define` directives
- Expands macros
- Removes comments
```bash
# See preprocessed output
gcc -E hello.c -o hello.i
cat hello.i
```

#### Stage 2: Compilation
- Converts C code to assembly language
- Optimizes code
```bash
# Generate assembly code
gcc -S hello.c -o hello.s
cat hello.s
```

#### Stage 3: Assembly
- Converts assembly to machine code
- Creates object file (.o)
```bash
# Generate object file
gcc -c hello.c -o hello.o

# Object files are binary (can't read with cat)
file hello.o
```

#### Stage 4: Linking
- Combines object files
- Links with libraries
- Creates final executable
```bash
# Link object file to create executable
gcc hello.o -o hello
```

**Why this matters:** Understanding stages helps debug build errors.

---

## 🛠️ Chapter 4: Common GCC Options

### Essential Flags
```bash
# Basic compilation
gcc program.c -o program

# Include warnings (good practice!)
gcc -Wall program.c -o program

# Include debugging symbols
gcc -g program.c -o program

# Optimization levels
gcc -O0 program.c -o program  # No optimization (default)
gcc -O1 program.c -o program  # Basic optimization
gcc -O2 program.c -o program  # Recommended optimization
gcc -O3 program.c -o program  # Aggressive optimization

# Specify C standard
gcc -std=c99 program.c -o program
gcc -std=c11 program.c -o program

# Link with math library
gcc program.c -o program -lm

# Multiple source files
gcc file1.c file2.c file3.c -o program
```

### Understanding Compiler Warnings
```bash
# Compile with all warnings
gcc -Wall -Wextra program.c -o program
```

**Warning example:**
```
warning: implicit declaration of function 'printf'
warning: unused variable 'x'
warning: format '%d' expects argument of type 'int'
```

**Best practice:** Fix all warnings before running!

---

## 📦 Chapter 5: Multi-File Projects

### Why Split Code?

Large projects like OAI have:
- Hundreds of source files
- Organized by functionality
- Easier to maintain
- Team collaboration

### Example: Multi-File Project

**File 1: math_utils.h** (header file)
```c
#ifndef MATH_UTILS_H
#define MATH_UTILS_H

int add(int a, int b);
int multiply(int a, int b);

#endif
```

**File 2: math_utils.c** (implementation)
```c
#include "math_utils.h"

int add(int a, int b) {
    return a + b;
}

int multiply(int a, int b) {
    return a * b;
}
```

**File 3: main.c** (main program)
```c
#include <stdio.h>
#include "math_utils.h"

int main() {
    int result = add(5, 3);
    printf("5 + 3 = %d\n", result);
    return 0;
}
```

**Compilation:**
```bash
# Compile each .c file to .o
gcc -c math_utils.c -o math_utils.o
gcc -c main.c -o main.o

# Link all .o files
gcc math_utils.o main.o -o calculator

# Or compile everything at once
gcc math_utils.c main.c -o calculator

# Run
./calculator
```

---

## 🔧 Chapter 6: Makefiles

### The Problem

For large projects:
```bash
# Typing this is tedious and error-prone:
gcc -c file1.c -o file1.o
gcc -c file2.c -o file2.o
gcc -c file3.c -o file3.o
gcc -c file4.c -o file4.o
gcc file1.o file2.o file3.o file4.o -o program
```

### The Solution: Makefile

A **Makefile** automates the build process.

**Basic Makefile syntax:**
```makefile
target: dependencies
	commands
```

### Example Makefile
```makefile
# Compiler and flags
CC = gcc
CFLAGS = -Wall -g

# Target: what to build
all: calculator

# How to build calculator
calculator: main.o math_utils.o
	$(CC) main.o math_utils.o -o calculator

# How to build object files
main.o: main.c math_utils.h
	$(CC) $(CFLAGS) -c main.c

math_utils.o: math_utils.c math_utils.h
	$(CC) $(CFLAGS) -c math_utils.c

# Clean up
clean:
	rm -f *.o calculator
```

### Using Make
```bash
# Build everything
make

# Or specifically
make all

# Clean up
make clean

# Rebuild from scratch
make clean
make
```

**Benefits:**
- ✅ Only recompiles changed files
- ✅ Handles dependencies automatically
- ✅ One command to build everything
- ✅ Easy cleanup

---

## 🏗️ Chapter 7: CMake Basics

### What is CMake?

**CMake** is a build system generator:
- Generates Makefiles (or other build files)
- Cross-platform (Linux, Windows, Mac)
- Handles complex dependencies
- Used by OAI

**Flow:**
```
CMakeLists.txt → CMake → Makefile → Make → Executable
  (recipe)      (generator)  (build)  (build) (program)
```

### Simple CMakeLists.txt
```cmake
cmake_minimum_required(VERSION 3.10)
project(Calculator)

# Set C standard
set(CMAKE_C_STANDARD 11)

# Add executable
add_executable(calculator 
    main.c 
    math_utils.c
)
```

### Using CMake
```bash
# Create build directory (out-of-source build)
mkdir build
cd build

# Generate Makefiles
cmake ..

# Build
make

# Run
./calculator
```

**Why separate build directory?**
- Keeps source directory clean
- Easy to delete build files (`rm -rf build`)
- Can have multiple build configurations

### CMake Benefits

- ✅ Cross-platform
- ✅ Finds dependencies automatically
- ✅ Supports complex projects (like OAI)
- ✅ Modern standard for C/C++ projects

---

## 🔍 Chapter 8: Understanding OAI Build Process

### OAI Build Structure
```
openairinterface5g/
├── cmake_targets/          ← Build scripts
│   ├── build_oai          ← Main build script
│   └── CMakeLists.txt     ← CMake configuration
├── openair1/              ← Physical layer
├── openair2/              ← MAC, RLC, PDCP
├── openair3/              ← RRC, NAS
└── common/                ← Shared utilities
```

### OAI Build Script

OAI provides a build script that handles everything:
```bash
cd ~/openairinterface5g/cmake_targets

# See build options
./build_oai --help

# Build gNB (5G base station)
./build_oai --gNB

# Build with RFsimulator (no hardware)
./build_oai --gNB --rfsimulator

# Clean build
./build_oai --clean

# Install dependencies first
./build_oai -I
```

### What Happens During Build
```
1. build_oai script runs
   ↓
2. Checks dependencies
   ↓
3. Runs CMake (generates Makefiles)
   ↓
4. Runs make (compiles code)
   ↓
5. Creates binaries in cmake_targets/ran_build/build/
   ↓
6. Done! (typically 10-20 minutes)
```

### Build Output Location
```
cmake_targets/ran_build/build/
├── nr-softmodem          ← gNB executable
├── nr-uesoftmodem        ← UE executable
└── other binaries
```

### Common Build Issues

**Issue: Missing dependencies**
```
CMake Error: Could not find package XYZ
```
**Solution:**
```bash
./build_oai -I  # Install dependencies
```

**Issue: Out of memory**
```
c++: fatal error: Killed signal terminated program
```
**Solution:**
```bash
# Build with fewer parallel jobs
./build_oai --gNB -j2  # Use 2 cores instead of all
```

**Issue: Old build artifacts**
```
undefined reference to...
```
**Solution:**
```bash
./build_oai --clean  # Clean old files
./build_oai --gNB    # Rebuild
```

---

## 🧪 Hands-On Exercise

### Task 1: Simple C Program (5 min)
```bash
# Create program
cat > greet.c << 'EOF'
#include <stdio.h>

int main() {
    printf("Welcome to OAI Workshop!\n");
    printf("Ready to build 5G networks!\n");
    return 0;
}
EOF

# Compile
gcc greet.c -o greet

# Run
./greet

# Compile with warnings
gcc -Wall greet.c -o greet

# With optimization
gcc -O2 -Wall greet.c -o greet
./greet

# Cleanup
rm greet greet.c
```

**Expected:** Program compiles and runs successfully

---

### Task 2: Multi-File Project (10 min)
```bash
# Create project directory
mkdir ~/c-project
cd ~/c-project

# Create header file
cat > operations.h << 'EOF'
#ifndef OPERATIONS_H
#define OPERATIONS_H

int add(int a, int b);
int subtract(int a, int b);

#endif
EOF

# Create implementation
cat > operations.c << 'EOF'
#include "operations.h"

int add(int a, int b) {
    return a + b;
}

int subtract(int a, int b) {
    return a - b;
}
EOF

# Create main program
cat > main.c << 'EOF'
#include <stdio.h>
#include "operations.h"

int main() {
    int x = 10, y = 5;
    printf("%d + %d = %d\n", x, y, add(x, y));
    printf("%d - %d = %d\n", x, y, subtract(x, y));
    return 0;
}
EOF

# Compile
gcc -c operations.c -o operations.o
gcc -c main.c -o main.o
gcc operations.o main.o -o calculator

# Run
./calculator

# Or compile in one command
gcc operations.c main.c -o calculator
./calculator
```

**Expected:** Output shows 10+5=15 and 10-5=5

---

### Task 3: Using Make (8 min)
```bash
# Still in ~/c-project

# Create Makefile
cat > Makefile << 'EOF'
CC = gcc
CFLAGS = -Wall -g

all: calculator

calculator: main.o operations.o
	$(CC) main.o operations.o -o calculator

main.o: main.c operations.h
	$(CC) $(CFLAGS) -c main.c

operations.o: operations.c operations.h
	$(CC) $(CFLAGS) -c operations.c

clean:
	rm -f *.o calculator
EOF

# Clean previous build
rm -f *.o calculator

# Build with make
make

# Run
./calculator

# Modify operations.c (add multiply function)
cat >> operations.h << 'EOF'
int multiply(int a, int b);
EOF

cat >> operations.c << 'EOF'

int multiply(int a, int b) {
    return a * b;
}
EOF

# Rebuild (only operations.c will recompile!)
make

# Clean
make clean

# Cleanup
cd ~
rm -rf ~/c-project
```

**Expected:** Make builds project efficiently

---

### Task 4: CMake Practice (7 min)
```bash
# Create project
mkdir ~/cmake-project
cd ~/cmake-project

# Create source files (reuse from before)
cat > hello.c << 'EOF'
#include <stdio.h>

int main() {
    printf("Hello from CMake!\n");
    return 0;
}
EOF

# Create CMakeLists.txt
cat > CMakeLists.txt << 'EOF'
cmake_minimum_required(VERSION 3.10)
project(HelloCMake)

set(CMAKE_C_STANDARD 11)

add_executable(hello hello.c)
EOF

# Create build directory
mkdir build
cd build

# Run CMake
cmake ..

# Build
make

# Run
./hello

# Cleanup
cd ~
rm -rf ~/cmake-project
```

**Expected:** CMake generates Makefiles and builds successfully

---

## ✅ Self-Check Quiz

<details>
<summary>❓ What does GCC do?</summary>

**Answer:** GCC (GNU Compiler Collection) is a compiler that translates C source code into executable machine code.
</details>

<details>
<summary>❓ What's the difference between .c, .h, .o files?</summary>

**Answer:**
- `.c` - C source code (implementation)
- `.h` - Header file (declarations, interfaces)
- `.o` - Object file (compiled machine code, not yet linked)
</details>

<details>
<summary>❓ Why use `-Wall` flag?</summary>

**Answer:** Shows compiler warnings to catch potential bugs. Good practice to fix all warnings.
</details>

<details>
<summary>❓ What does `make clean` do?</summary>

**Answer:** Removes compiled files (.o files and executables) to start fresh.
</details>

<details>
<summary>❓ What's the purpose of CMake?</summary>

**Answer:** CMake generates build files (like Makefiles) for different platforms. It's a build system generator, not a build system itself.
</details>

<details>
<summary>❓ Why create a separate `build/` directory with CMake?</summary>

**Answer:** Keeps source directory clean, easy to delete build artifacts, can have multiple build configurations.
</details>

<details>
<summary>❓ What does the OAI `build_oai` script do?</summary>

**Answer:** Automates the entire build process: checks dependencies, runs CMake, compiles code, and creates executables.
</details>

<details>
<summary>❓ Where are OAI binaries created after building?</summary>

**Answer:** `cmake_targets/ran_build/build/` directory (e.g., `nr-softmodem` for gNB)
</details>

---

## 🎓 Summary

### Commands Mastered

| Command | Purpose | Example |
|---------|---------|---------|
| `gcc file.c -o output` | Compile C program | `gcc hello.c -o hello` |
| `gcc -Wall` | Show warnings | `gcc -Wall program.c` |
| `gcc -g` | Include debug symbols | `gcc -g program.c` |
| `gcc -O2` | Optimize code | `gcc -O2 program.c` |
| `gcc -c` | Compile to object file | `gcc -c file.c` |
| `make` | Build using Makefile | `make` |
| `make clean` | Remove build files | `make clean` |
| `cmake ..` | Generate Makefiles | `cmake ..` |
| `./build_oai` | Build OAI | `./build_oai --gNB` |

### Key Concepts

- ✅ C compiles to efficient machine code
- ✅ Compilation has 4 stages: preprocessing, compilation, assembly, linking
- ✅ GCC is the standard C compiler on Linux
- ✅ Makefiles automate the build process
- ✅ CMake generates Makefiles (cross-platform)
- ✅ Large projects split code into multiple files
- ✅ Header files (.h) declare interfaces
- ✅ Source files (.c) contain implementations
- ✅ Object files (.o) are compiled but not linked

### OAI Build Process

- ✅ OAI is written in C for performance
- ✅ Uses CMake as build system
- ✅ `build_oai` script handles everything
- ✅ Typical build takes 10-20 minutes
- ✅ Binaries created in `cmake_targets/ran_build/build/`
- ✅ Can build gNB, UE, and other components
- ✅ Use `--rfsimulator` for software-only testing

---

## 📚 Additional Resources

**Learn C Programming:**
- [Learn C](https://www.learn-c.org/) - Interactive tutorial
- [C Programming Tutorial](https://www.cprogramming.com/)
- "The C Programming Language" by K&R - Classic book

**Compiler and Build Tools:**
- [GCC Documentation](https://gcc.gnu.org/onlinedocs/)
- [GNU Make Manual](https://www.gnu.org/software/make/manual/)
- [CMake Tutorial](https://cmake.org/cmake/help/latest/guide/tutorial/)

**Understanding Compilation:**
- [How Compilation Works](https://www.cprogramming.com/compilingandlinking.html)
- [Makefiles Tutorial](https://makefiletutorial.com/)

**Best Practices:**
- Always use `-Wall` to see warnings
- Fix warnings before running code
- Use `-g` for debugging builds
- Use `-O2` or `-O3` for release builds
- Keep source and build directories separate
- Use version control (Git) for code
- Comment your code
- Use meaningful variable names

---

## 🚀 What's Next

You now understand C compilation! This knowledge helps because:

- ✅ You'll build OAI from source code
- ✅ You'll understand build errors
- ✅ You'll use the `build_oai` script confidently
- ✅ You'll know where to find compiled binaries
- ✅ You can troubleshoot compilation issues

**Next module:** Network Basics - understanding IP addresses, connectivity, and interfaces.

---

**Module Complete!** ✅

[⬅️ Previous: Module 4](module-4-docker) | [Next: Module 6 →](module-6-networking)

# Chapter 3 & Chapter 4 Demonstration
## Guide to Computer Processor Architecture: A RISC-V Approach (2022 Edition)


---

# Overview

This project demonstrates the practical implementation of **Chapter 3** and **Chapter 4** from the book *Guide to Computer Processor Architecture: A RISC-V Approach (2022 Edition)*.

The objective of this work was to install the RISC-V development environment, compile C programs for the RV32I architecture, generate RISC-V assembly code, and analyze the compiler-generated instructions. The demonstration focuses on understanding how high-level C programs are translated into RISC-V assembly and how compiler optimizations affect the generated code.

---

# Objectives

The objectives of this demonstration were:

- Install the RISC-V GNU Cross Compiler Toolchain
- Install the Spike RISC-V ISA Simulator
- Cross-compile C programs for the RV32I architecture
- Generate and analyze RISC-V assembly code
- Understand compiler optimizations
- Study function calls, loops, conditional branches, and register usage
- Understand the RISC-V calling convention

---

# Development Environment

| Component | Details |
|-----------|---------|
| Operating System | Ubuntu 26.04 LTS |
| Virtual Machine | Oracle VirtualBox |
| Target Architecture | RV32I |
| Compiler | riscv32-unknown-elf-gcc 16.1.0 |
| Simulator | Spike RISC-V ISA Simulator |
| Repository | goossens-book-ip-projects (2022.1) |

---

# Repository Structure

The practical examples used during this demonstration are provided in the official repository accompanying the book.

```text
goossens-book-ip-projects/
└── 2022.1/
    ├── chapter_3/
    └── chapter_4/
```

---

# Chapter 3

## Installing the RISC-V GNU Toolchain

The official RISC-V GNU Toolchain was cloned from the GitHub repository.

```bash
git clone https://github.com/riscv/riscv-gnu-toolchain
```

The toolchain was configured for the RV32I architecture.

```bash
./configure \
    --prefix=/opt/riscv \
    --enable-multilib \
    --with-arch=rv32i
```

The toolchain was then compiled.

```bash
make -j4
```

After the build completed successfully, the following tools became available:

- riscv32-unknown-elf-gcc
- riscv32-unknown-elf-as
- riscv32-unknown-elf-ld
- riscv32-unknown-elf-objdump
- riscv32-unknown-elf-gdb

The installation was verified using:

```bash
riscv32-unknown-elf-gcc --version
```

Example output:

```text
riscv32-unknown-elf-gcc 16.1.0
```

This confirmed that the RISC-V cross-compilation environment was successfully installed.

---

## Compiling the First Program

The first example program supplied with the book was:

```text
hello.c
```

The program was compiled using:

```bash
riscv32-unknown-elf-gcc hello.c -o hello
```

This generated a RISC-V executable in ELF (Executable and Linkable Format).

Since the executable targets the RV32I architecture, it cannot be executed directly on an x86-based host machine.

---

## Generating the Assembly Listing

The generated executable was disassembled using:

```bash
riscv32-unknown-elf-objdump -d hello > hello.dump
```

This produced the file:

```text
hello.dump
```

The disassembly provides the compiler-generated RISC-V instructions corresponding to the original C program. The generated assembly includes:

- Function prologue
- Stack allocation
- Register initialization
- Function calls
- Function epilogue

Studying this file provides insight into how the compiler translates C code into RISC-V instructions.

---

## Installing the Spike ISA Simulator

The Spike RISC-V ISA Simulator was cloned from its official repository.

```bash
git clone https://github.com/riscv-software-src/riscv-isa-sim.git
```

The simulator was configured and built using:

```bash
mkdir build
cd build

../configure --prefix=/opt/riscv

make -j4
sudo make install
```

The installation was verified using:

```bash
spike --help
```

Successful execution confirmed that the simulator had been installed correctly.

---

# Chapter 3: Installing the RISC-V Toolchain and Running Programs on Spike

## Objective

The objective of this chapter was to set up a complete RISC-V software development environment and successfully compile, disassemble, and execute a RISC-V program using the Spike ISA simulator and Proxy Kernel (pk).

---

# Development Environment

| Component | Details |
|-----------|---------|
| Operating System | Ubuntu 22.04 (VirtualBox) |
| Compiler | riscv32-unknown-elf-gcc 16.1.0 |
| ISA Simulator | Spike |
| Proxy Kernel | riscv-pk |
| Architecture | RV32I + Zicsr + Zifencei |
| ABI | ilp32 |

---

# Repository

```
goossens-book-ip-projects/2022.1/chapter_3
```

---

# Toolchain Verification

Verify that the RISC-V compiler is installed correctly.

```bash
riscv32-unknown-elf-gcc --version
```

Example Output

```
riscv32-unknown-elf-gcc (g6afcc4f6d) 16.1.0
```

---

# Compiling the Program

Compile the sample C program.

```bash
cd ~/goossens-book-ip-projects/2022.1/chapter_3

riscv32-unknown-elf-gcc -o hello hello.c
```

This generates a 32-bit RISC-V executable named `hello`.

---

# Generating Assembly

Generate the assembly source produced by the compiler.

```bash
riscv32-unknown-elf-gcc -S hello.c
```

Output

```
hello.s
```

---

# Generating Disassembly

Generate the machine-code disassembly.

```bash
riscv32-unknown-elf-objdump -D hello > hello.dump
```

The disassembly file contains the generated RISC-V instructions.

---

# Building Spike and Proxy Kernel

Spike and the Proxy Kernel (pk) were compiled from source.

During compilation of the latest versions, additional RISC-V ISA extensions were required.

Required ISA extensions:

- Zicsr
- Zifencei

The Proxy Kernel was configured for:

```
Architecture : RV32I + Zicsr + Zifencei
ABI          : ilp32
```

---

# Running the Program on Spike

Execute the compiled program using Spike and the Proxy Kernel.

```bash
spike --isa=RV32IZicsr_Zifencei \
~/riscv-pk/build/pk \
hello
```

Output

```
hello world
```

---

# Verification

Executable information

```bash
file hello
```

Output

```
ELF 32-bit LSB executable
UCB RISC-V
Statically linked
```

Verify the executable architecture.

```bash
riscv32-unknown-elf-readelf -h hello
```

Output

```
Machine: RISC-V
```

---

# Workflow

```
hello.c
     │
     ▼
riscv32-unknown-elf-gcc
     │
     ▼
hello (ELF Executable)
     │
     ▼
Proxy Kernel (pk)
     │
     ▼
Spike ISA Simulator
     │
     ▼
Program Output
```

---

# Files Generated

```
hello
hello.s
hello.dump
```

---

# Learning Outcomes

- Installed the RISC-V GNU Toolchain.
- Cross-compiled C programs for the RISC-V architecture.
- Generated assembly code using GCC.
- Generated instruction-level disassembly using objdump.
- Built the Spike ISA Simulator.
- Built the RISC-V Proxy Kernel (pk).
- Executed a 32-bit RISC-V program using Spike.

---

# Challenges Faced

### Proxy Kernel Build Failure

While building the latest version of `riscv-pk`, the following errors occurred:

```
extension 'zicsr' required
extension 'zifencei' required
```

These were resolved by configuring the Proxy Kernel with the required ISA extensions:

```
RV32I + Zicsr + Zifencei
```

After rebuilding, the Proxy Kernel compiled successfully.

---

# Conclusion

The complete RISC-V software development environment was successfully established. A C program was cross-compiled into a 32-bit RISC-V executable, disassembled for instruction-level analysis, and executed successfully on the Spike ISA simulator using the Proxy Kernel.

# Chapter 3 Summary

The following tasks were successfully completed during Chapter 3:

- Installed the RISC-V GNU Toolchain
- Verified the compiler installation
- Cross-compiled a C program for RV32I
- Generated a RISC-V ELF executable
- Produced compiler-generated assembly using `objdump`
- Installed and verified the Spike ISA Simulator
- Investigated the Proxy Kernel compatibility issue with the modern RISC-V software stack

Chapter 3 established the complete software development environment required for generating and analyzing RISC-V programs and prepared the system for the assembly analysis performed in Chapter 4.

# Chapter 4

Chapter 4 focuses on understanding how the RISC-V compiler translates C programs into assembly language. Instead of generating executable files, the compiler is instructed to stop after producing assembly code using the **-S** option. This allows the generated instructions to be studied and compared with the original C program.

The practical exercises consist of four example programs:

- exp.c
- test.c
- loop.c
- fib.c

The first two examples were compiled without optimization (`-O0`), while the remaining examples were compiled with optimization level `-O1` to observe the impact of compiler optimizations on the generated assembly.

---

# Example 1 : exp.c

## Source Code

```c
void main(){
    int a=3, b=5, c=2, delta;
    delta = b*b - 4*a*c;
}
```

## Compilation

```bash
riscv32-unknown-elf-gcc -O0 -S exp.c
```

The compiler generated the assembly file:

```text
exp.s
```

---

## Purpose

This example demonstrates how arithmetic expressions written in C are translated into RISC-V assembly instructions.

The generated assembly contains:

- Variable initialization
- Stack allocation
- Register usage
- Software multiplication
- Arithmetic operations
- Function prologue and epilogue

---

## Important Observations

### Function Prologue

The generated assembly begins by allocating stack space.

```assembly
addi sp,sp,-32
sw ra,28(sp)
sw s0,24(sp)
sw s1,20(sp)
addi s0,sp,32
```

The compiler creates a stack frame and stores the registers that will be used during execution.

---

### Variable Initialization

The variables

```c
a = 3;
b = 5;
c = 2;
```

are translated into

```assembly
li a5,3
sw a5,-20(s0)

li a5,5
sw a5,-24(s0)

li a5,2
sw a5,-28(s0)
```

The immediate values are first loaded into a register and then stored on the stack.

---

### Software Multiplication

Instead of generating a hardware multiplication instruction, GCC generated

```assembly
call __mulsi3
```

This occurs because the compiler targets the **RV32I** instruction set.

RV32I does not include the hardware multiplication instruction (`mul`). Therefore, multiplication is performed using the software routine **__mulsi3**.

This demonstrates the difference between:

- RV32I
- RV32IM

where RV32IM includes hardware multiplication.

---

### Multiplication by Four

The expression

```c
4*a*c
```

was translated as

```assembly
slli a5,a5,2
```

instead of another multiplication instruction.

The compiler replaces multiplication by four with a left shift of two bits because

```text
x << 2 = x × 4
```

This is a faster operation.

---

### Final Computation

The expression

```c
delta = b*b - 4*a*c;
```

becomes

```assembly
sub a5,s1,a5
```

followed by

```assembly
sw a5,-32(s0)
```

which stores the result.

---

## Learning Outcome

This example demonstrates

- stack allocation
- local variable storage
- software multiplication
- arithmetic instructions
- compiler generated function structure

---

# Example 2 : test.c

## Source Code

```c
#include <stdio.h>

void main(){
    int a=3,b=5,c=2,delta;

    delta=b*b-4*a*c;

    if(delta<0)
        printf("no real solution\n");

    else if(delta==0)
        printf("one solution\n");

    else
        printf("two solutions\n");
}
```

---

## Compilation

```bash
riscv32-unknown-elf-gcc -O0 -S test.c
```

Generated file

```text
test.s
```

---

## Purpose

This example demonstrates

- Conditional branching
- String storage
- Function calls
- Comparison instructions

---

## Important Observations

### Read Only Data Section

The compiler stores string literals inside

```assembly
.section .rodata
```

Example

```assembly
.LC0:
.string "no real solution"
```

instead of embedding strings directly inside the code.

---

### Conditional Branches

The statement

```c
if(delta<0)
```

becomes

```assembly
bge a5,zero,.L2
```

Rather than checking

```text
delta < 0
```

the compiler reverses the condition and branches when

```text
delta >= 0
```

This produces more efficient control flow.

---

### Else-if Statement

The statement

```c
else if(delta==0)
```

is translated into

```assembly
bne a5,zero,.L4
```

Again, GCC reverses the comparison.

---

### Function Call

The compiler loads the address of the string

```assembly
lui a5,%hi(.LC0)
addi a0,a5,%lo(.LC0)
```

and then executes

```assembly
call puts
```

Although the original source uses

```c
printf()
```

the compiler replaces it with

```text
puts()
```

because only a string literal is being printed.

---

## Learning Outcome

This example demonstrates

- conditional branches
- labels
- read-only data section
- compiler optimization of function calls
- branch instructions

---

# Example 3 : loop.c

## Source Code

```c
#include <stdio.h>

void main(){

    int i,un,unm1=1,unm2=0;

    for(i=2;i<=10;i++){

        un=unm1+unm2;

        unm2=unm1;

        unm1=un;

    }

    printf("fibonacci(10)=%d\n",un);

}
```

---

## Compilation

```bash
riscv32-unknown-elf-gcc -O1 -S loop.c
```

Generated file

```text
loop.s
```

---

## Purpose

This example demonstrates the effect of compiler optimization.

Unlike previous examples, this program is compiled using

```text
-O1
```

---

## Important Observations

### Variables Remain in Registers

Unlike the previous examples, almost no stack variables are created.

The compiler stores variables directly inside registers.

This significantly reduces

- load instructions
- store instructions
- memory accesses

---

### Loop Optimization

The original loop

```c
for(i=2;i<=10;i++)
```

is transformed into an optimized countdown loop.

Instead of comparing

```text
i <= 10
```

the compiler initializes a counter

```assembly
li a5,9
```

and decrements it until zero.

This produces fewer instructions.

---

### Register Allocation

The generated assembly uses registers instead of stack variables.

For example

| Register | Purpose |
|-----------|----------|
| a3 | unm2 |
| a4 | unm1 |
| a1 | un |
| a5 | loop counter |

---

### Loop Body

The Fibonacci computation

```c
un = unm1 + unm2;
```

becomes

```assembly
add a1,a4,a3
```

followed by register moves

```assembly
mv a3,a4

mv a4,a1
```

No stack memory is accessed.

---

## Learning Outcome

This example demonstrates

- compiler optimization
- register allocation
- reduced memory accesses
- optimized loop generation

---

# Example 4 : fib.c

## Source Code

The final example implements the Fibonacci function using a separate function

```c
unsigned int fibonacci(unsigned int n)
```

which is called multiple times from `main()`.

---

## Compilation

```bash
riscv32-unknown-elf-gcc -O1 -S fib.c
```

Generated file

```text
fib.s
```

---

## Purpose

This example demonstrates

- function calls
- parameter passing
- return values
- unsigned comparisons
- optimized loops

---

## Important Observations

### Function Parameter

The function parameter

```c
unsigned int n
```

is received in

```assembly
a0
```

which follows the RISC-V calling convention.

---

### Return Value

Every call to

```assembly
call fibonacci
```

returns its result in

```assembly
a0
```

The compiler then copies the value into

```assembly
a1
```

before calling

```assembly
printf
```

because

- a0 stores the format string
- a1 stores the first integer argument

---

### Unsigned Comparison

The compiler generates

```assembly
bgeu
```

instead of

```assembly
bge
```

because the variables are declared as

```c
unsigned int
```

The suffix

```text
u
```

indicates an unsigned comparison.

---

### Optimized Function

The generated Fibonacci function contains

- no unnecessary stack frame
- almost no memory accesses
- efficient register usage
- optimized branching

The compiler keeps all temporary variables inside registers, resulting in compact and efficient assembly.

---

# Comparison of Optimization Levels

| Feature | -O0 | -O1 |
|----------|-----|-----|
| Variables stored on stack | Yes | Mostly No |
| Memory accesses | High | Low |
| Register usage | Limited | Extensive |
| Loop optimization | No | Yes |
| Generated instructions | More | Fewer |
| Execution efficiency | Lower | Higher |

---

# Chapter 4 Summary

By the end of Chapter 4, the following concepts were successfully demonstrated:

- Translation of C programs into RISC-V assembly
- Function prologue and epilogue generation
- Arithmetic instruction generation
- Software multiplication using `__mulsi3`
- Conditional branching using `bge`, `bne`, and `bgeu`
- Read-only data storage using `.rodata`
- Function calls and the RISC-V calling convention
- Register allocation
- Compiler optimizations using `-O1`
- Loop optimization
- Efficient register-based computation

The chapter provided practical insight into how a modern compiler converts high-level C programs into efficient RISC-V assembly code while following the RV32I instruction set and RISC-V calling conventions.

# Conclusion

The practical exercises from Chapter 3 and Chapter 4 successfully demonstrated the complete software development flow for the RV32I architecture using the RISC-V GNU Toolchain.

The work began with setting up the cross-compilation environment by installing the RISC-V GNU Toolchain and the Spike ISA Simulator. After verifying the installation, C programs were compiled for the RV32I architecture and disassembled to study the compiler-generated assembly instructions.

Chapter 4 further demonstrated how different C language constructs are translated into RISC-V assembly language. Arithmetic expressions, conditional statements, loops, function calls, and parameter passing were analyzed by comparing the original C programs with the generated assembly code. Compiler optimizations were also studied by comparing assembly generated using the `-O0` and `-O1` optimization levels.

Overall, these experiments provided practical understanding of:

- Cross compilation for the RV32I architecture
- Generation of RISC-V assembly code
- Function prologue and epilogue
- Register allocation
- Stack management
- Software multiplication using `__mulsi3`
- Conditional branch instructions
- Loop implementation
- Function calling conventions
- Compiler optimizations

These chapters provide a strong foundation for understanding the internal working of the RISC-V compiler and prepare the learner for more advanced topics involving processor architecture, assembly programming, and hardware implementation.

---

# Challenges Encountered

During the practical implementation, several compatibility issues were encountered due to differences between the software versions used by the 2022 edition of the book and the latest RISC-V development tools.

## 1. Proxy Kernel Compatibility

The latest version of the RISC-V Proxy Kernel (`riscv-pk`) could not be successfully compiled with the RV32I-only toolchain because it requires the newer Zicsr extension.

Assembler errors similar to the following were observed:

```text
Error: extension 'zicsr' required
```

Although this prevented execution using:

```bash
spike pk hello
```

it did not affect the compilation, assembly generation, or analysis performed during Chapters 3 and 4.

---

## 2. Modern GCC Compatibility

The `test.c` example provided in the book does not include the required header file for `printf()`.

Older versions of GCC accepted this as a warning, whereas GCC 16 reports it as an error.

The issue was resolved by including:

```c
#include <stdio.h>
```

No changes to the program logic were required.

---

## 3. Software Version Differences

Some commands and software packages referenced in the book have evolved since the publication of the 2022 edition.

Minor modifications were required during installation and compilation to maintain compatibility with the current development environment while preserving the objectives of the practical exercises.

---

# Skills Acquired

After completing these practical exercises, the following skills were developed:

- Installation of the RISC-V GNU Toolchain
- Installation and verification of the Spike ISA Simulator
- Cross-compilation for the RV32I architecture
- Generation and analysis of RISC-V assembly language
- Understanding compiler-generated instructions
- Interpretation of stack operations and register usage
- Analysis of compiler optimizations
- Understanding of RISC-V function calling conventions
- Debugging software compatibility issues during toolchain setup

---

# Files Generated During the Demonstration

| File | Description |
|------|-------------|
| hello | RV32I ELF executable |
| hello.dump | Disassembled assembly listing |
| exp.s | Assembly generated from `exp.c` |
| test.s | Assembly generated from `test.c` |
| loop.s | Optimized assembly generated from `loop.c` |
| fib.s | Optimized assembly generated from `fib.c` |

---

# Commands Used

## Toolchain Verification

```bash
riscv32-unknown-elf-gcc --version
```

---

## Compile C Program

```bash
riscv32-unknown-elf-gcc hello.c -o hello
```

---

## Generate Disassembly

```bash
riscv32-unknown-elf-objdump -d hello > hello.dump
```

---

## Generate Assembly Files

```bash
riscv32-unknown-elf-gcc -O0 -S exp.c

riscv32-unknown-elf-gcc -O0 -S test.c

riscv32-unknown-elf-gcc -O1 -S loop.c

riscv32-unknown-elf-gcc -O1 -S fib.c
```

---

## Verify Spike Installation

```bash
spike --help
```

---

# Learning Outcomes

The completion of Chapters 3 and 4 enabled the following learning outcomes:

- Understand the complete software workflow for developing applications targeting the RV32I architecture.
- Generate and analyze compiler-produced RISC-V assembly code.
- Understand how arithmetic operations, branches, loops, and function calls are implemented in assembly language.
- Compare compiler output produced using different optimization levels.
- Understand the importance of register allocation and efficient code generation.
- Gain familiarity with the RISC-V calling convention and compiler behavior.
- Recognize software compatibility challenges that arise when using modern development tools with educational material written for earlier software versions.

---

# References

1. Goossens, S., *Guide to Computer Processor Architecture: A RISC-V Approach*, 2022 Edition.

2. RISC-V GNU Toolchain

   https://github.com/riscv/riscv-gnu-toolchain

3. Spike RISC-V ISA Simulator

   https://github.com/riscv-software-src/riscv-isa-sim

4. Official RISC-V Foundation

   https://riscv.org

---

# Author

**Name:** *Sonali*

**Target Architecture:** RV32I

**Operating System:** Ubuntu 26.04 LTS (Oracle VirtualBox)

**Year:** 2026

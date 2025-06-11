# Week 1: RISC-V Bare-Metal Toolchain & Debugging
This week focused on establishing the foundational workflow for RISC-V development, including toolchain setup, cross-compilation, assembly analysis, and debugging. Key RISC-V architectural concepts were also explored.


# task 1 : Install & Sanity-Check the Toolchain
Step 1: Extract the Toolchain
```bash
cd ~/Downloads
tar -xzvf riscv-toolchain-rv32imac-x86_64-ubuntu.tar.gz
```
Step 2: Add the Toolchain to PATH

Append the following line to your shell configuration file:
```bash
echo 'export PATH=$PATH:$HOME/Downloads/opt/riscv/bin' >> ~/.bashrc
```

Then apply the change:
```bash
source ~/.bashrc
```
Step 3: Verify the Installation
Run the following commands to check that the RISC-V tools are installed correctly:
```bash
riscv32-unknown-elf-gcc --version
riscv32-unknown-linux-gnu-gcc --version
```
Output:
```bash
riscv32-unknown-elf-gcc (g04696df096) 14.2.0
Copyright (C) 2024 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

riscv32-unknown-linux-gnu-gcc (GCC) 14.2.0
Copyright (C) 2024 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```
![Screenshot 2025-06-06 190155](https://github.com/user-attachments/assets/6986b010-f405-4afc-9554-ae8c53be6cde)
![Screenshot 2025-06-06 190412](https://github.com/user-attachments/assets/e239d7f8-e346-41fa-b9fc-eec37a2d4ffd)





# Task 2: Compile “Hello, RISC-V” (Minimal C Program for RV32IMC)
This section demonstrates how to create a minimal bare-metal C "Hello, World!" program and cross-compile it for the RV32IMC architecture, producing an ELF binary.


 Step 1: Write the Minimal C Program
Create a file named hello.c with the following content:
```bash
nano ira.c
```

```c
#include <stdio.h>

int main() {
    printf("Hello, RISC-V\n");
    return 0;
}
```
Save and exit the editor (Ctrl + O, Enter, then Ctrl + X in nano)


Step 2: Compile using the RISC-V cross-compiler</b></summary>
Run this command in the terminal:

```bash

riscv32-unknown-elf-gcc -march=rv32imc -mabi=ilp32 -o hello.elf hello.c
```
 If you get an error like "cannot find suitable multilib", it means your toolchain doesn't support rv32imc. You can try replacing with:

```bash
riscv32-unknown-elf-gcc  -o ira.elf ira.c
```


Step 3: Verify the output ELF file</b>
Check the type of the file using:

```bash

file ira.elf
```
output: 
```bash
ira.elf: ELF 32-bit LSB executable, UCB RISC-V, RVC, soft-float ABI, version 1 (SYSV), statically linked, not stripped
```
![Screenshot 2025-06-06 193521](https://github.com/user-attachments/assets/ab9c6776-8b9a-4f1c-96f9-2d16fd50be39)

![Screenshot 2025-06-06 193649](https://github.com/user-attachments/assets/3b53acf6-6a93-4149-9470-f22ce1cdc5cc)



# Task 3: From C to Assembly (RV32IMC)

This task demonstrates how to generate the assembly (.s) file from a C program using the RISC-V toolchain for RV32IMC and explains key assembly prologue/epilogue instructions.

🛠️ Step 1: Generate Assembly (.s) File
Use the RISC-V GCC to compile the C file into an assembly file:
```bash
riscv32-unknown-elf-gcc -S -O0 ira.c
```
View the output using:

```bash

cat ira.s
```
you should see the output something as this:
![Screenshot 2025-06-06 194949](https://github.com/user-attachments/assets/45c8c270-9bc3-4e57-8c9b-8758e1363700)

# Task 4: Hex Dump & Disassembly
This task demonstrates how to convert an ELF file into a raw hex file and how to disassemble it using objdump. It also explains the meaning of each column in the disassembly output.
1. A human-readable disassembly (addresses + instructions).
2. A raw hexadecimal file (Intel HEX format) that can be used for flashing or simulation.

Step 1: Disassemble the ELF file

Use `objdump` to convert your ELF binary into readable RISC-V assembly with addresses:
```bash
riscv32-unknown-elf-objdump -d ira.elf > ira.dump
```
To view it:

```bash
cat ira.dump
```

output should be something like:

![Screenshot 2025-06-06 195752](https://github.com/user-attachments/assets/30b68721-483a-46ea-bad1-5d7bd724e5d0)

Step 2: Generate the raw hex format
  
Use objcopy to convert the ELF file into Intel HEX format:

```bash

riscv32-unknown-elf-objcopy -O ihex ira.elf ira.hex
```

To view the hex contents:

```bash
cat ira.hex
```

output was like:


![Screenshot 2025-06-06 200630](https://github.com/user-attachments/assets/406d93ec-de3f-43f1-9f7f-6c0a0ca4c37e)

### Explanation of each field:


000100b4 -	Address of the instruction

1141 -	Machine code opcode (in hex)

addi	Mnemonic - (assembly instruction)

sp, sp, -16	Operands - (registers and values)




{The address is where the instruction resides in memory.

The opcode is the raw machine code.

The mnemonic is the human-readable instruction.

The operands specify the registers and immediate values used.}


# Task 5 : RV32 Integer Registers and Calling Convention Roles

This section provides a complete list of the 32 integer registers in RV32I, along with their ABI names and conventional roles in the calling convention.

### RV32I Integer Register Table:
| Reg No | ABI Name | Role / Usage |
|--------|----------|--------------|
| x0     | zero     | Constant zero (hardwired, always 0) |
| x1     | ra       | Return address |
| x2     | sp       | Stack pointer |
| x3     | gp       | Global pointer |
| x4     | tp       | Thread pointer |
| x5     | t0       | Temporary register 0 |
| x6     | t1       | Temporary register 1 |
| x7     | t2       | Temporary register 2 |
| x8     | s0 / fp  | Saved register 0 / Frame pointer |
| x9     | s1       | Saved register 1 |
| x10    | a0       | Argument 0 / Return value |
| x11    | a1       | Argument 1 / Return value |
| x12    | a2       | Argument 2 |
| x13    | a3       | Argument 3 |
| x14    | a4       | Argument 4 |
| x15    | a5       | Argument 5 |
| x16    | a6       | Argument 6 |
| x17    | a7       | Argument 7 |
| x18    | s2       | Saved register 2 |
| x19    | s3       | Saved register 3 |
| x20    | s4       | Saved register 4 |
| x21    | s5       | Saved register 5 |
| x22    | s6       | Saved register 6 |
| x23    | s7       | Saved register 7 |
| x24    | s8       | Saved register 8 |
| x25    | s9       | Saved register 9 |
| x26    | s10      | Saved register 10 |
| x27    | s11      | Saved register 11 |
| x28    | t3       | Temporary register 3 |
| x29    | t4       | Temporary register 4 |
| x30    | t5       | Temporary register 5 |
| x31    | t6       | Temporary register 6 |

### Calling Convention Summary:
- **Caller-saved**: `a0–a7`, `t0–t6`. These registers may be overwritten by a called function. The caller must save them before the call if preservation is needed.
- **Callee-saved**: `s0–s11`. These registers must be preserved by the called function across calls.
- **Special-purpose**:
  - `x0` (zero): Always returns 0.
  - `x1` (ra): Stores the return address from a function call.
  - `x2` (sp): Points to the top of the stack.
  - `x8` (s0/fp): Often used as the frame pointer for stack frames.

# Task 6: Stepping with GDB
 Step 1: Start GDB with ELF
 ```bash
riscv32-unknown-elf-gdb ira.elf
```

Starts GDB with your program loaded.
 Step 2: Connect to Simulator

```bash
(gdb) target sim
```

Connects GDB to the built-in RISC-V simulator.
 Step 3: Set Breakpoint at main

```bash
(gdb) break main
```
Step 4: Run the Program

```bash
(gdb) run
```

Step 5: Step Through the Program
Step one machine instruction at a time:

```bash
(gdb) step
```

 Step 6: Inspect Registers
Inspect all general-purpose registers:

```bash
(gdb) info registers
```
then quit:
```bash
(gdb) quit
```

output that came:
![Screenshot 2025-06-06 211108](https://github.com/user-attachments/assets/1d059fb9-d805-4036-a1e3-e6b0b2f27cf5)
![Screenshot 2025-06-06 211122](https://github.com/user-attachments/assets/d22c4836-ce97-42bc-97ef-7f10a211c82e)
![Screenshot 2025-06-06 211138](https://github.com/user-attachments/assets/c004cae7-28b1-444f-9f09-25305216333f)
![Screenshot 2025-06-06 211148](https://github.com/user-attachments/assets/160592a8-c261-49be-8d59-a00b988c9ad6)

### I was not able to use run command inside gdb and asked the AI assistant about reasons possible for this : Because gdb is unable to combine printf command and was not able to comprehend the (include <stdio.h>) . So, I ran an alternative c file named sample.c as follows and that was able to follow run command:
![Screenshot 2025-06-07 212307](https://github.com/user-attachments/assets/5257cdbd-e47c-4f28-b7d5-d2f50e7e42ee)

![Screenshot 2025-06-06 222140](https://github.com/user-attachments/assets/5f5a34f9-9f90-4490-9c12-1c84308e5be2)
![Screenshot 2025-06-06 222341](https://github.com/user-attachments/assets/f85b6e29-f421-43d5-bb3d-655223e160be)
![Screenshot 2025-06-06 222412](https://github.com/user-attachments/assets/7877c5db-f75c-41b5-9b06-f4dc31cff1d0)
![Screenshot 2025-06-06 222437](https://github.com/user-attachments/assets/a553e546-45ce-481a-8d80-771b9f5adbbc)

# Task 7: Running Under an Emulator


Run a bare-metal RISC-V ELF program using an emulator (Spike or QEMU) and see output via UART console.  
This is essential if you do not have access to real hardware yet.

---

Step 1: Compile your bare-metal program with debug symbols

Use the following command to compile your C program (`hello.c`) and include debug info:

```bash
riscv64-linux-gnu-gcc -static -o hello.elf hello.c
```

Step 2: Run the ELF using QEMU
Use QEMU's RISC-V system emulator to run your ELF and get UART output:

```bash
qemu-riscv64 ./hello.elf


```


output :

![Screenshot 2025-06-11 220234](https://github.com/user-attachments/assets/60d43683-6344-49a7-b2a2-670bb9c6d2b9)
![Screenshot 2025-06-11 220324](https://github.com/user-attachments/assets/10c189d0-d977-4a2a-a123-95abe74a2e7f)


 # Task 8: Exploring GCC Optimisation

Question:  
“Compile the same file with -O0 vs -O2. What differences appear in the assembly and why?”

---

### Goal

To observe how GCC optimizations affect assembly output by comparing two compiler optimization levels:  
- `-O0` (no optimization)  
- `-O2` (aggressive optimizations)

 Step 1: Compile the Same File with Different Optimization Flags

Run these two commands to generate assembly .s files

```bash
riscv32-unknown-elf-gcc -O0 -S hello.c -o ira_O0.s
riscv32-unknown-elf-gcc -O2 -S hello.c -o ira_O2.s
```

 Step 2: Compare the Assembly Listings Side-by-Side

Command line diff side-by-side:

```bash
diff ira_O0.s ira_O2.s
```

![Screenshot 2025-06-06 235907](https://github.com/user-attachments/assets/6f0ded1e-d410-4f5e-93fd-c7fe258b435a)
![Screenshot 2025-06-06 235639](https://github.com/user-attachments/assets/0e57f619-af3c-4b66-b10e-83e4bdc5d27f)
![Screenshot 2025-06-06 235722](https://github.com/user-attachments/assets/ac94e9ed-23ad-460e-a746-3e1020ecd612)

Step 3: Key Differences to Look For and Why They Occur

| Optimization Aspect       | `-O0` Output                                | `-O2` Output                      | Explanation                                                    |
| ------------------------- | ------------------------------------------- | --------------------------------- | -------------------------------------------------------------- |
| **Dead Code Elimination** | Variables and code are kept even if unused  | Unused variables and code removed | Saves space and CPU time by eliminating redundant instructions |
| **Register Allocation**   | Heavy stack usage, more memory loads/stores | Registers used efficiently        | Reduces memory accesses, speeds up execution                   |
| **Function Inlining**     | Functions called explicitly                 | Small functions may be inlined    | Removes call overhead, enables further optimization            |
| **Instruction Count**     | More, straightforward instructions          | Fewer, optimized instructions     | Optimized code is smaller and faster                           |
| **Stack Frame Setup**     | Full prologue and epilogue                  | Simplified or omitted if possible | Avoids unnecessary setup/teardown for efficiency               |



# Task 9: Inline Assembly Basics

 Goal

  To write a function in C that returns the current value of the RISC-V cycle counter by accessing CSR register 0xC00 using inline assembly.

  ---

C Function Code
```c
__asm__ volatile (
    "add %[res], %[a], %[b]"
    : [res] "=r"(c)
    : [a] "r"(a), [b] "r"(b)
);
```
![Screenshot 2025-06-07 003754](https://github.com/user-attachments/assets/6ad05664-460b-4584-be50-35db94946fcf)
![Screenshot 2025-06-07 003205](https://github.com/user-attachments/assets/524fbfb3-4eb6-4f74-a3a2-28a6246d568e)
![Screenshot 2025-06-07 003218](https://github.com/user-attachments/assets/19e4efa3-9e59-4134-ba0c-c3ab86e2ba3e)


asm volatile ("csrr %0, cycle" : "=r"(c));
- asm volatile
   - asm: Embed assembly code.
   - volatile: Prevent compiler from optimizing this line away.
- "csrr %0, cycle"
   - csrr: Control and Status Register Read instruction.
   - cycle: Name for CSR 0xC00 (cycle counter).
   - %0: Placeholder for the first operand (here, the output variable c).
- : "=r"(c)
   - "=r": Output operand (= means write-only), stored in a general-purpose register (r).
   - (c): The C variable that receives the result.


# Task 10: Memory-Mapped I/O Demo

Problem Statement:

“Show a bare-metal C snippet to toggle a GPIO register located at 0x10012000. How do I prevent the compiler from optimizing the store away?”

 Explanation
   
- volatile:
  - Tells the compiler not to optimize access to the variable.
  - Required when accessing hardware registers or memory-mapped I/O.
  - Without it, compiler might skip the write because it sees no effect in program logic.

- (uint32_t*)0x10012000:
  - Casts the GPIO memory address to a pointer of type uint32_t*.
  - This assumes the hardware register is 4 bytes wide.

- *gpio = 0x1;:
  - Writes the value 0x1 to the register.
  - This could, for example, turn on a specific output pin.

- Alignment:
The address 0x10012000 is 4-byte aligned (divisible by 4), which is necessary for uint32_t access on many systems.


![image](https://github.com/user-attachments/assets/b15eb744-536f-4970-8008-b6b0a3fc172d)

# Task 11: Linker Script 101

Problem Statement
“Provide a minimal linker script that places .text at 0x00000000 and .data at 0x10000000 for RV32IMC.”

---
Minimal Linker ScriptLinker Script 101
![image](https://github.com/user-attachments/assets/d21b7e68-1b29-4405-b7b0-2033249b0aa3)
![Screenshot 2025-06-07 010539](https://github.com/user-attachments/assets/026d8154-abf6-4557-b4f5-b529216e305a)
![Screenshot 2025-06-07 012249](https://github.com/user-attachments/assets/1931b28f-6fe0-4da4-ac4c-1d876cf1477c)


Why Flash and SRAM Addresses Differ</strong></summary><br>
  
- Flash (e.g., 0x00000000):

  - Non-volatile memory.

  - Stores program code that must survive resets/power cycles.

- SRAM (e.g., 0x10000000):

  - Volatile memory used for runtime data.

  - Faster and writable, unlike Flash.

 During startup, boot code usually copies .data from Flash to SRAM and zeros .bss, so variables are correctly initialized.

 # Task 12:  Start-up Code & crt0
 *Question*

“What does crt0.S typically do in a bare-metal RISC-V program and where do I get one?”

---

What is `crt0.S`?
  
- crt0.S is the "C Runtime Zero" file — the very first code that runs before main() in a bare-metal program.

- It's written in assembly and is architecture-specific.

- It performs system-level initialization that C programs assume has already been done.


Key Responsibilities of `crt0.S`
  
- Set the Stack Pointer (sp)

  - Stack is essential for calling C functions.

  - crt0 sets sp to a known safe memory location (usually top of RAM).

- Zero out .bss section

  - .bss holds uninitialized global/static variables, which should start as 0.

  - crt0 clears this region with a loop.

- Copy .data from Flash to RAM

  - .data holds initialized globals.

  - crt0 copies them from ROM (Flash) to SRAM.

- Call main()

  - After setting everything up, crt0 branches to your main program.

  - Infinite Loop After main Returns

  - If main() returns, crt0 halts or spins forever to prevent undefined behavior.

Where to Get `crt0.S`

- Option 1: Write Your Own

  - Very short (usually ~30–50 lines of RISC-V assembly).

- Option 2: Use from a Runtime Library

- Newlib and libc implementations often include crt0.S.

- You can extract one from:

  - newlib source

  - SiFive’s freedom-e-sdk
 
  example of crt0.s:
  ![Screenshot 2025-06-07 012342](https://github.com/user-attachments/assets/50007613-de38-417a-bb39-db9e5e8f50b0)

  # Task 13: Interrupt Primer

## Question

**Demonstrate how to enable the machine-timer interrupt (MTIP) and write a simple handler in C/asm.**

## Answer Highlights

- Write to mtimecmp

- Set mie (enable machine timer interrupt)

- Set mstatus (enable global interrupt)

- Point mtvec to a valid handler

- Use __attribute__((interrupt)) in C

CODE FOR IT:
![Screenshot 2025-06-07 012729](https://github.com/user-attachments/assets/0e46be1c-3123-4078-ba50-c977e4e865a8)
![Screenshot 2025-06-07 012749](https://github.com/user-attachments/assets/03c35dd6-5e75-4fb8-bd4d-8e35cbe270c9)




# Task 14: rv32imac vs rv32imc – What’s the “A”?

### ❓ Question  
**“Explain the ‘A’ (atomic) extension in rv32imac. What instructions are added and why are they useful?”**

---

### What is the “A” Extension?

- The **“A” stands for Atomic** — it adds **atomic read-modify-write** instructions.
- These instructions allow for **safe, hardware-supported access to shared memory**.
- It is used when multiple cores/threads need to access the same variable without conflicts.

---

###  Instructions Introduced by the "A" Extension

| Instruction   | Meaning                             | Purpose                            |
|---------------|--------------------------------------|-------------------------------------|
| `lr.w`        | Load-Reserved word                   | Starts an atomic load-store block   |
| `sc.w`        | Store-Conditional word               | Stores only if no one else touched |
| `amoadd.w`    | Atomic add                           | Adds a value atomically             |
| `amoswap.w`   | Atomic swap                          | Atomically swaps memory             |
| `amoand.w`    | Atomic AND                           | Useful for bitmask flags            |
| `amoor.w`     | Atomic OR                            | Used in setting flags atomically    |

---

###  Use Cases

- **Operating Systems**: Manage shared memory between tasks/interrupts.
- **Multithreading**: Protect shared counters or variables.
- **Lock-Free Data Structures**: Like queues or stacks, to avoid performance penalties from locking.

---

###  Analogy

Imagine you're writing on a shared whiteboard:

- `lr.w` → You reserve a spot to write.  
- `sc.w` → You try to write, but **only succeed if no one else has written there** since you reserved it.  
- `amoadd.w` → You **add a number** to the current value, and it's done **safely**, even if others are trying at the same time.

---

### Why These Instructions Are Useful:
- **Synchronization**: Essential for implementing synchronization primitives like locks, mutexes, and semaphores in multi-core systems. For example, `lr.w/sc.w` can implement a spinlock.
- **Data Consistency**: Prevent race conditions when multiple harts access shared variables (e.g., counters, queues). `amoadd.w` can atomically increment a shared counter.
- **Efficiency**: AMOs perform complex operations in one instruction, reducing software overhead. LR/SC allows flexible atomic operations with minimal hardware complexity.
- **Scalability**: In multi-core SoCs, atomic instructions ensure scalable synchronization without relying on global bus locks, which degrade performance.
- **Use Cases**: Operating systems (thread scheduling, resource allocation), bare-metal (coordinating harts), atomic counters, lock-free data structures, interrupt-safe updates.


### Summary

- The “A” extension makes **safe concurrent memory access** possible in RISC-V.
- It is essential for low-level OS and embedded systems programming.
- It **distinguishes `rv32imac` (which supports atomics)** from `rv32imc` (which does not).

atomic code:
![Screenshot 2025-06-07 013106](https://github.com/user-attachments/assets/6c99d140-a805-4fed-9d57-aafaa6326ef3)

# Task 15: Atomic Test Program

Question:

“Provide a two-thread mutex example (pseudo-threads in main) using lr.w/sc.w on RV32.”

---

Method:
- Simulate two threads using functions in main.

- Use lr.w/sc.w instructions in inline assembly for spinlock mutex.

- Demonstrate protection of a shared variable (shared_counter) from race conditions.

![Screenshot 2025-06-07 013341](https://github.com/user-attachments/assets/9d90c1ff-01dc-4706-9822-dba205af431c)
![Screenshot 2025-06-07 013359](https://github.com/user-attachments/assets/c7a42f97-ffeb-4f60-93ea-bfc6a1582b0f)





Expected Output:

```vbnet
Shared counter: 3
```
Each function locks the variable, modifies it safely, and releases the lock.

---

### Concepts
- Spinlock: A busy-wait loop that tries acquiring a lock until it succeeds.

- lr.w / sc.w: RISC-V atomic pair that guarantees safe update without interference.

- Volatile: Prevents compiler from caching or optimizing memory accesses.

- Pseudo-threads: We simulate threads using plain functions to show locking logic.

# Task 16: Using Newlib `printf` Without an OS

This task shows how to **redirect `printf` output to UART** in a **bare-metal RISC-V** program by retargeting the `_write()` syscall.

---
- Implement `_write(int fd, char* buf, int len)` that loops over bytes to `UART_TX`.
- Re-link with `-nostartfiles` plus custom `syscalls.c`.
- Run in QEMU and verify UART output.


Important Note on UART Address

When running on QEMU with `-machine sifive_e`, the **UART memory-mapped I/O address is `0x10013000`**, not `0x10000000`. Using the wrong address will cause no output and program hang.

---

step 1: using the original ira.c file
Step 2: Create syscalls.c


![Screenshot 2025-06-07 013755](https://github.com/user-attachments/assets/688020b9-c22f-4bf3-be8f-ebdd51498c30)


![Screenshot 2025-06-07 013814](https://github.com/user-attachments/assets/8dcc4edc-e2cc-46a9-ab46-caec94116060)

step 3 : compile with 
```bash
riscv32-unknown-elf-gcc -nostartfiles -o ira.elf ira.c syscalls1.c
```

 Step 6: Run in QEMU

```bash
qemu-system-riscv32 -nographic -machine sifive_e -kernel ira.elf
```

# Task 17: Endianness Check (Little vs Big Endian)

### Determine the endianness of a RISC-V RV32 system using a union trick to inspect byte ordering
In little-endian systems (like RV32 by default), the least significant byte of a multi-byte value is stored at the **lowest** memory address.


main.c

```c
#include <stdio.h>
#include <stdint.h>

int main() {
    union {
        uint32_t value;
        uint8_t bytes[4];
    } test;

    test.value = 0x01020304;

    printf("Byte 0: 0x%02x\n", test.bytes[0]);
    printf("Byte 1: 0x%02x\n", test.bytes[1]);
    printf("Byte 2: 0x%02x\n", test.bytes[2]);
    printf("Byte 3: 0x%02x\n", test.bytes[3]);

    while (1);
    return 0;
}
```
 Compilation Command

```bash
riscv32-unknown-elf-gcc -Wall -O2 -ffreestanding -nostdlib \
  -mabi=ilp32 -march=rv32imac_zicsr \
  -o endian.elf main.c syscalls.c crt0.S -T link.ld -lc -lgcc
```
Run on QEMU

```bash
qemu-system-riscv32 -nographic -machine virt -bios none \
  -kernel endian.elf -serial mon:stdio
```

link.ld

```ld
ENTRY(_start)

SECTIONS {
  . = 0x80000000;

  .text : {
    *(.init)
    *(.text*)
  }

  .rodata : {
    *(.rodata*)
  }

  .data : {
    *(.data*)
  }

  .bss : {
    __bss_start = .;
    *(.bss*)
    *(COMMON)
    __bss_end = .;
    . = ALIGN(4);
    _stack_top = . + 0x1000;
    _end = .;
  }
}
```
crt0.S (Minimal Startup)

```asm
.section .init
.global _start
_start:
    la a0, __bss_start
    la a1, __bss_end
1:
    beq a0, a1, 2f
    sw zero, 0(a0)
    addi a0, a0, 4
    j 1b
2:
    call main
1:  j 1b
```
syscalls.c (Minimal Syscalls)

```c
#include <sys/stat.h>
#include <stdint.h>

extern int _end;
static char *heap_end = (char *)&_end;

caddr_t _sbrk(int incr) {
    char *prev_heap_end = heap_end;
    heap_end += incr;
    return (caddr_t)prev_heap_end;
}

int _write(int file, char *ptr, int len) {
    for (int i = 0; i < len; i++) {
        volatile char *uart = (char *)0x10000000;
        *uart = ptr[i];
    }
    return len;
}

int _read(int file, char *ptr, int len) { return 0; }
int _close(int file) { return -1; }
int _fstat(int file, struct stat *st) { return 0; }
int _lseek(int file, int ptr, int dir) { return 0; }
int _isatty(int file) { return 1; }
```

![Screenshot 2025-06-08 213003](https://github.com/user-attachments/assets/3f6d897a-99fb-4d06-921d-07a30f962079)
![Screenshot 2025-06-08 213434](https://github.com/user-attachments/assets/03ed210c-6f5e-43c5-b9e8-6d37ba0d18e6)
![Screenshot 2025-06-08 213631](https://github.com/user-attachments/assets/64592e4b-448f-47d7-92ee-f9cb5e5a03c6)
![Screenshot 2025-06-08 213809](https://github.com/user-attachments/assets/0568ee79-082d-4b21-82c7-3a3e6a2d6a57)

![Screenshot 2025-06-08 221727](https://github.com/user-attachments/assets/f22e23a5-68ae-4614-8504-1aac09553159)

### My file got compiled but qemu is unable to show the output as itS cursor infinitely got stuck and could be resolved by ctrl A + X

## 📘 What is RISC-V?

* RISC-V is an **open Instruction Set Architecture (ISA)** — a blueprint that defines the instructions a processor can execute.
* ❌ It is **not** a processor, Verilog code, or a physical chip.
* ✅ It **can** be implemented in hardware using languages like Verilog.
* This decoupling is critical — like a programming language is separate from its compiler, RISC-V is separate from the CPU that implements it.
## 📅 Why We Did These Tasks


The weekly tasks were designed to build our understanding of embedded systems using RISC-V from the ground up. Each step removed layers of abstraction that modern operating systems usually hide from us.

| Task | Concept |
|------|---------|
| **Toolchain Setup** | We learned how to compile C code into RISC-V ELF binaries using a cross-compiler. |
| **Disassembly & `info reg`** | We analyzed how our C code translates into assembly and how CPU registers change at runtime. |
| **Memory-Mapped I/O** | We saw how to control hardware directly by writing to specific memory addresses. |
| **Linker Scripts** | We controlled where code and data sections are placed in memory, crucial for bare-metal systems. |
| **Start-up Code (`crt0.S`)** | We understood what happens before `main()` runs: setting the stack, zeroing `.bss`, and transferring control. |
| **Interrupts** | We configured and handled timer interrupts, learning how CPUs respond to asynchronous events. |
| **Atomic Operations & Endianness** | We explored concurrency control using `lr/sc` and verified byte ordering in memory. |
| **Retargeting `printf` via UART** | We enabled output without an OS by writing our own `_write` function to communicate with a memory-mapped UART. |






 



















 




















 














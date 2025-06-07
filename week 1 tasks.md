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
Then apply the change:
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









# Compilation Process

Developers write code in high level languages such as C or C++

These cannot be directly executed on the CPU because CPU understands only machine instructions

A compiler translates this program code, which is human readable text, into another form of human readable text known as assembly code.

Assembler converts this assembly code into object code

Finally linker, combines different object code along with dependencies to get a binary file

The binary file has instructions that CPU can understand and execute

Binary format can adopt to any format that defines the structure it should follow.

On Linux, the format for executable is ELF


1. Pre-Processing
2. Compiling (Assembly File Creation)
3. Assembler (Object File Creation)
4. Linking (Links all object files to create an executable)

$ gcc userspace.c -o userspace

or
$  gcc -E userspace.c -o userspace.i

$ /usr/lib/gcc/x86_64-linux-gnu/9/cc1 userspace.i

$ as userspace.s -o userspace.o

$ ld -dynamic-linker /lib64/ld-linux-x86-64.so.2 -o userspace userspace.o /lib/x86_64-linux-gnu/crt1.o /lib/x86_64-linux-gnu/crti.o /lib/x86_64-linux-gnu/crtn.o /usr/lib/x86_64-linux-gnu/libc.so

ld = Link Editor

$ file ./userspace.o

$ file ./userspace

Using objdump we can display the disassembled code of the .o file, showing both the hexadecimal machine code and the corresponding assembly instructions

$ objdump -d -M intel userspace.o

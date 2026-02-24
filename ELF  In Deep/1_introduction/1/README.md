# ELF
ELF Stands for Executable and Linkable Format

## It's a format for

### Binaries
  These are the files which are the output of the linker and can be used by exec program to create a memory image for the process.
### Libraries
  These are the .so files which are used by dynamic linkers at runtime to form a runable process.
### Core dumps
  Core dumps are created when a program terminated with a fault
### Object Code:
  These are the files which end up with .o extension and are primarily a target of the Linker Program to be linked into the executable
### Kernel Module
  This file is loaded by kernel to create a process 

## ELF format 
is versatile, its design allows it to be executed on various processor types.

### It supports
  Big Endian and Little Edian
  32-bit and 64-bit Architecture Systems

### It also supports features such as dynamic linking
  Which allows shared libraries to be loaded and linked at runtime position-independent code,
  which enables executables and libraries to be loaded at different memory addresses

### Filename extension
Specification does not clarify the filename extension for ELF files.
In use is a variety of letter combinations, such as .axf, .bin, .elf, .o, .prx, .puff, .ko, .so, and .mod, or none.

$ man 5 elf

What about other OS?
-----------------------
However on macOS the de-facto format is Mach-O instead.
On Windows, .exe files uses the Portable Executable format 

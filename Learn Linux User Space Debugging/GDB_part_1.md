# Comprehensive GDB Guide

A complete guide to using the GNU Debugger, synthesized from notes and examples.

[[_TOC_]]

---

## Introduction

What is GDB?
================

GDB stands for GNU DeBugger which helps you to debug your binary object file created in compilation process.

GDB allows to you see what is happening in your program which really helps much when program crashes, especially when segmentation fault occurs

Some uses of gdb:
==================
	Step through a program line by line

	Set breakpoints that will stop your program

	Make your program stop on specified conditions

	Show the present values of variables

	Examine the contents of any frame on the call stack


Languages supported by GDB 
===========================

Ada, Assembly, C, C++, D, Fortran, Go, Objective-C, OpenCL, Modula-2, Pascal, Rust 


Install gdb
==============

$ sudo apt-get install gdb

$ gdb --version

---

## Part 1: How To Use

### Theory: How To Use

How to compile a program to use with gdb
========================================

$ gcc -g -o binaryfile source_code.c

-g tells the compiler to store symbol table information in the executable.

this includes:

	--> symbol names
	--> type info for symbols
	--> files and line numbers where the symbols came from

Tip: Check the size of binary with and without debugging symbols

$ gdb ./binaryfile

or

$ gdb

(gdb) file path_to_binary

GDB is a command line interface.

This means you will be provided with a prompt at which you can type commands.

The GDB commandline looks like this:

(gdb)

Basic Commands
=================

This starts the program which you want to debug. 
(gdb)run

To exit GDB, use the quit command (abbreviated q) or type an end-of-file character (usually Ctrl-d).

(gdb)quit

The "-q" (or "--quiet") option on the command line just tells GDB not to print version information on startup. 
(gdb)quiet


Help
====================

If you’re ever confused about a command or just want more information, use the “help” command, with or without an argument:

(gdb) help [command]

### Source Code Examples

<details>
<summary><strong>Click to expand <code>source_code.c</code></strong></summary>

```c
#include <stdio.h>

unsigned int factorial(unsigned int n)
{
    if (n == 0)  
		return 1;
    return n * factorial(n-1);
}

int main(int argc, char* argv[])
{
	unsigned int loop = 50;
	while(loop--) {
    printf("Factorial of number %d is %u\n", 
            loop, 
            factorial(loop));
	}
    
	return 0;
}
```

</details>

---

## Part 2: Breakpoints

### Theory: Breakpoints

The whole purpose of coming to gdb was to pause, observe and proceed. 

There is no point in running a program without a breakpoint! 

Breakpoints
=================

Breakpoints can be used to stop the program run in the middle, at a designated point.

Whenever gdb gets to a breakpoint it halts execution of your program and allows you to examine it

Simplest way of putting a breakpoint is using the function name or a line number.

(gdb) break factorial 
Breakpoint 1 at 0x400538: file source_code.c, line 5.

(gdb) break 7
Breakpoint 2 at 0x400545: file source_code.c, line 7.

List of functions available
========================================

(gdb) info functions 

List of breakpoints available
================================

(gdb) info breakpoints 

Run the program and it will stop at the first breakpoint

(gdb) r

Delete Breakpoints
==========================

(gdb) delete <bpnumber>

Disable/Enable Breakpoints
===========================

(gdb) disable <bpnumber>

(gdb) enable <bpnumber>

Step by step
==============

Once you have hit a breakpoint, you can have fine control over the execution of the program, using the following commands

(gdb) continue

Continue execution till the next break point or end of program

Typing 'run' again instead of 'continue' would restart the program from the beginning.

(gdb) next

Proceed to the next line of execution (Doesn’t step into a function call in the current line)

(gdb) step

The same as next, but with difference that if you are at a function call
	next -  the function will execute and return
	step - step into the first line of the called function.

(gdb) finish
	Finish executing the current function, then pause (also called step out). Useful if you accidentally stepped into a function.


(gdb) print n

The print command prints the value of the variable specified

(gdb) print/x n
Prints the value in hexadecimal

Tip
========
Typing “step” or “next” a lot of times can be tedious. If you just press ENTER, gdb will repeat the same command you just gave it.

### Source Code Examples

<details>
<summary><strong>Click to expand <code>source_code.c</code></strong></summary>

```c
#include <stdio.h>

unsigned int factorial(unsigned int n)
{
    if (n == 0)  
		return 1;
    return n * factorial(n-1);
}

int main(int argc, char* argv[])
{
	unsigned int loop = 50;
	while(loop--) {
    printf("Factorial of number %d is %u\n", 
            loop, 
            factorial(loop));
	}
    
	return 0;
}
```

</details>

<details>
<summary><strong>Click to expand <code>step_vs_next.c</code></strong></summary>

```c
#include <stdio.h>


void printMessage(char *msg) {
	if (msg != NULL)
		printf("msg:%s\n", msg);
}


int main(){
	
	char buffer[] = "Hello World";
	int i = 10;

	printMessage(buffer);
	return 0;
}
```

</details>

---

## Part 3: Pass Args

### Theory: Pass Args

Different ways to pass command line arguments
==============================================

You can run gdb with --args parameter,

$ gdb --args executablename arg1 arg2 arg3

========================

$ gdb ./a.out

(gdb) r arg1 arg2 arg3

=======================

### Source Code Examples

<details>
<summary><strong>Click to expand <code>source_code.c</code></strong></summary>

```c
#include <stdio.h>

unsigned int factorial(unsigned int n)
{
    if (n == 0)  
		return 1;
    return n * factorial(n-1);
}

int main(int argc, char* argv[])
{
	unsigned int loop = 50;
	if (argc == 2) {
		loop = atoi(argv[1]);
	} 
	while(loop--) {
    printf("Factorial of number %d is %u\n", 
            loop, 
            factorial(loop));
	}
    
	return 0;
}
```

</details>

---

## Part 4: Debugging Segfault

### Theory: Debugging Segfault

Debugging Segmentation Fault Example
=====================================

The first step is to compile the program with debugging flags:

$ gcc -g source_code.c

$ gdb ./a.out

We'll just run it and see what happens:

(gdb) run
Starting program: /home/panther2/Linux_System_Prog/day3_debugging/gdb/4/a.out 
hello

Program received signal SIGSEGV, Segmentation fault.
__GI__IO_getline_info (fp=fp@entry=0x7ffff7dd4640 <_IO_2_1_stdin_>, buf=buf@entry=0x0, n=1022, delim=delim@entry=10, 
    extract_delim=extract_delim@entry=1, eof=eof@entry=0x0) at iogetline.c:86
86	iogetline.c: No such file or directory.


So we received the SIGSEGV signal from the operating system. This means that we tried to access an invalid memory address

(gdb) backtrace
The command backtrace (or bt) will show you the current function call stack, with the current function at the top, and the callers in order beneath it:


(gdb) bt
#0  __GI__IO_getline_info (fp=fp@entry=0x7ffff7dd4640 <_IO_2_1_stdin_>, buf=buf@entry=0x0, n=1022, delim=delim@entry=10, 
    extract_delim=extract_delim@entry=1, eof=eof@entry=0x0) at iogetline.c:86
#1  0x00007ffff7a80368 in __GI__IO_getline (fp=fp@entry=0x7ffff7dd4640 <_IO_2_1_stdin_>, buf=buf@entry=0x0, n=<optimized out>, 
    delim=delim@entry=10, extract_delim=extract_delim@entry=1) at iogetline.c:38
#2  0x00007ffff7a7f206 in _IO_fgets (buf=0x0, n=<optimized out>, fp=0x7ffff7dd4640 <_IO_2_1_stdin_>) at iofgets.c:56
#3  0x0000000000400634 in main (argc=1, argv=0x7fffffffdfb8) at source_code.c:10

We are only interested in our own code here, so we want to switch to stack frame 3 and see where the program crashed:

(gdb) frame 3
#3  0x0000000000400634 in main (argc=1, argv=0x7fffffffdfb8) at source_code.c:10
10		fgets(buf, 1024, stdin);

We crashed inside the call to fgets.
So the problem must be one of our arguments

(gdb) print buf
$1 = 0x0

The value of buf is 0x0, which is the NULL pointer. 
malloc returns NULL when it cannot allocate the amount of memory requested. So our malloc must have failed. 




=======================
Print Source Code In GDB Console
======================

(gdb) list
prints 10 lines of source code at a time

You can also pass the list command <a line number> or <a function name> to tell GDB where to start.

Display Lines After A Line Number
(gdb) list 12

Display Lines After A Function
(gdb) list main

Display Lines 1 to 14
(gdb) list 1,14

### Source Code Examples

<details>
<summary><strong>Click to expand <code>source_code.c</code></strong></summary>

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char **argv)
{
	char *buf;

	buf = malloc(1<<31);

	fgets(buf, 1024, stdin);
	printf("%s\n", buf);

	return 1;
}
```

</details>

---

## Part 5: 1 Notes

### Theory: 1 Notes

Displaying Data
======================

You can use print command to display the value of variables and other expressions

The print or p command takes any C expression as its argument:

p [/format] [expression]

(gdb) print i

(gdb) print &i

(gdb) print sizeof(i)

(gdb) print sizeof(&i)


Gdb comes with a powerful tool for directly examing memory: the x command. 

The x command examines memory, starting at a particular address. 

(gdb) x &i
0x7fffffffdedc:	0x00000539

It comes with a number of formatting commands that provide precise control over 
	how many bytes you’d like to examine and 
	how you’d like to print them

(gdb) x/4xb &i
0x7fffffffdedc:	0x39	0x05	0x00	0x00

The flags indicate that I want to examine 4 values
	formatted as he'x' numerals, one 'b'yte at a time

on Intel machines, bytes are stored in “little-endian” order

Examining types with ptype
============================

It tells you the type of a C expression

(gdb) ptype i
type = int

(gdb) ptype &i
type = int *

(gdb) ptype main
type = int ()

### Theory: 2 Notes

(gdb) print a
$2 = {1, 2, 3}

(gdb) ptype a
type = int [3]

use x to see what a looks like under the hood

(gdb) x/12xb &a
0x7fffffffded0:	0x01	0x00	0x00	0x00	0x02	0x00	0x00	0x00
0x7fffffffded8:	0x03	0x00	0x00	0x00

The first four bytes store a[0], the next four store a[1], and the final four store a[2]. 

Indeed, you can check that sizeof knows that a’s size in memory is twelve bytes:

(gdb) print sizeof(a)
$3 = 12

### Source Code Examples

<details>
<summary><strong>Click to expand <code>1.c</code></strong></summary>

```c
int main()
{
  int i = 1337;
  return 0;
}
```

</details>

<details>
<summary><strong>Click to expand <code>2.c</code></strong></summary>

```c
int main()
{
    int a[] = {1,2,3};
    return 0;
}
```

</details>

<details>
<summary><strong>Click to expand <code>3.c</code></strong></summary>

```c
/*
	Printing string using x
	p &msg
	x/s address
*/

#include <stdio.h>

int main()
{
	char msg[] = "Hello World";

	printf("Message:%s\n", msg);
	return 0;
}
```

</details>

---

## Part 6: Notes

### Theory: Notes

Frames
==================

A running application maintains a call stack that contains information about its functions that have been called.

Each item in the stack is a call frame, and each frame contains both the information needed to return to its caller and the information needed to provide the local variables of the function.

When your program starts, the call stack has only one frame, that of the function main. 

Each function call pushes a new frame onto the stack, and each function return removes the frame for that function from the stack.

Recursive functions can generate many frames.

Use backtrace.c for the below commands

$ gcc backtrace.c -o backtrace -g

$ gdb ./backtrace

(gdb) b main

(gdb) run

(gdb) bt

(gdb) b func1

(gdb) bt

Moving from one frame to another
================================

You can move between the stack frames using ‘frame [number]’


Get Information about a Stack Frame
=====================================

You can get the information about a particular frame using ‘info frame [number]’ 

(gdb) info frame  1   # list information about the current stack frame

(gdb) info variables # list all global and static variables 

(gdb) info locals   # list local variable values of current stack frame

(gdb) info args     # list argument values of current stack frame


(gdb) info registers        # list register values

(gdb) info registers eax    # shows just the register eax


(gdb) info breakpoints      # list status of all breakpoints

### Theory: Processor Registers

Using gdb to View the CPU Registers
===================================

The i r command displays the current contents of the CPU registers. 

 The first column is the name of the register

The second shows the current bit pattern in the register, in hexadecimal.

The third column shows some the register contents in 32-bit/64-bit unsigned decimal.

### Source Code Examples

<details>
<summary><strong>Click to expand <code>backtrace.c</code></strong></summary>

```c
#include<stdio.h>	 	 
void func1();	 	 
void func2();	 	 

int main() 
{	 	 
	int i=10;	 	 
	func1();	 	 
	printf("In Main(): %d\n",i);	 	 
}	 	 

void func1() 
{	 	 
	int n=20;	 	 
	printf("In func1(): %d\n",n);	 	 
	func2();	 	 
}	 	 

void func2() 
{	 	 
	int n = 30;	 	 
	printf("In func2() : %d\n",n);	 	 
}
```

</details>

<details>
<summary><strong>Click to expand <code>example1.c</code></strong></summary>

```c
#include <stdio.h>

int main(void)
{
  register int wye;
  int *ptr;
  int ex;

  ptr = &ex;
  ex = 305441741;
  wye = -1;
  printf("Enter an integer: ");
  scanf("%i", ptr);
  wye += *ptr;
  printf("The result is %i\n", wye);

  return 0;
}
```

</details>

<details>
<summary><strong>Click to expand <code>source_code.c</code></strong></summary>

```c
#include <stdio.h>

int global = 10;

unsigned int factorial(unsigned int n)
{
    if (n == 0)  
		return 1;
    return n * factorial(n-1);
}

int main(int argc, char* argv[])
{
	static int i = 10;
	unsigned int loop = 50;
	while(loop--) {
    printf("Factorial of number %d is %u\n", 
            loop, 
            factorial(loop));
	}
    
	return 0;
}
```

</details>

---

## Part 7: Conditional Breakpoints

### Theory: Conditional Breakpoints

Conditional Breakpoints
=========================

As long as a breakpoint is enabled, the debugger always stops at that breakpoint.

However, sometimes it's useful to tell the debugger to stop at a break point only if some condition is met, like the when a variable has a particularly interesting value.


You can specify a break condition when you set a breakpoint by appending the keyword if to a normal break statement

break [position] if expression

In the above syntax position can be a function name or line number.

If you already set a breakpoint at the desired position, you can use the condition command to add or change its break condition:

condition bp_number [expression]

### Source Code Examples

<details>
<summary><strong>Click to expand <code>source_code.c</code></strong></summary>

```c
/*
	you want to break in myFunction, but only the 4th time you enter that function. 
	steps:
	1. Set a breakpoint to myFunction
	2. condition <num> sFunctionCounter==4
*/

#include <stdio.h>

void myFunction()
{
   // Initialize the counter to 0
   static int sFunctionCounter = 0;
 
   // Each time we enter the function,
   // increment the counter
   sFunctionCounter++;
 
}
 
int main (int argc, const char * argv[])
{
   int i;
 
   // Call 10 times myFunction.
   for(i = 0 ; i < 10 ; i++)
        myFunction();
 
   return 0;
}
```

</details>

<details>
<summary><strong>Click to expand <code>source_code1.c</code></strong></summary>

```c
/*
	 break when the value of i is 500
	 (gdb) b line number if i==500
*/
#include <stdio.h>
int main() 
{
        int i=0;
        for (i=0;i<1000;i++) 
		{
                printf("%d\n",i);
        }
}
```

</details>

---

## Part 8: Watchpoints

### Theory: Watchpoints

Watchpoints are similar to breakpoints

Watchpoints are set on variables. 

When those variables are read or written, the watchpoint is triggered and program execution stops.

How do I set a write watchpoint for a variable?
===============================================

Use the watch command.

The argument to the watch command is an expression that is evaluated. 

 This implies that the variabel you want to set a watchpoint on must be in the current scope.

So, to set a watchpoint on a non-global variable, you must have set a breakpoint that will stop your program when the variable is in scope. 

You set the watchpoint after the program breaks.


(gdb) watch x

How do I set a read watchpoint for a variable?
===============================================

Use the rwatch command. Usage is identical to the watch command.


(gdb) rwatch y 

How do I set a read/write watchpoint for a variable?
=====================================================

Use the awatch command. Usage is identical to the watch command.

How do I disable watchpoints?
=============================

Active watchpoints show up the breakpoint list. Use the info breakpoints command to get this list. Then use the disable command to turn off a watchpoint, just like disabling a breakpoint.

### Source Code Examples

<details>
<summary><strong>Click to expand <code>source_code.c</code></strong></summary>

```c
#include <stdio.h>

int main(int argc, char **argv)
{
  int x = 30;
  int y = 10;

  x = y;

  return 0;
}
```

</details>

---

## Part 9: Notes Tui

### Theory: Notes Tui

gdb Text User Interface (TUI)
=============================

The gdb Text User Interface (TUI) is a terminal interface which uses the curses library to show the
	
	 source file
	 assembly output
	 program registers
	 and GDB Commands

in separate text windows

The TUI mode is supported only on platforms where a suitable version of the curses library is available.

The TUI mode is enabled by default when you invoke gdb as either ‘gdbtui’ or ‘gdb -tui’.

You can also switch in and out of TUI mode while gdb runs by using various TUI commands and key bindings, such as Ctrl-x a
	
Commands
=============

Ctrl - l -- to repaint the screen //when there are printf's displayed

You can type commands on the command line like usual, but the arrow keys will scroll the source code view instead of paging through history or navigating the entered command.

To switch focus to the command line view, type ctrl-x o and the arrow keys works as in the normal command line mode. 

Switching back to the source code view is done using the same key combination a second time.

### Source Code Examples

<details>
<summary><strong>Click to expand <code>source_code.c</code></strong></summary>

```c
#include <stdio.h>

unsigned int factorial(unsigned int n)
{
    if (n == 0)  
		return 1;
    return n * factorial(n-1);
}

int main(int argc, char* argv[])
{
	unsigned int loop = 50;
	while(loop--) {
    printf("Factorial of number %d is %u\n", 
            loop, 
            factorial(loop));
	}
    
	return 0;
}
```

</details>

---

## Part 10: Gdb

### Theory: Gdb

Starting program: /home/panther2/Linux_System_Prog/day3_debugging/gdb/10/source_code.c 

Breakpoint 1, main (argc=1, argv=0x7fffffffdfa8) at source_code.c:12
Line number 12 out of range; source_code.c has 7 lines.
1	^?ELF^B^A^A^@^@^@^@^@^@^@^@^@^B^@>^@^A^@^@^@@^D@^@^@^@^@^@@^@^@^@^@^@^@^@È^T^@^@^@^@^@^@^@^@^@^@@^@8^@	^@@^@#^@ ^@^F^@^@^@^E^@^@^@@^@^@^@^@^@^@^@@^@@^@^@^@^@^@@^@@^@^@^@^@^@ø^A^@^@^@^@^@^@ø^A^@^@^@^@^@^@^H^@^@^@^@^@^@^@^C^@^@^@^D^@^@^@8^B^@^@^@^@^@^@8^B@^@^@^@^@^@8^B@^@^@^@^@^@^\^@^@^@^@^@^@^@^\^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^A^@^@^@^E^@^@^@^@^@^@^@^@^@^@^@^@^@@^@^@^@^@^@^@^@@^@^@^@^@^@¤^G^@^@^@^@^@^@¤^G^@^@^@^@^@^@^@^@ ^@^@^@^@^@^A^@^@^@^F^@^@^@^P^N^@^@^@^@^@^@^P^N`^@^@^@^@^@^P^N`^@^@^@^@^@0^B^@^@^@^@^@^@8^B^@^@^@^@^@^@^@^@ ^@^@^@^@^@^B^@^@^@^F^@^@^@(^N^@^@^@^@^@^@(^N`^@^@^@^@^@(^N`^@^@^@^@^@Ð^A^@^@^@^@^@^@Ð^A^@^@^@^@^@^@^H^@^@^@^@^@^@^@^D^@^@^@^D^@^@^@T^B^@^@^@^@^@^@T^B@^@^@^@^@^@T^B@^@^@^@^@^@D^@^@^@^@^@^@^@D^@^@^@^@^@^@^@^D^@^@^@^@^@^@^@Påtd^D^@^@^@T^F^@^@^@^@^@^@T^F@^@^@^@^@^@T^F@^@^@^@^@^@<^@^@^@^@^@^@^@<^@^@^@^@^@^@^@^D^@^@^@^@^@^@^@Qåtd^F^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^P^@^@^@^@^@^@^@Råtd^D^@^@^@^P^N^@^@^@^@^@^@^P^N`^@^@^@^@^@^P^N`^@^@^@^@^@ð^A^@^@^@^@^@^@ð^A^@^@^@^@^@^@^A^@^@^@^@^@^@^@/lib64/ld-linux-x86-64.so.2^@^D^@^@^@^P^@^@^@^A^@^@^@GNU^@^@^@^@^@^B^@^@^@^F^@^@^@^X^@^@^@^D^@^@^@^T^@^@^@^C^@^@^@GNU^@Ü¹ó·C üäDÆc¦^FÔ^Aq0^L^A^@^@^@^A^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^K^@^@^@^R^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^R^@^@^@^R^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@$^@^@^@ ^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@libc.so.6^@printf^@__libc_start_main^@__gmon_start__^@GLIBC_2.2.5^@^@^@^@^B^@^B^@^@^@^A^@^A^@^A^@^@^@^P^@^@^@^@^@^@^@u^Zi	^@^@^B^@3^@^@^@^@^@^@^@ø^O`^@^@^@^@^@^F^@^@^@^C^@^@^@^@^@^@^@^@^@^@^@^X^P`^@^@^@^@^@^G^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@ ^P`^@^@^@^@^@^G^@^@^@^B^@^@^@^@^@^@^@^@^@^@^@(^P`^@^@^@^@^@^G^@^@^@^C^@^@^@^@^@^@^@^@^@^@^@Hì^HH^E^M^L ^@HÀt^Eè;^@^@^@HÄ^HÃ^@^@^@^@^@^@ÿ5^B^L ^@ÿ%^D^L ^@^O^_@^@ÿ%^B^L ^@h^@^@^@^@éàÿÿÿÿ%ú^K ^@h^A^@^@^@éÐÿÿÿÿ%ò^K ^@h^B^@^@^@éÀÿÿÿ1íIÑ^HâHäðPTIÇÀ ^F@^@HÇÁ°^E@^@HÇÇX^E@^@è·ÿÿÿôf^O^_D^@^@¸G^P`^@UH-@^P`^@Hø^NHåw^B]Ã¸^@^@^@^@HÀtô]¿@^P`^@ÿà^O^_^@^@^@^@¸@^P`^@UH-@^P`^@HÁø^CHåHÂHÁê?H^AÐHÑøu^B]Ãº^@^@^@^@HÒtô]HÆ¿@^P`^@ÿâ^O^_^@^@^@^@=Y^K ^@^@u^QUHåè~ÿÿÿ]Æ^EF^K ^@^AóÃ^O^_@^@H=^X	 ^@^@t^^¸^@^@^@^@HÀt^TU¿ ^N`^@HåÿÐ]é{ÿÿÿ^O^_^@ésÿÿÿUHåHì^P}ü}ü^@u^G¸^A^@^@^@ë^QEüè^AÇèÛÿÿÿ^O¯EüÉÃUHåHì }ìHuàÇEü2^@^@^@ë EüÇè³ÿÿÿÂEüÆ¿4^F@^@¸^@^@^@^@èþÿÿEüPÿUüÀuÓ¸^@^@^@^@ÉÃf.^O^_^@^@^@^@^@fAWAÿAVIöAUIÕATL%H^H ^@UH-H^H ^@SL)å1ÛHÁý^CHì^HèýýÿÿHít^^^O^_^@^@^@^@^@LêLöDÿAÿ^TÜHÃ^AH9ëuêHÄ^H[]A\A]A^A_Ãff.^O^_^@^@^@^@^@óÃ^@^@Hì^HHÄ^HÃ^@^@^@^A^@^B^@Factorial of number %d is %u
2	^@^@^@^A^[^C;8^@^@^@^F^@^@^@¬ýÿÿ^@^@^@ìýÿÿT^@^@^@Ùþÿÿ¬^@^@^@^DÿÿÿÌ^@^@^@\ÿÿÿì^@^@^@Ìÿÿÿ4^A^@^@^T^@^@^@^@^@^@^@^AzR^@^Ax^P^A^[^L^G^H^A^G^P^T^@^@^@^\^@^@^@ýÿÿ*^@^@^@^@^@^@^@^@^@^@^@^T^@^@^@^@^@^@^@^AzR^@^Ax^P^A^[^L^G^H^A^@^@$^@^@^@^\^@^@^@ ýÿÿ@^@^@^@^@^N^PF^N^XJ^O^Kw^H^@?^Z;*3$"^@^@^@^@^\^@^@^@D^@^@^@%þÿÿ+^@^@^@^@A^N^P^BC^M^Ff^L^G^H^@^@^@^\^@^@^@d^@^@^@0þÿÿL^@^@^@^@A^N^P^BC^M^F^BG^L^G^H^@^@D^@^@^@^@^@^@hþÿÿe^@^@^@^@B^N^P^BE^N^X^CE^N ^DE^N(^EH^N0^FH^N8^GM^N@l^N8A^N0A^N(B^N B^N^XB^N^PB^N^H^@^T^@^@^@Ì^@^@^@þÿÿ^B^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^E@^@^@^@^@^@à^D@^@^@^@^@^@^@^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^L^@^@^@^@^@^@^@à^C@^@^@^@^@^@^M^@^@^@^@^@^@^@$^F@^@^@^@^@^@^Y^@^@^@^@^@^@^@^P^N`^@^@^@^@^@^[^@^@^@^@^@^@^@^H^@^@^@^@^@^@^@^Z^@^@^@^@^@^@^@^X^N`^@^@^@^@^@^\^@^@^@^@^@^@^@^H^@^@^@^@^@^@^@õþÿo^@^@^@^@^B@^@^@^@^@^@^E^@^@^@^@^@^@^@^X^C@^@^@^@^@^@^F^@^@^@^@^@^@^@¸^B@^@^@^@^@^@
3	^@^@^@^@^@^@^@?^@^@^@^@^@^@^@^K^@^@^@^@^@^@^@^X^@^@^@^@^@^@^@^U^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^C^@^@^@^@^@^@^@^@^P`^@^@^@^@^@^B^@^@^@^@^@^@^@H^@^@^@^@^@^@^@^T^@^@^@^@^@^@^@^G^@^@^@^@^@^@^@^W^@^@^@^@^@^@^@^C@^@^@^@^@^@^G^@^@^@^@^@^@^@^C@^@^@^@^@^@^H^@^@^@^@^@^@^@^X^@^@^@^@^@^@^@	^@^@^@^@^@^@^@^X^@^@^@^@^@^@^@þÿÿo^@^@^@^@`^C@^@^@^@^@^@ÿÿÿo^@^@^@^@^A^@^@^@^@^@^@^@ðÿÿo^@^@^@^@X^C@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@(^N`^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^V^D@^@^@^@^@^@&^D@^@^@^@^@^@6^D@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@GCC: (Ubuntu 4.8.4-2ubuntu1~14.04.4) 4.8.4^@GCC: (Ubuntu 4.8.4-2ubuntu1~14.04.3) 4.8.4^@,^@^@^@^B^@^@^@^@^@^H^@^@^@^@^@-^E@^@^@^@^@^@w^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@ö^@^@^@^D^@^@^@^@^@^H^A^@^@^@^@^Av^@^@^@^@^@^@-^E@^@^@^@^@^@w^@^@^@^@^@^@^@^@^@^@^@^B^H^G>^@^@^@^B^A^HP^@^@^@^B^B^GÀ^@^@^@^B^D^GC^@^@^@^B^A^FR^@^@^@^B^B^EØ^@^@^@^C^D^Eint^@^B^H^Ec^@^@^@^B^H^Gâ^@^@^@^D^Hr^@^@^@^B^A^FY^@^@^@^El^@^@^@^A^CB^@^@^@-^E@^@^@^@^@^@+^@^@^@^@^@^@^@^A§^@^@^@^Fn^@^A^CB^@^@^@^Bl^@^E^^@^@^@^A
4	W^@^@^@X^E@^@^@^@^@^@L^@^@^@^@^@^@^@^Aó^@^@^@^G»^@^@^@^A
5	W^@^@^@^B\^Gë^@^@^@^A
6	ó^@^@^@^BP^HÓ^@^@^@^A^LB^@^@^@^Bl^@^D^Hl^@^@^@^@^A^Q^A%^N^S^K^C^N^[^N^Q^A^R^G^P^W^@^@^B$^@^K^K>^K^C^N^@^@^C$^@^K^K>^K^C^H^@^@^D^O^@^K^KI^S^@^@^E.^A?^Y^C^N:^K;^K'^YI^S^Q^A^R^G@^XB^Y^A^S^@^@^F^E^@^C^H:^K;^KI^S^B^X^@^@^G^E^@^C^N:^K;^KI^S^B^X^@^@^H4^@^C^N:^K;^KI^S^B^X^@^@^@L^@^@^@^B^@$^@^@^@^A^Aû^N^M^@^A^A^A^A^@^@^@^A^@^@^A^@source_code.c^@^@^@^@^@^@	^B-^E@^@^@^@^@^@^U­gu^H^S1åu/^@^B^D^A^HãÎY^B^B^@^A^AGNU C 4.8.4 -mtune=generic -march=x86-64 -g -fstack-protector^@long unsigned int^@unsigned char^@main^@long int^@factorial^@source_code.c^@/home/panther2/Linux_System_Prog/day3_debugging/gdb/10^@argc^@short unsigned int^@loop^@short int^@sizetype^@argv^@^@.symtab^@.strtab^@.shstrtab^@.interp^@.note.ABI-tag^@.note.gnu.build-id^@.gnu.hash^@.dynsym^@.dynstr^@.gnu.version^@.gnu.version_r^@.rela.dyn^@.rela.plt^@.init^@.text^@.fini^@.rodata^@.eh_frame_hdr^@.eh_frame^@.init_array^@.fini_array^@.jcr^@.dynamic^@.got^@.got.plt^@.data^@.bss^@.comment^@.debug_aranges^@.debug_info^@.debug_abbrev^@.debug_line^@.debug_str^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^[^@^@^@^A^@^@^@^B^@^@^@^@^@^@^@8^B@^@^@^@^@^@8^B^@^@^@^@^@^@^\^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@#^@^@^@^G^@^@^@^B^@^@^@^@^@^@^@T^B@^@^@^@^@^@T^B^@^@^@^@^@^@ ^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^D^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@1^@^@^@^G^@^@^@^B^@^@^@^@^@^@^@t^B@^@^@^@^@^@t^B^@^@^@^@^@^@$^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^D^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@D^@^@^@öÿÿo^B^@^@^@^@^@^@^@^B@^@^@^@^@^@^B^@^@^@^@^@^@^\^@^@^@^@^@^@^@^E^@^@^@^@^@^@^@^H^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@N^@^@^@^K^@^@^@^B^@^@^@^@^@^@^@¸^B@^@^@^@^@^@¸^B^@^@^@^@^@^@`^@^@^@^@^@^@^@^F^@^@^@^A^@^@^@^H^@^@^@^@^@^@^@^X^@^@^@^@^@^@^@V^@^@^@^C^@^@^@^B^@^@^@^@^@^@^@^X^C@^@^@^@^@^@^X^C^@^@^@^@^@^@?^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^^@^@^@ÿÿÿo^B^@^@^@^@^@^@^@X^C@^@^@^@^@^@X^C^@^@^@^@^@^@^H^@^@^@^@^@^@^@^E^@^@^@^@^@^@^@^B^@^@^@^@^@^@^@^B^@^@^@^@^@^@^@k^@^@^@þÿÿo^B^@^@^@^@^@^@^@`^C@^@^@^@^@^@`^C^@^@^@^@^@^@ ^@^@^@^@^@^@^@^F^@^@^@^A^@^@^@^H^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@z^@^@^@^D^@^@^@^B^@^@^@^@^@^@^@^C@^@^@^@^@^@^C^@^@^@^@^@^@^X^@^@^@^@^@^@^@^E^@^@^@^@^@^@^@^H^@^@^@^@^@^@^@^X^@^@^@^@^@^@^@^@^@^@^D^@^@^@^B^@^@^@^@^@^@^@^C@^@^@^@^@^@^C^@^@^@^@^@^@H^@^@^@^@^@^@^@^E^@^@^@^L^@^@^@^H^@^@^@^@^@^@^@^X^@^@^@^@^@^@^@^@^@^@^A^@^@^@^F^@^@^@^@^@^@^@à^C@^@^@^@^@^@à^C^@^@^@^@^@^@^Z^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^D^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^A^@^@^@^F^@^@^@^@^@^@^@^@^D@^@^@^@^@^@^@^D^@^@^@^@^@^@@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^P^@^@^@^@^@^@^@^P^@^@^@^@^@^@^@^@^@^@^A^@^@^@^F^@^@^@^@^@^@^@@^D@^@^@^@^@^@@^D^@^@^@^@^@^@â^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^P^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^A^@^@^@^F^@^@^@^@^@^@^@$^F@^@^@^@^@^@$^F^@^@^@^@^@^@	^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^D^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@ ^@^@^@^A^@^@^@^B^@^@^@^@^@^@^@0^F@^@^@^@^@^@0^F^@^@^@^@^@^@"^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^D^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@¨^@^@^@^A^@^@^@^B^@^@^@^@^@^@^@T^F@^@^@^@^@^@T^F^@^@^@^@^@^@<^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^D^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@¶^@^@^@^A^@^@^@^B^@^@^@^@^@^@^@^F@^@^@^@^@^@^F^@^@^@^@^@^@^T^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^H^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@À^@^@^@^N^@^@^@^C^@^@^@^@^@^@^@^P^N`^@^@^@^@^@^P^N^@^@^@^@^@^@^H^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^H^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@Ì^@^@^@^O^@^@^@^C^@^@^@^@^@^@^@^X^N`^@^@^@^@^@^X^N^@^@^@^@^@^@^H^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^H^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@Ø^@^@^@^A^@^@^@^C^@^@^@^@^@^@^@ ^N`^@^@^@^@^@ ^N^@^@^@^@^@^@^H^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^H^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@Ý^@^@^@^F^@^@^@^C^@^@^@^@^@^@^@(^N`^@^@^@^@^@(^N^@^@^@^@^@^@Ð^A^@^@^@^@^@^@^F^@^@^@^@^@^@^@^H^@^@^@^@^@^@^@^P^@^@^@^@^@^@^@æ^@^@^@^A^@^@^@^C^@^@^@^@^@^@^@ø^O`^@^@^@^@^@ø^O^@^@^@^@^@^@^H^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^H^@^@^@^@^@^@^@^H^@^@^@^@^@^@^@ë^@^@^@^A^@^@^@^C^@^@^@^@^@^@^@^@^P`^@^@^@^@^@^@^P^@^@^@^@^@^@0^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^H^@^@^@^@^@^@^@^H^@^@^@^@^@^@^@ô^@^@^@^A^@^@^@^C^@^@^@^@^@^@^@0^P`^@^@^@^@^@0^P^@^@^@^@^@^@^P^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^H^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@ú^@^@^@^H^@^@^@^C^@^@^@^@^@^@^@@^P`^@^@^@^@^@@^P^@^@^@^@^@^@^H^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@ÿ^@^@^@^A^@^@^@0^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@@^P^@^@^@^@^@^@V^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^H^A^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^P^@^@^@^@^@^@0^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^W^A^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@Æ^P^@^@^@^@^@^@ú^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@#^A^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@À^Q^@^@^@^@^@^@|^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@1^A^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@<^R^@^@^@^@^@^@P^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@=^A^@^@^A^@^@^@0^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^R^@^@^@^@^@^@ð^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^Q^@^@^@^C^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@|^S^@^@^@^@^@^@H^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^A^@^@^@^B^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^]^@^@^@^@^@^@¨^F^@^@^@^@^@^@"^@^@^@2^@^@^@^H^@^@^@^@^@^@^@^X^@^@^@^@^@^@^@	^@^@^@^C^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@0$^@^@^@^@^@^@I^B^@^@^@^@^@^@^@^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^C^@^A^@8^B@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^C^@^B^@T^B@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^C^@^Quit
#0  main (argc=1, argv=0x7fffffffdfa8) at source_code.c:12
The program being debugged has been started already.
Start it from the beginning? (y or n) Starting program: /home/panther2/Linux_System_Prog/day3_debugging/gdb/10/source_code.c 

Breakpoint 1, main (argc=1, argv=0x7fffffffdfa8) at source_code.c:12
Line number 12 out of range; source_code.c has 7 lines.
1	^?ELF^B^A^A^@^@^@^@^@^@^@^@^@^B^@>^@^A^@^@^@@^D@^@^@^@^@^@@^@^@^@^@^@^@^@È^T^@^@^@^@^@^@^@^@^@^@@^@8^@	^@@^@#^@ ^@^F^@^@^@^E^@^@^@@^@^@^@^@^@^@^@@^@@^@^@^@^@^@@^@@^@^@^@^@^@ø^A^@^@^@^@^@^@ø^A^@^@^@^@^@^@^H^@^@^@^@^@^@^@^C^@^@^@^D^@^@^@8^B^@^@^@^@^@^@8^B@^@^@^@^@^@8^B@^@^@^@^@^@^\^@^@^@^@^@^@^@^\^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^A^@^@^@^E^@^@^@^@^@^@^@^@^@^@^@^@^@@^@^@^@^@^@^@^@@^@^@^@^@^@¤^G^@^@^@^@^@^@¤^G^@^@^@^@^@^@^@^@ ^@^@^@^@^@^A^@^@^@^F^@^@^@^P^N^@^@^@^@^@^@^P^N`^@^@^@^@^@^P^N`^@^@^@^@^@0^B^@^@^@^@^@^@8^B^@^@^@^@^@^@^@^@ ^@^@^@^@^@^B^@^@^@^F^@^@^@(^N^@^@^@^@^@^@(^N`^@^@^@^@^@(^N`^@^@^@^@^@Ð^A^@^@^@^@^@^@Ð^A^@^@^@^@^@^@^H^@^@^@^@^@^@^@^D^@^@^@^D^@^@^@T^B^@^@^@^@^@^@T^B@^@^@^@^@^@T^B@^@^@^@^@^@D^@^@^@^@^@^@^@D^@^@^@^@^@^@^@^D^@^@^@^@^@^@^@Påtd^D^@^@^@T^F^@^@^@^@^@^@T^F@^@^@^@^@^@T^F@^@^@^@^@^@<^@^@^@^@^@^@^@<^@^@^@^@^@^@^@^D^@^@^@^@^@^@^@Qåtd^F^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^P^@^@^@^@^@^@^@Råtd^D^@^@^@^P^N^@^@^@^@^@^@^P^N`^@^@^@^@^@^P^N`^@^@^@^@^@ð^A^@^@^@^@^@^@ð^A^@^@^@^@^@^@^A^@^@^@^@^@^@^@/lib64/ld-linux-x86-64.so.2^@^D^@^@^@^P^@^@^@^A^@^@^@GNU^@^@^@^@^@^B^@^@^@^F^@^@^@^X^@^@^@^D^@^@^@^T^@^@^@^C^@^@^@GNU^@Ü¹ó·C üäDÆc¦^FÔ^Aq0^L^A^@^@^@^A^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^K^@^@^@^R^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^R^@^@^@^R^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@$^@^@^@ ^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@libc.so.6^@printf^@__libc_start_main^@__gmon_start__^@GLIBC_2.2.5^@^@^@^@^B^@^B^@^@^@^A^@^A^@^A^@^@^@^P^@^@^@^@^@^@^@u^Zi	^@^@^B^@3^@^@^@^@^@^@^@ø^O`^@^@^@^@^@^F^@^@^@^C^@^@^@^@^@^@^@^@^@^@^@^X^P`^@^@^@^@^@^G^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@ ^P`^@^@^@^@^@^G^@^@^@^B^@^@^@^@^@^@^@^@^@^@^@(^P`^@^@^@^@^@^G^@^@^@^C^@^@^@^@^@^@^@^@^@^@^@Hì^HH^E^M^L ^@HÀt^Eè;^@^@^@HÄ^HÃ^@^@^@^@^@^@ÿ5^B^L ^@ÿ%^D^L ^@^O^_@^@ÿ%^B^L ^@h^@^@^@^@éàÿÿÿÿ%ú^K ^@h^A^@^@^@éÐÿÿÿÿ%ò^K ^@h^B^@^@^@éÀÿÿÿ1íIÑ^HâHäðPTIÇÀ ^F@^@HÇÁ°^E@^@HÇÇX^E@^@è·ÿÿÿôf^O^_D^@^@¸G^P`^@UH-@^P`^@Hø^NHåw^B]Ã¸^@^@^@^@HÀtô]¿@^P`^@ÿà^O^_^@^@^@^@¸@^P`^@UH-@^P`^@HÁø^CHåHÂHÁê?H^AÐHÑøu^B]Ãº^@^@^@^@HÒtô]HÆ¿@^P`^@ÿâ^O^_^@^@^@^@=Y^K ^@^@u^QUHåè~ÿÿÿ]Æ^EF^K ^@^AóÃ^O^_@^@H=^X	 ^@^@t^^¸^@^@^@^@HÀt^TU¿ ^N`^@HåÿÐ]é{ÿÿÿ^O^_^@ésÿÿÿUHåHì^P}ü}ü^@u^G¸^A^@^@^@ë^QEüè^AÇèÛÿÿÿ^O¯EüÉÃUHåHì }ìHuàÇEü2^@^@^@ë EüÇè³ÿÿÿÂEüÆ¿4^F@^@¸^@^@^@^@èþÿÿEüPÿUüÀuÓ¸^@^@^@^@ÉÃf.^O^_^@^@^@^@^@fAWAÿAVIöAUIÕATL%H^H ^@UH-H^H ^@SL)å1ÛHÁý^CHì^HèýýÿÿHít^^^O^_^@^@^@^@^@LêLöDÿAÿ^TÜHÃ^AH9ëuêHÄ^H[]A\A]A^A_Ãff.^O^_^@^@^@^@^@óÃ^@^@Hì^HHÄ^HÃ^@^@^@^A^@^B^@Factorial of number %d is %u
1	^?ELF^B^A^A^@^@^@^@^@^@^@^@^@^B^@>^@^A^@^@^@@^D@^@^@^@^@^@@^@^@^@^@^@^@^@È^T^@^@^@^@^@^@^@^@^@^@@^@8^@	^@@^@#^@ ^@^F^@^@^@^E^@^@^@@^@^@^@^@^@^@^@@^@@^@^@^@^@^@@^@@^@^@^@^@^@ø^A^@^@^@^@^@^@ø^A^@^@^@^@^@^@^H^@^@^@^@^@^@^@^C^@^@^@^D^@^@^@8^B^@^@^@^@^@^@8^B@^@^@^@^@^@8^B@^@^@^@^@^@^\^@^@^@^@^@^@^@^\^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^A^@^@^@^E^@^@^@^@^@^@^@^@^@^@^@^@^@@^@^@^@^@^@^@^@@^@^@^@^@^@¤^G^@^@^@^@^@^@¤^G^@^@^@^@^@^@^@^@ ^@^@^@^@^@^A^@^@^@^F^@^@^@^P^N^@^@^@^@^@^@^P^N`^@^@^@^@^@^P^N`^@^@^@^@^@0^B^@^@^@^@^@^@8^B^@^@^@^@^@^@^@^@ ^@^@^@^@^@^B^@^@^@^F^@^@^@(^N^@^@^@^@^@^@(^N`^@^@^@^@^@(^N`^@^@^@^@^@Ð^A^@^@^@^@^@^@Ð^A^@^@^@^@^@^@^H^@^@^@^@^@^@^@^D^@^@^@^D^@^@^@T^B^@^@^@^@^@^@T^B@^@^@^@^@^@T^B@^@^@^@^@^@D^@^@^@^@^@^@^@D^@^@^@^@^@^@^@^D^@^@^@^@^@^@^@Påtd^D^@^@^@T^F^@^@^@^@^@^@T^F@^@^@^@^@^@T^F@^@^@^@^@^@<^@^@^@^@^@^@^@<^@^@^@^@^@^@^@^D^@^@^@^@^@^@^@Qåtd^F^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^P^@^@^@^@^@^@^@Råtd^D^@^@^@^P^N^@^@^@^@^@^@^P^N`^@^@^@^@^@^P^N`^@^@^@^@^@ð^A^@^@^@^@^@^@ð^A^@^@^@^@^@^@^A^@^@^@^@^@^@^@/lib64/ld-linux-x86-64.so.2^@^D^@^@^@^P^@^@^@^A^@^@^@GNU^@^@^@^@^@^B^@^@^@^F^@^@^@^X^@^@^@^D^@^@^@^T^@^@^@^C^@^@^@GNU^@Ü¹ó·C üäDÆc¦^FÔ^Aq0^L^A^@^@^@^A^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^K^@^@^@^R^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^R^@^@^@^R^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@$^@^@^@ ^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@libc.so.6^@printf^@__libc_start_main^@__gmon_start__^@GLIBC_2.2.5^@^@^@^@^B^@^B^@^@^@^A^@^A^@^A^@^@^@^P^@^@^@^@^@^@^@u^Zi	^@^@^B^@3^@^@^@^@^@^@^@ø^O`^@^@^@^@^@^F^@^@^@^C^@^@^@^@^@^@^@^@^@^@^@^X^P`^@^@^@^@^@^G^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@ ^P`^@^@^@^@^@^G^@^@^@^B^@^@^@^@^@^@^@^@^@^@^@(^P`^@^@^@^@^@^G^@^@^@^C^@^@^@^@^@^@^@^@^@^@^@Hì^HH^E^M^L ^@HÀt^Eè;^@^@^@HÄ^HÃ^@^@^@^@^@^@ÿ5^B^L ^@ÿ%^D^L ^@^O^_@^@ÿ%^B^L ^@h^@^@^@^@éàÿÿÿÿ%ú^K ^@h^A^@^@^@éÐÿÿÿÿ%ò^K ^@h^B^@^@^@éÀÿÿÿ1íIÑ^HâHäðPTIÇÀ ^F@^@HÇÁ°^E@^@HÇÇX^E@^@è·ÿÿÿôf^O^_D^@^@¸G^P`^@UH-@^P`^@Hø^NHåw^B]Ã¸^@^@^@^@HÀtô]¿@^P`^@ÿà^O^_^@^@^@^@¸@^P`^@UH-@^P`^@HÁø^CHåHÂHÁê?H^AÐHÑøu^B]Ãº^@^@^@^@HÒtô]HÆ¿@^P`^@ÿâ^O^_^@^@^@^@=Y^K ^@^@u^QUHåè~ÿÿÿ]Æ^EF^K ^@^AóÃ^O^_@^@H=^X	 ^@^@t^^¸^@^@^@^@HÀt^TU¿ ^N`^@HåÿÐ]é{ÿÿÿ^O^_^@ésÿÿÿUHåHì^P}ü}ü^@u^G¸^A^@^@^@ë^QEüè^AÇèÛÿÿÿ^O¯EüÉÃUHåHì }ìHuàÇEü2^@^@^@ë EüÇè³ÿÿÿÂEüÆ¿4^F@^@¸^@^@^@^@èþÿÿEüPÿUüÀuÓ¸^@^@^@^@ÉÃf.^O^_^@^@^@^@^@fAWAÿAVIöAUIÕATL%H^H ^@UH-H^H ^@SL)å1ÛHÁý^CHì^HèýýÿÿHít^^^O^_^@^@^@^@^@LêLöDÿAÿ^TÜHÃ^AH9ëuêHÄ^H[]A\A]A^A_Ãff.^O^_^@^@^@^@^@óÃ^@^@Hì^HHÄ^HÃ^@^@^@^A^@^B^@Factorial of number %d is %u
2	^@^@^@^A^[^C;8^@^@^@^F^@^@^@¬ýÿÿ^@^@^@ìýÿÿT^@^@^@Ùþÿÿ¬^@^@^@^DÿÿÿÌ^@^@^@\ÿÿÿì^@^@^@Ìÿÿÿ4^A^@^@^T^@^@^@^@^@^@^@^AzR^@^Ax^P^A^[^L^G^H^A^G^P^T^@^@^@^\^@^@^@ýÿÿ*^@^@^@^@^@^@^@^@^@^@^@^T^@^@^@^@^@^@^@^AzR^@^Ax^P^A^[^L^G^H^A^@^@$^@^@^@^\^@^@^@ ýÿÿ@^@^@^@^@^N^PF^N^XJ^O^Kw^H^@?^Z;*3$"^@^@^@^@^\^@^@^@D^@^@^@%þÿÿ+^@^@^@^@A^N^P^BC^M^Ff^L^G^H^@^@^@^\^@^@^@d^@^@^@0þÿÿL^@^@^@^@A^N^P^BC^M^F^BG^L^G^H^@^@D^@^@^@^@^@^@hþÿÿe^@^@^@^@B^N^P^BE^N^X^CE^N ^DE^N(^EH^N0^FH^N8^GM^N@l^N8A^N0A^N(B^N B^N^XB^N^PB^N^H^@^T^@^@^@Ì^@^@^@þÿÿ^B^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^Quit
The program being debugged has been started already.
Start it from the beginning? (y or n) Starting program: /home/panther2/Linux_System_Prog/day3_debugging/gdb/10/source_code.c 

Breakpoint 1, main (argc=1, argv=0x7fffffffdfa8) at source_code.c:12
Line number 12 out of range; source_code.c has 7 lines.
1	^?ELF^B^A^A^@^@^@^@^@^@^@^@^@^B^@>^@^A^@^@^@@^D@^@^@^@^@^@@^@^@^@^@^@^@^@È^T^@^@^@^@^@^@^@^@^@^@@^@8^@	^@@^@#^@ ^@^F^@^@^@^E^@^@^@@^@^@^@^@^@^@^@@^@@^@^@^@^@^@@^@@^@^@^@^@^@ø^A^@^@^@^@^@^@ø^A^@^@^@^@^@^@^H^@^@^@^@^@^@^@^C^@^@^@^D^@^@^@8^B^@^@^@^@^@^@8^B@^@^@^@^@^@8^B@^@^@^@^@^@^\^@^@^@^@^@^@^@^\^@^@^@^@^@^@^@^A^@^@^@^@^@^@^@^A^@^@^@^E^@^@^@^@^@^@^@^@^@^@^@^@^@@^@^@^@^@^@^@^@@^@^@^@^@^@¤^G^@^@^@^@^@^@¤^G^@^@^@^@^@^@^@^@ ^@^@^@^@^@^A^@^@^@^F^@^@^@^P^N^@^@^@^@^@^@^P^N`^@^@^@^@^@^P^N`^@^@^@^@^@0^B^@^@^@^@^@^@8^B^@^@^@^@^@^@^@^@ ^@^@^@^@^@^B^@^@^@^F^@^@^@(^N^@^@^@^@^@^@(^N`^@^@^@^@^@(^N`^@^@^@^@^@Ð^A^@^@^@^@^@^@Ð^A^@^@^@^@^@^@^H^@^@^@^@^@^@^@^D^@^@^@^D^@^@^@T^B^@^@^@^@^@^@T^B@^@^@^@^@^@T^B@^@^@^@^@^@D^@^@^@^@^@^@^@D^@^@^@^@^@^@^@^D^@^@^@^@^@^@^@Påtd^D^@^@^@T^F^@^@^@^@^@^@T^F@^@^@^@^@^@T^F@^@^@^@^@^@<^@^@^@^@^@^@^@<^@^@^@^@^@^@^@^D^@^@^@^@^@^@^@Qåtd^F^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^P^@^@^@^@^@^@^@Råtd^D^@^@^@^P^N^@^@^@^@^@^@^P^N`^@^@^@^@^@^P^N`^@^@^@^@^@ð^A^@^@^@^@^@^@ð^A^@^@^@^@^@^@^A^@^@^@^@^@^@^@/lib64/ld-linux-x86-64.so.2^@^D^@^@^@^P^@^@^@^A^@^@^@GNU^@^@^@^@^@^B^@^@^@^F^@^@^@^X^@^@^@^D^@^@^@^T^@^@^@^C^@^@^@GNU^@Ü¹ó·C üäDÆc¦^FÔ^Aq0^L^A^@^@^@^A^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^K^@^@^@^R^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^R^@^@^@^R^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@$^@^@^@ ^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@libc.so.6^@printf^@__libc_start_main^@__gmon_start__^@GLIBC_2.2.5^@^@^@^@^B^@^B^@^@^@^A^@^A^@^A^@^@^@^P^@^@^@^@^@^@^@u^Zi	^@^@^B^@3^@^@^@^@^@^@^@ø^O`^@^@^@^@^@^F^@^@^@^C^@^@^@^@^@^@^@^@^@^@^@^X^P`^@^@^@^@^@^G^@^@^@^A^@^@^@^@^@^@^@^@^@^@^@ ^P`^@^@^@^@^@^G^@^@^@^B^@^@^@^@^@^@^@^@^@^@^@(^P`^@^@^@^@^@^G^@^@^@^C^@^@^@^@^@^@^@^@^@^@^@Hì^HH^E^M^L ^@HÀt^Eè;^@^@^@HÄ^HÃ^@^@^@^@^@^@ÿ5^B^L ^@ÿ%^D^L ^@^O^_@^@ÿ%^B^L ^@h^@^@^@^@éàÿÿÿÿ%ú^K ^@h^A^@^@^@éÐÿÿÿÿ%ò^K ^@h^B^@^@^@éÀÿÿÿ1íIÑ^HâHäðPTIÇÀ ^F@^@HÇÁ°^E@^@HÇÇX^E@^@è·ÿÿÿôf^O^_D^@^@¸G^P`^@UH-@^P`^@Hø^NHåw^B]Ã¸^@^@^@^@HÀtô]¿@^P`^@ÿà^O^_^@^@^@^@¸@^P`^@UH-@^P`^@HÁø^CHåHÂHÁê?H^AÐHÑøu^B]Ãº^@^@^@^@HÒtô]HÆ¿@^P`^@ÿâ^O^_^@^@^@^@=Y^K ^@^@u^QUHåè~ÿÿÿ]Æ^EF^K ^@^AóÃ^O^_@^@H=^X	 ^@^@t^^¸^@^@^@^@HÀt^TU¿ ^N`^@HåÿÐ]é{ÿÿÿ^O^_^@ésÿÿÿUHåHì^P}ü}ü^@u^G¸^A^@^@^@ë^QEüè^AÇèÛÿÿÿ^O¯EüÉÃUHåHì }ìHuàÇEü2^@^@^@ë EüÇè³ÿÿÿÂEüÆ¿4^F@^@¸^@^@^@^@èþÿÿEüPÿUüÀuÓ¸^@^@^@^@ÉÃf.^O^_^@^@^@^@^@fAWAÿAVIöAUIÕATL%H^H ^@UH-H^H ^@SL)å1ÛHÁý^CHì^HèýýÿÿHít^^^O^_^@^@^@^@^@LêLöDÿAÿ^TÜHÃ^AH9ëuêHÄ^H[]A\A]A^A_Ãff.^O^_^@^@^@^@^@óÃ^@^@Hì^HHÄ^HÃ^@^@^@^A^@^B^@Factorial of number %d is %u
2	^@^@^@^A^[^C;8^@^@^@^F^@^@^@¬ýÿÿ^@^@^@ìýÿÿT^@^@^@Ùþÿÿ¬^@^@^@^DÿÿÿÌ^@^@^@\ÿÿÿì^@^@^@Ìÿÿÿ4^A^@^@^T^@^@^@^@^@^@^@^AzR^@^Ax^P^A^[^L^G^H^A^G^P^T^@^@^@^\^@^@^@ýÿÿ*^@^@^@^@^@^@^@^@^@^@^@^T^@^@^@^@^@^@^@^AzR^@^Ax^P^A^[^L^G^H^A^@^@$^@^@^@^\^@^@^@ ýÿÿ@^@^@^@^@^N^PF^N^XJ^O^Kw^H^@?^Z;*3$"^@^@^@^@^\^@^@^@D^@^@^@%þÿÿ+^@^@^@^@A^N^P^BC^M^Ff^L^G^H^@^@^@^\^@^@^@d^@^@^@0þÿÿL^@^@^@^@A^N^P^BC^M^F^BG^L^G^H^@^@D^@^@^@^@^@^@hþÿÿe^@^@^@^@B^N^P^BE^N^X^CE^N ^DE^N(^EH^N0^FH^N8^GM^N@l^N8A^N0A^N(B^N B^N^XB^N^PB^N^H^@^T^@^@^@Ì^@^@^@þÿÿ^B^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^@^Quit
A debugging session is active.

	Inferior 1 [process 22859] will be killed.

Quit anyway? (y or n) Breakpoint 1 at 0x400567: file source_code.c, line 12.
Starting program: /home/panther2/Linux_System_Prog/day3_debugging/gdb/10/source_code 

Breakpoint 1, main (argc=1, argv=0x7fffffffdfa8) at source_code.c:12
12		unsigned int loop = 50;
$1 = 0
13		while(loop--) {
$2 = 50
A debugging session is active.

	Inferior 1 [process 22881] will be killed.

Quit anyway? (y or n)

### Theory: Logging

You may want to save the output of gdb commands to a file

There are several commands to control gdb’s logging.

set logging on
	Enable logging. 
	GDB saves all output from this point in a text file called gdb.txt that resides in the directory in which you are running GDB 

set logging off
	Disable logging.
	Note that you can turn logging on and off several times and GDB will concatenate output to the gdb.txt file

set logging file file
	Change the name of the current logfile. The default logfile is ‘gdb.txt’.

Useful when you’re dealing with a long stack trace, or a multi-threaded stack trace

### Source Code Examples

<details>
<summary><strong>Click to expand <code>source_code.c</code></strong></summary>

```c
#include <stdio.h>

unsigned int factorial(unsigned int n)
{
    if (n == 0)  
		return 1;
    return n * factorial(n-1);
}

int main(int argc, char* argv[])
{
	unsigned int loop = 50;
	while(loop--) {
    printf("Factorial of number %d is %u\n", 
            loop, 
            factorial(loop));
	}
    
	return 0;
}
```

</details>

---

## Part 11: Attach Process

### Theory: Attach Process

Debugging an Already-running Process
=======================================

attach process-id

	This command attaches to a running process—one that was started outside gdb

detach

	This command attaches to a running process—one that was started outside gdb

### Source Code Examples

<details>
<summary><strong>Click to expand <code>source_code.c</code></strong></summary>

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[])
{
	int i = 1;

	while (i < 60) {
		i++;
		sleep(1);
	}

	return 0;
}
```

</details>

---

## Part 12: Notes

### Theory: Notes

Another Useful function of gdb debugger is the disassemble command. 
as its name suggesting, this command helps in disassembling of provide function assembler codes. 

like if we want to disassemble main function. we just need to type

(gdb) disassemble main

### Source Code Examples

<details>
<summary><strong>Click to expand <code>assembly.c</code></strong></summary>

```c
#include <stdio.h>

int main()
{

	int i = 10;
	int j = 20;

	i += j;
	i -= j;
	i *= j;
	i /= j;

	return 0;
}
```

</details>

---

## Part 13: Start

### Theory: Start

start command
================

Sets a temporary breakpoint on main() and starts executing a program under GDB.

### Source Code Examples

<details>
<summary><strong>Click to expand <code>source_code.c</code></strong></summary>

```c
#include <stdio.h>

unsigned int factorial(unsigned int n)
{
    if (n == 0)  
		return 1;
    return n * factorial(n-1);
}

int main(int argc, char* argv[])
{
	unsigned int loop = 50;
	while(loop--) {
    printf("Factorial of number %d is %u\n", 
            loop, 
            factorial(loop));
	}
    
	return 0;
}
```

</details>

---

## Part 14: Command

### Theory: Command

command breakpoint-number specifies commands to run whenever the breakpoint is reached. 

(gdb) command 2
  Type commands for when breakpoint 2 is hit, one per line.
  End with a line saying just "end".

### Source Code Examples

<details>
<summary><strong>Click to expand <code>source_code.c</code></strong></summary>

```c
#include <stdio.h>

unsigned int factorial(unsigned int n)
{
    if (n == 0)  
		return 1;
    return n * factorial(n-1);
}

int main(int argc, char* argv[])
{
	unsigned int loop = 50;
	while(loop--) {
    printf("Factorial of number %d is %u\n", 
            loop, 
            factorial(loop));
	}
    
	return 0;
}
```

</details>

---

## Quiz

1. For debugging with GDB, the file “source_code” can be created with the command
a) gcc -g -o source_code source_code.c
b) gcc -g source_code.c
c) gdb source_code
d) none of the mentioned

2. For debugging with GDB, the compiled program can be run by the command
a) run
b) execute
c) ./<filename>
d) none of the mentioned

3. In GDB, breakpoints can be set by the command
a) break
b) b
c) both break and b
d) none of the mentioned

4. GDB stands for
a) GNU debugger
b) General debugging breakpoint
c) General debugger
d) None of the mentioned

5. GDB can be used for
a) c language
b) c++ language
c) both c and c++ language
d) none of the mentioned

6. The command “gdb source_code”
a) will start debugging for the file “source_code” if the file is compiled with -g option with GCC
b) will create executable for debugging
c) will provide all errors present in the file “source_code”
d) none of the mentioned

7. In debugging with GDB, break points can be set to
a) any line
b) any function
c) both any line and function
d) none of the mentioned

8. In GDB debugging, we can proceed to the next breakpoint with command
a) next
b) continue
c) both next and continue
d) none of the mentioned

9. At the time of debugging with GDB, if we just press ENTER
a) GDB will repeat the same command you just gave it
b) GDB will do nothing
c) GDB will exit
d) None of the mentioned

10. To print the value of a variable while debugging with GDB, ______ command can be used.
a) printf
b) print
c) show
d) none of the mentioned

11. Which GDB command prints the value of a variable in hex.
a) print/x
b) print/h
c) print/e
d) none of the mentioned

12. Which GDB command interrupts the program whenever the value of a variable is modified and prints the value old and new values of the variable?
a) watch
b) show
c) trace
d) none of the mentioned

13. Which GDB command produces a stack trace of the function calls that lead to a segmentation fault?
a) trace
b) backtrace
c) forwardtrace
d) none of the mentioned

14. The specific break point can be deleted by _____ command in GDB.
a) delete
b) del
c) remove
d) none of the mentioned

15. The “step” command of GDB
a) executes the current line of the program
b) stops the next statement to be executed
c) executes the current line of the program & stops the next statement to be executed
d) none of the mentioned

16. GDB can be used
a) to find out the memory leakages
b) to get the result of a particular expression in a program
c) to find the reason of segementation fault
d) all of the mentioned

17.Which GDB command can be used to put a breakpoint at the beginning of the program?
a) b main
b) b start
c) break
d) none of the mentioned

18. To put the breakpoint at the current line ____ command can be used?
a) b
b) break
c) both b and break
d) none of the mentioned

19.We can list all the breakpoint in GDB by the command
a) info break
b) break all
c) both info break and break all
d) none of the mentioned


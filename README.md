linux-x64-nasm-cheatsheet
=========================

Registers
---------

|                         | 64 bit    | 32 bit      | 16 bit      | 8 bit       |
|-------------------------|-----------|-------------|-------------|-------------|
| General purpose:        |           |             |             |             |
| A (accumulator)         | RAX       | EAX         | AX          | AL          |
| B (base, addressing)    | RBX       | EBX         | BX          | BL          |
| C (counter, iterations) | RCX       | ECX         | CX          | CL          |
| D (data)                | RDX       | EDX         | DX          | DL          |
| ======================= | ========= | =========== | =========== | =========== |
| Stack:                  |           |             |             |             |
| Stack pointer           | RSP       | ESP         | SP          | SPL         |
| Frame pointer           | RBP       | EBP         | BP          | BPL         |
| ======================= | ========= | =========== | =========== | =========== |
| Other:                  |           |             |             |             |
|                         | RDI       | EDI         | DI          | DIL         |
|                         | RSI       | ESI         | SI          | SIL         |
| ======================= | ========= | =========== | =========== | =========== |
|                         | R0 .. R15 | R0D .. R15D | R0W .. R15W | R0B .. R15B |       

Calling C
---------

Put function arguments (first to last) in the following registers (64 bit representations):  
RDI, RSI, RDX, RCX, R8, R9, then push to stack (in reverse, has to be cleaned up by the caller!)  
XMM0 - XMM7 for floats  

Return values are stored in RAX (int) or XMM0 (float)  

RBP, RBX, R12, R13, R14, R15 will not be changed by the called function, all others may be  

Align stack pointer (RSP..) to 16 byte, calling pushes 8 bytes!  

Keep in mind that strings (in C) are 0-terminated  

Like in a normal C program, the label that is (de facto) called first is "main", with the args  
argc (argcount) in RDI, and the char** argvec in RSI (the commandline arguments as in C's main function).  

Data
----

| type                                      | define      |
|-------------------------------------------|-------------|
| 8 bits integer                            | `db`        |
| 16 bits integer                           | `dw`        |
| 32 bits integer or 32 bits float          | `dd`        |
| 64 bits integer or 64 bits float (double) | `dq` \ `do` |
| extended precision                        | `dt`        |

Conditionals
------------

cmp op1, op2 -> mimics 'sub op1, op2' but only changes the zero and carry flag for comparing.  

Prefixes:    

| command     | explain                         |
|-------------|---------------------------------|
| `j~ x`      | jump to x if ~                  |
| `cmov~ x,y` | conditional mov x, y if ~       |
| `setc~ x`   | set x to 1 if ~, x is 8 bit reg |

Many suffixes, including:  
* general:
  * `n~` (not condition, !)
  * `e` (equal, =)
* unsigned number
  * `a`  (above, >)  
  * `ae` (above or equal, >=)  
  * `b` (below, <)  
  * `be` (below or equal, <=)  
* signed number
  * `g`  (greater, >)  
  * `ge` (greater or equal, >=)  
  * `l` (less, <)  
  * `le` (less or equal, <=), 



Program structure
-----------------

`global <entry>` -> exposes entry point  

`extern <function>` -> declares a function in another linked .o file (e.g. C function, other asm file)  

`section <sectiontype>` -> sets section, usually:  

* `.text` -> program code
* `.data` -> data

The program entry point of a standalone program is the label "_start".  
When compiled with gcc, C provides "_start", which inits and then jumps to "main",  
which should then be implemented by the program.  


Syscalls
--------

- put syscall number in EAX (e.g. 60 for exit, 1 for write to stdout)
- put arguments in the registers (see above) like when calling a C function
- execute the 'syscall' command

Compile
=======

1. `nasm -felf64 [ASM]`
2. 
   * without gcc: `ld -o [OUTPUT] [OBJECT]`
   * with gcc:    `gcc -o [OUTPUT] [OBJECT]`



Sources
-------

Main: [cs.lmu.edu](http://cs.lmu.edu/~ray/notes/nasmtutorial)  
Registers: [tutorialspoint.com](https://tutorialspoint.com/assembly_programming/assembly_registers.htm)  
Conditionals: [de.wikibooks.org](https://de.wikibooks.org/wiki/Assembler-Programmierung_für_x86-Prozessoren/_Sprünge_und_Schleifen) 

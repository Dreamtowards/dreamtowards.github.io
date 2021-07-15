
# x86 Assembly Instructions
Posted on Jul 4, 2021.  
Tags: Compilers, ASM.


Every C program has four main components: Heap, Stack, Registers and Instructions.  
at this time, there are two main architectures that dictate how our program is compiled and executed. 32-bit and 64-bit.

- Heap  
  <small>Heap is an area in memory designed for the purpose of manual memory allocation. (rl. malloc / calloc / global-static.) </small>
- Stack  
  <small>Stack is a data structure comprised of elements that added and removed with two operations: *push* and *pop*.  
  The stack "always" grows towards lower memory address (depend on compilers and o.s.)   
  </small>
    - <small>[Stack-Frame](#stack-frame)</small>
- Registers <small>(32-bit / 64-bit)</small>  
  <small>Registers are essentially small storages' area in a processor, 
  they can be used to store memory addresses, values, or anything that can be represented with 8-bytes (64-bit) or less.  
  In the x86 architecture, there are 6 general-purpose registers: [eax, ebx, ecx, edx, esi, edi.]. these registers are generally
  used on an as-needed basis. There are also 3 registers for specific-purposes: [ebp, esp, eip.].
  </small> 
  - x86 architecture (32-bit)
    - <small>General</small>  
      eax, ebx, ecx, edx, esi, edi.
    - <small>Reserved</small>  
      ebp, esp, eip.
      
- Instructions

### Core Memory Layout of C program.
```
        HIGHER  ADDRESS
   +------------------------+
   |  Unmapped or reserved  | Command-line argument & Environment variables
   |------------------------|------------------------
   |     Stack segment      | |
   |           |            | | Stack frame
   |           v            | v
   |                        |
   |           ^            | ^
   |           |            | | Dynamic memory
   |     Heap segment       | |
   |------------------------|------------------------
   |   Uninitialized data   |
   |------------------------| Data segment
   |    Initialized data    | 
   |------------------------|------------------------
   |                        |
   |      Text segment      | Executable code
   |                        |
   +------------------------+
         LOWER  ADDRESS
```
// referred from http://www.vishalchovatiya.com/how-c-program-stored-in-ram-memory/



## Stack-Frame
<small>*(Stack-Context, x86(not x86-64))*</small>  
> Whenever a function is called, that function is set up with what's called a stack-frame.
all the local-variables for that function will be stored in that stack-frame.

> Two special-registers: *ebp* and *esp*. `ebp`: a.k.a. the base-pointer, contains the address of the base of the current stack-frame. `esp` register: also referred to as a stack-pointer, contains the address of the top element of the current stack-frame.
All the space between these two registers, make up the stack-frame of whatever function is currently being called.

Let's go over a simple example of what happens when a function is called from *main*. 
How it's stack-frame is set up and how it's variables are stored on the stack.

```c
#include <stdio.h>

void func(int x) {
    int a = 0;
    int b = x;
}

int main() {
    func(10);
}
```

First, the value of the argument is pushed onto the stack, 
then the return address of the function is pushed onto the stack. 
the return address is simply the 4 byte address of the instruction that will be executed as soon as the function has gone out of scope.

then the base-pointer is pushed onto the stack, 
then the stack-pointer is given the value of the base-pointer. 
finally, the stack pointer is decremented to make room for the local variables.

```

           <---     esp then 'decreasing' more.



+--------+ <-- ebp, esp
|ret_addr|
+--------+
|   10   |
+--------+
```


## Instruction Format

There are two syntaxes that assembly is normally written: AT&T and Intel. 
while the instruction themselves are the same regardless of the syntax. 
the way the instructions are presented different. we will be covering the intel syntax.

Every instruction has two parts: the operation, and it's arguments.
Operations can take either one or two arguments. if takes two arguments, they are separated by a comma.

the move instruction takes two arguments, 
and copies the value referred to by its second argument into the location referred by its first argument.

         v-----+
    mov arg1, arg2

## Basic Instructions

```
# mov arg1, arg2      // copies arg2 into arg1.
        ^----+    
  Examples:
  mov eax, ebp-0x8   // bad. stored an address..
  mov eax, [ebp-0x8] // good. as expected, copied the value from dereference of the address.


# add arg1, arg2
        ^---= (arg1 + arg2)  
  Examples:
  add eax, 0x5    // eax-before: 10, eax-after: 15.
        
# sub arg1, arg2
        ^---= (arg1 - arg2)

# push arg        // push arg1 onto the stack-top: dec esp, then put data.

# pop reg         // move the stack-top element into the register 

# lea reg, addr   // load effective address.  ??? load value of the memory address into the register.

```

- mov *arg1*, *arg2*  
  copy the actually arg2 value onto the arg1. if arg2 is an address, then use dereference to read value then just pass value.
- add *arg1*, *arg2*  
  result = (arg1+arg2), stored on arg1.
- push *arg*  
- pop *reg*


### Instruction Address

Every instruction has an instruction address. 
this is the area in memory where the instruction is stored. 
the EIP register a.k.a. instruction pointer, always contains the address of the instruction that is currently being executed.
the computer will execute whatever the instruction pointer is pointing to, and then the instruction pointer will be moved to the next instruction


#### Refers

https://www.youtube.com/watch?v=75gBFiFtAb8

https://godbolt.org/

https://www.cnblogs.com/tongongV/p/13713210.html
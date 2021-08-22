
# x86 Assembly Intro.

Welcome to the x86 Assembly Basics.

## Basic Intro

Assembly Syntax in the Whole Article are Intel syntax.

### What's 'Assembly' Standfor.? 

'汇编' 介于编程语言 和机器码之间。

机器码紧凑地由一个个指令/操作码组成，通过人眼阅读几乎是不可能的。  
为了更好的阅读指令，'汇编'在此加入了游戏。

```
---------------Programming Languages---------------
#include<stdio.h>
int main() { ... }
---------------------------------------------------
                          v
------------Assembly Language Instructions---------
  mov     eax, 0
  call    printf
  lea     rax, [rbp-4] 
  ...
---------------------------------------------------
                          v
---------------------Machine Code------------------
'01010010100100101010010100101010010101010010010011
 0101000101010100101010010101010101010010101001...'
---------------------------------------------------
```

### Playground

- gcc  
  `gcc -S -masm=intel test.c -o test.s`  
  `gcc test.s`  
  `./a.out`
  

- Compiler Explorer. (Online Compiler)  
  https://godbolt.org/


## Questions

### What's `[]` on the operand do.? 

Operands of that type (brackets around a memory address), such as `[ebp]` are called [memory operands](https://github.com/simon987/Much-Assembly-Required/wiki/Basic-Assembly-tutorial#instruction-and-operands).

its behavior like `*ebp` in C language. its Dereference.

Operation 'Equality':
```
// ASM intel vs. C language.
mov eax, ebx     // eax = ebx;
mov eax, [ebx]   // eax = *ebx;
mov eax, ebx-4   // eax = ebx-4;
mov eax, [ebx-4] // eax = *(ebx-4)
```

> if brackets, then dereference. (except when it's the lea instruction.)

#### The Exception, LEA instruction

in the Source-Operand(2nd place) of LEA instruction, address value just been calculated and put into the destination operand, regardless of the `[]` on the src-operand anyway. 
```
lea eax, [ebp-4]
```  
The value of ebp is subtracted by 4 and the resulting value is stored in eax.


### LEA vs. MOV.

the LEA Source-Operand, Just calculates the address value, doesn't involve Dereference anyway(regardless of the `[]` in source-operand).  

note that 2nd operand of LEA required have `[]` in syntactical.

Examples:
```
mov eax, var       == lea eax, [var]   ; i.e. mov r32, imm32
lea eax, [var+16]  == mov eax, var+16
lea eax, [eax*4]   == shl eax, 2        ; but without setting flags
```

However, LEA is often used as a "trick" to do certain computations, but that's not its primary purpose.

- LEA details https://stackoverflow.com/questions/1658294/whats-the-purpose-of-the-lea-instruction
- LEA. vs MOV. https://stackoverflow.com/questions/1699748/what-is-the-difference-between-mov-and-lea

## References

- HackUCF - x86 Assembly Crash Course  
  https://www.youtube.com/watch?v=75gBFiFtAb8
- 


- https://arthurchiao.art/blog/x86-asm-guide-zh/
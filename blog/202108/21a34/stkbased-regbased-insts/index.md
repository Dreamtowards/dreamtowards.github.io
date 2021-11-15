
# StackBased Rd. RegisterBased VM and Instructions.

```
/* hi-lv language representation. */
a = 1+1   

/* stkbas instructions */
iconst 1
iconst 1
iadd
istore slot_0

/* regbas instructions */
mov eax, 1
add eax, 1
mov [ebp-4], eax
```

## Abstraction Level

who's abs level higher.?  
at beginning i am considering stkbased-insts lower abs, since it expanded every steps into foundational instructions, add sub etc. while the Regbased-MachineCode-AssemblyLanguage still using composed syntax such as `mov eax, [ebp-8]` its seems on the operand has more need-been-expanded runtime-operations.

but may this considering is wrong, cuz the asm-language is a language, since we comparing instructions, hilv-languages are excluded.  
and the Stack is actually a kind of hi-lv thing compared with basic register(vars) (and storages).


## the 'Converting'



# 21a34 ldv *_updates.

## Preplain

in this week, we want implement pointer-based char-array string class.



> **Are pointers required.?**  
y. since the abstraction base on kind-low-level.  
when no pointers, lots of things are wrapped by built-in arrays, etc.

> **Runtime VM required.?**  
y. but compiled binary should also been provided, usually should be VM+JIT.

Functionality:
1. Cross-Platform VM and Common Bytecode (abslv JBC .75 ASM)
2. langfn
    1. pointers.
    2. static types and dynamic types.
    3. stack-alloc
    4. function is pointer.

Process:
1. Intermediate Bytecode Instructions define.
2. Manual Compilation Example and Checkout
3. Compilation Program
4. Execution Machine.

#### the string class

```
class string {
    u16* base;
    
    void init(u16* p) {
        base = p;
    }
    u16 char_at(u32 i) {
        return *char_ptr(i);
    }
    u16* char_ptr(u32 i) {
        return base + i*sizeof(u16);
    }
    
    u32 strlen() {
        u32 i = 0;
        while (char_at(i++) != 0);
        return i;
    }
    string substr(u32 begin, u32 end) {
        u32 len = end - begin;
        char* p = malloc(len);
        memcpy(char_ptr(begin), p, len);
        return string(p);
    }
}

void main() {
    string s = string("TestStr");
    
    u16 c = s.char_at(1);
    *s.char_ptr(1) = 'a';
    
    u32 l = s.strlen();
    
    
}
```

#### expect bytecode.

- has LocalVariableTable? (VM hlv-managed locals) -> false.
  - case true: instructions like `load 0`, `store 0` and a table records every slot vartype. the slot-storage-size is `sizeof(slot(idx).vartype)`?
  - case false: more low-level, specified.  
  
Mid-Lv
```
main:
    stacknew string
    store 0
    
    load 0
    iconst 1
    invokevirtual string.char_at
    store 1
    
    load 0
    iconst 1
    invokevirtual string.char_ptr
    iconst 'a'
    memset 
    
    load 0
    invokevirtual string.strlen
    store 2
```
Lo-Lv.
```
main:
    pushl %ebp
    movl %esp, %ebp
    subl $12, %esp
    
    leal -6(%ebp), %eax
    pushl %eax            # prepare addr. pass in.
    pushl $.C0str
    call string.init
    addl %esp, 8          # clear args.
    
    leal -6(%ebp), %eax
    pushl %eax
    pushl $1
    call string.char_at
    addl %esp, 8
    movl %eax, -10(%ebp)
    
    
    
    
    
    movl %ebp, %esp
    popl %ebp
    popl %eip
    
string_init:
    pushl $ebp
    movl %esp, %ebp
    
    movl -12(%ebp), %eax   # arg1 thisptr
    movl -8(%ebp), %ebx    # arg2 arg_charptr
    movl %ebx, (%eax)
    
    movl %ebp, %esp
    popl %ebp
    popl %eip
```
```
localtmps
localvars
old_ebp
old_eip
arg3
arg2
arg1
```



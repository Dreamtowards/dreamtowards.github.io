
# 21a34 ldv *_updates.

## Preplain

in this week, we want implement pointer-based char-array string class.



> **Are pointers required.?**  
y. since the abstraction base on kind-low-level.  
when no pointers, lots of things are wrapped by built-in arrays, etc.

> **Runtime VM required.?**  
y. but compiled binary should also been provided, usually should be VM+JIT.

so we need be good at memory access.

1. Intermediate Bytecode Instructions define.
2. Manual Compilation Example and Checkout
3. Compilation Program
4. Execution Machine.

#### the string class

```
class string {
    u16* base;
    
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
```

#### expect bytecode.







# 21a33 ldv *_updates. <small style="font-size: 50%;">`Aug 16-22, 2021`</small>


### Mon. 16
```
stmtwhile compile.
basic instruction ldc.
astexpr.evaltype. TypeSymbol.
conditionbranch
classmember modifiers
namespace.
```

### Tue.
```
namespace singlestmt.
lite docs.
```

### Wed, Thu.
```
Lexer KEYWORD tokentype. 'new'.., borders.
Lexer INT, FLOAT, CHAR type. AST-Literal Int, Float, Char.
```
What's currently needed to do, is a string class impl.  
```
class string {
    array<ushort> value;  // ushort* base;
    
    int len();
    int find(string s, int from);
    string substr(int begin, int end);
}
```
对于string类，一个数组是必要的 用于存储字符。  
届时，数组应只是一个指针 而不是一个拥有实际大小的类型，因为string本身应只是一个指针 它的类型大小应该是固定的 不会因为字符内容的多少而改变。
> 字符串作为指针 这样的设定是合理的，因为字符串内容通常不小 栈上可能有危险 而放在堆里是比较合适的 
> 由于内容可能较大 因此创建与销毁的开销并不会有小定长对象那么敏感。另外使用体验繁杂度和一些空间优化上也存在好处</small>

> 但是顺便一说 作为另一个极端 矢量运算 vecs mats 的类型设计似乎在"相反"的方向。  
> 由于矢量的类型通常是很有限的 编译期可知，并且由于高密度运算与存储原因 效率极为敏感。因此类型定长 持有实际空间是极为必要的。  
> 但是有时在最'理想'情况下 我们只想设计一个vec原型 就能适用于所有vec2 vec3 vec4类 (通过填充类型参数`vec<3, float>`).  
> 真正做到这一点 似乎未必容易 要是采用运行时参数vec(3, typeof(float)) 那么功能上没问题 但效率上会冒汗，最佳效率的方式是采用所谓'元编程' 在编译期间将实现展开 `lenSq = sqrt(x*x y*y)`，而非运行时进行流控制 `lenSq = {float sum = 0; for (float f : v) sum+= f*f; ret sqrt(sum);}`  
> 然而 所谓'元编程' 在带来最佳效率的可能的同时 也会让代码变得晦涩难懂 脱离控制。

[comment]: <> (> by the way, in the opposite of a string&#40;as a char[] ptr&#41;,   )
[comment]: <> (> the vec and mats, wanted real spaced-type, means sth like `sizeof&#40;vec<3,float>&#41; == 3*sizeof&#40;float&#41;` which is tends compact, instead of a ptr.  )
[comment]: <> (> this might bring some other-side impl problems.)

```
class array<T> {
    T* base;
    uint len;
    
    T get(int i) {
        return mem.get<T>(addr(i));  // *(base+sizeof(T)*idx)
    }
    void set(int i, T v) {
        mem.set<T>(addr(i), v);  // 
    }
    
    @inline
    T* addr(int i) =>
        base + i*sizeof(T);
}
```
数组未必是最主要的，他像是一种'抽象'，然而实际上我们只是打算使用连续空间+指针。
> 数组通常意指 在连续空间上存放相同大小元素。在某些不设计指针的语言中，内置数组作为这种功能的包装。
> 然而即使有指针 也可以包装一个使用指针的数组类。

> 顺便一说。虽然C拥有指针，但仍然有语法层面的数组。不仅其数组访问是指针使用的语法糖，
> 数组声明 却可以在编译期间 根据常量值声明出对应类型大小.

> 然而 '不幸'的是, C#等语言的范型 并没有为常量类型提供可乘之机 可能是由于繁杂性的考量。因此无法直接做到类似cpp std::array<int, 123> 编译时类型数组类型大小的功能。  
> 但是有趣的是 Rust 似乎拥有 int字面量范型参数传入的功能 `const generics`


#### Pointers Impl

无论如何，目前只考虑内存访问(数组访问)，不考虑静态大小声明 '增强'范型 模板展开等事物。

> 解引用dereference 原始类型 vs 对象类型  
> 原始类型通常很纯粹 类型是静态的 只有数据值，而对象类型可能包含RTTI类型信息 及继承内容。  
> 然而cpp可以在可能的情况下 让对象类型也做到无额外消耗 无RTTI信息。这对于OOP可能有一点'激进'。  
> C#的struct对象 可以无rtti信息，系统的int float等原始类型也是由struct声明。普通对象则会有RTTI信息.  

然而 无论是struct primitive 还是objects，某种角度都可以概括为对象。他们有一块内存空间。  
我们需要做的，给出一个地址 找到那个空间的位置，给出一个类型 去根据类型来解析那块空间内的数据。

primitive struct的类型通常是静态的。然而为了达到一些高级oop特性 RTTI类型信息需要被存储在对象实例空间中。这会增加一个指针空间占用。  
因此 解引用的操作或许大致是这样:

static_types:  `mem.get_slice(addr, sizeof(typename))`   // 静态给出类型  
dynamic_types: `mem.get_slice(addr, get_rtti(addr).typesize)`  // 动态根据地址上RTTI指针 获取类型

```
void func(int j) {  // push (int)0     /* Common Stack Variable Store and Load */
    int i = 8;      // push (int)8
    int sum = i + j;// push [base+4]; push [base]; iadd;
    
    ptr p = &i;     // push base       /* Stack Variable Address */
    
    Entity* pe = new Entity;
    
    (*pe).i = 2;    
    // mov ebx, [ebp-16]  # eax==base, ebx== pe val.
    // iadd ebx, 8        # .i offset, ebx now == & (*pe).i
    // mov ebx, DWORD 2
    
    // load pe
    // push 8     # .i offset
    // iadd       # (*pe).i addr.
    // cpy_push 
    
    pe->i = 1;      // 
    
    (*pe).str->val = nullptr;
    (*pe).color.v = 10;
    
}
```

即使很多地方存在类似AST解释器之类的事情，但是在实际上 主要还是将抽象繁杂的事物 编译转化生成成一条条基本的指令。  
因此我们考虑的将是编译过程 转化成指令的过程与方法。
```
    # int j;
iadd esp 4    # esp address +=4. alloc space.

    # j = 1;
mov [ebp] 1   # put 1 into &ebp pointed space.

    # int* p = &j;


```

[comment]: <> (Artist Choice 012. Dynamic III Part 1. Deep Selection.)




### Fri


#### Dereference of UInt16 by pointers. for string char-array elements.  passing u16 around vars/funcs.

Use of Pointers:
```
char* p = ...;  // long -> a_heap_space.

char get_char_at(char* p, uint idx) => 
    *(p + idx*sizeof(char));
    
// register based x86 instructions.
mov  eax, [ebp+8]  # get  idx.
imul eax, 8        # calc idx * sizeof(char)
mov  ebx, [ebp]    # get  p
iadd eax, ebx
mov  ebx, [eax]
ret ->  ebx
// stack based x86 instructions
push *(stk)   // $p
  push *(stk+8) // $idx
  push 8
  imul
iadd
ld
// pt
```

ignore the array indexing, just direct load by addr

```
mov ebx, [eax]
// or stack based
push ptr
ld
```

? use rel pos/addr to get, or use absolute-str-addr.?

#### VM Exec-Arch, Stack-Based / Register Based.


### Sun

Compilation Instance.

```
class str_impl {  // real rand.
    ptr* base;
    
    char char_at(int i) {
        return *( base + i*sizeof(char) )
    }
    int len() {
        int i = 0;
        while (true) {
            if (char_at(i++) == 0)
                return i;
        }
        return -1;
    }
    int find(string s, int from);
    string substr(int begin, int end);
}

void main() {
    str_impl s = str_impl("some_str.");
    
    char c = s.char_at(1);
    int i = s.len();
    
}

```

```
main:
    new 'str_impl'
    store 0
    
    load 0
    iconst 1
    invokevirtual str_impl:char_at
    store 1
    
    load 0
    invokevirtual str_impl:len
    store 2
char_at:
    load 0
    getfield 'base'
    
    load 1
    iconst_typeof char
    imul
    
    iadd
    
    cpy_push 2  // sizeof(char)
```


### sn- pm6. why pointers are required.? stk-based or reg-based.?





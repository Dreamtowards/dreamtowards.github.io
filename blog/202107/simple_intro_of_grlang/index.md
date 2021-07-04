

# Basic Intro of Grlang<sup>(alias)</sup>. 
Posted on Jul 4, 2021.

Grlang is a programming language, intend to do High-Density Computation, 
fit for Large Program System. it's big effort on Flexibly, Consistent and Minimal Design.

*Characteristics:*
- Low Abstract Level
- Manual Memory Management at Default.
  - Heap GC can Enable as an Extra Extension.
- Minimal, Flexibly, Consistent.
- VM + InTime-Nat-Compile
- OOP but for Simple.



```

class ptr<T> {

    long addr;
    
    ptr(this.addr) {}
    
    T get() {
        return Memory.get(addr);
    }
    
    void set(T o) {
        Memory.set(addr, o);
    }

}

class Array<T> {

    final int length;
    final long _base_ptr;

    T get(int i) {
        _checkIdx(i);
        return Memory.get(_addrAt(i));
    }
    
    void set(int i, T o) {
        _checkIdx(i);
        Memory.set(_addrAt(i), o);
    }

    int _addrAt(int i) {
        return _base_ptr + sizeof(T)*i;
    }
    
    void _checkIdx(int i) {
        assert(i >= 0 && i < length);
    }
    
}

class Main {

    static void main() {
        Array<int> ls = new Array<>(10);
    
        Array<String> args = Runtime.getProgramArguments();
        
        args.get(0)
        
    }

}

```
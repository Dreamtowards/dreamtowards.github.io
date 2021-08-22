
## Functional

### Stack Alloc - Object Creation.

```
T obj = stackalloc T(..);  // MSCS.

T obj; 
T obj(..);
T obj = T(..); // Cpp

T obj = T(..);
```

### Type Cast

```
T(obj)
(T)obj
obj as T
dynamic_cast<T>(obj)
```

### Stack obj, Heap obj, Pointer obj. Representation.

```
// Cplusplus
T obj = T(..);  // Stack
T* obj = new T(..);  // Heap, Pointer.

// Java
T obj = new T(..);  // Fake Stack, Escape Analysis.
T obj = new T(..);  // Heap, Reference.

// MSCS
T obj = stackalloc T(..);  // Stack
T obj = new T(..);  // Heap, Reference.
```

Dereference `*expr` ? Address `&expr`

## Interface

### Program-Arguments

lot languages make program-arguments as an argument of the main-function.  
Java,C# are Force required, C is Optical required.

However I think this might deserve re-think about.

```
fn main() { println!( std::env::args() ); }
```

### Main Function, the return-type



## Syntax

### Scope End, the Representation

ruby, VB are use `end` keyword as a scope encloser.

### Varying Arguments

Varargs is a very convenient utility syntax sugar, a typical example is Java Varargs.

but sometimes its still have some problem in practice, its might caused by some violate of essential principles.

1. Problem1: Confuse
2. Not Clear Enough

Considerable solution: initialized_list<T> in cpp.

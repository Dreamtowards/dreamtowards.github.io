

# Think about _prog langs.

### Java

Java is a 'kind of' classic programming language. little conservative.  
the design and style is Consistent, which is 'valuable'.

but its seems not very focus on High-Performance Details.  
TypeErase-Generics `List<Integer>` is not very bearable,  
too much `new`, the high-density vector computation are worried.

- pros
  - the design & style. consistence.
  - the abstraction. uniform, simplified.
- cons
  - type-erase generics. not `List<int>` yet.
  - too much `new`. is dynamic-memory controllable.? escape-analysis really works.?
  - the old `Vector, Stack, Enumeration, StringBuffer` still in the stdlib.
  - abstraction cost. object header contains lot thing.
    

### C

C is great.  

- pros
  - detailed control.
- cons
  - confused type and name declaration, for example, `int* p` and `int[8] arr` are preferred than `int *p, int arr[8]`.  the `void (*funcptr_arr[2])(int, int)` declaration..
  - confused type. for instance, `unsigned long long int` type are allowed. 

### Cplusplus

Cpp is not too bad.

- pros
  - detailed controls
  - far more functionality than C.
  - pursuit of zero-cost-abstraction.
- cons
  - syntax and functionality are kind of messy.
    
### C#

this like another topic. the Microsoft are casually do some random thing. J#, F#.. and its always leaking of 'cultural heritage', see some of microsoft behavior/wording, the "IL", "CLR", really? does he think there is only his casual language in the world.? "Intermediate", "Common" ok whatever.

however, tho the MS behavior is sometimes is real bad, the C# is still useful in sometimes.


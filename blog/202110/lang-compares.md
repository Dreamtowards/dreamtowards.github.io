
### Primary
- C
  - \+ strength, low-level.
  - \- confusion(vardecl: name and type confused)  
- Cplusplus
  - \+ high expression ability, lot functionality, high precious controllable, low-level.
  - \- messy. chaos.
- Java
  - \+ good dev effective. kind-of-high-level. 
  - \+ simplicity, consistent design style. well-organization.
  - \- principal performance leak.
  - \- historical mistakes.
- C#
  - \+ several-good-techs. performance acceptable.
  - \+ 'direct', good-to-basically-use.
  - \- made from microsoft, kind of casual. not too much trust.
  - \- some design not very consistence.
- Rust
  - \+ several innovation inventions.
  - \- syntax..
- Dart
  - \+ good syntax design
  - \+ good using experience
  - \- not very strong yet.

### Secondary
- D
  - \+ kind-of-elegant in some aspect.
  - \- the language abstraction level sometimes is inconsistent. some design 'mistakes'.
  - Example: 'import std.stdio;' 1. the 'import' keyword, doesn't really import, but referring. the lang is kind-low-level, the difference should be noticed. 2. since imported 'std.stdio', all functions inside can be use. this causes code-confusion. C/Cpp are acceptable, they are macro-level-including. but there.. you simply dont know the 'writeln' top-level function where came from if u just looking at plain text code. and btw the name 'writeln' is kind of weird, 'write' is a low-level/common 'term', it can be literally 'anything'. but where it writes to? hm.. the actual behavior is 'print'..  
- Go
  - \+ several new ideas.
  - \- syntax..
- Kotlin


### Et cetrals
- Pythen
- PHP



### Points:

#### Welled Organization.
Bad: where 'println' came from?. kind-of lost control.
```
using stl.io;
...
println("..");
```
Better: Well-Controllable.
```
using static stl.io.console.println;
...
println("..");
```
or more precious referring expression.
```
using stl.io.console;
...
console.println("..");
```

Every 'public' variable/function should be 'only' in a class.

Namespaces are just only stand for Organizes/Arranges Classes.

#### Referring 'static-entities'.
Look at these three kind of referring a 'static-entity'.
```
// cpp:
std::vector<int> l;
std::to_string(..);
```
Cpp is well. these namespaces are always just 1-2 levels, we just
don't talk about cpp's entity-organization right now, but the think of design is right, 
`::` operation takes more attention than `.`, it's deserved since level very few.  
However, the namespaces in cpp is like a "Domain", it's always just a global-identify-diff, but not structure-relation-organization anyway.

```
// rust:
use std::collections::hash_map::HashMap;
```
Rust, hmm, smells some abuse of attention..  
in rust, modules are meanness organized, thus there a lot of levels.  
but use of '::' operator seems took too many attentions.

```
// java:
import java.util.concurrency.Lock;
```
Java, the attention seems ok. but might have some literal un-clear.  
We talk 'static-entity-referring', they are just 'symbols', not runtime-actual-operation at all.
Since use of `.` dot operator may cause confuses with its primary usage MemberAccess(a runtime-operation (ptr_offset/dat_slice)), its might not distinct enough.

```
import java/util/concurrency/Lock;  // feel not flexiable enough. rigid.

import java:util:concurrency:Lock;
import static java:util:concurrency:Lock::VALUE;

vec::string(abc);

java:util:concurrency:Lock::VALUE;

```

#### Functions: static, instanced, virtual

We have functions, they originally are all 'statically'.  
but then, as OOP comes in to play, Instanced-Functions once become the mainstream.  
in Cpp, Java, Csharp, in a class, static functions always needs "static" keyword to explicitly declare.

this seems not a big deal. but, the OOP, can u really trust it.? it's a kind of high-leveled stuff. static-functions are Foundational which can obviously relies on, but OOP, is it really deserve get the 'mainstream' default function.?

OOP mainstreamed: 
```
// class Animal { ...

static void main() {
  ...
}

void eat() {
  ...
}

virtual void walk() {

}
```

Balanced Persistence:
```
// class Animal { ...

void main() {
  ...
}

void eat(Animal* this) {
  ...
}

virtual void walk(Animal* this) {
  ...
}
```
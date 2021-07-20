
# Grlang Q&A. in Jul 2021. midmo.
Posted on 20210715. Tags: Grlang.

# Goals

### Applicative Goals

- Hi-Performance, Hi-Functionality, Flexibly, Compact, Powerful - GUI Framework.
- Hi-Performance Vector Computations.
- Hi-Performance Rendering Dispatches.

### Focus On

- Complicated, Large  System.
- Minimise Language-Design. Less-SpecialFeature, More-Flexible, More-Compact, More-Reliable. Is-Extensible.

### Dev Behavior

- Detail Specified
- Big-Refac may occur on Big-Version-Publish.

### Standard Library

- <small>Media Format</small>
  - <small>[image]</small> `png`, `jpg`, `gif`
  - <small>[font]</small> `ttf`
  - <small>[model]</small> `wavefront-obj`
  - <small>[audio]</small> `ogg`, `mp3`
  - <small>[archive]</small> `zip`
  - <small>[struct]</small> `json`, `xml`, `yaml`  ?properties, ini
  - <small>d</small>
    - <small>[encode/decode]</small> `base64`, `url`
    - <small>[digest]</small> `md5`, `sha256`
    - <small>[charset]</small> `utf8`
    
- <small>IO</small>
  - <small>Networking</small>
    - `Socket`
    - `HttpClient`, `HttpServer`
    - `WebSocketClient`, `WebSocketServer`
  - `File`

[comment]: <> (- X Library)
[comment]: <> (  - logging)
[comment]: <> (  - profiler)
[comment]: <> (  - opengl)
[comment]: <> (  - openal)
[comment]: <> (  - glfw)
[comment]: <> (  - desktop-window / mouse / keyboard)
[comment]: <> (  - desktop-robot)







# Global Organization

### Namespace or Package?

Package. because Clearness of organization.

Namespace always meant a big scope. in programming history C++ and even in C#.

Even though the Namespace can have multi-level nesting, but its always still not "flexible" / "clear-details" enough compared with Package.

<small>*// Syntax on the Context.*</small>
```
namespace std { 
    ...
}
```

```
package std;

...
```

#### Pro and Cons:
- Pros of Namespace:
  - **No Source-File Concept**: Namespace(s) can be defined without concept of Source-Files
- Cons of Namespace:  
  - **Not Clear Defining**: Multiple "source-files" often/always using a same namespace, even in different source-folders.  
    <small>Chaos will be caused when only just use namespace to refer a thing. (not C++ because required `#define <...>`, but C# because only have `using ...`)</small>
  - **Syntactic Chaos**: More Syntactic Top-Level Nesting.


- Pros of Package:
  - **Well Defining**: Source-Files and Source-Folders are Defined Clear.
  - **Syntactic Clear** No-More Nesting.
- Cons of Package:
  - **Required Source-Files/Folders Concept**
  - Package name always not short, it's good for human to read and classification, but a little meaningless for unique-identifying.


### Have Global Variable or Function.?

No. All things are organized by class(es).

Its more clear, safety.





# OOP

## About OOP.?

The OOP, it's a convenient and well-used 'tool', it's conducive to modeling.

But it maybe a bit too high-level. Sometimes OOP just out of essences - Operations and Data, the functions and structs in C.
The OOP involves Inheritances, Instances, .. which is kind of high-level.

We try to make OOP as an extra-mainly feature, but keep Essential-Design in mind.

## Inheritances. single or multiple.?

```
class EntitySheep : EntityCreature {
    ...
    Objects.getInstanceLayer<EntitySheep>(this).
    InnerClass.this
    class Entry {
        this
    }
}

```

## Class Member. about Function, Variable.?

```
class TestEntity {

    static int TYPE_N;

    String name;
    
    function<void> update = () {
    
    };
    
    void save() {
    
    }
    // static function<void, TestEntity> save;

}
```

## How present static member in a class.?

## Setters and Getters .?

no field-access-style Getter/Setter. because it's actually functions. don't hide hidden-danger behind.

the field-access-style Getters have some cool tricks, like in vector access: position.xz. but its still hidden-dangers.

Field Modify Monitor are available by using the stdlib.

    class Test {

        @OnModified(this.onNameChanged)
        String name;
    }

## Function Overwrite.? the func-signature

the Function is actually a Variable, but just like a sort of instructions.
the function Parameters and Return-Type is just variable type.

but variables-key is its name, not include the type. 
the problem is, since this 2 reason, the function can't been Overwritten by same name even if have different params/ret-type.

the solution not available yet.

## Function Default/Optical Arguments.?

## Function VarArgs.?

## Implicit Constructors.?

## How visibility control? default public or private.? use public/private/protected keywords.?

## Expr-Operators Override.?

## Inner Classes.?


# Programming 

### Explicit Heap / Stack alloc.?

### About Null Control.?

### throw Exceptions and try-catch .?




# Details

### How about the "def"/"var" + ":*Type*" type-carry defining design.?

### Is ";" required end of statement.?

### "a ? b : c" Tri-Con Expr. or "if (a) b else c" If-Else-Expr.? 

### language-level assert.?

### Have Language-Level Serialization.?

## Keyword details.

### "not"/ "and" / "do" / "then" / "end" keywords.?

### "self" or "this".?

### "var" or "auto" or "let" / "val".

### for-foreach: ":" or "in" or "of" / "as"
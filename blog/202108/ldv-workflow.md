
```

namespace mynamspc.special.spc {
    
    class MyType {
        
        int num = 2;
    
        @entrypoint
        static void main() {
            
        }
    
    }
    
}


```


Schedule:

```
langdev-libs
  stl.lang.string               | 2021 aug20 - sept24
  stl.vector linearmath-library | 2021 dec
  stlx.opengl opengl-binding.
  
langdev-features
  pointers 2021 sept10-20
  heap-object-creation
  refied-generics
  const-generics
  lambda
  
graphics
  vr interface
  vulkan
  gui system
  
physics
  rigidbody-dynamics
  collision-detection
  constraint-solve
  
surface-systems
  
microcraft
gymcontroller



```

```mermaid

gantt 
axisformat %Y-%m-%d

SECTION Syntax Parse
  Lexer :done, 2021-08-10,6d
  Parser: done, 2021-08-14,5d
SECTION Symbol Entering
  EnterSymbols: 2021-09-30,20d
SECTION Code Generation
  CodeGen: 2021-10-10,40d

```

Workflow:
```mermaid

graph TD
  
  subgraph Syntax Parsing
    Lexer -->  |Token Stream| Parser 
  end
  Parser --> |AST TreeNode| EnterType[Enter Types]
  
  subgraph Symbol Entering
    EnterType --> EnterSymbols[Enter Symbols]
  end
  EnterSymbols --> |Attributed AST| CheckFlow[Validate Flow]
  
  subgraph Semantic
    CheckFlow --> LegalAST[(Legal AST)]
  end
  LegalAST --> Desugar
  Desugar --> CodeGen[Code Gen]
  
  subgraph Code Generation
    CodeGen
  end
  CodeGen --> CompiledBinaryInstructions


  LegalAST --> Printer
  LegalAST --> Translator
  subgraph Misc
    Printer
    Translator
  end
```


Components:
```mermaid

classDiagram

  class Lexer {
    +String source
    +int readidx
    +read(TokenType expected, bool skip) Token|null
    +next() Token
    +next(TokenType expected) Token
    +trynext(TokenType expected) Token|null
    +nexting(TokenType expected) bool
    +selnext(TokenType... expecteds) Token|null
    +peek() Token
    +peeking(TokenType expected) bool
    +selpeeking(TokenType... expecteds) bool
  }
  
  class Parser {
    +parseCompilationUnit(Lexer lx) AST__CompilationUnit
  
  }

```
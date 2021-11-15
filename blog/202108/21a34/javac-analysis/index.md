
# JavaCompiler processunderstanding.
[comment]: <> (Posted on Aug 23, 2021. Tags javac)

## Process Overview

1. Lexical Resolve, Syntax Parsing.
2. Classes SymbolTable Entering.
3. Annotation Processing
4. AST SymbolTable / Attribute Completing.
5. Special Semantic Analysis.
6. Desugar Transformation
7. Code Generation.

## Code Lookup

### Entrance

```java
// jdk.compiler/com.sun.tools.javac on jdk16.  
// Main entry point. Original:
public static void main(String[] args) throws Exception {
    System.exit(compile(args));
}

// Main::main(String[] args): void
//   -> Main::compile(String[] args): int
//     -> main.Main.compile(String[] args): Result
//       -> main.Main.compile(String[] argv, Context context): Result
// Simplified:
public Result compile(String[] argv, Context context /* = new Context(); */) {  

    Arguments args = Arguments.instance(context);
    args.init(CommandLine.parse(List.from(argv): List<String>);

    // init JavaCompiler
    JavaCompiler comp = JavaCompiler.instance(context);

    comp.compile(args.getFileObjects(), args.getClassNames(), null, List.nil());

    return (comp.errorCount() == 0) ? Result.OK : Result.ERROR;
}
```

### Main Process

```java
// main.JavaCompiler.compile(Collection<JavaFileObject> sourceFileObjects, Collection<String> classnames, Iterable<? extends Processor> processors, Collection<String> addModules): void;
// Simplified:
public void compile(Collection<JavaFileObject> sourceFileObjects,
                    Collection<String> classnames,
                    Iterable<? extends Processor> processors,  // null
                    Collection<String> addModules)             // List.nil()
{

    // initProcessAnnotations(/*processors,*/ sourceFileObjects, classnames): void;  // init this.procEnvImpl

    // These method calls must be chained to avoid memory leaks
    processAnnotations(
        enterTrees(
            parseFiles(sourceFileObjects): List<JCCompilationUnit>
            /*initModules(..)*/
        ): List<JCCompilationUnit>,
        classnames
    ): void;

    generate(
        desugar(
            flow(
                attribute(
                    todo: Queue<Env<AttrContext>>
                ) /* : Queue<Env<AttrContext>> */
            ) /* : Queue<Env<AttrContext>> */
        ): Queue<Pair<Env<AttrContext>, JCClassDecl>>
    ): void;

}
```

### 1. Lexical Resolve, Syntax Parsing.
EntryPoint: `parseFiles(fileObjects): List<JCCompilationUnit>`

```java
// JavaCompiler.parseFiles(Iterable<JavaFileObject> fileObjects): List<JCCompilationUnit>
//   -> JavaCompiler.parseFiles(Iterable<JavaFileObject> fileObjects, boolean force): List<JCCompilationUnit>
//     ->  JavaCompiler.parse(JavaFileObject filename): JCTree.JCCompilationUnit
//       ->  JavaCompiler.parse(JavaFileObject filename, CharSequence content): JCCompilationUnit
// Simplified: 
public List<JCCompilationUnit> parseFiles(List<JavaFileObject> fileObjects) {
    List<JCCompilationUnit> l = new ArrayList();
    for (JavaFileObject sourcefile : fileObjects) {
        Lexer lxr = new Scanner(new JavaTokenizer(sourcefile.getStringContent()));
        Parser psr = new JavacParser(lxr);

        JCCompilatioinUnit tre = psr.parseCompilationUnit();
        l.add(tre);
    }
    return l;
}
```

### 2. Classes SymbolTable Entering. 
EntryPoint: `enterTrees(List<JCCompilationUnit> roots)`

```java
// JavaCompiler.enterTrees(List<JCCompilationUnit> roots): List<JCCompilationUnit>
//   -> comp.Enter.main(List<JCCompilationUnit> trees): void
//     -> Enter.complete(List<JCCompilationUnit> trees, ClassSymbol c /* = null */): void 
//       -> Enter.classEnter(List<T extends JCTree> trees, Env<AttrContext> env /* = null */): List<code.Type> /*discard*/
//         -> Enter.classEnter(JCTree tree, Env<AttrContext> env /* = null */): Type /*discard*/
// Abstracted:
Type classEnter(JCTree tree, Env<AttrContext> env /* = null */) {
    tree.accept(this);
    return this.result; /*discard*/ // like: Enter.enterTree(tree): Type;
}
```

Only for: Class, CompilationUnit, TypeParameter, ModuleDef

### 3. Annotation Processing 
EntryPoint: `processAnnotations(List<JCCompilationUnit> roots ..)`

```java
// JavaCompiler.processAnnotations(List<JCCompilationUnit> roots, Collection<String> classnames): void
// Simplified:
public void processAnnotations(List<JCCompilationUnit> roots,
                               Collection<String> classnames) {

    List<ClassSymbol> classSymbols = List.nil();
    List<PackageSymbol> pckSymbols = List.nil(); 

    for (String clname : classnames) {
        Symbol sym = resolveBinaryNameOrIdent(clname);
        if (sym.kind == PCK) pckSymbols.add((PackageSymbol)sym);
        else classSymbols.add((ClassSymbol)sym);
    }

    procEnvImpl.doProcessing(roots, classSymbols, pckSymbols/*, deferredDiagnosticHandler*/);

}
```

Annotation processing is a characteristic feature for Java, it's introduced in jdk5. `Pluggable Annotation Processing API (JSR269)`

In this phase, AnnotationProcessor(s) will be invoked, for Process/Build new source code etc.


### 4. AST SymbolTable / Attribute Entering.
EnterPoint: `attribute(Queue<Env<AttrContext>> envs)`

```java
// JavaCimpiler.attribute(Queue<Env<AttrContext>> envs): Queue<Env<AttrContext>> /* passing */
//   -> JavaCimpiler.attribute(Env<AttrContext> env): Env<AttrContext> /* passing */
//     -> comp.Attr.attrib(Env<AttrContext> env): void;
// Original:
public void attrib(Env<AttrContext> env) {
    switch (env.tree.getTag()) {
        case MODULEDEF:
            attribModule(env.tree.pos(), ((JCModuleDecl)env.tree).sym);
            break;
        case TOPLEVEL:
            attribTopLevel(env);
            break;
        case PACKAGEDEF:
            attribPackage(env.tree.pos(), ((JCPackageDecl) env.tree).packge);
            break;
        default:
            attribClass(env.tree.pos(), env.enclClass.sym);  // -> ... attribClassBody(env, c);
    }
}
```

### 5. Special Semantic Analysis
EntryPoint: `flow(Queue<Env<AttrContext>> envs)`

```java
// JavaCompiler.flow(Queue<Env<AttrContext>> envs): Queue<Env<AttrContext>> /* passing successed */
//   -> JavaCimpiler.flow(Env<AttrContext> env, Queue<Env<AttrContext>> results): void
//     -> comp.Flow.analyzeTree(Env<AttrContext> env, TreeMaker make): void
// Original Commented:
public void analyzeTree(Env<AttrContext> env, TreeMaker make) {
    new AliveAnalyzer().analyzeTree(env, make);   // reachable statements checkout
    new AssignAnalyzer().analyzeTree(env, make);  // 1. ensure variables are assigned when used, 2. ensure no final variable assigned more than once. 
    new FlowAnalyzer().analyzeTree(env, make);    // checked exceptions analysis.
    new CaptureAnalyzer().analyzeTree(env, make); // checks every local variable reference from a lambda body/local inner class is either final or effectively final. 
}
```

### 6. Desugar Transformation
EntryPoint: `desugar(Queue<Env<AttrContext>> envs): Queue<Pair<Env<AttrContext>, JCClassDecl>>`

```java
// JavaCompiler.desugar(Queue<Env<AttrContext>> envs): Queue<Pair<Env<AttrContext>, JCClassDecl>>
//   -> JavaCompiler.desugar(final Env<AttrContext> env, Queue<Pair<Env<AttrContext>, JCClassDecl>> results /* putting succeed */): void
//     -> ... (? extends TreeTranslator).translateTopLevelClass(Env<AttrContext> env, JCTree cdef, TreeMaker make)
// .
```

### 7. Code Generation
EntryPoint: `generate(Queue<Pair<Env<AttrContext>, JCClassDecl>>  queue): resultFiles`

```java
// JavaCompiler.generate(Queue<Pair<Env<AttrContext>, JCClassDecl>> queue): void
//   -> JavaCompiler.generate(Queue<Pair<Env<AttrContext>, JCClassDecl>> queue, Queue<JavaFileObject> results): void
// Simplified:
public void generate(Queue<Pair<Env<AttrContext>, JCClassDecl>> queue, Queue<JavaFileObject> results /* = null */) {
    for (Env<AttrContext> env, JCClassDecl cdef : queue):     

    JavaFileObject file;
    if (sourceOutput) {
        file = printSource(env, cdef);  // new tree.Pretty(outfile, OUT_SOURCE).printUnit(env.toplevel, cdef);
    } else {
        file = genCode(env, cdef);
    }
    if (results  != null && file != null) results.add(file);
}

JavaFileObject genCode(Env<AttrContext> env, JCClassDecl cdef) {
    if (gen.genClass(env, cdef) && (errorCount() == 0))
        return writer.writeClass(cdef.sym);  // -> jvm.ClassWriter.writeClassFile(OutputStream out, ClassSymbol c): void
    return null;
}

// jvm.Gen.genClass(Env<AttrContext> env, JCClassDecl cdef): boolean
// Simplified: 
public boolean genClass(Env<AttrContext> env, JCClassDecl cdef) {
    cdef.defs = normalizeDefs(cdef.defs, cdef.sym);

    for (JCTree d : cdef.defs) {
        genDef(d, localEnv);  // tree.accept(this, argenv);
    }
    return nerrs == 0;
}
```

### Overall Review

```
// jdk16/jdk.compiler com.sun.tools.javac.

Main.main(args): void -> Main.compile(args): int
    -> main.Main.compile(args): main.Main.Result -> main.Main.compile(String[] args, util.Context context): main.Main.Result 
        -> main.JavaCompiler.compile(List<JavaFileObject> sourceFileObjects, List<String> classnames ..): void {
    
    initProcessAnnotations(sourceFileObjects, classnames): void;  // setup sth.
    
    processAnnotations(
        enterTrees(
            initModules(
                parseFiles(
                    sourceFileObjects
                ): List<JCTree.JCCompilationUnit>
            ): List<JCTree.JCCompilationUnit>
        ): List<JCTree.JCCompilationUnit>,
        classnames   
    ): void;
    
    generate(
        desugar(
            flow(
                attribute(
                    todo: Queue<Env<AttrContext>>
                ): Queue<Env<AttrContext>>
            ): Queue<Env<AttrContext>>
        ): Queue<Pair<Env<AttrContext>, JCClassDecl>>
    ): void;
    
}  
```
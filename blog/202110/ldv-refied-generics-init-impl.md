
## ldv. ReifiedGenerics. init_impl. classes.

For Generics, there have 2 type in schedule. ReifiedGenerics and ConstGenerics. where the former is TypeParameter, latter is ConstValueParameter.

this time, is classes ReifiedGenerics.

Problem1. generics-instances should be 'build' on compile-time(static) or runtime(VM).?
 - on compile-time. cuz this is more foundational. later to considers on-runtime.

### Approaches:

There is a Generics-Prototype.
```
class vec2<T> {
    T x;
    T y;
}
```
{"vec2" SymbolClass} will be defined on the scope, but nothing more, members will not be resolves at all.

Then, a Generics-Instance appear.
```
sizeof(vec2<int>);
```
we compile it only for Arguments-Filled Generics-Instance, not for Generics-Prototype.

`vec2<int>` there is a Expr-AST.  
`AST_Expr_GenericsArgumented{AST_Expr type, AST_Expr[] args}`

everytime we resolve an AST_Expr_GenericsArgumented, we check is that Generics-Instance exists.

```
void visitExprGenericsArgumented(AST_Expr_GenericsArgumented a, Scope p) {
    visitExpr(a.getTypeExpr(), p);
    for (AST_Expr garg : a.getArguments()) {
        visitExpr(garg);
    }
    
    SymbolClass proto = a.getExprSymbol().getTypeSymbol();
    SymbolClass s = lookupGenericsInstance(proto, a.getArgumentsSymbols());
    if (s == null) {
        // define.
        visitStmtDefClass(proto.astclass, proto.getSymbolTable().getParent(), gargs);
    }
    a.setExprSymbol(s);
}
```
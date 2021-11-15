

我收到了 2/3 项信息(1(2), 3)!

第一项信息中 很认同您/你说的SyntaxParse和SemanticAnalysis阶段分开，实际上我也是这样做的 (目前处于*SyntaxParse阶段之后的 **SymbolEntering&Semantic阶段***) (sry好像没有明确说明阶段上下文当时..)

我没有当时就回复 而是过了两天.. 因为期间我在梳理一些问题 做一些尝试 希望准备好 能让我的消息更有效一点。不知道您/你对此的看法 是否希望我立刻回复。

最后 那个原问题经过梳理和尝试后 似乎有了较为理想的解决方法(解决方法在最下面).  

### 正文
#### 论点: 若AST上不装饰Symbol

第二项信息中 您/你知道我的上下文了!.(Semantic)

我从中得到的信息是: *"可能不用往AST上添加符号信息 因为只需要验证 或者即时发给别的需要用的地方"*  
-&nbsp;但是我正在考虑这样是否可行 (i.e. 要是AST上没有挂着Symbol信息)

在Semantic完之后 AST树仍然需要被遍历: 打印信息 / 生成代码 / 解释执行... 届时可能常需对应AST的相关Symbol 那该怎么办/如何获取呢

例: 对以下语句 生成指令代码

```
SomeType name1 = new SomeType();
```

```
void compileStmtVarDecl(AST_Stmt_VarDecl a, CodeBuf buf) {
    buf.defineLocalVariable(a.getName() /* <String> Variable-name "name1" */, 
                            ..?         /* <int>    Type-size.     sizeof(SomeType)  */);
    // 问题出现了: 此时需要用到 VarDecl的类型的符号 用来获取类型大小(为了给局部栈定义空间)
    // 那 如何获取类型符号呢 我之前的做法是 a.getTypeSymbol().getTypesize(). (VarDecl中存入符号)
    ...
}
```
我想到的方法
1. 在每个AST中 存放一个 Scope对象，表示AST所在的作用域。然后在遍历AST时 根据AST信息 在Scope里进行查找.  
   上例中: `(a.scope.resolve(a.getName()) as VariableSymbol).getTypeSymbol().getTypesize()`  
   但是我觉得这个方法缺点是 时间效率被浪费了一点 操作也复杂了一些
   
小节结论: 要是AST中不存储相关Symbol信息 那么之后遍历时如何快速直接的获取对应Symbol.? 好像个人还没想到一个较好的方法。

<br><br> 
### 原问题: <small>对于 表达式的*返回类型符号* 如何区分其是'字面类型'(pkg.to.MyType) 还是'实例类型'(new MyType())呢</small>

可能是我理解力不够好.. 我还没有得知这个问题的答案。

##### 问题再次表述:  
据说有一种传统，在`AST_Expr`类中，存放一个`TypeSymbol evaltype`类型符号 用于表示*表达式的返回类型*。 - "一次解析 到处直接取用"  
> (TypeSymbol 接口表示: ClassSymbol(自定义类) 和 BuiltinTypeSymbol(int等内置类型)

但是问题出现了!:   
当一个表达式的语义为 *"字面量类型"*(例`pkg.to.MyType`) 或 *"实例类型"*(`new MyType()`)时，他们的返回类型符号 都是同一个TypeSymbol对象，因此无法确定他到底是 实例对象 还是 字面类型 了!!!........

例:
```
MyType()      // 创建实例 (省去了new关键字
myVariable()  // 调用变量的 "(..)"操作符
myFunction()  // 调用原生方法 "invokespecial nnn.myFunction"
```
他们的AST结构都是一样的，但是实际行为却完全不一样。  
为了区分他们 似乎只能根据其Symbol来判断  
然而前两者的符号是一样的(ClassSymbol) 因此由于无区别 无法判断

[comment]: <> (AST_Expr_FuncCall &#40;)
[comment]: <> (AST_Expr expr: AST_Expr_Identifier&#40;"MyType或myFunction或myVariable.."&#41;,)
[comment]: <> (AST_Expr[] args: [])
[comment]: <> (&#41;;)
```
void compileExprFuncCall(AST_Expr_FuncCall a, CodeBuf buf) {
    Symbol s = a.getExprSymbol();  // 表达式 返回值类型 的符号

    if (s is ClassSymbol) {
        if (isInstance(s as TypeSymbol)) {  // !!?? 如何判断是否实例..
            // invoke variable "()" operator.
        } else {  // assert(isLiteralType(s));
            // new object.
        }
    } else if (s is FunctionSymbol) {
        // #invokespecial (s as FunctionSymbol).getQualifiedName();
    } else
        throw UNSUPPORTED;
}
```

更离谱的例子:
```
MyType()    // 创建对象
MyType()()  // 调用刚创建的对象的"()"操作符
```
由于符号都是ClassSymbol 因此上面无法判断 到底应该是创建对象 还是调用操作符, 最荒谬的情况下 有可能第二行创建了两次对象.


[comment]: <> (- 顺便一说的小疑惑: 我在怀疑 AST_Expr 存的到底应该是 "返回值类型 的符号" 还是应该是 "表达式本身所指 的符号")
[comment]: <> (  - 要是前者的话&#40;存表达式返回类型 的符号&#41;，那么益处是 明确 其类型直接就是TypeSymbol了&#40;类型符号&#41; 但是坏处就是 表述有限制: 不可以表述其他符号&#40;NamespaceSymbol&#40;转赋null&#41;, FunctionSymbol&#40;赋ret-type&#41;, VariableSymbol&#40;赋var-type&#41;..&#41;)

### 解决的思路

?**细节是什么时候丢失的**?

在解析变量引用时(Identifier, MemberAccess) 将表达式的符号赋为VariableSymbol.getType(): TypeSymbol了.. 因此丢失了变量信息 只存了类型信息。

?**为什么要这样做? 为什么要丢失这个信息 而只返回类型符号**?

因为 好像有一些人这样做，他们似乎是为了统一接口，以便判断类型时更加方便 无需转换。  
说实话我后来感觉 不应该这样。因为就算接口统一了 但实际上他们还是有很根本但不同 很多时候都必须针对性的对待 因此也没必要省这点转换的'方便'了。否则会有点含糊其辞 混乱了 而且丢失细节.

?**AST_Expr中 应该存`TypeSymbol evaltype` 还是 `Symbol exprsymbol`**?

前者 好像是一些人的做法 好像是为了统一方便的取用 得知类型。  
但是我仍然在使用后者 因为他确切的表示了对应的符号 更根本 更明确，而不是某种经过高级转换后的类型结果。  

之所以选择后者 有一个硬性原因: 我需要让有的表达式存NamespaceSymbol或FunctionSymbol，而他们恰好不属于TypeSymbol。因此我必须提高宽度以容纳他们。

但是我后来几乎完全坚定选择后者了。因为前者唯一的'优点'(方便统一)似乎不算优点了(含糊 缺失细节 看上去统一但实际上却不一样 含有隐患): 因为就算是类型判断 也应该通过明确的方式获取类型(是变量 就显式获取变量的类型 是字面类型 就直接拿取) 他们之间是有区别的 不当的丢失细节可能会含糊其辞 甚至出错

?**后者的问题: 即使存储确切的符号 那么无变量名的零时实例(`new sth()`,`3+4`) 又应该怎样表示**?

这似乎是个问题 他其实当时有点让我迷惑。难道应该再来一个TemporaryInstanceSymbol吗.. 但是每个Symbol都是有name的呀。

其实我现在也没想出来。但是目前是使用 VariableSymbol并使用null name。因为
1. 他们的结构是一样的 包含类型，
2. 他们都是实例 代表的事物/行为应是一样的 应该使用相同的符号(徒增额外的符号无意义(也无法体现额外细节) 增加判断繁杂度)
3. 就算VariableSymbol的name为null 似乎也没什么影响。  
   (零时变量实例 不定义到作用域 不会用到name 只是挂在AST_Expr上做符号判断)

?**现在的相关具体主要做法是怎样的.?**?

现在已经可以判断他们('int' vs. '3+4')的区别了 并且很明确 更安全少隐患 更可靠 适用性更根本宽广。
1. AST_Expr 中存的是 其明确对应的 Symbol. (不应是某种转换后的Symbol/TypeSymbol之类的)
2. AST_Expr中 提供`getSymbol()`来获取其符号.  
   也提供几个工具方法 `getTypeSymbol() => (TypeSymbol)getSymbol()`(仅适用于表示字面类型符号的表达式), `getVarTypeSymbol() => ((VariableSymbol)getSymbol()).getType()`(仅适用于表示变量实例符号的表达式)以更方便的使用。

用例1 - 明确判断:
```
Code:
myVar1 instanceof MyType

Analyzer:
void visitExprInstanceOf(AST_Expr_InstanceOf a) {
    // 填充符号表时 就明确了是变量还是字面类型的信息, 提取时也精确 不混淆. 
    TypeSymbol exprType = a.getExpression().getVarTypeSymbol();  
    TypeSymbol destType = a.getTypeExpression().getTypeSymbol(); 
    // 要是以前那种混淆的方法的话 是这样的: (填充符号时 全转换成TypeSymbol
    // exprType = a.getExpression().getEvalTypeSymbol();
    // destType = a.getTypeExpression().getEvalTypeSymbol();
    // 由于缺失了变量/字面类型信息 因此 instanceof 双方甚至可以错误地互换
    
    boolean exprResult = isSubclass(exprType, destType);
}
```
用例2 - 上面"原问题"小节 问题用例的解决方法

```
Code:
MyType()    
myVariable()
myFunction()

Analyzer:
void compileExprFuncCall(AST_Expr_FuncCall a, CodeBuf buf) {
    Symbol s = a.getSymbol();
    
    if (s is ClassSymbol) {
        // new MyType()
    } else if (s is VariableSymbol) {
        // myVariable.operator()
    } else if (s is FunctionSymbol) {
        // #invokespecial myFunction()
    } else 
        // ILLEGAL..
}

```

至此 似乎完美解决...

现在看上去 好像并不是那么迷惑 甚至有点感觉理所当然。但是之前 却很迷惑，当时想了一个下午 多天，列了多种草稿 举例分析问题 但是还是很迷惑 被困在局部。

可能这个问题并不一定很难 但是当时线索有点没摸到 有点缭绕。也有可能也没有完全解决。但目前似乎较为ok..
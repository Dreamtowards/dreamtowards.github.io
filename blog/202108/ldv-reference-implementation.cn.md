

### ldv. Reference Implementation. CN

#### 基本

```
mytype o1 = mytype();

int p = reference(o1);
```

由于变量 'o1' 是一个name-available的VariableSymbol, 即 一个lvalue.
因此 第二行的o1引用 实际上会put出一个指向其变量的地址。

在此 实际上reference操作，并没有带来一个额外的操作。  
他只是表述一种语义类型 该如何处理对应对象/数据。就像某位U.S.DEV所说 "reference只是一个动词".

```
mytype o2 = o1;
int p = reference(o1);
```
在这两行中，右边的内容 从运行时数据的角度上 数据几乎是相同的 - 都是一个指向o1变量的指针。  
只不过前者 在赋值时 由于右边类型是mytype(组合数据类型) 因此实际操作是 复制一块数据: 从右边的地址 到左边的地址 (复制长度为类型大 `sizeof(mytype)`)  
而后者 在赋值时 由于知道右边的类型是`int`(内部指针的类型) 而实际上运行时数据也是一个`int`(o1的地址) 因此就'直接当做int来赋值了'.  
// 注 这里把指针类型阐述做int 是为了方便理解。实际上指针通常是无符号的 且宽度不小。

#### CPP中的 连环套 - 解引用-引用-解引用-...

```
mytype _o1 = *(&(*(&o1)));
```
CPP允许这样的语法。为什么这样是可行的.?是如何工作的.?会造成细节丢失吗.?会有额外运行时性能损耗吗.?

**?是如何工作的.?**
```
Code /Expression:
  *(&(*(&o1)))

Semantic:  

void visitReference(AST_Reference a) {
    auto expr = a.getExpression();
    visitExpr(expr);
    
    VariableSymbol s = a.getSymbol();
    assert s.getSimpleName() != null;  // make sure there is a lvalue.
    
    a.setVarTypeSymbol(_PTR<expr.getVarTypeSymbol()>);
}

void visitDereference(AST_Dereference a) {
    auto expr = a.getExpression();
    visitExpr(expr);
    
    TypeSymbol s = expr.getVarTypeSymbol();
    assert s is _PTR;
    
    a.setVarTypeSymbol(_GetPointerType(s as _PTR));
}

Generation:

void visitReference(AST_Reference a) {
    auto expr = a.getExpression();
    visitExpr(expr);
    
    // nothing more. the point is symbol-type.
}

void visitDereference(AST_Dereference a) {
    auto expr = a.getExpression();
    visitExpr(expr);
    
    // also. nothing more.
}


void visitAssignment(AST_Assignment a) {
    
}
```

Semantic阶段，只是递归解析符号 最内部的'o1'先被解析，随后外部的引用/解引用只是切换类型。

Generation阶段，传递表达式的编译 但是不做额外工作。*'他们的确是动词'*。要点是符号 根据符号类型而不同的其他操作。

由于只是符号类型的转换。因此不会丢失细节 没有运行时损耗。


#### 单独存储指针

```
int p = reference(o1);

mytype _o = dereference<mytype>(p);
```
第一行 结果符号是rvalue 复制过去。
第二行 dereference后 仍然是lvalue(..?) 因为有地址 (但是没有变量名.?!)

#### 如何判断一个变量 是lvalue.?

"一个lvalue是一个有名称的变量" 这很好理解。但实际上或许未必如此。

lvalue指一个有实际地址的变量。"有名称的变量"总是有地址。但`*ptr`没有名称 却也有地址..



#### dereference, for arg: rvalue / lvalue

```
*(p)
*(8)
```
这两者都是deref，但数据结构实际上不同。前者参数是一个lvalue 表示p的地址 但不是p的值，
后者是rvalue 直接表示确切的值。

因此p在deref阶段 似乎应该转换成其值，并丢失掉p变量的地址。

但这种操作是否有些 不必要的特殊。？


#### lvalue 与 rvalue，如何让操作方法相同.?

```
*p    // 解p值
*(4)  // 直接值
```

```
i + 1  // 解i值
1 + 9  // 直接值
```

```
info.myvalue      // 解info
myinfo().myvalue  // 直接存操作栈 切片
```

可能的方法:
1. 每个需要用到值的地方 都对符号进行判断 根据lr来生成指令/操作  
   太繁琐了 那里兼顾得到那么多地方 有点死板
2. 在操作时 统一使用 数据提到栈上，lvalue复制到栈上  
   小对象还有可能 但是大对象 就很夸张。复制太多太浪费。`bigarray[3]`
3. 在操作时 统一使用 数据地址，rvalue就把零时数据地址 拿出来  
   好多层地址.? 不够明朗.? iadd就是两个数 不应该还有解引用的操作..  
   rvalue的消亡 可能也有问题。
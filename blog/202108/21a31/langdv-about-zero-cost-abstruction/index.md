
# 

cpp可以做到"零消耗的抽象"。
```cpp
class Point2 {
    int x;
    int y;
}
```
当使用该类时，`Point2 p;`的运行时空间与时间开销 等于`int x; int y;`。此类没有额外的类型信息等。  
cpp编译器将抽象操作尽可能'映射'到原始操作 或尽可能无额外消耗的操作。  

cpp对零消耗抽象有很大程度的执着，从模版展开的功能可以看到 为了做到零消耗 即使要绕'巨大弯路'也义不容辞。比起性能优化 这更像是一种原则上的规则。


相反javascript将"最大消耗抽象"发挥到了极致。object变成了一种map, `obj[field] = val`


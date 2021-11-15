
### ldv. assignment and memberaccess.

```
int i = 10;
int j = i;

i = j;
```

#### 赋值时，应该使用putfield, stloc 还是统一的ptr cpy呢

前者更高级 包含更多信息 可能有利于优化的空间吗。？

而后者更统一 甚至灵活自如一些。

#### 赋值表达式 和 变量声明表达式+init 是否有重复.?

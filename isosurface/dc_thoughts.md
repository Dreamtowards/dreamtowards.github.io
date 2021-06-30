# Dualc .thoughts



## 存储结构方法

### - Hermite Data

直接存取HermiteData，优点是直接性。可显式直接存取 O(1)，透明直接。
但是不太利于CSG操作。相比于SDFs 精度有限。

> taoju原文的 contour/format-dcf 即是采用这种方法。

### - SDFs. CSG Tree

通过存储一系列SDFs序列，计算求值。优点是操作有利性 和精准度。但是封装较多，求值时不确定 O(n)。

> upvoid/ csgtree

## 分析实现

### Miguel Cepero



### Upvoid


### nickgildea .leven


### Lin20
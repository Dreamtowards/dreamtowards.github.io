# SAT (Separating Axis Theorem) - DYN4J
Posted on January 1, 2010  \|  <http://www.dyn4j.org/2010/01/sat/>

有段时间以来我一直在想写本篇文章，但却一直没有开始。首先我想说的是 网络上有大量的资源关于此碰撞检测算法。但问题是 在我看到的有效资源中 有很多资源对实现细节的解释常常较为含糊不清。

遂我计划去讲解此算法 并且填补一些空白对于我自己在实现中所遇到的。

首先我要说"[这里](http://www.metanetsoftware.com/technique/tutorialA.html)"是一个很棒的教程 其中带有可互交的Flash实例。

1. 介绍
2. 凸多边形
3. 投影 (Projection
4. 算法
    1. 不相交 (No Intersection
    2. 相交
5. 获取分离轴
6. 投影一个形状到一个轴
7. 寻找MTV
8. 弯曲的形状
9. 遏制 (Containment
10. 其他事情需要注意的


## 介绍
分离轴算法，简称SAT，是一个方法用于确定是否两个凸多边形为相交状态。该算法也可以被用于寻找最小渗透向量 这十分有用对于物理模拟以及大量其他应用。SAT是一种快速的通用算法，它可以去除对于每种形状类型对(shape type pair)的特定碰撞检测代码的需要 从而降低了代码及维护量。

## 凸多边形性 (Convexity
如前所说，SAT是一个方法可以确定是否两个凸多边形相交。若形状A为凸多边形，则通过该形状的任意一条线 将只会和该形状相交2次。 若一条通过该形状的线与该形状相交2次以上 那么该形状则为非凸多边形(或凹多边形Concave)。参见"Wiki" 或 “MathWorld”对此的定义以获得更多数学和式子上的信息。所以让我们看看一些例子：

![figure1-3](attach/sat_figure1-3.png)

第一个形状被认为是凸多边形，因为此处不存在一条通过该形状且与该形状相交超过2次的线。而第二个形状则不是凸多边形，因为此处存在一条与该形状相交超过2次的线。

SAT只能处理凸多边形，但这是ok的，因为非凸形(或凹形)可以由凸形的组合来表示(称为凸多边形分解(convex decomposition))。所以若我们用Figure.2的凹多边形 执行一次凸多边形分解，我们将可以得到两个凸多边形。随后我们将可对其中每个凸多边形进行测试 以得到整个形状的碰撞结果。

## 投影 (Projection
SAT使用的下一个概念是投影。想象一下，你有一个光线完全平行的光源。若您将光线照射到物体上，则在该物体表面将产生阴影。阴影是三维对象的二维投影，二维对象的投影是一维"阴影"。

![figure4](attach/sat_figure4.png)

## 算法
SAT指出：“**若两个凸多边形没有穿透，则存在一个物体的投影不会重叠的轴。**”

### 不相交
首先让我们讨论一下SAT是如何确定两个形状为不相交的。从Figure.5中我们知道这两个形状是不相交的。一条画在他们之间的直线说明了这一点。

![figure5](attach/sat_figure5.png)

若我们选择在Figure.5中分离两个形状的那条直线上做垂线，并将两个形状投影于该垂线上，我们将可以看到他们的投影将不会交叠。一条形状的投影间不重叠的线称之为分离轴。在Figure.6中 深灰色直线为分离轴，而相应的彩色线为对应形状在该分离轴上的投影。请注意，在figure.6中 投影没有相交，因此根据SAT规则 形状为不相交状态。

![figure6](attach/sat_figure6.png)

SAT可能会测试许多轴是否重叠。但是，一旦得到第一个投影不重叠的轴，算法则可即刻退出 并确定形状为不相交。由于这种早期退出，SAT非常适合用于具有许多对象但几乎没有碰撞的应用程序(例游戏，模拟等)。

为了进一步讲解，请检查以下伪代码。

```java
Axis[] axes = // 获取轴用于测试
// 遍历这些轴
for (int i = 0; i < axes.length; i++) {
  Axis axis = axes[i];
  // 将两个形状都投影在该轴上
  Projection p1 = shape1.project(axis);
  Projection p2 = shape2.project(axis);
  // 投影重叠了吗。。？
  if (!p1.overlap(p2)) {
    // 那么我们可以确保形状没有重叠
    return false;
  }
}
```

### 相交
若形状的投影在所有轴上都重叠，那么我们将得知该形状为重叠状态。Figure.7描述了两个凸多边形在许多轴上的投影测试结果。该对形状在这些轴上的投影均都重叠，因此我们可以结论该对形状为重叠状态。

为了确定是否相交，**所有**轴都必须要经过测试。将上面的代码修改过后为：

```java
Axis[] axes = // 获取轴用于测试
// 遍历这些轴
for (int i = 0; i < axes.length; i++) {
  Axis axis = axes[i];
  // 将两个形状都投影在该轴上
  Projection p1 = shape1.project(axis);
  Projection p2 = shape2.project(axis);
  // 投影重叠了吗。。？
  if (!p1.overlap(p2)) {
    // 那么我们可以确保形状没有相交
    return false;
  }
}
// 若我们到达这里，那么我们将知道每个轴都有所重叠
// 所以我们可以确保相交
return true;
```

## 获取分离轴
在我实现该算法时遇到的第一个问题则是 如何知道要测试的轴？这实际上比较简单：

**你必须要测试的轴是 形状的每一条边(edges)的法线**

![figure8](attach/sat_figure8.png)

边的法线可以通过反转坐标并取反来获得。例：

```java
Vector[] axes = new Vector[shape.vertices.length];
// 遍历顶点
for (int i = 0; i < shape.vertices.length; i++) {
  // 获取当前顶点
  Vector p1 = shape.vertices[i];
  // 获取下一个顶点
  Vector p2 = shape.vertices[i + 1 == shape.vertices.length ? 0 : i + 1];
  // 相减得到边向量(edge vector)
  Vector edge = p1.subtract(p2);
  // 得到任意一垂直向量
  Vector normal = edge.perp();
  // perp(垂线)方法是 (x, y) => (-y, x) or (y, -x)
  axes[i] = normal;
}
```

> 在上面的方法中，我们返回了形状每条边的垂线向量。这些向量称之为“法线”向量。然而这些向量并没有被标准化(不是单位长度)。若你只需要从SAT算法中获得一个布尔(boolean)结果，则这是没有问题的。但若你需要碰撞信息(等会将在MTV部分讨论)，那么这些向量将需要被标准化！(见 投影一个形状到一个轴 部分)。

对每个形状执行此操作将获得两个用于测试的轴列表。做到这一点 我们将更改上面的伪代码为：

```java
Axis[] axes1 = shape1.getAxes();
Axis[] axes2 = shape2.getAxes();
// 遍历axes1
for (int i = 0; i < axes1.length; i++) {
  Axis axis = axes1[i];
  // 将两个形状都投影在该轴上
  Projection p1 = shape1.project(axis);
  Projection p2 = shape2.project(axis);
  // 投影重叠了吗。。？
  if (!p1.overlap(p2)) {
    // 那么我们可以确保形状没有相交
    return false;
  }
}
// loop over the axes2
for (int i = 0; i < axes2.length; i++) {
  Axis axis = axes2[i];
  // 将两个形状都投影在该轴上
  Projection p1 = shape1.project(axis);
  Projection p2 = shape2.project(axis);
  // 投影重叠了吗。。？
  if (!p1.overlap(p2)) {
    // 那么我们可以确保形状没有相交
    return false;
  }
}
// 若我们到达这里，那么我们将知道每个轴都有所重叠
// 所以我们可以确保相交
return true;
```



# GJK (Gilbert-Johnson-Keerthi) - DYN4J
Posted on April 13, 2010  \|  <http://www.dyn4j.org/2010/04/gjk-gilbert-johnson-keerthi/>

今天，我要来谈论另一个封装在dyn4j中的碰撞检测算法。你可以找到许多关于GJK的文档，但是很大一部分文档都比较学术专业 因为他们是研究资料。我强烈推荐这个[视频教程](http://mollyrocket.com/849)，老实说 你可能在看完这个视频后就无需再做进一步阅读。但若你感觉看完视频后还需要更多信息，那么请继续阅读。

1. 介绍
2. 凸性 (Convexity
3. 明可夫斯基和 (Minkowski Sum
4. 单面 (Simplex
5. Support函数
6. 创建单面 (Creating The Simplex
7. 检测碰撞
8. 迭代
9. 检查单面 (Checking The Simplex

## 介绍
GJK, 就像SAT，只能在凸多边形上进行操作。一个GJK最具吸引力的功能之一是 他可以支持实现了"support函数"(待会将会讨论这个)的任意形状。因此，不像SAT，你无需单独的来处理曲面形状(像用特定的代码或算法)。

GJK是一个迭代方法，但收敛非常快，若你已经准备好了最后一个 渗透/分离(penetration/separation) 向量，那么甚至可以以接近固定的时间运行。这在3D环境中是一个更佳的选择，因为SAT必须测试巨大量的轴。

GJK的原本意图是确定两个凸多边形之间的距离。GJK还可以被用于返回碰撞信息 以获取小穿透(small penetrations)，并可以通过其他算法以补充更深的穿透(deeper penetrations)。

## 凸性 (Convixity
就像我之前说的，GJK是一个只能用于凸多边形的算法。若要获取更多对凸性的解释 可查看我的[SAT](http://www.dyn4j.org/archives/55#sat-convex)文章。

## 明可夫斯基和 (Minkowski Sum
GJK算法很大程度上依赖于一个称为明可夫斯基和的概念。明可夫斯基和从概念上讲很容易理解。假设你有两个形状，这两个形状的明可夫斯基和则是形状1的所有点加上形状2的所有点：

> A + B = { a+b | a∈A, b∈B }
若两个形状都是凸多边形，那么返回的形状也将是一个凸多边形。

你也许会在想，“ok，那很酷，但这和我有是否有什么关系呢？”。其中的意义并非在加法上，若我们选择用减法取而代之：

> A - B = { a-b | a∈A, b∈B }

作为旁注，在我们继续之前，即使我们是在使用"差(difference)"的操作，但这仍然不叫明可夫斯基差(Minkowski Difference)，这仍然是明可夫斯基和。但为了清楚起见，我将在在下文中称这个为明可夫斯基差。

继续，在明可夫斯基和上执行差操作的关键点如下：

> **若两个形状处于 交叠/互交 状态，那么明可夫斯基差将会包含原点。**

让我们看一个例子，执行明可夫斯基差于Figure.1的两个形状上，你将会得到Figure.2的形状。请注意返回的形状包含原点，这是因为这两个形状是相交的。

![figure1](attach/gjk_figure1.png)

![figure2](attach/gjk_figure2.png)

现在执行该操作需要 shape1.vertices.size * shape2.vertices.size * 2 个减法。这一点是重要的，因为一个形状是由无数的点组成。因为这两个形状都是凸多边形且是以最外层的顶点定义的，所以我们只需要在那些顶点上执行这个操作。一个关于GJK的很棒的事情是，你**无需**真的去计算明可夫斯基差。

## 单面 (Simplex
我们不想去计算明可夫斯基差。相反，我们只是想知道明可夫斯基差是否包含原点。若包含原点，那么我们将知道形状处于互交状态，否则将不为互交状态。

取而代之，我们能做的是 在明可夫斯基差内<sup>(我认为是在集合元素内而坐标非空间内)</sup>迭代的构建一个试图封闭原点的多边形。若我们构建的多边形包含原点(并包含于明可夫斯基差内)，那么我们可以说明可夫斯基差包含该原点。这个我们想去构建的多边形称之为单面(Simplex)。

## Support 函数
所以下一个问题是 我们如何构建这个单面(Simplex)？单面是由一个称之为support函数的函数结果所构成。给定两个形状，support函数应该在明可夫斯基差内返回一个点。我们已经知道可以用形状1中的一个点和形状2中的一个点进行相减操作而获得一个于明可夫斯基差内的点，但我们不想每次都是一些相同的点。

若我们让support函数依赖于一个方向，在使用support函数时 我们可以确保我们不会每次获取到相同的点。换而言之，若我们让support函数返回(一个形状)在一个方向上最远的点，那么我们稍后则可以选择不同的方向去获取不同的点。

在一个方向上选择最远的点是有意义的 因为他创建了一个包含最大区域的单面 从而增加了算法快速的退出机会。除此之外，我们还可以利用这样的事实，即以这种方式返回的所有点都在明可夫斯基差的边(edge)上，因此 若我们不能再添加一个沿着某个方向经过原点的点 那么我们将知道这个明可夫斯基差不包含原点。这在无相交的情况下增加了算法快速退出的机会，稍后来详细谈论这一点。

```java
public Point support(Shape shape1, Shape shape2, Vector d) {
  // d is a vector direction (doesn't have to be normalized)
  // get points on the edge of the shapes in opposite directions
  Point p1 = shape1.getFarthestPointInDirection(d);
  Point p2 = shape2.getFarthestPointInDirection(d.negative());
  // perform the Minkowski Difference
  Point p3 = p1.subtract(p2);
  // p3 is now a point in Minkowski space on the edge of the Minkowski Difference
  return p3;
}
```

## 创建单面 (Creating The Simplex
让我们以一个例子开始。对Figure.2中的形状执行3次support函数：
首先让我们用 `d=(1, 0)` 开始
```
p1 = (9, 9);
p2 = (5, 7);
p3 = p1-p2 = (4, 2);
```
下一个用 `d=(-1, 0)`
 ```
 p1 = (4, 5);
 p2 = (12, 7);
 p3 = p1-p2=(-8, -2);
 ```
 注意p1可以是`(4, 5)`或`(4, 11)`，两者都可以在明可夫斯基差的边(edge)上产生一个点。

下一个用 `d=(0, 1)`
```
p1 = (4, 11);
p2 = (10, 2);
p3 = p1-p2 = (-6, 9);
```
我们现在得到了显示于Figure.3上的单面(Simplex)



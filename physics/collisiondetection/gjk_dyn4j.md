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

> """若两个形状处于 交叠/互交 状态，那么明可夫斯基差将会包含原点。"""


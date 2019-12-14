# GJK (Gilbert-Johnson-Keerthi) - DYN4J
Posted on April 13, 2010  |  <http://www.dyn4j.org/2010/04/gjk-gilbert-johnson-keerthi/>

今天，我要来谈论另一个封装在dyn4j中的碰撞检测算法。你可以找到许多关于GJK的文档，但是很大一部分文档都比较学术专业 因为他们是研究资料。我强烈推荐这个[视频教程](http://mollyrocket.com/849)，老实说 你可能在看完这个视频后就无需再做进一步阅读。但若你感觉你看完视频后还需要更多信息，那么请继续阅读。

1. 介绍
2. 凸性 (Convexity
3. 明可夫斯基和 (Minkowski Sum
4. 单面 (Simplex
5. 支持函数 (The Support Function
6. 创建单面 (Creating The Simplex
7. 检测碰撞
8. 迭代
9. 检查单面 (Checking The Simplex

## 介绍

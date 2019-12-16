# Video Game Physics Tutorial - Part I: An Introduction to Rigid Body Dynamics

原文 | [Video Game Physics Tutorial - Part I: An Introduction to Rigid Body Dynamics](https://www.toptal.com/game/video-game-physics-part-i-an-introduction-to-rigid-body-dynamics)
--- | --- 
作者 | Nilson Souto
翻译 | Dreamtowards
校对 | 暂无校对

本文为我们的视频游戏物理3章系列的第一章。本系列其他的部分，请见：
- [Part II: Collision Detection for Solid Objects](https://www.toptal.com/game/video-game-physics-part-ii-collision-detection-for-solid-objects)
- [Part III: Constrained Rigid Body Simulation](https://www.toptal.com/game/video-game-physics-part-iii-constrained-rigid-body-simulation)

物理模拟是计算机科学中的一项领域，其意旨(通常)为使用计算机来重现物理现象。一般情况下，这些模拟 将数字化的方法应用于现有的理论 以获得尽可能接近于我们在主世界中所见的(现象) 的结果。这允许我们 以[高级游戏开发者](https://www.toptal.com/game)的身份，去预测和仔细的分析 在构成他们之前 一些事情将会有着如何样的行为/表现，何者的简洁性和开销最优。

<!-- ![banner](banner.jpg) -->

物理模拟的应用范围乃是巨大。计算机最早是用于执行物理模拟的，例如 预测弹射性物体的弹道运动。他也是民事和自动化工程的一个基本器具。{illuminating how certain structures would behave in events like an earthquake or a car crash. }以及他不止于此。我们可以模拟 如天体物理学，相对论 及很多我们可在自然奇观中观察到的其他疯狂的事物。

模拟物理在视频游戏中是很常见的，因为许多游戏{are inspired by things we have in the real world}。有些游戏完全依靠于物理模拟为主要特性。这意味着这些游戏需要一个不会崩溃或变卡的稳定的模拟，但这通常并非易事。

在许多游戏中，只有几个物理效果是关切的。刚体动力学 - 固体的运动与互交。硬硬的物体 - 是迄今为止在游戏模拟中最常见的一种。那是因为{most of the objects we interact with in real life are fairly rigid}，以及模拟刚体是相对简单的(正如我们将会看到的，这并不意味着他易如反掌)。一小部分其他游戏则需要模拟更为复杂的实体，如可变性物体，流体，磁性物体等。。

在这个视频游戏物理教学系列，我们将会探索刚体模拟。以本文中较为简单的刚体运动开始，随后于接下来的章节中讨论 通过碰撞和约束 对多个刚体间进行互交。应用于像[Box2D](http://box2d.org/), [Bullet Physics](http://bulletphysics.org/wordpress/)和[Chipmunk Physics](https://chipmunk-physics.net/)这样的现代游戏物理引擎中的最常见的公式 (也)将会被呈现并讲解。


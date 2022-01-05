
# <sup>Making of</sup> Gymcontroller™ *Topic Series*

- E1. Intruducing
   1. Whats Gymcontroller.?
   2. Why Gymcontroller.?
   3. How Gymcontroller.?
- E2. Mobile Client. Phone Listener.
- E3. PC Server. Controlling Information Receiver
- E4. PC Dispatcher. Controlling Operations Dispatcher.

### Overall Instructions & Audience Roads

本系列面向外部观众 预览业务开发过程。看其中的变化 新奇或趣事。

但同时也面对开发者，简述方法方式。但不会太详细 而是较粗略。



## E1. Introducing

### What is Gymcontroller™

Gymcontroller™ 是一个计算机应用程序，允许您使用手机的陀螺仪 来控制电脑鼠标或方向。  
典型的例子是 
1. 根据手机的倾斜程度 进行移动鼠标 
2. 控制驾驶类游戏的方向与速度

### Why Gymcontroller.?

通常情况下，有多种控制电脑鼠标的方法:  
1. Mouse / 鼠标  
   <small>精确 敏捷，但需要一个硬件和平坦平台。</small>
2. Trackpad / 触控板  
   <small>不是那么敏捷跟手</small>
3. Touchscreen / 触摸屏  
   <small>方便 但是精确度和功能性可能有一点问题</small>
4. Gamepads / Xbox手柄  
   <small>占用额外空间 有一点不太严肃</small>

他们各有优缺点。

那么为何不试试用手机的陀螺仪.? 好主意 这听起来很不错。  
手机通常会被您随身携带 利用他的陀螺仪 只需要在空中转动倾斜 即可进行控制操作。

> 有时操作电脑并不像通常那么方便 - 比如在上课时。若您想在上课时使用电脑 屏幕在远处或一个隐藏的位置，那么您恐怕不会太方便总是使用鼠标。Gymcontroller™的出现或将帮助您改善这一窘境。此后 您甚至可以操作其它电脑于无形 - 将您的手机与其连接。<sup>(请提前确定当地法律条款)</sup>

另一个目的是，控制驾驶类游戏。在ETSII(欧洲卡车模拟)中 若可以通过手机倾斜程度 来控制方向和速度 那么将会很休闲和有趣。

### How Gymcontroller.?

Gymcontroller的基本开发 将被分为以下三个部分:

1. Phone Listener / 手机监听器
2. PC Server / 桌面终端 建立连接与通信
3. PC Dispatcher / 桌面执行器 根据控制信息执行对应操作


```
Hello大家好。欢迎来到Gymcontroller主题。  
在这个主题中 我们将会通过手机的陀螺仪 来控制PC的鼠标或方向

实际上我认为这是一个很有意思的事情。因为在传统上 
我们通常使用硬件鼠标 触控板 或触摸屏 甚至Xbox手柄 来控制鼠标。
但他们有各自的优缺点 鼠标需要带一个硬件 触摸板不一定很好用 触摸屏可能不够精确 而Xbox手柄 需要带一个不小的外设。

有趣的事实是 我们通常会带上手机，而手机可以无线连接到电脑 并通过在空中旋转的方式 来操作电脑鼠标。我认为这实在是太炫酷了。

那就让我们开始吧。本系列将会被分为几个阶段或小节 

```



# ES2 Mobile Client

1. MobileClient Construction  
1.1. Flutter vs Web
2. Web: DeviceOrientationEvent  
2.1. Test Data
3. WebSocket Connect to Server  
3.1 Test Communication
4. Client Network Interface  
4.1 Sending *Delta* Partial Offset
5. DesktopServer Construction  
5.1 Java+WebSocket+AWTDesktop
6. Listen to WebSocket
7. Desktop Control.

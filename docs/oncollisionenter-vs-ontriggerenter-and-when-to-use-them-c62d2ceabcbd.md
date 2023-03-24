# OnCollisionEnter 与 OnTriggerEnter 以及何时使用它们

> 原文：<https://levelup.gitconnected.com/oncollisionenter-vs-ontriggerenter-and-when-to-use-them-c62d2ceabcbd>

![](img/6b0a94ccdd0c3f6e8852048856d6e4e5.png)![](img/dc973c6b4a0077676a9ffc47252c2d8d.png)

OnCollisionEnter 将会像它听起来那样被使用…带着冲撞！当我想到碰撞时，我首先想到的是**硬表面碰撞**，这正是 OnCollisionEnter 所指的。汽车碰撞、球反弹和树倒下都是硬表面碰撞的例子！我将在本文中介绍一些不同版本的 OnCollision。首先，碰撞的**进入**方面可以在下面的文档中看到。基本上，**碰撞事件**发生在一个物体**接触另一个物体**的瞬间**。当这种情况发生时，关于碰撞的**信息**就在我们的指尖，可以**访问**。如下所述，**一个**物体绝对碰撞**需要**有一个**刚体连接**才能工作。**

![](img/b7d083fedf8810a7352e514d50a49f9f.png)![](img/e6fe2a84ac43c9a82420e2ffac8f69ab.png)

如下文所述，只要对象**相互接触**，则**oncolissionstay**方法对**有效。这可能就像当一个球停止反弹，停在地板上。还要注意的是，在进行接触时，每帧**调用一次 Stay 方法**。**

![](img/21077bdabf6cc1924e8668431ca5e334.png)![](img/40219385d323e180f5058bd6e58a58c8.png)

一旦对象**停止**接触，调用**oncolissionexit**方法关闭对象交互。

![](img/2b6cae3988d90eb4a153adceda33877c.png)![](img/d9daa3ccdab358876298327ba1186ca6.png)

**触发**碰撞是**通过**碰撞，此时物体**不会相互弹开**，但是当**接触**时**事件**可以被**触发**。我在我的子弹和敌人的碰撞中使用这个，这样子弹就不会被敌人弹开而消失。相反，它似乎被吸收了，然后就消失了。碰撞**信息**也可用于触发事件。就像 OnCollisionEnter 一样，OnTriggerEnter get 被称为对象碰撞的时刻。

![](img/1e9496f783881cc6d005da07b7a112de.png)

注意**是触发器**选项需要在上切换**以将其用作触发器。**

![](img/4327e922246bf861662642bd3f3c06c2.png)![](img/f8d18fd477a3c60b59280d463767c22c.png)

玩家盒子碰撞器(左)。敌箱碰撞器和刚体(右)。

![](img/f9858676a987e44b7a33f7930a35e03b.png)

**OnTriggerStay** 方法的工作原理类似于 OnCollisionStay，只是这些物体可以通过彼此自由移动**，然后 **Stay** 方法**继续**触发**，同时**物体碰撞器**接触**。**

![](img/670fd05a9fa411ecff8a53e3ab95027b.png)![](img/91c4683d98111e96f3d6a7a4c2fb668d.png)

如果你已经注意到这里的一个主题，那就是 **OnTriggerExit** 方法的工作方式类似于 OnCollisionExit，当游戏对象**停止接触**时**触发**。

![](img/19ea99e771d25b26023ef77f816d6535.png)

在我的敌人脚本中，我使用 OnTriggerEnter 来收集一些信息。我用**调试。日志**告诉我与我相撞的**其他**物体的**名称**。

![](img/90ef298b865a8f4a7df701eeaa4ef06f.png)

我运行了一个小的**测试**让一个敌人打**玩家**，用**子弹**射击另一个。您可以在下面控制台上看到冲突信息。

![](img/45fca102bc23ca9c4ccbe2af31ab82bf.png)

更进一步，我要求被碰撞物体的**名称**和**位置**。

![](img/06ead0c7ecaa8ad019fd0728a0847633.png)

这里可以看到，与敌人碰撞的**其他**物体的**名称**和**位置**以(x，y，z)的形式打印到控制台上！

![](img/cb1bfe533886bfce955c59422d338134.png)

显而易见，所有这些碰撞选项都可以用于一些高级的游戏机制。即使只是一次小小的碰撞，也有很多机会获取信息并采取行动！
说到**碰撞**可能产生的**动作**，我将在下一篇文章中开始使用 **GetComponent** 进行脚本通信，在那里这些碰撞可以开始触发一些更有意义的交互。
# 用 TypeScript 构建游戏。游戏循环 1/2

> 原文：<https://levelup.gitconnected.com/gamedev-patterns-and-algorithms-with-typescript-game-loop-part-1-2-699919bb9b71>

教程[系列](https://medium.com/@gregsolo/gamedev-patterns-and-algorithms-in-action-with-typescript-d29b913858e)中的第二章讲述了如何用 TypeScript 和本地浏览器 API 从头开始构建游戏

![](img/29f414bc9b541d480262c9579ce107d7.png)

[freepik 创建的图标向量](https://www.freepik.com/free-photos-vectors/icon')

欢迎回来！这是我们讨论如何用 TypeScript 和本地浏览器 API 构建一个简单的回合制游戏的系列文章！第二章致力于为这个游戏建立一个游戏循环，其他章节可以在这里找到:

*   [简介](https://medium.com/@gregsolo/gamedev-patterns-and-algorithms-in-action-with-typescript-d29b913858e)
*   [第一章实体组件系统](https://medium.com/@gregsolo/entity-component-system-in-action-with-typescript-f498ca82a08e)
*   第二章。游戏循环(第一部分，[第二部分](https://medium.com/@gregsolo/gamedev-patterns-and-algorithms-in-action-with-typescript-game-loop-2-2-c0d57a8e5ec2)
*   第三章。绘制网格([第 1 部分](https://medium.com/@gregsolo/building-a-game-with-typescript-drawing-grid-1-5-aaf68797a0bb)、[第 2 部分](https://medium.com/javascript-in-plain-english/building-a-game-with-typescript-drawing-grid-2-5-206555719490)、[第 3 部分](https://medium.com/@gregsolo/building-a-game-with-typescript-drawing-grid-3-5-1fb94211c4aa)、[第 4 部分](https://medium.com/@gregsolo/building-a-game-with-typescript-iii-drawing-grid-4-5-398af1dd638d)、[第 5 部分](https://medium.com/@gregsolo/building-a-game-with-typescript-drawing-grid-5-5-49454917b3af))
*   第四章。舰船([第一部分](https://medium.com/@gregsolo/building-a-game-with-typescript-colors-and-layers-337b0e4d71f)、[第二部分](https://medium.com/@gregsolo/building-a-game-with-typescript-team-and-fleet-f223d39e9248)、[第三部分](https://medium.com/@gregsolo/building-a-game-with-typescript-drawing-ship-14e6c19caa38)、[第四部分](https://gregsolo.medium.com/building-a-game-with-typescript-ship-and-locomotion-4f5969675993))
*   第五章输入系统([第一部分](https://gregsolo.medium.com/building-a-game-with-typescript-input-system-1-3-46d0b3dd7662)、[第二部分](https://gregsolo.medium.com/building-a-game-with-typescript-input-system-2-3-cd419e36027c)、[第三部分](https://gregsolo.medium.com/building-a-game-with-typescript-input-system-3-3-8492552579f1))
*   第六章。寻路和移动([第一部分](https://blog.gregsolo.me/articles/building-a-game-with-typescript-pathfinding-and-movement-17-introduction)、[第二部分](https://blog.gregsolo.me/articles/building-a-game-with-typescript-pathfinding-and-movement-27-highlighting-locomotion-range)、[第三部分](https://blog.gregsolo.me/articles/building-a-game-with-typescript-pathfinding-and-movement-37-graph-and-priority-queue)、[第四部分](https://blog.gregsolo.me/articles/building-a-game-with-typescript-pathfinding-and-movement-47-pathfinder)、[第五部分](https://blog.gregsolo.me/articles/building-a-game-with-typescript-pathfinding-and-movement-57-finding-the-path)、[第六部分](https://blog.gregsolo.me/articles/building-a-game-with-typescript-pathfinding-and-movement-6-instant-locomotion)、[第七部分](https://blog.gregsolo.me/articles/pathfinding-and-movement-7-animated-locomotion))
*   第七章。玛奇纳州
*   第八章。攻击系统:生命和伤害
*   第九章。比赛的输赢
*   第十章敌人 AI

[上次](https://medium.com/@gregsolo/entity-component-system-in-action-with-typescript-f498ca82a08e)我们讨论了实体组件系统。我们将把它作为我们游戏的基本构件。今天我们将要准备几乎任何游戏的另一个关键部分:游戏循环。

> F eel 自由切换到[库](https://github.com/soloschenko-grigoriy/gamedev-patterns-ts)的`*ecs*`分支。它包含了前几篇文章的工作成果，是这篇文章的一个很好的起点。

# 目录

1.  为什么我们需要一个游戏循环？
2.  什么是游戏循环？
3.  可更新的实体和组件
4.  让游戏开始吧！
5.  结论

# 为什么我们需要游戏循环？

当我们开发一个应该在浏览器中执行的应用程序时，我们非常依赖我们的用户。事实上，应用程序的几乎整个数据流都依赖于用户的输入:“单击这里”、“在那里键入”、“导航到那个页面”等等。

![](img/a0f4526d0957e4e783fbfd0518d4b16c.png)

[free pik 创建的设计向量](https://www.freepik.com/free-photos-vectors/design)

在游戏中，用户输入也是必不可少的。毕竟，如果没人玩，这个游戏就毫无意义。但是游戏作为软件，不管用户输入如何，都有很多事情要做。

我们在这个系列中构建的游戏是一个回合制游戏。意思是每个玩家一个接一个地移动，而不是同时移动。AI 控制了其中一个玩家。当人类玩家结束他们的移动时，游戏不会“停止”船只仍在飞行；人工智能进行计算，计时器开始计时，等等。大量的过程正在发生；其中一些对玩家来说是可见的，比如动画和计时器。有些是隐藏的。在任何情况下，它们都*独立于用户的输入*。在商业应用程序中，几乎所有的东西都在等待用户开始与它互动，与之相反，在某种意义上，游戏有自己的生活。

考虑到所有这些，我们必须有一种方法让游戏继续进行，即使一个人类玩家什么都不做。而**游戏循环**的存在正是为了帮助我们做到这一点。

# 什么是游戏循环？

![](img/9fcdcb85c070ffe133ede0ddec0e2d98.png)

[宏向量](https://www.freepik.com/free-photos-vectors/background)创建的背景向量

这个想法很简单。我们运行一个无限循环，通知游戏的每一个部分每次迭代的开始。游戏循环本身不应用任何功能或逻辑。它只是说，“又一个时刻过去了。”

被通知的游戏的每一个元素都以它认为合适的方式处理这些信息。例如，飞船动画可能会稍微改变宇宙飞船在世界上的位置。AI 引擎可能会考虑开始新一轮的计算。RPG 角色的生命值或法力值可能会恢复一点

> 理解这是一个**循环**是至关重要的，它总是运行**循环**。关于性能，所有元素的所有操作都必须尽可能的轻量。

什么样的元素应该收到关于新迭代的通知？循环可能事先不知道，坦率地说，它并不关心。在前一章中，我们利用了一种强大的架构方法:实体组件系统。我们现在可以扩展它来配合游戏循环。这样，ECS 之后的每个游戏元素都会被循环更新。宇宙飞船会飞；敌人将能够思考，法力将会恢复！

# 可更新的实体和组件

我们所需要的是一种确保实体和组件可以更新的方法。最简单的方法是给抽象`Entity`和接口`IComponent`添加一个方法`Update`。然后游戏循环将在每次迭代中调用这个方法，通知这些元素。就像一个电话！或者早上烦人的闹铃。

![](img/d9a65a7c8d80a6dc5410f9188ea919e7.png)

[rawpixel.com 创建的背景向量](https://www.freepik.com/free-photos-vectors/background')

然而，这不是一个非常清晰的方法。这样，我们将可更新的行为和 ECS 紧密地耦合在一起。游戏循环确实可以更新每个实体和组件。但这并不意味着他们是唯一能够获得更新的人。更干净的方法是定义一个专用协议(读:*接口*)。谁符合谁就可以更新。

> 注意，创建一个专用的接口很好地符合固体接口分离原则

实际的界面非常简单:

注意参数`deltaTime`。它包含自游戏循环的最后一次迭代以来所用时间的信息。拥有这个数字对于游戏的某些元素来说是非常方便的。

有了接口、实体和组件，就可以简单地实现它:

然后实体可以调用其每个组件上的更新:

> 注意，如果有必要，特定的实体可以扩展甚至覆盖抽象实体的`*Update*`。然而，默认情况下，实体只是更新它的所有组件

我们的操作破坏了测试，因为我们承诺每个实体和组件都有一个`Update`方法。模拟实体和组件没有实现这个承诺。希望这很容易解决:

既然我们就在附近，让我们也用测试覆盖`Update`。这件事做起来相当简单。我们可以向模拟实体添加模拟组件，监视它们的`Update`方法，并查看当我们执行实体的`Update`时是否调用了它:

如果您在此时运行`npm start`，您的代码应该编译无误。如果您在`npm t,`之前运行测试，它们现在应该都是成功的。

但是我们仍然不知道谁以及多久在每个实体上调用一次 Update。我们甚至没有游戏循环本身。是时候解决了！

# 让游戏开始吧！

我将创建一个`Game`实体。它将是游戏的根元素，是实体层级中最顶层的元素。这个实体将开始游戏循环并准备所有的游戏对象。

![](img/4e6da51b58a54e4b5c1afc8b962aafe3.png)

[free pik 创建的技术向量](https://www.freepik.com/free-photos-vectors/technology)

让我们首先在 src 下的专用“game”文件夹中创建一个空实体:

不要忘记桶文件:

该游戏的行为与游戏的任何其他实体略有不同。它的更新方法不需要 deltaTime。原因很简单:Game 是根实体，所以它要**计算** deltaTime。为此，我们必须跟踪执行`Update`的时间(读取:*最后一次迭代的时间戳*)。

在每个`Update`日，我们将比较该时间戳和当前时间，并计算差值:

我们现在可以将这个 deltaTime 传递给游戏的所有组件(也称为 invoke parent 的`Update`,因为它就是这样做的):

之后，我们应该更新时间戳:

为了结束这个循环，我们可以递归地调用 Update:

我们有我们的游戏循环！恭喜你。

然而，每一次新的迭代都在前一次迭代之后立即开始。我们不需要如此频繁地重复。

我们可以设置一个计时器，以某种基于时间的频率进行迭代，例如利用`setTimer`或`setInterval.`，每隔一个`300ms`。事实上，在过去，这是我们实现定期执行的唯一方法。这种方法的问题是，不同设备上的玩家会体验到不同的游戏速度。

更好(也更常见)的方法是每隔*帧*重复一次循环，而不是只重复一小段时间。这将确保每个玩家以相同的频率进行迭代，无论他们使用什么设备玩游戏。

> 为了说明游戏在不同设备上保持一致的速度是多么重要，请考虑一个多人游戏。想象一下，你和你的朋友比赛，他的机器上运行的 CPU 比你快/慢得多。这意味着他们的本地版游戏可以在相同的时间内完成更多的循环。在这些循环中，他们可以获得更多的力量，聚集更多的资源，在战斗中对你造成更多的伤害。最终，这使得游戏变得不公平。谁会想玩这样的游戏？

对我们来说，希望现在的浏览器允许我们在每一帧执行代码，提供全局`requestAnimationFrame`回调。

让我们用 requestAnimationFrame 替换 Update 的即时调用，并让我们的游戏循环运行每一帧:

唯一剩下的就是开始循环了。我们可以在构造函数的帮助下做到这一点:

但是，让我先走一步，跳出目前的情况思考一下。

正如我们在前一章中所确定的，组件被附加到实体上以便能够工作。我们也可以在实体的构造函数中这样做。但是之后就越来越臃肿了。保持构造函数精简并在专用方法中进行任何繁重的初始化通常是更好的做法。下次我们将从介绍这样一种方法开始。

> Y 你可以在[库](https://github.com/soloschenko-grigoriy/gamedev-patterns-ts)的`game-loop-1`分支中找到这篇文章的完整源代码。

# 结论

厉害！在这一章中，我们学习了什么是游戏循环，以及我们如何将它与实体组件系统相结合。下一篇文章将致力于理解我们如何开始游戏循环，以及我们如何更新游戏的其他实体。

如果您有任何意见、建议、问题或任何其他反馈，请不要犹豫，给我发私信或在下面留下评论！感谢您的阅读，我们下次再见！

*这是系列教程“* ***用打字稿*** *”中的第二章。其他章节可点击此处:*

*   [简介](https://medium.com/@gregsolo/gamedev-patterns-and-algorithms-in-action-with-typescript-d29b913858e)
*   [第一章实体组件系统](https://medium.com/@gregsolo/entity-component-system-in-action-with-typescript-f498ca82a08e)
*   第二章。游戏循环(第一部分，[第二部分](https://medium.com/@gregsolo/gamedev-patterns-and-algorithms-in-action-with-typescript-game-loop-2-2-c0d57a8e5ec2)
*   第三章。绘制网格([第一部分](https://medium.com/@gregsolo/building-a-game-with-typescript-drawing-grid-1-5-aaf68797a0bb)、[第二部分](https://medium.com/javascript-in-plain-english/building-a-game-with-typescript-drawing-grid-2-5-206555719490)、[第三部分](https://medium.com/@gregsolo/building-a-game-with-typescript-drawing-grid-3-5-1fb94211c4aa)、[第四部分](https://medium.com/@gregsolo/building-a-game-with-typescript-iii-drawing-grid-4-5-398af1dd638d)、[第五部分](https://medium.com/@gregsolo/building-a-game-with-typescript-drawing-grid-5-5-49454917b3af))
*   第四章。舰船([第一部分](https://medium.com/@gregsolo/building-a-game-with-typescript-colors-and-layers-337b0e4d71f)、[第二部分](https://medium.com/@gregsolo/building-a-game-with-typescript-team-and-fleet-f223d39e9248)、[第三部分](https://medium.com/@gregsolo/building-a-game-with-typescript-drawing-ship-14e6c19caa38)、[第四部分](https://gregsolo.medium.com/building-a-game-with-typescript-ship-and-locomotion-4f5969675993))
*   第五章输入系统([第一部分](https://gregsolo.medium.com/building-a-game-with-typescript-input-system-1-3-46d0b3dd7662)、[第二部分](https://gregsolo.medium.com/building-a-game-with-typescript-input-system-2-3-cd419e36027c)、[第三部分](https://gregsolo.medium.com/building-a-game-with-typescript-input-system-3-3-8492552579f1))
*   第六章。寻路和移动([部分 1](https://blog.gregsolo.me/articles/building-a-game-with-typescript-pathfinding-and-movement-17-introduction) 、[部分 2](https://blog.gregsolo.me/articles/building-a-game-with-typescript-pathfinding-and-movement-27-highlighting-locomotion-range) 、[部分 3](https://blog.gregsolo.me/articles/building-a-game-with-typescript-pathfinding-and-movement-37-graph-and-priority-queue) 、[部分 4](https://blog.gregsolo.me/articles/building-a-game-with-typescript-pathfinding-and-movement-47-pathfinder) 、[部分 5](https://blog.gregsolo.me/articles/building-a-game-with-typescript-pathfinding-and-movement-57-finding-the-path) 、[部分 6](https://blog.gregsolo.me/articles/building-a-game-with-typescript-pathfinding-and-movement-6-instant-locomotion) 、[部分 7](https://blog.gregsolo.me/articles/pathfinding-and-movement-7-animated-locomotion) )
*   第七章。玛奇纳州
*   第八章。攻击系统:生命和伤害
*   第九章。比赛的输赢
*   第十章敌人 AI
# 斯韦尔托初学者指南。带 Unity 的 ECS (3.0)

> 原文：<https://levelup.gitconnected.com/a-beginners-guide-to-svelto-ecs-3-0-with-unity-e9dbc88a2145>

## 核心概念的逐步介绍

我最近决定辞职，成为一名全职独立游戏开发者。当我到达必须做出架构决策的时候，我正在 Unity 中为一个想法制作原型。作为一名编程出身的人，我总是告诉自己，有一天我会创建一个样板文件，作为我所有游戏项目的基础，以尽量减少未来的重构，并将重点放在设计游戏而不是代码结构上。终于到了兑现对自己承诺的时候了。

我过去曾使用过几个不同的架构框架和库，但它们要么不能让我兴奋，要么不能与 Unity 顺利合作。在搜索游戏开发中其他屡试不爽的设计模式时，我偶然发现了实体组件系统(ECS)。我被掌握主要原则的容易程度所震惊，我想学习更多。这个视频很好地解释了基本原理:

免责声明:ECS 以其巨大的性能优势而闻名，它还可以帮助开发团队创建更干净、更好管理的代码，但是它常常被认为对一个人的项目来说是大材小用。我对 ECS 感兴趣，因为我可以接受我的原型花费更长的时间来制作，如果这意味着我可以将它们构建成任何它们可能成为的样子，而不必在未来进行重大的重构。还因为结构良好的代码让我开心！

# ECS Unity 框架

在研究生产就绪且与 Unity 集成良好的 ECS 框架时，有两个框架引起了我的注意，原因如下:

1.  [Entitas-CSharp](https://github.com/sschmid/Entitas-CSharp)

*   GitHub 上的 4.7k 星星
*   有很好的介绍视频
*   活跃不和谐社区

2.[斯维尔托。ECS](https://github.com/sebas77/Svelto.ECS)

*   超级活跃的创造者/维护者
*   频繁的更新和发布，Seba 的实验室[上有很多文章](https://www.sebaslab.com/)
*   发布了 3 款游戏(在创作者担任首席技术官的公司)
*   刚性框架——关注编码中有趣的部分
*   活跃不和谐社区

说实话，我决定调查一下斯维尔托。首先是 ECS，因为 Entitas 的介绍视频从设置 Jenny 代码生成器开始，这吓到了我，因为我不知道 Jenny 的目的。我不知道学习斯维尔托。ECS 最终将成为从各种文章中过滤和拼凑最新信息的艰巨任务。ECS wiki 和[微型示例](https://github.com/sebas77/Svelto.MiniExamples)中的评论。经过两天的努力理解这些概念，我发现自己只有一个简单的胶囊，你可以用箭头键移动。

![](img/4cb8bb0cc253d55639ffc1a61a678c77.png)

在投入了这么多小时后，我对结果感到沮丧，于是我回到 Entitas，意识到 Jenny 代码生成器会自动连接组件，这样你只需创建一个`XComponent`并运行 Jenny 就可以做像`entity.AddX();`这样的事情了！

尽管如此，我还是很高兴我花时间学习了斯维尔托。因为我觉得我对 ECS 框架的基础有了更好的理解。斯维尔托。ECS 似乎给了用户更多的控制权，因为所有的工作都是手动完成的，所以除非您明确地这样做，否则不会连接或暴露任何东西。

我决定通过写这篇指南来好好利用我的努力，希望其他人也有兴趣开始使用 Svelto。通过一个引导他们完成基本、核心部分的介绍教程，ECS 将更受鼓励。因此，我们将一步一步地重现你在上面的 gif 中看到的！

*编辑:我可能纠结了两天，因为我太不好意思在* [*Svelto 上问初学者问题。ECS Discord*](https://discord.gg/3qAdjDb) *，但事实证明，如果你是新手，你可能会在那里提问！希望这个指南能提供很多答案，但是如果你在学习框架的时候遇到了困难，一定要检查一下服务器！*

请注意，本教程中的所有引文都来自 Svelto.ECS 的创建者 Sebastiano Mandalà。它们摘自 Seba 的实验室的各种文章以及微型示例的评论。

# 陷害斯维尔托。ECS 3.0

在我写这篇文章的时候，斯维尔托。ECS 3.0 还没有正式发布。在浏览了 Seba 的实验室和 [Github Wiki](https://github.com/sebas77/Svelto.ECS/wiki) 上的文章后，我被有多少关于新版本过时的部分的参考文献所淹没，所以我决定从已经更新到最新版本的 [MiniExamples](https://github.com/sebas77/Svelto.MiniExamples) 中抓取它，并从那里拼凑逻辑，直接进入 3.0。特别是，我把重点放在例 2——生存上。以下是如何设置 Svelto 的方法。ECS 3.0:

1.  创建新的 3D Unity 项目
2.  从[生存迷你示例](https://github.com/sebas77/Svelto.MiniExamples/tree/master/Example2-Survival/Packages)或官方 GitHub repos if Svelto 获取以下模块。在您阅读本文时，ECS 3.0 已经发布:

*   [斯维尔托。ECS](https://github.com/sebas77/Svelto.ECS)
*   [斯维尔托。常见](https://github.com/sebas77/Svelto.Common)
*   斯韦尔托。任务
*   [com . sebaslab . svelto . unsafe](https://github.com/sebas77/com.sebaslab.svelto.unsafe)

3.在文件系统中打开 Unity 项目，并将模块复制到`Packages/` 文件夹中

4.打开`Packages/manifest.json` 并在*依赖关系*下添加以下内容:

```
"com.sebaslab.svelto.common": "com.sebaslab.svelto.common@3.0.0",
"com.sebaslab.svelto.ecs": "com.sebaslab.svelto.ecs@3.0.0",
"com.sebaslab.svelto.tasks": "com.sebaslab.svelto.tasks@1.5.8",
"com.sebaslab.svelto.unsafe": "com.sebaslab.svelto.unsafe@4.7.1",
```

5.如果您看到任何错误或 IntelliSense 无法正常工作，您可能需要重新生成项目文件:

*   确保您的 IDE 包在 Unity 包管理器中是最新的
*   首选项>外部工具>重新生成项目文件

我们开始吧！

# 上下文和组合根

> 斯维尔托。上下文不是 Svelto 的正式组成部分。ECS，但它有助于在没有上下文的环境中使用，比如在 Unity 中。是自举！

让我们为 Svelto 创建一个入口点。使用 Svelto.Context 的 ECS。

1.  在 Unity 中创建一个空的游戏对象；我们称之为*游戏环境*
2.  创建并附加一个`MainContext.cs`脚本到*游戏环境*

正如[源代码](https://github.com/sebas77/Svelto.MiniExamples/blob/master/Example2-Survival/Packages/com.sebaslab.svelto.common%403.0.0/Context/Extensions/Unity/UnityContext.cs)所示，`UnityContext`只是一个包装器，它基于 *GameContext 的* MonoBehavior *触发 CompositionRoot 中的方法。*但是`MainCompositionRoot`还不存在！让我们改变这一点:

这是最低限度。那么作文根还应该包括什么呢？

> 组合根是所有依赖项被创建和注入的地方。组合根属于上下文，但是一个上下文可以有多个组合根。例如，工厂是一个复合根。

因为**系统**，Svelto 称之为*引擎，*是 ECS 中唯一包含逻辑的部分，甚至创建实体和组件都发生在引擎内部。因此，自然地，我们将在复合根中创建一堆引擎！通过[引擎根](https://github.com/sebas77/Svelto.MiniExamples/blob/master/Example2-Survival/Packages/com.sebaslab.svelto.ecs%403.0.0/EnginesRoot.Engines.cs)管理引擎:

> *EnginesRoot* 类是 Svelto.ECS 的核心，通过它可以注册引擎并构建游戏的所有实体。动态创建引擎没有多大意义，所以所有引擎都应该从创建它的同一个复合根添加到 EnginesRoot 实例中。出于类似的原因，EnginesRoot 实例决不能被注入，并且一旦添加引擎就不能被删除。

好吧！因此，让我们向复合根添加一个 EnginesRoot 实例，并确保在上下文被破坏时清理它。

EnginesRoot 需要一个`EntitiesSubmissionsScheduler`，它每一次检查并提交实体的变更。我们使用的是`UnityEntitiesSubmissionScheduler`，它使用 MonoBehavior 的*更新*作为滴答。

现在我们已经设置好了，可以开始工作了！

# 产生一个虚拟玩家

在这个练习中，我们简单地创建了一个“玩家”游戏对象，我们可以用箭头键输入来移动它。让我们先创建游戏对象，然后再详细讨论玩家实体或其组件。

1.  创建您喜欢的任何 3D 游戏对象，将其重命名为`Player`，并将其拖至`Resources/Prefabs`，使其成为一个预设。从场景中删除对象。

为了实例化这个预置，我们需要一个`GameObjectFactory`和一个`PlayerSpawnerEngine`。让我们从引擎开始，我们在 ECS 中的第一个**系统**！

然后在`MainCompositionRoot`中，我们添加以下几行:

让我们按流程走一遍。因为它实现了`IQueryingEntitiesEngine`接口，当 PlayerSpawnerEngine 被添加到 MainCompositionRoot 中的 EnginesRoot 时，它获得了对 EnginesRoot 跟踪的实体`entitiesDB`的访问，然后触发了它的`Ready`方法。这里的`SpawnPlayer().Run()`中的`Run`是 Svelto 的一部分。任务模块，是以下内容的简写:

```
SpawnPlayer().RunOnScheduler(StandardSchedulers.standardScheduler);
```

这将触发一个期待一个`IEnumerator`的任务运行器，该任务运行器将在特定的 MonoBehavior 事件函数(如 *Update* 或 *FixedUpdate* )期间逐步执行。 [StandardSchedulers](https://github.com/sebas77/Svelto.MiniExamples/blob/master/Example2-Survival/Packages/com.sebaslab.svelto.tasks%401.5.8/Svelto.Tasks/StandardSchedulers.cs) 类显示可用的调度程序类型，而 [RunnerBehaviourUpdate](https://github.com/sebas77/Svelto.MiniExamples/blob/master/Example2-Survival/Packages/com.sebaslab.svelto.tasks%401.5.8/Svelto.Tasks/Runners/Unity/RunnerBehaviourUpdate.cs) 显示各种调度程序在哪个 MonoBehavior 事件中运行。到目前为止，您可能已经猜到大多数逻辑都在协程内部。

因为我们已经引入了任务运行器，所以如果上下文被破坏，我们也应该确保清理调度器，如`MainCompositionRoot.OnContextDestroyed`所示。

如果此时运行 Unity 项目，您应该会在控制台中看到“PlayerSpawnerEngine”。如果您将记录器和 yield 返回封装在一个`while(true)`循环中，您将看到每帧速率记录的消息。恭喜你！您已经连接了您的第一个系统！

让我们快速创建一个`GameObjectFactory`并将其传入复合根中的`PlayerSpawnerEngine`；记住，组合根是所有依赖项(包括工厂)应该被创建和注入的地方！

我们还将更新 PlayerSpawnerEngine 来创建游戏对象！

此时，当您运行 Unity 时，您应该会看到您的播放器被实例化。干得好！接下来，我们将进一步了解实体及其组件。

# 实体和组件

给你一些定义；不要担心，我们将一个一个地重温这些:

> **实体**:必须是真实具体的，你可以用游戏设计的角度来解释的实体。每个实体的名字应该反映游戏设计领域的一个特定概念
> 
> **EntityDescriptors** :给出了一种形式化实体的方法，它还定义了一旦构建了实体就必须生成的组件
> 
> IEntityComponent :可以和纯 ECS 一起使用的实体组件
> 
> **IEntityViewComponent** :实现这个的结构用于包装来自 OOP 库的对象。除非由于外部库或平台的原因，被迫将 ECS 代码与 OOP 代码混合使用，否则您永远不会使用它。这些特殊“混合”组件只能容纳接口
> 
> **实现者**:entityview component 公开的接口必须由实现者实现，实现者实际上是你需要包装的对象。

现在我们有了一个可见的玩家对象，让我们开始创建我们的第一个实体和组件！参见上述**实体**和**实体描述符**的定义。

> 一个 *EntityDescriptor* 实例给了编码者一个机会，由将要处理它们的引擎独立地正确命名它们的实体…这个类[被用来]构建实体，虽然它真正做的是完全不同的事情，但用户能够编写**build Entity<*Entity descriptor*>()**的事实极大地帮助了可视化要构建的实体，并向其他编码者传达意图…然而一个*Entity descriptor！！*

所以我们将创建一个`PlayerEntityDescriptor`，我们可以在头脑中将其概念化为玩家实体。首先，让我们想象一下这个实体需要什么组件或数据。我们需要位置和输入向量。

这些组件扩展了`IEntityComponent`，上面的定义说它将与纯 ECS 一起使用，这意味着数据不必由 Unity 等第三方库使用。让我们先以这种方式构建组件，然后在需要时升级到`IEntityViewComponent`。现在是时候提一下在 EntityDescriptors 中使用的 [ComponentBuilder](https://github.com/sebas77/Svelto.MiniExamples/blob/master/Example2-Survival/Packages/com.sebaslab.svelto.ecs%403.0.0/ComponentBuilder.cs) 只接受`IEntityComponent`或`IEntityViewComponent`。

为了构建这个实体，我们首先需要一个实体工厂——就像我们需要一个游戏对象工厂来创建玩家游戏对象一样。幸运的是，EnginesRoot 类[为我们准备了一个](https://github.com/sebas77/Svelto.MiniExamples/blob/master/Example2-Survival/Packages/com.sebaslab.svelto.ecs%403.0.0/EnginesRoot.GenericEntityFactory.cs)！

此时，您将看到最后一行的错误，因为`BuildEntity`需要参数。你可以查看 [IEntityFactory](https://github.com/sebas77/Svelto.MiniExamples/blob/master/Example2-Survival/Packages/com.sebaslab.svelto.ecs%403.0.0/IEntityFactory.cs) 的源代码来查看不同的选项，但是它们都需要一个组 ID。所以让我们快速建立一个组并修复 PlayerSpawnerEngine 中的断线:

> 斯维尔托。ECS 有一个独特的特性，让用户决定实体的 ID(它必须是唯一的)。

所以我只是将实体 ID 设置为 0。

现在尝试运行 Unity。恭喜你！您已经创建了第一个实体和组件！不相信我？我们做一个 PlayerInputEngine 来证明一下吧！

希望你能理解正在发生的一切。唯一的新部分是 [QueryEntities](https://github.com/sebas77/Svelto.MiniExamples/blob/master/Example2-Survival/Packages/com.sebaslab.svelto.ecs%403.0.0/EntitiesDB.cs) ，由于方法重载，它可以以许多不同的方式使用。一般来说，它返回您正在寻找的组件的数组，包装在一个 [NativeBuffer](https://github.com/sebas77/Svelto.MiniExamples/blob/master/Example2-Survival/Packages/com.sebaslab.svelto.common%403.0.0/DataStructures/Arrays/NB.cs) 中，以及找到的组件的计数(第一种类型，如果给定多个的话)。

现在，当您运行 Unity 时，您应该在按下箭头键时在控制台中看到您的输入日志！这实际上是更改和读取播放器实体的 InputComponent 数据。干得好，走了这么远！让我们转到最后一部分。

# 混合 ECS

> 执行者充当 Svelto 之间的桥梁。ECS 引擎和第三方平台。这个特性对于混合 ECS 和 OOP 库是非常重要的。如果您需要 unity 与 Svelto 引擎通信，您不需要使用笨拙的变通方法，只需创建一个 Monobehaviour 实现程序。通过这种方式，你可以在实现者内部使用 Unity 回调，比如 OnTriggerEnter/OnTriggerExit，并根据 Unity 回调修改数据。除了设置实体组件数据之外，不应在这些回调中使用逻辑。

到目前为止，我们只在“纯”ECS 中创建了组件，但是我们需要数据来与 Unity 的游戏对象进行交互。这就是`Implementors`和`IEntityViewComponent`的用武之地。看看前一节中它们的定义。这两部分是“混合”ECS 的核心，包括与外部库的集成，如 Unity 的。

这是提醒您的好时机，EntityDescriptors 中的`ComponentBuilder`只接受`IEntityComponent`或`IEntityViewComponent`。所以让我们为位置数据创建一个 *IEntityViewComponent* ，更新 PlayerEntityDescriptor 来构建 *PositionViewComponent* 而不是 *PositionComponent* ，并为玩家游戏对象构建一个*实现者*来与位置数据交互。

接下来，我们将把实现者添加到玩家游戏对象中，并确保在为 ECS 端构建实体时传递它:

最后，让我们制作一个 PlayerMovementEngine，它查询 PlayersGroup 中的 InputComponents 和 PositionComponents，然后根据输入数据更新位置数据！

不要忘记将新的引擎添加到 CompositionRoot 中的 EnginesRoot！

这一刻已经到来；继续运行您的 Unity 项目吧！瞧啊。

# 结论

我们刚刚构建的项目可以在 GitHub 上的[jiheh/sveltoecsinro](https://github.com/jiheh/SveltoEcsIntro)下找到。虽然我们只做了一个非常简单的演示，但我们已经了解了 Svelto 的核心部分。ECS 在流程中工作:

*   设置上下文和合成根
*   使用工厂构建 ECS 实体(和 Unity 游戏对象)
*   创建组件并通过属于指定组的 EntityDescriptors 构建它们
*   查询特定组中的数据组件并通过引擎更新它们
*   借助组件接口、IEntityViewComponent 和实现者，通过混合 ECS 与 Unity 集成

当然，斯维尔托。ECS 有许多更复杂的概念需要探索，但是希望在很好地掌握基础知识的情况下，您能够轻松地将新的部分与您现有的基础联系起来。

我想提最后一句话，当你们合并斯维尔托的时候。ECS 到您自己的项目中:

> 当用 svelto 开发时，用户需要计划一点他需要工作的实体以及这些实体将会有什么行为。引擎[和实体组件]可以随着时间的推移而添加，所以关键是要对要处理的实体有一个好的概念。所有的推理都应该从实体描述符开始。

本指南到此结束！作为下一步，我建议通过[生存小例子](https://github.com/sebas77/Svelto.MiniExamples/tree/master/Example2-Survival)来看看斯维尔托。ECS 为更大的项目而行动。

我开始调查斯维尔托。ECS，因为我想为我的游戏项目找到一个起点。不管是 Entitas 还是 Svelto。ECS 是答案还没有确定，但我已经学到了很多，希望你也是！
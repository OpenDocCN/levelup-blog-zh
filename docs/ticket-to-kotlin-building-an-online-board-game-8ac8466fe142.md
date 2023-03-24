# 科特林的门票——构建在线棋盘游戏

> 原文：<https://levelup.gitconnected.com/ticket-to-kotlin-building-an-online-board-game-8ac8466fe142>

三月底，疫情改变了俄国人通常的生活方式。到这个时候，我已经思考了一段时间，一个宠物项目的想法，以实践一些主题，这些主题大多超出了我的日常工作范围。我想用 Kotlin 编程语言构建一些东西，那时我已经集中学习了几个月，超越了简单的 [koans](https://kotlinlang.org/docs/tutorials/koans.html) 、[动手示例应用](https://play.kotlinlang.org/hands-on/overview)，以及在 Github 上的许多开源库中闲逛。我还有一个想法，用函数式编程风格构建一些真实世界的 web 应用程序。我唯一缺乏的是要构建什么样的应用程序的想法。

![](img/b59b582955437a5048dce1148426e791.png)

照片由[戴夫·亨利](https://unsplash.com/@mirapolis?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在疫情之前，我们曾经一两周和朋友聚会一次，玩一种著名的棋盘游戏[票骑](https://www.daysofwonder.com/tickettoride/en/)。所以当隔离开始时，我立刻想到了一个主意——我打算在接下来的几周里用我的空闲时间来制作这个游戏的网页版。我很快发现外面有这个游戏的一些网络版本，但我故意连看都不看。这首先是我的编程挑战，我的新编程语言、技巧和技术的游乐场，我最不想做的事情是模仿其他实现或试图以任何方式与它们竞争。

现在代码已经达到了某个稳定的阶段，已经玩了几个游戏，也收集了反馈，我决定以一系列博客文章的形式向外界展示它。同样，这篇博文的主要焦点，就像游戏本身一样，将是技术部分，而不是棋盘游戏规则、图形或网页设计主题。我将告诉你关于架构，我所做的设计决定，我所使用的库和 API 等等。在与你分享我大概有价值的经验的幌子下，我实际上是想收集来自其他极客的评论和批评，也许会有一些有趣的讨论，并在一天结束时向读者学习:)

源代码托管在 [GitHub](https://github.com/Kiryushin-Andrey/TicketToRide) 上。你也可以在[欧盟](https://ticketgame.herokuapp.com/)或者[美国](https://ticketgame-us.herokuapp.com/) Heroku 主机上试试这个游戏(没有区别，选一个最适合你的)。你可以在这里找到游戏规则——但是我强烈建议你买下它，因为在网下玩它是值得的。还要注意，我的在线游戏目前缺少双航线、隧道和渡轮。

![](img/e6b48b20aa3465432a98d909ad16462b.png)

由 [Max Duzij](https://unsplash.com/@max_duz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我要触及的话题进一步列出。

## 科特林多平台

项目代码全部是 [Kotlin](https://kotlinlang.org/) 并且使用 [Kotlin 多平台](https://kotlinlang.org/docs/reference/multiplatform.html)，前端编译成 JS，后端编译成 JVM，并且大量代码在后端和前端之间共享。由于我以前的背景，我很快就被 Kotlin 多平台所吸引。很长一段时间以来，我一直是一名使用 C#和 TypeScript 的全栈 web 开发人员。我甚至积极参与了一个[开源库](https://github.com/smartcatai/ts-client-gen)的开发，为这些特定的语言搭建前端和后端的桥梁——还有什么比用同一种语言编写两端并在它们之间自由共享所需的代码更好的桥梁呢？

## 函数式编程

我努力尽可能地坚持函数范式，它非常有效。代码易于编写和扩展，同样重要的是，它非常有趣。可变数据在代码库中很少见，我大多在集合上使用高阶函数而不是命令式循环，大部分核心逻辑函数都很纯粹，短到被编码成表达式体的地步。另外值得一提的是，我没有使用旨在帮助用 Kotlin 编写功能代码的 [Arrow](https://arrow-kt.io/) 库。我浏览了一下，我将学习更多关于它的知识，看看它如何适合我的代码——但一般来说，Kotlin 本身对函数式编程相当友好，你也不需要理解或使用所有那些函子、单子、类型类和其他东西来开始编写函数式的东西。

## 谷歌地图 JavaScript API

我决定使用谷歌地图作为游戏的底层地图。它让我不用自己绘制地图(我不擅长绘制)，还免费提供了许多功能，如缩放、滚动，以及为任何国家、地区或城市轻松构建新地图的能力。

这款游戏只是在谷歌地图上绘制了一些简单的折线和带有工具提示的标记——没有高级 SVG 或复杂的多边形。然而，如果这是你第一次将谷歌地图嵌入到你的网络应用程序中，我在谷歌地图 API 方面的经验可能对你有用。我还必须通过 [Dukat 工具](https://github.com/Kotlin/dukat)将 API 的类型声明转换为 Kotlin 代码，以便以静态类型的方式从 Kotlin 代码中使用它们。

## 在 Kotlin 中使用 React 构建前端

地图不是游戏前端的唯一部分——它周围有一些控件和一个欢迎屏幕。我在 React 和 Vue.js(我对 Angular 不太熟悉)之间犹豫了一段时间，但最终选择了 React。主要原因是开源社区使用了 React with Kotlin。虽然现在有针对 Vue.js 的 Kotlin 库，但我可能不得不手工编写更多的包装器——这也是一项有趣的任务，但也许下次吧。我还利用 [Material-UI 组件](https://material-ui.com/)构建了一个不错的 UI，没有太多的麻烦。

## 使用 WebSockets

我很快发现传统的基于 HTTP API 的通信并不适合这个游戏。通信必须是双向的，所以我必须使用 [WebSockets](https://developer.mozilla.org/ru/docs/WebSockets) 来即时更新玩家浏览器中的游戏状态。所以我想，为什么不同时使用 WebSockets 进行客户端-服务器和服务器-客户端通信呢？通过 WebSockets 的通信也很适合我在前端和后端用来实现游戏逻辑的状态机，因为通过单个 WebSocket 连接发送的所有请求都是序列化的(也就是说，它们不能彼此并发运行)。在即将到来的帖子中，我将仔细研究后端和前端状态机是如何构建的，它们如何通过 WebSockets 进行通信，以及游戏中断开和重新连接的处理方式。

## 基于属性和模型的测试

我很兴奋但还没有机会在现实项目中广泛使用的一个主题是基于属性的测试。其背后的思想是编写一个测试用例，作为一个接受一些输入参数的函数，然后检查对于这些输入的任何范围都必须保持的一些属性。然后，测试库随机生成输入数据，并试图伪造声明的属性。这个想法源于 Haskell QuickCheck 库，然后被移植到多种语言，包括 Kotlin。从科特林的角度很好地概述了这一技术，可以在这里找到，我是从关于优秀的斯科特·沃斯钦的 [F#为了乐趣和利益](https://fsharpforfunandprofit.com/)的[文章](https://fsharpforfunandprofit.com/posts/property-based-testing/)中学到的。我使用基于属性的测试来测试一些图形算法，这些算法根据玩家构建的路线来计算最终玩家的分数。

乍一看，基于属性的测试似乎只适用于某些范围狭窄的问题，但实际上它几乎可以应用于任何代码，无论是功能性的还是命令性的。基于模型的测试是基于属性的测试技术的扩展，它建议将随机生成的动作序列应用于被测系统和一个更简单的模型，然后将系统状态的某个子集与这个简化模型的状态进行比较。我参考了 [FsCheck 文档](https://fscheck.github.io/FsCheck/StatefulTesting.html)以获得清晰简洁的描述和这个强大方法的示例。游戏的代码库特别适合这种方法，因为它为游戏状态明确定义了后端和前端类，并且为每个可能应用于该状态的动作定义了一个类。

## 下一步是什么

我希望这些话题会引起一些黑客的兴趣，我将有机会得到一些关于我的作品和代码的反馈。以下是这个系列的故事列表:

*   [在 React 网络应用中使用谷歌地图](https://medium.com/@kiryushin.andrey/ticket-to-kotlin-using-google-maps-in-react-web-application-745d8fb0ab08)
*   [用 Dukat 在 Kotlin 和 JS 世界之间架起桥梁](https://medium.com/@kiryushin.andrey/ticket-to-kotlin-using-dukat-to-bridge-kotlin-with-the-js-world-431a2458b95c)

更多的还在后面。敬请期待！
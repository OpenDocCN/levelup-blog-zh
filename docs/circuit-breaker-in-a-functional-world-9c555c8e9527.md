# 功能世界中的断路器

> 原文：<https://levelup.gitconnected.com/circuit-breaker-in-a-functional-world-9c555c8e9527>

![](img/28170135237e3fee0ce1d19cdade74e2.png)

电路断路器内部构件

今天，我想谈一谈企业架构中最常用的模式之一——断路器——及其使用纯功能方法的实现。为了使它更有趣，我将把它实现为一个 TypeScript 模块——这样您就可以在您的前端应用程序中使用它。

> *注意:所述包装的设计灵感来自于* [*胶水。断路器*](https://hackage.haskell.org/package/glue-core-0.4.2/docs/Glue-CircuitBreaker.html) *模块为 Haskell。模块本身在这里有:*[*github://断路器-monad*](https://github.com/YBogomolov/circuit-breaker-monad) *。*

# 什么是断路器？

# 背景和问题定义

您的应用程序正在通过某种协议与第三方服务进行通信。它可能是一个 HTTP API、一个 socket API，或者其他什么东西，但是它的主要特征是:

*   你不能控制网络的可靠性，所以数据包可能会被随机丢弃。
*   您无法控制服务的可靠性，因此它可能会在意想不到的时候失败。
*   服务错误和网络故障是暂时的。一段时间后，服务和环境会自我恢复。
*   如果服务在一定的超时时间后没有响应，请求将被视为失败。

# 解决办法

断路器是一种设计模式，类似于具有以下状态的简单[有限状态机](https://en.wikipedia.org/wiki/Finite-state_machine):

1.  **关闭** —请求被直接传输到服务。如果请求失败，内部错误计数器增加。如果某个时间跨度内的错误量大于给定的阈值，断路器的状态变为**打开**，并且内部计时器启动——断路器给外部服务一些时间来恢复。定时器到时，状态变为**半开**。
2.  **打开** —请求立即完成，但出现错误。
3.  半开 —在这种状态下，第一个请求(从现在开始我称它为*金丝雀请求*)被允许传递给服务。如果金丝雀请求成功，则认为错误已解决，电路再次进入**关闭**状态。

这是对断路器模式的简单描述。你可能想阅读 Martin Fowler 的一篇博客文章，这篇文章讲述了断路器使用传统命令式语言的实现。

# 功能实现的挑战

当您阅读该模式的描述时，您可能已经注意到断路器是一个有状态模式，并且它需要该状态的原子变化(因为您希望这个东西尽可能健壮，因为它应该处理所有网络请求的*。*

在函数世界中，我们有一种方法来实现这样的需求:一个**IORef**monad——IO monad 中的一个可变变量。基本上，它让你拥有一段(纯)功能代码，与(不纯的)外部世界交流，并自动修改那些计算中的变量。你可能想看一看[它是 Haskell 实现](http://hackage.haskell.org/package/base-4.12.0.0/docs/Data-IORef.html)以获得更多细节。

# 履行

让我们深入研究代码！作为我选择的库，我将使用令人敬畏的 [fp-ts](https://github.com/gcanti/fp-ts) ，它已经实现了`IORef`，以及`Task`、`Either`和它们的组合(`TaskEither`)单子。

首先，我们来定义一下自己的基本逻辑。

1.  断路器是一个网络请求的代理包装器，我们将它建模为一个简单的【thunk，将调用的实现留给用户——无论他或她将使用`fetch`、普通 ol’`XMLHttpRequest`还是`axios`，这都无关紧要。
2.  当我们使用无状态函数方法来建模有状态断路器时，我们将利用一种通用的抽象技术，将状态作为主函数的参数。
3.  断路器调用的结果我们将建模为调用的下一个状态和延迟结果的元组。我们通常使用[任务](https://github.com/gcanti/fp-ts/blob/master/docs/modules/Task.ts.md)来表示异步交互，但是请求可能会失败，导致我们也使用`Either`单子。因此，最终的调用将使用一个[任务或者](https://github.com/gcanti/fp-ts/blob/master/docs/modules/TaskEither.ts.md)单子来建模。

让我们将断路器的状态表示为 sum 类型:

> *注意，我没有定义* `*BreakerHalfOpen*` *状态。只有有状态实现才需要它，在有状态实现中，breaker 本身控制它的状态。但是当我们使用“倒置”功能方法时，可以从当前时间和* `*BreakerOpen*` *的有效载荷、* `*timeOpened*` *的组合中容易地推断出“半开”状态。*

给定这些状态，我们的主断路器服务可以这样定义:

`callIfClosed`的实现非常简单:

功能`incErrors`用于在错误计数达到阈值时，将断路器的状态从“闭合”更改为“断开”:

功能`callIfOpen`比较有意思。请记住，如果超时结束，我们需要传递一个请求！

我们谜题的最后一部分`canaryCall`，非常简单:

# 结论

现在你有了一个(几乎)纯功能性的断路器模式！为了让它更有用，最好为`MAX_BREAKER_FAILURES`、`RESET_TIMEOUT`和`BREAKER_ERROR_DESCRIPTION`变量提供配置。我喜欢用`Reader`单子来做这件事。所以你可以在这里找到最后一个实现:[github://circuit-breaker-monad](https://github.com/YBogomolov/circuit-breaker-monad)。星，叉，并尝试在您的项目中使用！如果你有什么建议或者发现了什么问题，请在[问题版块](https://github.com/YBogomolov/circuit-breaker-monad/issues)给我写信。

我很乐意了解你的经历，所以请给我回电话，电报回复 [@ybogomolov](https://t.me/ybogomolov) ，推特回复 [@YuriyBogomolov](https://twitter.com/YuriyBogomolov) ，或者通过[yuriy.bogomolov@gmail.com 回复](mailto:yuriy.bogomolov@gmail.com?subject=Circuit%20Breaker%20as%20a%20Monad%20feedback)。

[![](img/9914c5dd23ac08b70eea6f4f9ba6fed2.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn/typescript) [## 学习 TypeScript -最佳 TypeScript 教程(2019) | gitconnected

### 18 大 TypeScript 教程-免费学习 TypeScript。课程由开发人员提交并投票，从而实现…

gitconnected.com](https://gitconnected.com/learn/typescript)
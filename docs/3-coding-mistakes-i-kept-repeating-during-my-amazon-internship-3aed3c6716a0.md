# 我在亚马逊实习期间不断重复的 3 个编码错误

> 原文：<https://levelup.gitconnected.com/3-coding-mistakes-i-kept-repeating-during-my-amazon-internship-3aed3c6716a0>

![](img/81fd240c8078f989b834697be70905db.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[Marques Thomas @ querysprout . com](https://unsplash.com/@querysprout?utm_source=medium&utm_medium=referral)拍摄的照片

## 从我的错误中吸取教训。像专业人士一样编码！

去年，我作为一名软件工程师在亚马逊商务部门实习。

我以前从未在科技公司实习过，所以我非常渴望吸收尽可能多的知识，获得尽可能多的经验。

完整的经历是另一天的故事，但今天我想写一下我在实习期间努力改掉的坏习惯。

![](img/c2bc628b050af59ab08f63cabe2e51ae.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[张彦宏](https://unsplash.com/@danielkcheung?utm_source=medium&utm_medium=referral)的照片

# 代码缩写。

出于某种原因，我无法停止一遍又一遍地犯同样的错误。每两次代码审查，我团队中的高级工程师都会强调同样的事情。

“不要缩写你的代码。令人困惑，也让别人更难理解”。

对于某些上下文，我会缩写一些我认为非常明显和容易理解的变量和类名。

例如:

*   “res 或 resp”表示回应
*   会话的“sesh”
*   用于连接的“连接器”
*   “err”表示错误
*   产品的“产品”
*   上下文的“ctx”

## 不要假设任何事情

其中一些可能看起来非常明显，但是我现在相信最好不要做假设。

在亚马逊，我的团队有着多样化的背景，这也是大多数团队的真实情况。

想想西班牙、墨西哥、加拿大、波多黎各，甚至意大利。

即使在同一种文化中，每个人都以自己独特的方式编写代码，所以仅仅因为这对你来说是显而易见的，并不意味着对其他人也是如此。

理解一个缩写的变量或类名的含义可能需要额外的一分钟甚至 30 秒，但是这些小事情累积起来，您希望您的团队成员能够容易地理解您的代码，而不必询问您或花费额外的时间自己去理解它。

我仍然认为这不是一条一成不变的规则，但是一般来说，我会谨慎地使我的代码尽可能地可读，即使现在没有其他人在做这件事。

![](img/58e3533d102041e82e97b78bff04ef95.png)

Artur Shamsutdinov 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 评论太多

这可能是团队或公司特有的。令我惊讶的是，我的队友评论的第一件事就是我对评论的过度使用。

以前评论很多。我在函数内部添加了注释，每隔几行就添加一条注释。

这是我学到的最令人震惊的事情，因为我从来没有想到评论会是一件坏事。

我了解到我的代码应该足够清晰，以便记录自己。我不确定这是为了节省生产中的代码行还是类似的事情。

但是我可能已经过度补偿了，感觉我需要通过注释来解释我的代码，因为我正在缩写和犯其他错误，我将在本文中进一步讨论。

然而，我了解到，类应该包括一个简短的注释，以帮助读者理解类的目的是什么，但是当某些东西确实令人困惑或难以理解时，应该谨慎使用注释。

![](img/fe843d99886743b70fed7503703379eb.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 在同一个地方执行太多的任务

好吧，这可能看起来像一个愚蠢的错误，但我一直在一遍又一遍地犯这个错误。

我遇到的这种情况大多发生在函数内部。

**你可能知道这就是单一责任原则。**

我在一个函数中写了太多代码，以至于**执行了多个松散相关的动作。**

这也是我当初写这么多评论的原因之一。

我学会了将职责分解成不同的功能。每一个都处理问题的一小部分，然后在主函数中把它集合起来。

这极大地提高了可读性，并使调试变得容易，因为您可以在出现错误的地方跳转函数。

这听起来很容易也很简单，但是很容易忘记，也很难确定在一个新功能中什么时候应该进一步分解。

![](img/7a1b4264e69309edae508186a236e204.png)

照片由 [Neven Krcmarek](https://unsplash.com/@nevenkrcmarek?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 不使用设计模式(当需要时)

当我加入时，我还没有学习设计模式，所以这一个有点难，因为我必须深入到一本书中去理解最常见的模式以及何时使用它们。

首席工程师推荐了一本书 ***头先设计模式*** ，我浏览了一下，找到了我在各种场合需要的模式。

这对我帮助很大！

![](img/9fe8054361e10ba771c61affbb862397.png)

彼得·西多罗夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 过度使用设计模式

作为任何 noob 工程师，我对赋予我的新知识兴奋不已，所以我开始尝试使用模式，即使它们并不是必需的。

错误地使用模式的问题是，如果被误用，它们**会带走灵活性**，并且**会给代码增加不必要的复杂性。**

我认识到，如果你能正确地识别何时使用模式，那么模式是一个很好的工具，但同样重要的是，何时不使用模式。

![](img/938d49f8cd49c5844f1e9ae5bb7c0a9a.png)

[engin akyurt](https://unsplash.com/@enginakyurt?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 你有哪些不好的编码习惯很难改掉？

你有没有和我提到的任何习惯作斗争？

请在评论区分享它们。

# 分级编码

感谢您成为我们社区的一员！更多内容见[升级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在转型理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)
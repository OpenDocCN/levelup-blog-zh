# 在我 15 年的软件工程师生涯中，我学会了避免的 10 件事

> 原文：<https://levelup.gitconnected.com/things-ive-learned-to-avoid-in-my-15-years-as-a-software-engineer-f9b661bf12a1>

## 思维程序员

## 好程序员与坏程序员

![](img/482f81db178b8dfe4fd31f1f7c8feaa3.png)

由[杰佛森·桑多斯](https://unsplash.com/@jefflssantos?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

写软件很难，但是写好的软件更难。有许多与编程技术相关的现有术语，如干净的代码、愚蠢的原则、可靠的原则等等。它们帮助我们成为一名优秀的程序员，避免那些糟糕的程序员所做的事情。

今天让我来帮你仔细看看它们。

# 1.不要只学习编码

对于任何编程语言来说，都有一套原则作为基础，比如语法、关键字、概念等等。

先不学编程语法，先学概念。每种编程语言的语法都不同。

你真正应该学习的是 OOP ( *面向对象编程)*、FOP ( *面向函数编程*)、POP ( *面向过程编程*)这样的概念；分支( *if-else，while-do* )、迭代( *for* )或设计模式。

如果你可以编写 *if-else* 程序块，但不知道如何使用它们，这意味着你学习了语法，但不理解基本概念。

# 2.避免过时的信息

关注科技新闻(*博客、时事通讯等等*)，就像我的页面一样，几乎每天都有新的更新出现。

但是请记住，每种类型的来源都有不同的优点和缺点。

技术变化非常快，使用旧资源会让你陷入困境。

所以让我们确保在你进一步查看之前，你已经检查了信息发布的日期。

在实践中，我每天都学到非常多的概念和知识。我已经跟上新兴技术了。

您还应该检查您在程序中使用的库的版本，以获得最新版本。

# 3.但是不要忘记旧书

让我们读旧书。

有些信息已经超过 10 年了，但仍然非常有用。

人类已经存在很久了。

所以几乎你遇到的每个问题都可能被生活在你之前的其他人写过。

# 4.停止在程序中使用 else 关键字

在一些简单的情况下，允许使用 else 关键字。

```
if (codition) {
    *if statements*
} else {
    *else statements*
}
```

作为一名优秀的程序员，你使用 switch-case 语句。

但是在复杂的情况下，优秀的程序员不会使用`else`关键字(或者 switch-case 语句)。

他们使用状态机代替了关键字`else`。

如果你对状态机一无所知，你就不是一个好的程序员。

更多阅读:[*https://JavaScript . plain English . io/stop-using-the-else-keyword-in-your-code-907 e82b 3054 a*](https://javascript.plainenglish.io/stop-using-the-else-keyword-in-your-code-907e82b3054a)

还有，作为一个优秀的程序员，你应该用德摩根来简化你的逻辑。

有两种方法重写布尔表达式如下所示。

```
!(a || b || c) == (!a && !b && !c)
!(a && b && c) == (!a || !b || !c)
```

# 5.不要写不好的评论

在我看来，评论是不必要的。

糟糕的评论确实比没有评论更糟糕。

如果一个评论是错误的，它就没有任何价值。

这里有一个应该避免的不好的注释，因为只有当注释描述了代码在做什么的时候。

```
/* set the value of the age integer to 32 */
int age = 32;
```

注释通常试图解释什么或如何解释，这往往只是重复代码。

所以，请记住，如果你在程序中使用注释，它应该解释为什么而不是如何使用。

也不要添加注释来解释不好的名称(变量、方法和类)。让我们花点时间来确定这些名字吧！

# 6.不要做一个愚蠢的程序员

它们是我们在编写程序时应该避免的 6 种 OOP 代码气味。

**独生子女模式**

这是最简单的设计模式之一。

它确保给定类只能有一个实例。

但是使用全局状态的程序很难测试。

依赖全局状态的程序隐藏了它们的依赖关系。

**尽量避免紧密耦合**

紧密耦合的模块很难重用。

也很难测试。

**不确定性**

每当你因为没有时间而不写单元测试的时候。

真正的问题是你的代码不好，但那是另一回事。

**过早优化的危险**

传奇的《计算机编程的艺术》[](https://www.amazon.com/Computer-Programming-Volumes-1-4A-Boxed/dp/0321751043)*的作者 Donald Knuth 有一句名言。*

*也是一本旧书。*

*过早优化是万恶之源。*

*优化应用程序只有两条规则。*

*不要这样做。*

*还是先别做。*

***非描述性命名***

*你为人们写代码，而不是为计算机。*

*计算机只是理解 0 和 1。编程语言是给人类用的。*

***重复就是浪费。***

*重复的代码是不好的。*

*当您复制代码时，您隐藏了重复的结构，并且复制了所有现有的错误。*

*所以请不要重复你自己，也保持简单，愚蠢的。*

# *7.不要从头开始构建一切*

*不要多此一举。*

*我们必须确保做正确的事情，不做不必要的事情。*

*让我们使用别人开发的工具和库。此外，还有跨语言的编码指南。*

*在冒险从头开始构建某个东西之前，试着理解世界上存在什么样的解决方案。*

# *8.不要专注于雇佣最优秀的人*

*如果你没有花时间为会议做准备。*

*也没有考虑到你需要哪些具体的信息来决定这个候选人是否是一个好的选择。*

*这次失败不仅让你失去了评估你的候选人的机会，也让你失去了你的候选人。*

*不要专注于雇佣最优秀的人。*

*最优秀的人可能是也可能不是你团队的合适成员。*

*当你招募到一个伟大的领导者时，其他人就会追随盖伊。*

*让我们雇佣合适的人，让他们开心。*

# *9.在招聘面试中避免偏见*

*有各种各样的认知偏见会影响招聘过程。*

*另一个有害的因素是个人偏见，这是人类的基本本能，让自己周围都是和自己相似的人。*

*人们有一种自然的愿望去雇佣那些具有相似特征的人:教育背景、职业经历、功能专长和相似的生活经历。*

*这种细微的相似之处通常与性能无关。这导致了视野狭窄的单一劳动力。*

# *10.不要害怕寻求帮助*

*有时候你陷入了一个问题。*

*你不可能什么都知道。*

*每个人都随时准备分享。*

*永远不要害怕或太骄傲去寻求别人的建议。*

*即使你有疯狂的问题。*

*如果我能有所帮助，请不要犹豫与我联系。*

*很简单，对吧？*

*感谢阅读，不要忘记我的一些建议。如果你喜欢我的内容，请关注我的页面。*

# *参考*

1.  *[如何设计一个系统来扩展到你的第一个 1 亿用户](/how-to-design-a-system-to-scale-to-your-first-100-million-users-4450a2f9703d)*
2.  *[每个人都应该知道的软件工程师名言](https://medium.com/geekculture/famous-quotes-about-software-engineer-everyone-should-know-9b946cd6ec66)*
3.  *[停止在代码中使用‘else’关键字](https://javascript.plainenglish.io/stop-using-the-else-keyword-in-your-code-907e82b3054a)*
4.  *[我是如何根据用途对 50 种图表类型进行分类的？](https://towardsdatascience.com/how-did-i-classify-50-chart-types-by-purpose-a6b0aa5b812d)*
5.  *[不要做一个愚蠢的程序员](/dont-be-a-stupid-programmer-be53ed49f99)*
6.  *[软件架构:你需要知道的最重要的架构模式](/software-architecture-the-important-architectural-patterns-you-need-to-know-a1f5ea7e4e3d)*
7.  *[重新思考 JavaScript 中的坚实原则](https://javascript.plainenglish.io/rethinking-solid-principles-in-javascript-7effdd4dc37d)*
8.  *[这些瓶颈正在扼杀你的代码](https://towardsdatascience.com/how-did-i-classify-50-chart-types-by-purpose-a6b0aa5b812d)*
9.  *[每个开发者都必须知道的前 20+Github Repos](/top-10-github-repos-every-developer-must-know-9da14292e284)*
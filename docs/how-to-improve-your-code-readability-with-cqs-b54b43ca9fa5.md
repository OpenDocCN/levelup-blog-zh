# 如何用 CQS 提高你的代码可读性

> 原文：<https://levelup.gitconnected.com/how-to-improve-your-code-readability-with-cqs-b54b43ca9fa5>

## 花更多时间阅读而不是编写代码是对代码库不信任的明显标志。命令查询分离(CQS)原则可以解决这个问题。

![](img/92873999adfdd8cf6eec10953fe56551.png)

米海三都

想象一下，每次你听“单身女士”这首歌都会改变。

第一次:“所有的单身女士……”

第二次:“每一位单身女士……”

第三次:“所有单身女士…”

如果是这样的话，没有人会分享这首歌，它不会成为热门歌曲，我们也不会嘲笑“单身女士”[婴儿舞蹈](https://www.youtube.com/watch?v=kU9MuM4lP18)。人类期望事物保持不变，而他们所做的只是观察。《老友记》每次重播都必须保持原样。

当代码是熟悉事物的自然延伸时，它是有意义的。这就是为什么我们给变量和方法起有意义的名字，而不命名 x1，x2，x3。

# 促进不信任的准则

检查以下代码:

在继续阅读之前，试着猜测每个方法应该做什么。

```
SetTemperature(int temperature) 
```

这个方法很明显，它应该设置机器的温度。返回的 int 代表什么？当前温度？或者一个身份代码？不知道。

```
GetTemperatureReadings()
```

根据名称，它应该返回所有温度读数的列表。酷毙了。

```
Alert(int temperature)
```

这个方法应该会发出某种警报。

好了，这些方法还不错，除了 SetTemperature()的返回值，我们对如何使用这个类有了一个很好的想法。但是让我们看看完整的实现。

## 背后的意图

冰淇淋类完全实现

SetTemperature()方法将更新冰淇淋机的温度。该方法将返回机器的最终温度，并将其添加到所有读数列表中。

如果当前温度高于 10 度，GetTemperatureReadings()方法将触发警报，并返回所有温度的列表。

## 问题是

我们不能相信它。

首先，对于 SetTemperature()方法，我们不知道它将返回什么，除非查看文档或实现。这意味着失去了时间。这就是我们如何得到“*这个代码烂透了”的感觉。*

好的代码应该是自文档化的。我争取一节课零行评语。这并不总是可能的，但我会一直记在心里。

理想情况下，所有开发人员都将把大部分时间花在编写代码上，而不是阅读上。大多数时候感觉恰恰相反。

现在，更大的问题是 GetTemperatureReadings。每次我们呼叫它，它都可能触发警报。如果我们只想得到名单，那就不好了。

# 重构 CQS

命令查询分离是一个编程原则，它规定每个方法都应该很容易被识别为命令或查询。换句话说，将改变系统状态的方法与查询系统状态的方法分开。

不要把 CQS 与 [CQRS](https://martinfowler.com/bliki/CQRS.html) (命令查询责任分离)混淆。CQRS 是一种应用于架构层面的设计模式，而 CQS 是一种原则。它可以应用于方法、类或整个解决方案。你决定吧。

如果你只记得这篇文章中的一件事，那就是:

*   命令方法应该总是返回 void
*   查询方法应该有一个返回类型，它们永远不会改变系统的状态。

在查询的方法和改变状态的方法之间有一个清晰的分离可以让开发人员信任代码。调用查询方法不能改变系统的状态。

想象一下，每次对数据库执行“选择”操作时，都会得到不同数量的行。那将是一场纯粹的噩梦。

CQS 就像是开发人员之间的一种通用语言，能够在最短的时间内传递最多的信息。

牢记 CQS 重构代码

好吧，发生了什么？

在 SetTemperature()中，我将返回类型更改为 void。现在，方法签名和名称传达了一个明确的信息:方法是一个命令。

我添加了一个 GetTemperature()来获取当前温度。请注意，您可以调用这个方法 1000 次，结果不会改变(假设当前温度没有通过 set 调用更新)。

**复活节彩蛋—**GetTemperatureReadings()方法仍然不符合 CQS。你能找出原因吗？文末的回答。

最后，我删除了 GetTemperatureReadings()中对 Alert()的调用。相反，我添加了一个新方法来模拟作业(理想情况下，这将通过一个单独的作业类来完成)。

我们以一个扩大的班级结束。五种方法而不是三种。现在你可能会争辩说，以前在一个方法调用 SetTemperature()中完成的事情，现在需要两个调用。这有点低效。对此，我说，“这无关紧要”。代码可维护性和可读性方面的收益远远大于负面影响。

这种小效率思维随处可见。想象一下，我们将当前温度存储在一个文件中，而不是存储在内存中。现在，我们有两个文件调用，而不是一个。请不要担心。这些收益是如此之小，以至于 97%的时间它们都不会产生影响。

> “程序员浪费了大量的时间去考虑或担心他们程序中非关键部分的速度，当考虑到调试和维护时，这些提高效率的尝试实际上会产生强烈的负面影响。我们*应该*忘记小的效率，比方说 97%的时间:**过早的优化是万恶之源。然而，我们不应该错过这关键的 3%的机会。”— [唐纳克努特](http://wiki.c2.com/?DonaldKnuth)**

## 规则就是用来被打破的

虽然“单身女士”的打击总是一样的，但也有观察到一些东西会改变其状态的情况。例如，如果你想测量轮胎内部的压力，在安装轮胎压力计时会损失少量的空气。不损失空气的测量太麻烦了。不值得。

编码也一样。CQS 是一个应该指导我们写代码的原则，但是有些情况下应用它会导致弊大于利。例如:

*   当我们想要弹出一个堆栈时，尽管可以遵循这个原则来实现，但在这种情况下，最好是打破它
*   在异步上下文中->在 C#异步中，你不应该返回 void

打破规则可能是有益的，但为了正确行事，你必须记住以下几点:

1.  打破规则之前先了解它
2.  在打破规则之前先了解它。

# 外卖食品

*   在命令和查询之间分离方法。
*   命令方法应该返回 void。
*   查询方法应该返回值，并且永远不改变系统状态。
*   简洁地命名你的方法

复活节彩蛋解决方案:

```
public List<int> GetTemperatureReadings() //removed call to alert
{
    //the problem was the Add reading to temperature readings.
    //the add was altering the list making this query method into a command. 
    return new List<int>(temperatureReadings); 
}
```

# 进一步阅读

[](https://martinfowler.com/bliki/CommandQuerySeparation.html) [## bliki: CommandQuerySeparation

### 术语“命令查询分离”是由 Bertrand Meyer 在他的书《面向对象软件构造》中提出的…

martinfowler.com](https://martinfowler.com/bliki/CommandQuerySeparation.html)  [## CQS 与服务器生成的 id

### 在保存实体时，如何既遵循命令查询分离，又给实体分配唯一的 id？这个帖子…

blog.ploeh.dk](https://blog.ploeh.dk/2014/08/11/cqs-versus-server-generated-ids/) [](https://danylomeister.blog/2020/06/16/cqs-and-its-impact-on-design/) [## CQS 及其对设计的影响

### 先从缩写说起。CQS 主张命令-查询分离。这个术语是伯特兰·迈耶发明的…

danylomeister .博客](https://danylomeister.blog/2020/06/16/cqs-and-its-impact-on-design/)
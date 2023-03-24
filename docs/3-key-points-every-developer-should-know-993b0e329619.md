# 每个开发人员都应该知道的 3 个要点

> 原文：<https://levelup.gitconnected.com/3-key-points-every-developer-should-know-993b0e329619>

## 每个开发人员都应该学习的 3 个关键技巧

![](img/de76bbda4e9454debf4d8f2db4b1ef89.png)

由[卢卡斯·布拉塞克](https://unsplash.com/@goumbik?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我的第一个演示充满了错误。未设置配置项。签到——然后祈祷。错误是我的错，但它们本来是可以避免的。我需要我的代码更加健壮。

遵循编码技巧——写出更好的代码。这代表了每一个开发者。包括我自己。

我利用导师来提高和成长。他们提出了解决方案、要读的书和要遵循的建议。我看了，学了，尽快落实。

让我们来看一下每个开发人员都应该知道的 3 个要点。你不应该就此打住，自己做研究，学的不止这些。

# 1.你的变量命名正确吗？

由于各种各样的误解，我造成了生产的停顿。对我来说，这个关键点很关键。

我已经暂停了签出过程，因为有一个变量命名不当。这一条导致了合格客户，失去所有资格。

查看变量的用途——适当地命名变量。

写完代码后检查一下。总有改进的空间。

## 旗帜

检查标志名称。标志是控制代码流的变量。

```
if(isCustomerInvalid) {...}
```

揭示你的旗帜的意图。你不应该在你的标志中使用`flag`或`cond`。这将导致糟糕的代码质量。总是用有意义的、易读的和易记的名字来命名旗帜。

## 神奇的数字

```
if(customer.getQualification().equals('A')) { ... }
```

阅读任何代码，你都会遇到带有幻数的块。将它们提取为常数。为什么？您可以重用该常量，轻松地更改它，并使用该常量或另一个常量进行测试。

```
if(customer.getQualification().equals(DEFAULT_QUALIFICATION_LEVEL)) { ... }
```

加常数，为加而加，无济于事。提取幻数，变成愚蠢的常数，不会有任何结果。

```
private static final int ONE = 1; // bad naming; what does ONE stands for? One Tesla? One Bitcoin?private static final int MINIMUM_QUANTITY = 1; // good name; readable; memorable; meaningful
```

## 功能

用主动语态命名函数。恰当地命名谓词，避免含糊和误导的名称。

我将用几个例子来跟进这个前提。你决定哪一个更好。

```
private boolean validateXYZProduct(XYZProductModel xyzProduct){...}private boolean isProductPurchasable(XYZProductModel xyzProduct) { ... }
```

# 2.所有的开发者都会犯错误

我制造并解决了很多 bug。到目前为止已经有几百个了。有些比较难，有些比较容易解决。

所有的开发者都会犯错误。测试并不能涵盖全部。我们应该努力尽可能多的测试。

> 初学者倾向于责怪编译器、库或者除了他们自己的代码之外的任何东西。有经验的程序员也想这么做，但是他们知道。现实一点，大部分问题都是自己的错。—编程的实践

## 检查最近的更改

大多数错误发生在代码更改之后。在现有代码的基础上添加新功能。修复相关代码模块中的错误。

跟踪您团队的拉动请求。检查任何不寻常的东西。在大多数情况下，这足以追踪 bug。

## 检查日志

通过适当的日志记录，可以检查堆栈跟踪。程序崩溃的状态在日志中。在本地复制它，调试并解决问题。

## 分享您的问题

与团队成员讨论您的 bug。这有助于你在这个问题上采取一种超然的观点。你可以找到解决办法。这种情况并不罕见。

## 防止未来的错误

修复 bug 是不够的。您需要准备代码，以备将来出现同样的问题。现在您知道了，您至少可以添加日志记录，并将其用于将来的问题。

这种事在我身上发生得太频繁了。我解决了这个问题，但也提供了更多的日志记录。或者添加一个异常来保留状态，并将其用于将来的调试。

问题已解决—编写测试。用测试覆盖您的更改，这样将来的更改不会影响您的代码。

# 3.捍卫你的代码

我的第一个 bug 是无效输入。我至今记得这件事。“米洛斯，你的代码不够健壮！” —我的雇主说。我没有在我的第一份工作中测试我的代码。当雇主用 real API 运行我的代码时，它由于无效输入而中断。

错误处理在任何软件中都是至关重要的。你应该总是保护你的代码，防止无效的输入。当无效输入发生时——尽最大努力保护程序的其余部分。

## 保护您的代码免受外部威胁

大多数安全威胁来自外部。输入、API 响应是失败的主要因素。净化您的输入，检查静态代码安全性分析，并创建安全、健壮的代码。

## 测试你的怀疑

从长远来看，创建单元测试来证明代码的有效性是有帮助的。您可以放心，您的代码正在运行。添加无效输入、恶意输入，并测试您的代码。

## 处理错误

下面的例子来自“错误处理”部分的“代码完整，第二版”。我会补充我对每一个的看法。更多细节请阅读本书本身。

处理错误的一种方法是返回错误代码。我发现这是最有益的。它遵循 REST 原则，其中有状态代码。如果需要，您可以轻松地解析错误代码并放置有价值的日志。

另一种方法是关机或重启。你试过重启吗？我做到了。看看仙丹里的[主管](https://hexdocs.pm/elixir/Supervisor.html)。处理错误的好方法。大多数错误都是暂时的，所以从一个新的状态开始可以修复很多错误。

从事 web 开发，我喜欢健壮性。你必须总是向你的顾客展示一些东西。永远不要把它们挂起来，或者放在错误页面上。这导致用户体验差，转化率下降。

# 额外收获:多读代码，少写代码

[读码小技巧](https://betterprogramming.pub/3-tips-to-become-a-better-code-reader-c84fcb4cedc3)常青。你读代码的次数是写代码的两倍。

通读时，您会发现许多错误代码。我已经防止了至少两个错误，仅仅是代码读取。缺乏代码阅读——更多的错误进入生产。

## 资源

代码完整第二版—史蒂夫·麦康奈尔

编程的实践——布莱恩·w·克尼根，罗布·派克

## 继续阅读相关文章

[](https://medium.com/dev-genius/does-jira-improve-agility-4a76db86ff52) [## JIRA 能提高敏捷性吗？

### 沿着敏捷和 JIRA 的兔子路走下去

medium.com](https://medium.com/dev-genius/does-jira-improve-agility-4a76db86ff52) [](https://medium.com/dev-genius/why-are-design-patterns-essential-for-your-career-6d14b1837f86) [## 为什么设计模式对你的职业生涯至关重要？

### 我们在代码中需要设计模式吗？

medium.com](https://medium.com/dev-genius/why-are-design-patterns-essential-for-your-career-6d14b1837f86) [](https://medium.com/dev-genius/why-do-you-need-a-development-journal-e06b5854368d) [## 你为什么需要一本发展期刊？

### 记录想法和工作有助于你的事业。这就是为什么你需要开始写日记。

medium.com](https://medium.com/dev-genius/why-do-you-need-a-development-journal-e06b5854368d)
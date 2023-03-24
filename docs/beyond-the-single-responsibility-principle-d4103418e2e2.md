# 超越单一责任原则

> 原文：<https://levelup.gitconnected.com/beyond-the-single-responsibility-principle-d4103418e2e2>

## 最著名的坚实原则以及它如何改变你的思维定势。

当谈到讨论[坚实的原则](https://en.wikipedia.org/wiki/SOLID)时，通常首先想到的是[单一责任原则](https://en.wikipedia.org/wiki/Single-responsibility_principle)，或简称 SRP，首字母缩写。它变得如此明显，以至于没有人真正去思考它，它的重要性在今天被认为是理所当然的。

这是罗伯特·c·马丁的原创表达(又名鲍勃叔叔:)

> 一个类应该只有一个改变的理由。

![](img/29b72de2c498eaf03337dc7a85b710db.png)

照片由[叫我汉格里·🇫🇷](https://unsplash.com/@callmehangry?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/single?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

在我早期编程的日子里，这个简单的想法给我留下了深刻的印象，以至于它成为了我的工作思维定势。现在它对我来说不仅仅意味着“编写小的、可测试的类”。我相信它可以应用于软件开发的任何方面。我会试着给你看。

# 密码

## 变量

让我们从一些小而简单的事情开始。这是一个改编自[史蒂夫·麦康奈尔](https://stevemcconnell.com)的[代码全集](https://www.amazon.com/Code-Complete-Practical-Handbook-Construction/dp/0735619670)的例子:

*(所有代码片段在*[*Swift*](https://swift.org)*中给出)。)*

```
**import** Foundation// Qadratic equation: a * x^2 + b * x + c + 0
**let** a = 1.0
**let** b = -8.0
**let** c = 12.0**var** temp = sqrt(pow(b, 2.0) - 4.0 * a * c) // Discriminant.
**var** root1 = (-b + temp) / (2.0 * a)
**var** root2 = (-b - temp) / (2.0 * a)// Do work.// Interchange roots.
temp = root1
root1 = root2
root2 = temp
```

重用变量会使代码更难理解。它还迫使以笨拙的方式命名变量，这反过来会对可读性产生负面影响。更不用说一些重复使用变量的情况可能导致的副作用了。

(考虑这样的情况，当一个变量被初始化一次，然后它参与一些工作，在那里它被赋予一个新的值。然后，有了新的值，它被进一步传递，以参与另一项工作。更改初始值会影响使用该变量的两段代码。而第二个可能是不想要的，甚至是不被注意的。)

这是一个稍微安全和可读的版本:

```
**import** Foundation// Qadratic equation: a * x^2 + b * x + c + 0
**let** a = 1.0
**let** b = -8.0
**let** c = 12.0**let** discriminant = sqrt(pow(b, 2.0) - 4.0 * a * c)
**var** root1 = (-b + discriminant) / (2.0 * a)
**var** root2 = (-b - discriminant) / (2.0 * a)// Do work.// Interchange roots.
**let** temp = root1
root1 = root2
root2 = temp
```

## 评论

考虑上面的代码注释。如果把它们合二为一呢？

```
**import** Foundation/*
Solves quadratic equation: a * x^2 + b * x + c + 0
(Does some work.)
Then interchanges equation's roots values.
*/**let** a = 1.0
**let** b = -8.0
**let** c = 12.0**var** temp = sqrt(pow(b, 2.0) - 4.0 * a * c) // Discriminant.
**var** root1 = (-b + temp) / (2.0 * a)
**var** root2 = (-b - temp) / (2.0 * a)// Do work.temp = root1
root1 = root2
root2 = temp
```

如果交换代码消失了呢？最有可能的是，评论会被忽略，不变，变得过时。这就是为什么，主要是，你在写评论的时候也要抓住 SRP。当然，首先要记住，良好的、自我记录的代码总是比注释好。

## 试验

再次考虑上面的代码。你如何测试它？很难检查整部作品中的一切是否都像预期的那样工作。这就是为什么编写一次只检查一件事情的单元测试被认为是一个好的实践:

```
**func** testDiscriminantCalcualtion() {
  // ...
}**func** testQuadraticEquationRootsWhenDiscriminantIsPositive() {
  // ...
}**func** testQuadraticEquationRootWhenDiscriminantIsZero() {
  // ...
}**func** testQuadraticEquationHasNoRootsWhenDiscriminantIsNegative() {
  // ...
}**func** testSwappingRoots() {
  // ...
}
```

把单元测试很好地分开，如果有什么东西坏了，你会确切地知道是什么。

## 功能/方法

测试的例子让我们明白了分离所有类方法和函数的原则:

```
**import** Foundation/// ax2 + bx + c = 0
**func** findQuadraticEquationRoots(a: Double,
                                b: Double,
                                c: Double) -> [Double] {
  **let** discriminant = findDiscriminant(a: a, b: b, c: c) **if** discriminant < 0 {
    return []
  }
  **if** discriminant == 0 {
    let root = -b / (2.0 * a)
    **return** [root]
  } **let** root1 = (-b + discriminant) / (2.0 * a)
  **let** root2 = (-b - discriminant) / (2.0 * a) **return** [root1,
          root2]
}**func** findDiscriminant(a: Double, b: Double, c: Double) -> Double {
  **return** sqrt(pow(b, 2.0) - 4.0 * a * c)
}**let** a = 1.0
**let** b = -8.0
**let** c = 12.0**let** roots = findQuadraticEquationRoots(a: a, b: b, c: c)
**var** root1 = roots[0]
**var** root2 = roots[1]// Do work.swap(&root1, &root2)
```

这样，上面所有的测试都可以很容易地编码。当然，这不仅仅是可测试性的问题。众所周知，程序员读代码的时间比写代码的时间多。方法关注点的分离也使得代码更具可读性(从而更易维护)。)

瞄准没有副作用的方法，所谓的[纯函数](https://en.wikipedia.org/wiki/Pure_function)，将使你的代码更不容易出错，错误更容易本地化。

## 班级

*(作为一个概括，我这里的类不是指，嗯，只是类。我是说所有时代的味道。即在 Swift 中，它们是* `class` *es、* `struct` *s 和* `enum` *s.)*

所以，经典的 SRP 来了。这个，希望不需要解释。让你的班级只负责一件事。这将使它们更容易理解，可测试，最重要的是，可组合。也就是说，如果一些需求发生了变化，它不会影响应用程序的其他部分。这正是“改变的一个理由”的含义。

## 模块

微服务、框架、后端-前端分离，所有这些(以及更多)都是模块化的味道。单独构建系统的不同部分，使它们能够独立地组合和部署，这一直被认为是一个好的实践。客户端 API、本地数据库层、业务规则集是独立模块的良好候选。有时候，独立地分离和组织代码是很棘手的，但是，总的来说，这是值得的。

# 发展

在我看来，SRP 不仅仅是编程。牢记这一点有助于组织你的工作，让你的日常工作更有条理。

## 承诺

这是大多数开发人员都听过的一个显而易见的问题。然而，从我的经验来看，很少有人真正遵循它。这里有一个:

> 尝试让每个提交成为一个逻辑上独立的变更集。

这是来自[Git 官方网站](https://git-scm.com/book/en/v2/Distributed-Git-Contributing-to-a-Project)的推荐。我会把它重新表述为“尝试让每个[提交](https://git-scm.com/docs/git-commit)原子化。”虽然在 95%的情况下，这种额外的努力不会被注意到，但最终会有回报的。[恢复](https://git-scm.com/docs/git-revert)、[精选](https://git-scm.com/docs/git-cherry-pick)、[重定基础](https://git-scm.com/docs/git-rebase)和 bug 搜索 git 历史简洁明了，用原子提交构建，一切都简单多了。

此外，如果提交的更改需要单元测试更新或文档修改，我强烈建议您将所有这些附属工作包括到提交中。这将确保在您需要恢复或重置主提交时不会忘记它。

## 拉式请求/票证

关于提交的讨论完美地扩展到了分支(或吉拉票)。)如果您不想通过提交来跟踪小特性，以便禁用它们，您最好遵守与提交相同的规则。

## 应用程序

最后，这是上述所有小事的原因。也是博文中最有争议的部分。

就个人而言，我不喜欢流行应用程序的发展方式。记得 [Foursquare](https://foursquare.com) 吗？它曾经是一个很好很简单的应用程序，让你的朋友知道你现在在哪里玩得开心。然而，功能越来越多，这个应用变成了两个——four square 和 Swarm。在那之后不久，从我的印象中，整个想法变得过时了。

另一个更有争议的例子是著名的 Instagram。当它只是我朋友的照片时，我真的很喜欢它。后来他们添加了视频，然后是故事，广告，很多东西。尽管如此，这款应用仍然非常受欢迎。然而，当我现在打开它时，它并不像过去那样有趣。我就像一个得到了一艘 [NASA](https://www.nasa.gov) 星际飞船而不是一套[乐高](https://www.lego.com/)火箭套件的小孩一样，跌倒在那里。

我在这里的观点是，应用程序应该有一个清晰的想法，它们应该简单和令人愉快，而不是充斥着所有可以想象的功能的怪物。当然，你很容易不同意我的观点。我不是营销专家，我只是个工程师。但我认为，任何应用程序都必须首先关注其主旨。

当然，你应用这些原则的程度取决于环境。永远不要忘记思考你当前的目标，牢记常识和原因。

如果你喜欢阅读我(和其他作者)在 Medium 上的博客，你可以**成为一名正式的 Medium 成员**(如果还没有) [**这里**](https://lazarevzubov.medium.com/membership) 。这样做，你就支持了所有的媒体作者。🙏

…或者你可以给我买杯咖啡！☕
# 如何避免在代码中使用 Else

> 原文：<https://levelup.gitconnected.com/how-you-can-avoid-using-else-in-your-code-871197a1adbc>

![](img/3ab301f767fbce0d377e8444934f2ff9.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Courtney Corlew](https://unsplash.com/@courtneycorlew?utm_source=medium&utm_medium=referral) 拍摄的照片

## 有更好的方法来组织你的代码

我们一直在代码中使用 else 子句。很可能是出于习惯，没有想太多。Else 子句似乎已经融入了我们解决编码问题的技能库中。一旦你开始学习如何编程，你要学习的第一个概念就是 if-else 语句。

在代码中使用 else 看似无害，但真的是这样吗？

当你稍微多考虑一下你的代码时，你会发现大多数 else 块都是多余的。所有这些多余的 else 块给你的代码带来的是不必要的冗长，可读性差，看起来比实际更复杂。

尽管当使用一个 if-else 语句时，代码仍然具有很好的可读性。它会很快变得更糟。例如，在第一个 if-else 语句的 else 块中使用另一个 if-else 语句时。一旦你需要这样的结构，你可能需要重构代码。

如果我们知道如何去除这些 else 块，代码将变得更容易阅读。最重要的是，将会有一个单一的真理之路，使你的代码更容易被其他开发者理解。

这就是为什么我们要讨论四种方法来避免代码中的 else 块。这些备选方案的最大好处是它们使您的代码更加优雅。

# 使用退货

if-else 语句最经典的形式看起来非常简单。

```
if (someCondition) {
   // 10 lines of code
} else {
   // More code..
}
```

这里不涉及火箭科学。可以肯定地说，每个程序员以前都写过类似这样的代码。大多数程序员在日常工作中都会编写这样的代码，而不会花太多心思。

但是如果我们去掉 else 块，代码会是什么样子呢？

不使用 else 块，我们可以选择使用 return。这将有助于摆脱 else 块，并避免不必要的缩进代码。

这就是我们如何使用 return 来删除 else 块:

```
if (someCondition) {
   // 10 lines of code
   return;
} // More code..
```

当使用 return 时，else 块变得多余。

# 使用默认值

If-else 语句经常被用来给变量赋值——这毫无意义。您可能以前见过这样的代码:

```
if (someCondition) {
   number = someNumber * 2
} else {
   number = 1
}
```

当你发现这样的代码时，改变它。去掉 else 块的方法是分配一个默认值。

```
number = 1if (someCondition) {
   number = someNumber * 2
}
```

看起来好多了，对吧？

去掉 else 块并使用默认值有两个主要优点。第一个与变量的范围有关。使用默认值时，变量在 if-else 语句之外可用。第二个是我们有更少的代码行，但是可读性提高了。

有一种方法可以进一步优化这段代码。我们可以通过使用条件操作符使这段代码成为一行代码:

```
number = someCondition ? someNumber * 2 : 1;
```

# 保护条款

保护条款也被称为提前归还。提前返回背后的思想是，编写在函数结束时返回预期正结果的函数。

函数中的其余代码应该在与函数的目的不一致的情况下尽快触发终止。

早归顾名思义。这就是早期回报的样子:

```
function someFunction() {
    if (!someCondition) {
        return;
    }        // Do something
}
```

Guard 子句受到许多程序员的青睐，因为它们清楚地表明当前方法在某些情况下不感兴趣。如果你想执行这个方法，你必须满足一定的标准。如果你不这样做，你会提前回来。

一如既往，有更多可能的解决方案。您还可以选择反转 guard 子句，如下所示:

```
function someFunction() {
    if (someCondition) {
        // Do something
    }
}
```

就我个人而言，我更喜欢早点回来。你可以考虑使用早期返回的一个原因是它可以让你的代码看起来更扁平。如果您选择使用包装 if 语句的替代方法，那么就不需要额外的缩进。这使得代码更具可读性。

最重要的是，使用早期回报可以让程序员安心。通过使用早期返回，您可以首先处理无效的情况，然后在那里放一个空行，然后您可以专注于函数的“真正的”主体。

# 事情太多了

我们要讨论的不恰当的 if-else 语句的最后一个例子是，当你的代码中有太多东西在运行时。您可能会联想到下面这段代码:

```
if (...) {
    for(var i = 0; i < someArray.length; i++) {
        var isValid = hasValid(someVar[i]);
        if (!isValid) {
            toBeDeleted.push(someVar[i]);
        }
    }
}
else {
    var service = initSomeService();
    service.doSomeStuff(); if (service.someCondition != null) {
        service.scheduleNextTask();
    }
}
```

当 if 块与 else 块无关时，您知道 if-else 语句中发生了太多事情。将两个完全不相关的代码块组合在一起会让人很难理解，也很容易出错。最后但同样重要的是，很难为这个方法找到一个合适的名字并添加适当的注释，因为它做了很多事情。

解决这个问题的最好方法是通过尝试在代码中划分职责来重构代码。
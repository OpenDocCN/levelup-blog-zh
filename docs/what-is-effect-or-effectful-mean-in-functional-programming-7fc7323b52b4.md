# 函数式编程中的“effect”或“effective”是什么意思？

> 原文：<https://levelup.gitconnected.com/what-is-effect-or-effectful-mean-in-functional-programming-7fc7323b52b4>

![](img/2aa2cf0a1ae13b8d6e50ebcc40dc0c33.png)

很多时候，当我们讨论效果时，我们通常会谈到副作用。然而，随着我对函数式编程越来越深入的研究和阅读越来越多的函数式编程书籍，我注意到在描述抽象事物时,“Effect”或“effective”在 FP 社区中被广泛使用了很多次。

我对“效果”或“有效果的”的含义做了更深入的挖掘，并把它写在这篇博文里，作为对未来自己的一个提示。

# 这不是副作用

通常，他们所说的“效果”或“有效”是没有副作用的(有时会有副作用)。这是*的主要效果*。

# 这与类型类别有关

类型类别是一种数学结构，用于抽象出数学中所有不同领域的表示。在设计程序时，我们可以在编写代码之前考虑程序的属性，而不是反过来。比如一个函数`sum`可以为空(同一律)，具有组合运算的性质，需要结合律。(1+2 等于 2+1)。我们可以将它们刻画为并将输入函数限制为一个*幺半群*。这样，我们可以用系统化的方法创建一个解决方案，产生更少的错误。

“在类型范畴内”是一个时髦的词，指的是对给定类型产生“效果”的包装器。我将引用 Alvin Alexander 在 [*功能和反应域建模*](https://www.amazon.com/Functional-Reactive-Domain-Modeling-Debasish/dp/1617292249/ref=as_li_ss_tl?ie=UTF8&linkCode=sl1&tag=devdaily-20&linkId=031fc4db53f522208ba4bbea056be0f8&language=en_US) 中提到的陈述:

1.  *选项*模拟可选性的影响
2.  *未来*将延迟建模为一种效果
3.  *尝试*抽象失败的后果

这些语句可以重写为:

1.  *Option* 是一个*单子*，它对可选性的效果进行建模
2.  *Future* 是一个 *monad* 来模拟延迟的影响
3.  *Try* 是一个*单子*，它对失败的影响进行建模(将异常作为一种效果进行管理)

类似地:

1.  Reader 是一个*单子*，它基于一些输入来模拟堆肥操作的效果。
2.  Writer 是一个*单子*，它模拟日志记录的影响
3.  状态是一个*单子*，对状态的影响进行建模
4.  Cats 中的 sync-effect 是一个*单子*，它模拟了同步延迟执行的效果。

# 这是 F[A]而不是 A

单子处理的东西可以说是有影响的。

引用 Rob Norris 在《带效果的函数式编程》中的话——一个有效的函数返回`F[A]`而不是`A`。

例如:

通过查看上面的代码，我们知道无需深入代码，该函数就可以根据输入类型返回一些异常或整数。通过显式声明你的函数可以返回什么效果，其他调用`division`的任务将需要提供一种机制来处理那些“效果”类型，使你的程序具有确定性。

> *“我不记得在哪里读到的，但是有人说，如果你要从哲学上思考，当一个函数返回一个 A 的时候，那个 A 已经被充分求值了；但是如果这个函数返回 F[A],那么这个结果还没有被完全计算，A 仍然在 F[A]里面等待计算。因此，有效函数不是编写一个返回原始类型的函数，而是在一个有用的包装器中返回一个原始类型——其中包装器是一个单子，允许结果以单子的形式使用(例如，在 Scala for-expression 中)。”*
> 
> *——阿尔文·亚历山大*

# 摘要

*   Effect 也可以指程序的“主要效果”。
*   而不是考虑副作用，而副作用是单子处理的。
*   一个“有效的”函数返回一个`F[A]`而不是`A`

# 来源

[效果或“有效”类型](https://alvinalexander.com/scala/what-effects-effectful-mean-in-functional-programming/#:~:text=As%20mentioned%2C%20rather%20than%20thinking,monad%20that%20you're%20using%3A&text=Reader%20is%20a%20monad%20that%20models%20the%20effect%20of%20composing,models%20logging%20as%20an%20effect)

*最初发表于*[*https://edward-huang.com*](https://edward-huang.com/functional-programming/scala/monad/2020/06/21/what-is-effect-or-effectful-mean-in-functional-programming/)*。*
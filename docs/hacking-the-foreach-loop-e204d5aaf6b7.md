# 破解 foreach 循环

> 原文：<https://levelup.gitconnected.com/hacking-the-foreach-loop-e204d5aaf6b7>

![](img/b90bea9027c9a12d2fdcd952acf9b85e.png)

由 [Setyaki Irham](https://unsplash.com/@setyaki) 在 [Unsplash](https://unsplash.com/) 上拍摄的照片

如果你更新一个`List<T>` 4294967295 次会怎么样？

要跟上进度，你需要先阅读这篇文章。

# _version 是 Int32

`_version`是一个`int`，它也从未被包裹在[选中的](https://medium.com/@DavidKlempfner/why-you-should-learn-cil-2435b2b8d8ae)块中，这意味着它会溢出。

让我们看看如果你更新一个列表 4294967295 次会发生什么:

# 漏洞

当你更新一个列表那么多次时，`_version`最终会比它的初始值小 1。

这意味着，下一次列表更新时，`_version`将回到开始时的状态。

在列表中，它认为它根本没有更新。

因此，列表可以更新，但是在下一次迭代中，当它更新时，`_version`不再与`Enumerator`中的内容相同。

这意味着在第三次迭代时，`MoveNext()`方法将抛出一个异常。

我想不出这怎么会在真实情况下意外发生，但这仍然很有趣。

# 为什么不阻止开发者溢出 _version？

如果`_version`被包裹在[检查的](https://medium.com/@DavidKlempfner/why-you-should-learn-cil-2435b2b8d8ae)块中，它将限制`List<T>`可以被更新的次数。

编译器团队决定允许任意数量的更新发生，通过允许`_version`循环所有可用的值。

我认为编译器团队理所当然地认为没有人会在更新了 4294967295 次之后尝试更新一个`List<T>`。

# 一种更有效的方式

前面描述的代码理论上可以由不想打破规则的新开发人员编写。

显然，不会编写精确的代码，但可能有一些复杂的数学算法会产生类似的代码。如果你故意试图打破规则，你可以使用**反射**来更新`_version`。
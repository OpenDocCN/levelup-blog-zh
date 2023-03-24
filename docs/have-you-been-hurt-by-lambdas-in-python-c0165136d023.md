# 你被 Python 里的 lambdas 伤害过吗？

> 原文：<https://levelup.gitconnected.com/have-you-been-hurt-by-lambdas-in-python-c0165136d023>

## 拯救默认值

奇普·肯特

![](img/45f79f5d1a17bc23469aeec604e26de3.png)

照片由[杰克逊在](https://unsplash.com/@simmerdownjpg?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上煨

在 Python 中使用 lambdas 可以节省开发人员的时间和精力，因为它创造了更高阶的思维，而无需定义一个全新的函数。然而，在某些情况下，lambdas 的行为令人困惑，甚至会导致意想不到的或不正确的结果。

在[Python 默认参数](https://deephaven.io/blog/2022/02/10/python-default-args/)中的小精灵中，我们看到了[最小惊讶原则(POLA)](https://en.wikipedia.org/wiki/Principle_of_least_astonishment#:~:text=The%20principle%20of%20least%20astonishment,will%20expect%20it%20to%20behave.) 以及它与 [Python](https://python.org/) 中默认参数的关系。在接下来的文章中，让我们来看一个有趣的例子，用 [Python](https://python.org/) lambdas 展示如何调试匿名函数的行为。

你认为这段代码会打印出什么？

```
def f():
  lambdas = []for i in range(5):
    lambdas.append(lambda x: x+i)for l in lambdas:
    print(l(3))f()
```

大多数用户希望看到:

![](img/02ab7f7ddace90826db58261b94f75e4.png)

事实上，你会看到:

![](img/f8ce351d917fb35437705bb406974f10.png)

这很奇怪。在评估时，所有的 lambdas 都有`i = 4`，即使它们是用值为 0、1、2、3 和 4 的`i`创建的。

也许 [Python](https://python.org/) 正在为整个循环创建一个 lambda。一个快速的测试表明事实并非如此。

```
def f():
  lambdas = []for i in range(5):
    lambdas.append(lambda x: x+i)for l in lambdas:
    print(l)
    print(l(3))f()
```

可能会发生什么？

用计算机科学的行话来说，这个例子中的 lambdas 是[闭包](https://en.wikipedia.org/wiki/Closure_(computer_programming)#:~:text=In%20programming%20languages%2C%20a%20closure,function%20together%20with%20an%20environment.)。lambda 函数使用了其定义范围内的绑定变量——在本例中是`i`变量。

为了更好地理解，让我们来看看绑定到 lambdas 的变量。在 [Python 的数据模型](https://docs.python.org/3/reference/datamodel.html)中，`__closure__`是一个元组，包含函数关闭的变量。这里，我打印了 lambdas 的闭包变量的地址加上来自`__closure__`的`i`的值。

```
def f():
  lambdas = []for i in range(5):
    lambdas.append(lambda x: x+i)for l in lambdas:
    print(l)
    print(f"i_addr={hex(id(l.__closure__[0]))} i={l.__closure__[0].cell_contents}")
    print(l(3))f()
```

您将很快注意到所有的 lambda 共享相同的闭包变量元组，因为闭包内容有相同的地址，并且所有的 lambda 都有`i = 4`！所以，所有的 lambdas 都是不同的，但它们都有相同的变量。lambda 从创建时就与变量名`i`相关联，而不是与变量值`i`相关联。这解释了为什么只打印值 7。

当内部函数被定义时，闭包也可以被创建。他们也有同样奇怪的行为吗？是的，他们有。

```
def f():
  funcs = []for i in range(5):
    def inner(x):
      return x+i

    funcs.append(inner)for l in funcs:
    print(l(3))f()
```

我们可以通过使用默认值为每个 lambda 绑定一个新变量来解决这个问题。这种情况下，`ii`。

```
def f():
  lambdas = []for i in range(5):
    lambdas.append(lambda x, ii=i: x+ii)for l in lambdas:
    print(l(3))f()
```

在幕后，lambda 现在在`__closure__`中不再有值，因为没有变量被封闭，但是它现在在`__defaults__`中有预期的值。

```
def f():
  lambdas = []for i in range(5):
    lambdas.append(lambda x, ii=i: x+ii)for l in lambdas:
    print(l)
    print(f"closure={l.__closure__} defaults={l.__defaults__}")
    print(l(3))f()
```

希望这能对难以调试的情况提供一些见解。当心 lambdas 和带有循环的内部函数。

你认为这违反了[最小惊讶原则(POLA)](https://en.wikipedia.org/wiki/Principle_of_least_astonishment#:~:text=The%20principle%20of%20least%20astonishment,will%20expect%20it%20to%20behave.) ？请在我们的 [Gitter 讨论](https://gitter.im/deephaven/deephaven)或 [Slack 社区中告诉我们。](https://join.slack.com/t/deephavencommunity/shared_invite/zt-11x3hiufp-DmOMWDAvXv_pNDUlVkagLQ)
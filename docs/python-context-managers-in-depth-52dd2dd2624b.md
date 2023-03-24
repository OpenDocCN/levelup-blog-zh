# Python 上下文管理器的深度

> 原文：<https://levelup.gitconnected.com/python-context-managers-in-depth-52dd2dd2624b>

像专业人士一样管理您的资源

![](img/935b1d5a193381e86435510fd4573f58.png)

来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=960248) 的[erikawittleb](https://pixabay.com/users/ErikaWittlieb-427626/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=960248)的图片

Python 是一种特别干净和甜蜜的语言，这要归功于它的许多便利特性。在这篇文章中，我将深入探讨 Python 中的上下文管理器，如何使用它们，在哪里可以找到它们，以及如何编写自己的上下文管理器。

# 为什么我们需要上下文管理器？

当我们谈论*资源*时，最常使用上下文管理器。例如，从文件中读取/写入。检查这个简单的片段:

```
f = open('log.txt', 'w') 
f.write('hello world') 
f.close()
```

这段代码所做的就是打开`log.txt`进行写入，并在其中写入`hello world`。很简单。但是假设第 2 行的代码无缘无故地抛出了一个异常。在这种情况下，文件仍然打开，但不会关闭，因为解释器不会到达第 3 行。对于一次性脚本来说，这不是问题，但是如果您要在 Python 上开发任何严肃的东西，这可能会成为一个令人头痛的问题。

我们如何解决这个问题？脑海中浮现出`try` / `finally`的建筑:

```
f = open('log.txt', 'w') 
try: 
    f.write('hello world') 
finally: 
    f.close()
```

这是可行的，因为即使抛出异常，也会执行`finally`，并且文件描述符将被丢弃。然而，这看起来并不十分 Pythonish 化...

这就是`with`关键字发挥作用的地方。它是为这个特定的目的而开发的:使资源管理可读。这个代码片段在功能上与上面的示例相同:

```
with open('log.txt', 'w') as f: 
    f.write('hello world')
```

看起来干净多了，你同意吗？

# 上下文管理器是如何工作的？

上下文管理器背后的逻辑实际上非常简单。让我们通过编写自己的上下文管理器来探索它。对于要被识别为上下文管理器的对象，它必须实现两个方法:`__enter__`和`__exit__`:

如果您尝试运行此示例，您将获得以下输出:

```
Entered into context manager! 
Inside context manager! 
Exiting context manager!
```

现在让我们尝试在`with`中抛出一个异常:

```
with TestContextManager(): 
    raise Exception()
```

如果您尝试运行这个版本，请注意仍然调用了`__exit__`方法，很像`try`块中的`finally`子句。这可用于清理和释放该上下文管理器使用的任何资源。

# `contextlib`模块

如果没有`contextlib`模块，所有这些都不会特别有用。这个模块是标准库的一部分，它提供了一些通用的构造来简化您的工作。

最值得注意的添加是`@contextmanager` [装饰器](https://everyday.codes/python/why-do-you-need-decorators-in-your-python-code/)。它可以让你将任何生成器函数转换成一个上下文管理器，根本不需要额外的代码！这里有一个例子:

虽然这仍然需要使用`try` / `finally`，但这可以让您将它抽象一次并忘记它，同时还可以让您的代码与代码库的其余部分兼容，这些代码库可能不像您现在这样理解上下文管理器。

`contextlib`的另一个有用的特性是`AbstractContextManager`类(Python 3.6 中的新特性)。这是一个[抽象基类](https://everyday.codes/python/abstract-classes-and-meta-classes-in-python/)，它实现了`__enter__`和`__exit__`功能。在您的自定义上下文管理器中使用它，以确保类型兼容。

# 结束语

感谢您的阅读，希望您喜欢这篇文章。在我的下一篇文章中，我将讨论**异步**上下文管理器。敬请期待！

# 资源

*   [Python 中的抽象类](https://medium.com/better-programming/abstract-classes-and-metaclasses-in-python-9236ccfbf88b)
*   [Python 中的自定义装饰器](https://medium.com/better-programming/why-you-need-decorators-in-your-python-code-df12d43eac9c)
*   [pep 343—](https://docs.python.org/2.5/whatsnew/pep-343.html)`[with](https://docs.python.org/2.5/whatsnew/pep-343.html)`[语句](https://docs.python.org/2.5/whatsnew/pep-343.html)
*   `[contextlib](https://docs.python.org/3/library/contextlib.html)` [单据](https://docs.python.org/3/library/contextlib.html)
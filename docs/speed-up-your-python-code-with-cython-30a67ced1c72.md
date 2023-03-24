# 使用 Cython 加速您的 Python 代码

> 原文：<https://levelup.gitconnected.com/speed-up-your-python-code-with-cython-30a67ced1c72>

## cy thon:Python 的 C 扩展

![](img/59cb172f003656f07627e6783da5a059.png)

照片由 [Grzegorz Walczak](https://unsplash.com/@grzegorzwalczak?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

你可能听过有人抱怨 Python 非常慢。他们经常在代码执行时间方面比较 Python 和 C 语言。

他们忘记了一件事，Python 是比 C 更高级的语言，它允许我们以更接近人类思维方式的方式编写代码。

就代码执行时间而言，C 编程语言确实比 Python 编程语言快很多倍。尽管如此，如果算上开发时间，Python 往往胜过 C 语言。

如果您想提高 Python 代码的速度，有一个选项可供您选择， **Cython。**在本文中，我们将讨论什么是 Cython，以及我们如何使用它来加速我们的 Python 代码。我们开始吧！

# Cython

Cython 是 Python 的 C 扩展。它允许您编写纯 Python 代码，只需做一些小的更改，就可以翻译成 C 代码，并为您提供高性能，就像 C 编程语言一样。

如果你想拥有像 Python 一样的简单语法和像 C 语言一样的高性能，Cython 就是你的选择。它是 Python 和 C 编程语言的结合。

使用 Cython，您可以使用 Python 的简单语法编写代码，并实现 C 语言的高性能。

# 装置

每个操作系统的安装过程都不同。为了使用 Cython，我们需要一个 C 编译器。你可以在这里查看不同操作系统的 C 编译器的兼容性[。](https://cython.readthedocs.io/en/latest/src/quickstart/install.html)

一旦在系统中安装了 C 编译器，我们可以使用`pip`命令简单地安装 Cython:

```
pip install Cython
```

# Cython 中可用的数据类型

我们可以使用所有静态类型来声明在 **C/C++** 语言中可用的变量和函数。

**在 Cython 中声明变量:**

我们可以使用`int`、`float`、`double`、`list`、`dict`和`object`等数据类型来声明变量。

**例如:**

```
**cdef** int x = 10;
```

**在 Cython 中声明一个函数:**

下面是我们可以用来编写函数的方法:

*   **def —** 用于常规 python 函数。只能从 Python 中调用它们。
*   **cdef —** 仅用于 Cython 功能。它们不能从 python 代码中访问，必须在 Cython 中调用。
*   **cpdef —** 对于C 和 Python 都适用。用`cpdef`类型定义的函数可以从 Python 和 C 代码中访问。

**举例:**

```
**cdef** int addition(int x, int y):
    **return** x + y
```

在这里，你可以看到语法是如何与 Python 语言相同，但我们需要静态地使用数据类型与每个变量声明。

# **如何使用 Cython？**

您可以在`Jupyter Notebook`中轻松使用 Cython。Jupyter Notebook 是一个交互式网络工具，用于开发基于数据科学和 python 的项目。

要在 Jupyter 笔记本中运行 Cython 代码，您需要运行:

```
%load_ext Cython
```

现在，每当您想要运行 Cython 代码时，我们需要在代码单元格中添加以下命令:

```
%%cython
```

完成这两步后，就可以开始在笔记本上写 Cython 代码了。

# Cython 有多快？

我们知道如何在 Cython 中声明变量以及它的语法。现在，我们想比较 Cython 与 Python 相比快了多少。

让我们以斐波那契数为例，用 Cython 和 Python 计算执行时间。

**Python 代码:**

```
Output:24157817 
Execution time: 14.819252490997314
```

这里我们可以看到，计算第 38 个斐波那契数用了 14.81 秒。现在让我们看看 Cython 代码在相同的计算中是如何执行的。

**Cython 代码:**

```
Output:24157817
Execution time: 0.6341381072998047
```

这里我们可以看到 **Cython 代码比 Python 代码快大约 23.5 倍。**这个例子完美地展示了使用 Cython 的能力。

# Cython 的特点

*   Cython 与 Python 完全兼容，高效易用。
*   编写与 C 代码来回调用的 Python 代码。
*   具有 C 语言性能的可读 Python 代码。
*   高效处理大型数据集。
*   在 CPython 生态系统中快速构建应用程序。

# 结论

这篇文章就是这么说的。在本文中，我们讨论了如何使用 Cython 来加速我们的 Python 代码，并使用它的功能来改进代码执行时间。您可以练习 Cython 代码，以便更好地实践，并使您的代码运行得更快。

感谢阅读！

你可以在这里免费订阅我的时事通讯: [Pralabh 的时事通讯](https://pralabhsaxena.medium.com/subscribe)
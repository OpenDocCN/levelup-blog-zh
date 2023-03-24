# 了解 Python 中的 copy()函数

> 原文：<https://levelup.gitconnected.com/understanding-the-copy-function-in-python-c41a4262bd23>

## 创建具有不同参考 id 的重复列表

![](img/af69839812d21ae94f0dbab93f5fdf55.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

`copy()`函数允许你复制一个列表，同时给它一个不同的引用`id`。

为了帮助说明这一点，让我们首先创建一个 1 和 0 的列表，并将其设置为一个名为`binary`的变量:

```
>>> binary = [1, 1, 0, 1, 1, 0, 1, 0, 0, 1]
```

这些信息在计算机的内存中占据特定的位置。这是它的`id`，有点像它的地址。二进制的`id`是:

```
>>> id(binary)
140482932383808
```

如果我通过设置一个名为`binary_copy`的变量等于`binary`来复制这个列表，它们会有相同的引用`id`。换句话说，它们会通过引用同一个`id`来占据计算机内存中的相同位置。

```
>>> binary_copy = binary
>>> id(binary_copy)
140482932383808
```

这种复制可变对象(如列表)的方法的问题是，每当我对`binary_copy`、`binary`进行更改时，它们也会更改，因为它们有相同的`id`。

```
>>> binary_copy[1] = 'milk'
>>> binary_copy
[1, 'milk', 0, 1, 1, 0, 1, 0, 0, 1]
>>> binary
[1, 'milk', 0, 1, 1, 0, 1, 0, 0, 1]
```

## copy()是做什么的？

`Copy()`在您想要复制列表以便在不更改原始列表的情况下更改它的情况下会很有帮助。为了说明这一点，首先，我们需要将复制模块导入我们的 Python REPL(或您的代码)。

```
>>> import copy
```

在创建了`binary`列表之后，我们可以使用`copy`模块对其进行复制:

```
>>> binary = [1, 1, 0, 1, 1, 0, 1, 0, 0, 1]
>>> binary_copy = copy.copy(binary)
```

现在当我们调用`binary`和`binary_copy`时，它们看起来是一样的。

```
>>> binary
[1, 1, 0, 1, 1, 0, 1, 0, 0, 1]
>>> binary_copy
[1, 1, 0, 1, 1, 0, 1, 0, 0, 1]
```

然而，它们现在占据了计算机内存的不同位置。我们可以再次使用`id()`函数看到这一点，这就是我们如何在 Python 中获得某些东西的“身份”。如您所见，id 是不同的:

```
>>> id(binary)
140482932383808
>>> id(binary_copy)
140482932387392
```

现在如果我改变`binary_copy`，它与`binary`不同。

```
>>> binary_copy[1] = 'toast'
>>> binary_copy
[1, 'toast', 0, 1, 1, 0, 1, 0, 0, 1]
>>> binary
[1, 1, 0, 1, 1, 0, 1, 0, 0, 1]
```
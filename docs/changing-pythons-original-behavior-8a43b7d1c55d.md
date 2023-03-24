# 更改 Python 中的默认函数行为

> 原文：<https://levelup.gitconnected.com/changing-pythons-original-behavior-8a43b7d1c55d>

![](img/4f04830c46c0e3ed26c6d8b334c0f2b6.png)

Python 中的恶作剧

大家好，在这篇文章中，我们将讨论如何在 Python 中修改现有类方法或函数的原始行为。

## 你为什么需要它？

有时你经常用相同值的参数调用一个函数。但是，有时您可能会忘记或错过一个没有指定该值的调用。使用`partial`，您可以修改该函数的原始行为，并使其默认按照您想要的方式运行，而不必每次都指定。

## 你如何使用它？

`partial`的用法非常简单，让我们举一个简单的例子。

假设您总是使用`pandas.read_csv`来读取一个 tsv(制表符分隔值)文件，并且您不希望在解析一个错误的行时引发异常。您可以执行以下操作:

```
from functools import partial
import pandas as pdpd.read_csv = partial(pd.read_csv, sep='\t', error_bad_lines=False)
```

此后对`pd.read_csv`的每次调用都将默认使用制表符作为分隔符，而不是逗号。但是，如果仍然需要原来的行为呢？我们仍然可以做得更好，并通过执行以下操作来保留原始功能:

```
pd.read_tsv = partial(pd.read_csv, sep='\t', error_bad_lines=False)
```

现在，我们有了自己的`read_csv`版本，只处理 tsv 文件。所以，这给了我们一个不需要改变原始函数的单行实现。

## 它是如何工作的？

在 Python 中，一切正常。`partial`只是一个函数，它接受一个函数和几个关键字参数，并返回该函数的修改版本。让我们试着从零开始实现(这只是一个简单的教育版):

```
def my_partial(func, *args, **kwargs):
    def new_funct(*new_args, **new_kwargs):
        new_kwargs.update(kwargs.copy())
        return func(*args, *new_args, **kwargs)
    return new_funct
```

## 与函数一起工作！方法怎么样？

对于方法，我们总是用类似的方式来定义它们。

```
class Example:
   def my_method(self, arg1):
       ...
```

如果你用`partial`来修补这个，那么`self`参数就会被搞乱。Partial 会将方法重定义为函数，这导致`self`只是一个普通的参数。

对于方法，您将不得不使用来自`functools`的`partialmethod`。它允许您修补方法定义，而无需将它们更改为函数。在这种情况下，用法与`partial`并无不同。

```
pd.DataFrame.to_tsv = partialmethod(pd.DataFrame.to_csv, sep='\t')
```

现在，无论何时我们有了一个数据框架(例如`df`),我们都可以马上调用`df.to_tsv`。

## 重要注意事项:

*   确保检查您的更改的范围，并确保如果您不小心导入，任何部分更改都不会影响整个项目。
*   您可以将原始函数存储在一个变量中，以便在需要时返回。
*   确保不要为文件名或要转换的值(例如`partial(int, x=10)`)设置默认值，因为在某些情况下，您可能会无意中在内存中存储大型对象而没有意识到这一点。
*   让你的队友知道你在代码库中的某个地方使用了`partial`,否则，他们会带着不同的假设编码。

## 延伸阅读:

*   [https://docs . python . org/3.8/library/func tools . html # func tools . partial](https://docs.python.org/3.8/library/functools.html#functools.partial)
*   【https://en.wikipedia.org/wiki/Monkey_patch 
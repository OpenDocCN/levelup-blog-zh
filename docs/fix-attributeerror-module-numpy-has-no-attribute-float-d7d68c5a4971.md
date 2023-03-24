# 如何修复属性错误:模块“numpy”没有属性“float”

> 原文：<https://levelup.gitconnected.com/fix-attributeerror-module-numpy-has-no-attribute-float-d7d68c5a4971>

## 升级到 numpy 1.24.0 后如何解决错误

![](img/989588be948f8f851e4f8c48d575ae88.png)

照片由[将](https://unsplash.com/@will0629?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)放在 [Unsplash](https://unsplash.com/photos/DH5183gvKUg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在过去的几天里，随着最近的`numpy` v1.24.0 发布，许多用户抱怨他们的代码会失败，并出现以下错误:

```
AttributeError: module 'numpy' has no attribute 'float'
```

在本文中，我们将尝试重现该错误，了解我们最初为什么会收到它，并建议一些解决方法，以便您可以快速修复它。

## 重现错误

最近升级到`numpy` v1.24.0 的用户报告了此错误。为了重现此错误，让我们创建并激活一个全新的虚拟环境:

```
python3 -m venv numpy-reproduce-error-venv 
```

```
source numpy-reproduce-error-venv/bin/activate
```

然后安装`numpy` 1.24.0:

```
pip install numpy==1.24.0
```

现在通过运行`python3`激活一个 python shell，并在 shell import numpy:

```
>>> import numpy as np
```

现在，如果您试图使用 numpy 的`float`别名构建一个浮点数:

```
>>> x = np.float(10)
```

您最终会看到以下错误:

```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/Users/username/numpy-reproduce-error-venv/lib/python3.9/site-packages/numpy/__init__.py", line 284, in __getattr__
    raise AttributeError("module {!r} has no attribute "
AttributeError: module 'numpy' has no attribute 'float'
```

## 为什么我们会看到这个错误？

从`numpy` v1.20 开始，`numpy.float`以及类似的别名(包括`numpy.int`)都被弃用。

> 长期以来，`np.int`一直是内置`int`的别名。这对新来者来说常常是一个困惑的原因，并且主要是由于历史原因而存在。
> 
> 这些别名已被弃用。下表显示了不推荐使用的别名的完整列表及其确切含义。用第二列中的内容替换第一列中的条目的用法将会产生相同的效果，并且会消除弃用警告。
> 
> — [numpy 1.20 发行说明](https://numpy.org/doc/stable/release/1.20.0-notes.html#using-the-aliases-of-builtin-types-like-np-int-is-deprecated)

展望未来，`numpy` v1.24.0 已经完全删除了这些别名，这就是为什么每当使用这些别名时我们都会看到这个错误。

> 别名`np.object`、`np.bool`、`np.float`、`np.complex`、`np.str`和`np.int`的弃用已过期(引入 NumPy 1.20)。其中一些除了引发错误之外，现在还会给出未来警告，因为它们将来会被映射到 NumPy 标量。
> 
> — [numpy 1.24.0 发行说明](https://numpy.org/doc/stable/release/1.24.0-notes.html#expired-deprecations)

## 怎么修

在大多数情况下，只需将 numpy 的别名替换成内置的 Python 类型就可以了。

```
import numpy as np

# Instead of numpy's float alias
x = np.float(10)

# Use the built-in float
x = float(10)
```

在 v1.20 发行说明中，您还可以找到关于如何处理这种弃用的更详细的指南:

> 为了给绝大多数情况一个明确的指导方针，对于类型`bool`、`object`、`str`(和`unicode`)使用普通版本更短更清晰，一般是一个很好的替代品。对于`float`和`complex`,如果您希望更明确地了解精度，您可以使用`float64`和`complex128`。
> 
> 对于`np.int`，用`np.int_`或`int`直接替换也是好的，不会改变行为，但是精度将继续取决于计算机和操作系统。如果您想更清楚地了解当前的使用情况，您有以下选择:
> 
> `- np.int64`或`np.int32`精确指定精度。这确保了结果不会依赖于计算机或操作系统。
> 
> `- np.int_`或`int`(默认)，但要注意这取决于电脑和操作系统。
> 
> ——C 类型:`np.cint` (int)、`np.int_` (long)、`np.longlong`。
> 
> 32 位机器上的 32 位和 64 位机器上的 64 位。这可能是用于索引的最佳类型。
> 
> 当与`np.dtype(...)`或`dtype=...`一起使用时，如上所述将其更改为数字名称不会影响输出。如果作为标量使用:
> 
> np.float(123)
> 
> 改变它可以微妙地改变结果。在这种情况下，Python 版本`float(123)`或`int(12.)`通常是更好的，尽管 NumPy 版本对于与 NumPy 数组的一致性可能是有用的(例如，NumPy 对于像被零除这样的事情表现不同)。
> 
> — [numpy 文档](https://numpy.org/doc/stable/release/1.20.0-notes.html#using-the-aliases-of-builtin-types-like-np-int-is-deprecated)

如果您仍然需要`numpy`标量类型，您可以使用`np.float32`、`np.float64`或`np.float128`:

```
>>> x = np.float64(10)
```

或者，您仍然可以选择降级您的`numpy`版本(< 1.24.0)

## 最后的想法

在最近的 v1.24.0 `numpy`发布之后，用户会抱怨`AttributeError: module ‘numpy’ has no attribute ‘float’`。这是因为移除了 numpy 的 float、int 和类似数据类型的别名。

我个人建议尝试修改您的源代码，使其现在引用等效的内置类型，而不是降低您的 numpy 版本。

[**成为会员**](https://gmyrianthous.medium.com/membership) **阅读介质上的每一个故事。你的会员费直接支持我和你看的其他作家。你也可以在媒体上看到所有的故事。**

[](https://gmyrianthous.medium.com/membership) [## 通过我的推荐链接加入 Medium-Giorgos Myrianthous

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

gmyrianthous.medium.com](https://gmyrianthous.medium.com/membership) 

**相关文章你可能也喜欢**

[](https://towardsdatascience.com/diagrams-as-code-python-d9cbaa959ed5) [## Python 中作为代码的图

### 用 Python 创建云系统架构图

towardsdatascience.com](https://towardsdatascience.com/diagrams-as-code-python-d9cbaa959ed5) [](https://towardsdatascience.com/python-poetry-83f184ac9ed1) [## 用诗歌管理 Python 依赖关系

### 依赖性管理和用诗歌打包

towardsdatascience.com](https://towardsdatascience.com/python-poetry-83f184ac9ed1) [](https://towardsdatascience.com/data-engineer-tools-c7e68eed28ad) [## 数据工程师的工具

### 数据工程工具箱的基础

towardsdatascience.com](https://towardsdatascience.com/data-engineer-tools-c7e68eed28ad)
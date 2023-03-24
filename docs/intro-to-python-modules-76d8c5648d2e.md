# Python 模块简介

> 原文：<https://levelup.gitconnected.com/intro-to-python-modules-76d8c5648d2e>

![](img/6fd853ffa92bdcfcc4858fca7944fd7b.png)

照片由 [Michael Dziedzic](https://unsplash.com/@lazycreekimages?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Python 是一种方便的语言，通常用于脚本编写、数据科学和 web 开发。

在本文中，我们将看看如何定义和使用模块将大程序分成小块。

# 导入模块

大多数程序都不止几行。这意味着，为了便于管理，它们必须分成多个小块。

Python 程序可以分成模块。每个模块都是一个 Python 程序。例如，`math`模块有数学函数，`random`模块有随机数相关的函数。

要在另一个模块中使用 Python 模块，我们必须使用`import`关键字和模块名。

如果我们想要导入多个模块，我们可以用逗号分隔模块名。

例如，我们可以导入`random`模块，然后通过调用`randrange`函数打印出一个随机生成的数字，如下所示:

```
import random
print(random.randrange(0, 101, 2))
```

在上面的代码中，我们导入了`random`模块并调用了`randrange`函数，将我们想要生成的数字的开始和结束数字作为前两个参数。

结束编号不在可生成的编号范围内。

最后一个参数是起始数字和结束数字之间要跳过的步骤数。

上面的代码将生成一个 0 到 100 之间的偶数。

# 从导入语句

我们可以用`from`关键字导入 Python 模块的一个成员。

例如，我们可以从`random`模块导入`randrange`函数，如下所示:

```
from random import randrange
print(randrange(0, 101, 2))
```

在上面的代码中，我们只是导入了`randrange`函数，而不是整个模块。

但其余的逻辑是一样的。

这样效率更高，因为我们没有导入所有内容。

# 创建我们自己的模块

Python 文件顶层的任何内容都可以从 Python 模块中导入。

例如，我们可以如下创建一个名为`foo.py`的模块:

```
x = 1
```

那么在`main.py`中，我们可以如下导入和使用它:

```
import foo
print(foo.x)
```

我们应该在屏幕上看到 1，因为我们已经将`x`设置为 1 并导入了它。

# 模块搜索路径

Python 模块在代码目录、设置为`PYTHONPATH`环境变量的值的路径以及安装 Python 时设置的默认目录中进行搜索。

# `dir()`功能

`dir`函数用于列出一个 Python 模块的成员。

例如，我们可以打印一个`math`模块的成员列表，如下所示:

```
import math
print(dir(math))
```

然后我们在屏幕上看到以下内容:

```
['__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'acos', 'acosh', 'asin', 'asinh', 'atan', 'atan2', 'atanh', 'ceil', 'copysign', 'cos', 'cosh', 'degrees', 'e', 'erf', 'erfc', 'exp', 'expm1', 'fabs', 'factorial', 'floor', 'fmod', 'frexp', 'fsum', 'gamma', 'gcd', 'hypot', 'inf', 'isclose', 'isfinite', 'isinf', 'isnan', 'ldexp', 'lgamma', 'log', 'log10', 'log1p', 'log2', 'modf', 'nan', 'pi', 'pow', 'radians', 'remainder', 'sin', 'sinh', 'sqrt', 'tan', 'tanh', 'tau', 'trunc']
```

我们可以从上面的数组中看到可以调用的函数。

![](img/67e7cd9ba3852157f39e2e60439e2c7d.png)

托比·斯托达特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 包装

我们可以将 Python 文件放在目录中，将它们组织成包。

例如，我们可以将`foo.py`放在`package`文件夹中，添加`__init__.py`到其中。

然后我们可以导入和使用包的成员，如下所示:

```
from package import foo
print(foo.x)
```

# 从包中导入*

星号字符表示我们从一个包中导入所有成员。

例如，如果我们写:

```
from sound.effects import *
```

我们从`sound`包中的`effects`模块导入所有成员。

这是一种不好的做法，因为代码是低效的，因为我们导入所有的东西。此外，我们可能会有冲突的名称，因为我们导入了比我们应该导入的更多的成员。

# 导入为

我们可以使用`as`关键字来导入一个有别名的模块。这有助于我们避免来自不同模块的名称冲突，因为我们的成员在不同的模块中有相同的名称。

例如，我们可以编写以下代码:

```
import random as r
print(r.randrange(0, 101, 2))
```

导入别名为`r`的`random`模块，并引用它而不是`random`。

我们还可以导入一个别名为的成员，如下所示:

```
from random import randrange as rr
print(rr(0, 101, 2))
```

我们可以调用`rr`而不是`randrange`来调用`randrange`。

# 结论

我们可以通过创建 Python 代码文件来定义和导入 Python 模块，然后我们可以导入 Python 文件的成员。

这让我们可以将代码分成小块。

此外，我们可以通过将模块文件放在文件夹中并将`__init__.py`添加到每个目录来将 Python 模块组织成包。
# 使用模块更好地组织您的 Python 代码

> 原文：<https://levelup.gitconnected.com/use-modules-to-better-organize-your-python-code-75690ba6b6e>

## 了解 Python 模块以及如何使用它们来更好地组织您不断增长的代码库。

![](img/61e8ef0d52eb1b373496603b81a45028.png)

由 [La-Rel 复活节](https://unsplash.com/@lastnameeaster?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

任何有编写 Python 代码经验的人都应该熟悉像`import x`这样的语句或它的一些变体。这是 Python 让其用户使用额外功能的方式，这些功能以*模块*和*函数*的形式出现。在本帖中，我们将尝试理解 Python *模块*。

将要讨论的内容:

1.  什么和为什么？
    —什么是模块？
    —为什么使用模块是有用的
    —理解*名称空间*
2.  如何使用一个模块？
    —导入模块及其组件的不同方式
    —演示模块的用途
    —如何检查模块的组件
3.  内置模块
4.  后续步骤

# 什么和为什么？

很快你开始编码，你会发现你的代码有增长的趋势。管理不断增长的代码库的有效方法是将代码分解成更小的、可管理的块。这样这些模块不仅可以单独运行，还可以作为更大项目的一部分一起工作。模块是我们组织独立代码块的一种方式。

> 模块是一个包含 Python 定义和语句的文件—来源: [*Python 教程*](https://docs.python.org/3/tutorial/modules.html)

例如，让我们假设您已经创建了在不同项目中经常使用的可重用函数。使用它们的非模块化方式可能是，

*   直接复制和粘贴每个项目中的函数，或者
*   将它们保存在脚本中，并在项目期间运行脚本。例如，可以使用神奇的命令`%run`来执行这样的脚本。

第一种选择有重复工作的明显限制。而第二种选择是一个更好的主意，但是具有以下限制:

*   通过执行脚本，您实际上创建了脚本中所有可用的变量和函数。因此，您可以用该脚本的所有组件加载您的工作区，即使您只需要选择几个组件。
*   此外，创建的对象将占用*名称空间*，并可能导致名称冲突或意想不到的后果。稍后将详细介绍名称空间。
*   第三，在极端情况下，它可能会使您面临风险，尤其是当您正在处理一个开放的项目或使用别人的脚本时。如果您盲目地执行这样的脚本，您可能最终会运行一些恶意代码，从而暴露您的恶意意图。

使用模块给了我们一种克服这些限制的方法。我们将在下一节中演示使用模块如何帮助我们克服这些限制，同时我们将讨论如何使用模块。

**💡关于名称空间的说明**

我们讨论了术语— *名称空间*。简单来说，名称空间是在计算机中分配的内存空间，其中存在对象名称。在命名空间内，名称在命名空间内是唯一的。如果我们把名称空间看作组，那么不同的组可能有同名的成员，但是在一个组中不会有任何重名。使用模块是保持名称空间独立和干净的好方法。

> *当一个模块被正确使用时，Python 使得来自该模块的所有名字都可用，但是不使它们对代码的名称空间可用。*

下一节中的演示小节应该会说明这一点。

# 如何使用一个模块？

让我们首先创建一个名为`messingAround.py`的 Python 脚本，并将其保存在我们将运行 Python 程序的同一个目录中。这将是我们函数和变量的家。首先，让我们在`messingAround.py`中定义一个变量`pi = 6.38`和一个函数`makeHalf()`。因此，该脚本将如下所示:

```
# messingAround module
pi = 6.38def makeHalf(val):
    return val/2
```

## 导入方式

要使一个模块可用，我们必须使用关键字`import`导入它。导入模块有不同的方法。以`messingAround`为例，我们将看到导入模块的不同方式:

*   我们可以通过简单地调用`import messingAround`来导入整个模块。
    —这将使模块的所有组件可供调用。但是不会将它们作为 Python 对象加载。
    —我们也可以像`import <module01> <module02>`一样在一行中调用多个模块。
*   我们可以从一个模块中导入特定组件。在我们的例子中，我们可以通过调用`from messingAround import pi`从`messingAround`模块加载`pi`。
    —我们也可以通过添加由逗号分隔的组件名称:`from module01 import component01, component02`使用单行从同一个模块导入多个组件。
*   我们可以在导入模块或组件时给它们起别名:
    ——将`messingAround`导入为`ma` : `import messingAround as ma`。
    —导入组件 01 作为 c01，组件 02 作为 c02: `from module01 import component01 as c01, component02 as c02`。
    —🚨请记住，如果您在导入时给出了别名，您将不得不通过别名来调用它们。用他们的真名称呼他们不再管用了。
*   我们可以通过调用`from messingAround import *`从一个模块中加载任何东西。
    —🚨在任何情况下，你都不可能想那么做。这与执行脚本本质上是一样的，并且带有我们前面讨论过的所有限制。

## 演示

出于演示目的，我们将使用一个名为`math`的内置 Python 模块以及我们的练习模块`messingAround`。这些演示将展示如何使用模块，以及它们如何帮助我们避免命名空间的复杂性。

**导入模块并使用其组件**

```
import messingAround as ma
import math as mpi = m.pi * 3print("My pi value = {} \nmessingAround's pi value = {} \nMath module's pi value = {}".format(pi, ma.pi, m.pi))My pi value = 9.42477796076938 
messingAround's pi value = 6.28 
Math module's pi value = 3.141592653589793
```

`Math`模块带有一个名为`pi`的变量，其中包含π的值。我们的模块中还有一个同名但值不同的变量。在此基础上，我们使用来自`math`模块的`pi`变量创建了一个定制的`pi`变量。

注意，我们是如何使用点符号调用模块中的`pi`变量和我们的自定义`pi`变量的，并且没有在名称空间冲突/混淆中结束- `pi`来自不同来源的变量保留了它们的值。

**使用魔法命令并导入***

相反，考虑下面的例子。

```
# import *
from messingAround import *
import math as m
print("pi value = {} \nMath module's pi value = {}".format(pi, m.pi))print()
pi = m.pi * 3
print("pi value = {} \nMath module's pi value = {}".format(pi, m.pi))pi value = 6.28 
Math module's pi value = 3.141592653589793pi value = 9.42477796076938 
Math module's pi value = 3.141592653589793
```

注意，`pi`的值是如何不断变化的！

*   当我们从`messingAround`模块加载所有内容时，一个名为`pi`的变量在我们的工作空间中被创建——检查打印出的值 6.28。
*   但是一旦我们创建了一个新变量并给它起了同样的名字— `pi`，从`messingAround`模块加载的原始变量就被这个新变量替换了。检查`pi`的值后来是如何变化的。
*   同时注意来自`math`模块的`pi`变量的值保持不变。

🛑用`%run -i messingAround.py`代替了这一行`from messingAround import *`。结果一样吗？想想“为什么”？

## 偷窥模块内部

当模块变得更大时，我们可能希望在导入模块后立即检查模块内部的内容。`dir()`允许我们这样做。`dir(<module name/alias>)`按字母顺序打印组件。

```
dir(ma)['__builtins__',
 '__cached__',
 '__doc__',
 '__file__',
 '__loader__',
 '__name__',
 '__package__',
 '__spec__',
 'makeHalf',
 'pi']
```

注意，输出列出了我们的两个组件`makeHalf`和`pi`。现在，忽略带有下划线的组件。当我们讨论构建 Python 包时，我们会谈到这些。

# 内置模块和 Python 模块索引

当我们安装 Python 时，我们得到了一堆模块。如果你把 Python 看作一辆汽车，那么 Python 程序本身就像是基本模型，所有的模块都像是插件/升级。所有这些模块和内置函数被称为 [Python 标准库](https://docs.python.org/3/library/index.html)。

要找到 Python 模块的详细列表，你可以查看官方的 [Python 模块索引](https://docs.python.org/3/py-modindex.html)网站。不过，提醒一句，

> 不要被模块的数量淹没。选择与您的目标相关的内容，即数据科学与应用程序开发，并只关注它们。

# 下一步是什么？

理解模块是理解 Python *包*的第一步。包实际上是模块的集合。在这篇文章中，我们学习了模块，使用模块的好处，以及如何使用它们。在后续的文章中，我们将学习如何在一个包中组织我们的模块。

**更新:**查看下一篇帖子，了解如何从模块中获得包:

[](https://towardsdatascience.com/from-functions-to-python-package-f8a3bba8bb6b) [## 从函数到 Python 包

### 从头开始学习如何将函数转换成包并分发

towardsdatascience.com](https://towardsdatascience.com/from-functions-to-python-package-f8a3bba8bb6b) 

感谢你阅读这篇文章。如果你喜欢这篇文章，请点击这里查看我的 Python 系列面向对象编程:[https://medium.com/@curious-joe](https://medium.com/@curious-joe)。
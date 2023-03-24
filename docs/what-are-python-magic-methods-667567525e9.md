# Python 魔术方法有哪些？

> 原文：<https://levelup.gitconnected.com/what-are-python-magic-methods-667567525e9>

## Python 魔术方法的简要概述以及了解它们的重要性

![](img/5d6e473082fb62145beff478f4890752.png)

在 [Unsplash](https://unsplash.com) 上由 [Aditya Saxena](https://unsplash.com/@adityaries) 发声

魔术方法，也称为 *Dunder* 方法，是 Python 在程序生命周期的特定时间调用的特殊函数。这些方法的名字两边都有两个下划线。让我们看一个例子。

## 构造函数初始值设定项:__init__

如果你在 Python 中做过面向对象编程(OOP ),你可能熟悉 *__init__* 方法。为了那些新的面向对象概念的好处，类有构造函数，它们在实例化时调用。在 Python 中，构造函数是 *__init__* 方法。让我们看一个样本代码。

```
**class** Book:
    **def** __init__(self, title, author):
        self.title = title
        self.author = author
        print(f"{title} was written by {author}")
```

上面的代码片段是一个简单的 Book 类，它有两个参数，title 和 author。我们初始化参数，然后在控制台上打印一些东西。

对于这堂课，让我们假设方法定义中的第一个参数 *self* 是一个我们不必担心的强制字段。Python 需要它，所以我们不会为它费心太多。附带说明一下，名字可以是任何东西，但是社区已经按照惯例选择了自己。让我们创建一个 book 类的对象。

我们在上面的代码片段中实例化了 book 类，正如您所看到的，我们用实例化过程中传递的变量填充了 print 语句。我们可以看到，我们没有在任何地方调用 __init__ 方法，但是 Python 以某种方式调用了它。这是因为当你实例化一个类时，它的构造函数会被自动调用，因此输出。

## 对象创建:__ 新建 _ _

在我们开始之前，让我们暂停一下。如果你来自 C/C++和它们的衍生物，你会想为什么当我们实例化对象时没有新的关键字。Python 做事情有点不同。它隐式调用 __new__ magic 方法，该方法在 __init__ 初始化之前返回一个新对象。就在 Book 类中的 __init__ 方法定义之前，我们添加以下内容:

```
def __new__(cls, title, author):
    print("A new instance of book")
    instance = object.__new__(cls)
    return instance
```

还是那句话，不要太在意第一个参数，以及为什么这里叫 cls，而不是 self，就像我们之前看到的。在 Python 的 OOP 方法中命名第一个参数有细微差别，但是为了本教程的目的，假设 cls 和 self 分别是类和实例方法的命名约定。由于这不是关于 OOP 的教程，我将把类和实例方法之间的区别推迟到后面的博客中。现在，当我们重新运行代码时，我们会得到:

```
A new instance of book is created
Things Fall Apart Chinua Achebe
```

我们有完整的源代码如下:

```
**class** Book:
    **def** __new__(cls, title, author):
        print("A new instance of book")
        instance = object.__new__(cls)
        return instance **def** __init__(self, title, author):
        self.title = title
        self.author = author
        print(f"{title} was written by {author}")book = Book(“Things Fall Apart”, “Chinua Achebe”)
# A new instance of book is created
# Things Fall Apart was written by Chinua Achebe
```

Python 中有许多神奇的方法，我们不会花时间一一介绍，但是如果您足够好奇，可以运行 dir(int)来查找 int 类的神奇方法。我们将看看其他神奇的方法，从 __add__ 开始。

## 加法中缀运算符:__add__

说到 int，让我们通过做一些基本的数学来获得乐趣。在终端上输入 python 打开 REPL，然后运行以下命令。我假设您已经安装了 Python。

注意:下面代码片段中前面的三个右尖括号表示提示。

```
>>> balance = 3
>>> balance + 2
5
```

如你所料，我们得到了答案 5。但是等等，Python 是怎么解读方程的？为了更好地理解这一点，我们必须调查到底发生了什么。int 类有一个 __add__ magic 方法来计算等式。每当 Python 看到+运算符时，它首先检查两个操作数是否都是 int 类型，并调用它的 __add__。

```
>>> balance = 3
>>> balance.__add__(2)
5
```

上面的代码片段给了我们和以前一样的结果，但是 __add__ 方法并不像看上去那样简单。Python 如何使用同一个+运算符进行字符串串联？我之前提交的材料里有个暗示。因为 Python 是一种动态类型语言，所以+运算符不会直接将操作数相加。它首先检查它们，如果它们不属于同一类型，就在对它们采取行动之前强制它们采用正确的格式。

与 int 类一样，str 类中也有一个 __add__ 方法，这使得连接成为可能。让我们看看下面的实际情况:

```
>>> name = “John”
>>> name + “Snow”
‘JohnSnow’
>>> name.__add__("Doe")
'JohnDoe'
```

上面的代码片段展示了 Python 中的一些基本连接。

## 使用魔法方法的好处

使用 Python 的神奇方法的最大好处之一是，它们提供了一种简单的方法来使对象表现得像内置类型一样。这样，您可以很容易地捕捉潜在的错误，并尽早修复它们。我们可以在两种不同的类型上尝试使用 __add__ 方法。让我们稍微修改一下加法代码。

```
>>> balance = 3
>>> name = "John"
>>> balance + name
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

从上面的代码片段中，当我们试图向字符串添加整数时，Python 引发了一个异常，甚至在 TypeError 细节中告诉了我们原因。解决这个问题的一个方法是重写 add 方法，这超出了我们的支付级别。

## 结论

在本教程中，我们学习了什么是魔法方法及其用法。现在去写一些 python 代码。

如果你喜欢这篇文章，请关注我的 handle [Bright](https://medium.com/@jessebrite) 了解更多关于软件开发和一般技术的故事。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰更多内容请查看[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级达人集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)
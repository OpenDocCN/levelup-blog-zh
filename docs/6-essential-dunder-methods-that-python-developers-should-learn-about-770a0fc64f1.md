# Python 开发人员应该了解的 6 个基本方法

> 原文：<https://levelup.gitconnected.com/6-essential-dunder-methods-that-python-developers-should-learn-about-770a0fc64f1>

![](img/dc4a695b12c00d5db134e5c471be5a88.png)

由[麦克斯韦·纳尔逊](https://unsplash.com/@maxcodes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 什么是邓德方法？

这些是与 Python 中的类相关联的方法。

这样做的目的是为类提供有用的功能。

要查看与一个对象相关联的所有 Dunder 方法(在我们的示例中为`str`，我们可以使用内置的`dir`函数，如下所示。

```
print(dir(str))
```

这将输出到:

```
[**'__add__', '__class__', '__contains__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__getnewargs__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mod__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__rmod__', '__rmul__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__'**, 'capitalize', 'casefold', 'center', 'count', 'encode', 'endswith', 'expandtabs', 'find', 'format', 'format_map', 'index', 'isalnum', 'isalpha', 'isascii', 'isdecimal', 'isdigit', 'isidentifier', 'islower', 'isnumeric', 'isprintable', 'isspace', 'istitle', 'isupper', 'join', 'ljust', 'lower', 'lstrip', 'maketrans', 'partition', 'removeprefix', 'removesuffix', 'replace', 'rfind', 'rindex', 'rjust', 'rpartition', 'rsplit', 'rstrip', 'split', 'splitlines', 'startswith', 'strip', 'swapcase', 'title', 'translate', 'upper', 'zfill']
```

## 什么是‘邓德’？

这些方法有双下划线作为它们名字的前缀和后缀，因此它们被称为“Dunder”(双下划线)方法。

例如，`__init__`法。

## 你什么时候给他们打电话？

您不直接调用这些方法，但是当您访问这些方法提供的相关功能时，会在内部调用这些方法。

例如，当初始化一个类实例时，会在内部调用`__init__`方法。

![](img/af187b53d4afea3a27b4df1f7eae4656.png)

克里斯·里德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们开始吧！

如果您是 Python 中的面向对象编程人员，应该了解以下常见的 Dunder 方法:

# __init__

当初始化一个类实例/对象时，调用这个方法。

它将`self`关键字作为必需的参数。

`self`表示已定义类的一个实例。

# __ 新 _ _

它是初始化类实例/对象时调用的第一个方法。

它将`cls`关键字作为必需的参数。

`cls`是已定义类的表示。

`__new__`返回稍后由`__init__`初始化的类实例/对象。

![](img/a900de6495839c95422d9462fd9d32be.png)

照片由[大卫·舒尔茨](https://unsplash.com/@davidschultz?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# __eq__

该方法用于定义`==`操作符的功能。

让我们看看它是如何工作的！

我们创建了先前定义的`Person`类的两个实例，分别为`person_1`和`person_2`。

使用`__eq__`方法，我们改变了`==`操作符的行为，根据操作结果返回一个字符串。

同样的，

`__ne__(self, other)`可用于`!=`

`__lt__(self, other)`可用于`<`

`__gt__(self, other)`可用于`>`

`__le__(self, other)`可用于`<=`

`__ge__(self, other)`可用于`>=`

# __ 添加 _ _

该方法用于定义`+`操作器的功能。

让我们看看它是如何工作的！

我们创建了包含单个参数`balance`的`Account`类的两个实例。

使用`__add__`方法，我们改变了`+`操作符的行为，以字符串格式返回账户余额的总和。

同样的，

`__sub__(self, other)`可用于减法运算符(`-`)

`__mul__(self, other)`可用于乘法运算符(`*`)

`__div__(self, other)`可用于除法运算符(`/`)

`__mod__(self, other)`可用于模运算符(`%`)

`__pow__(self, other)`可用于求幂运算符(`**`)

![](img/3c37742adf78f88ef434ce645d723d89.png)

由 [Paul Esch-Laurent](https://unsplash.com/@pinjasaur?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# __repr__

此方法返回类实例/对象的字符串表示形式。

让我们看看它是如何工作的！

我们将创建一个 Wallet 类，并将其`__repr__`方法定义如下:

# __getattr__

使用这种方法，可以控制用户试图访问不存在的属性时的行为。

要了解更多 Dunder 方法，请参考下面提到的资源。

 [## Python 神奇方法指南

### 版权所有 2012 Rafe Kettler 版本 1.17 本指南的 PDF 版本可以从我的网站或 Github 获得。的…

rszalski.github.io](https://rszalski.github.io/magicmethods/) [](https://www.geeksforgeeks.org/dunder-magic-methods-python/) [## Python 中的 Dunder 或 magic 方法

### Python 中的 Dunder 或 magic 方法是在方法名中有两个前缀和后缀下划线的方法。邓德…

www.geeksforgeeks.org](https://www.geeksforgeeks.org/dunder-magic-methods-python/) 

非常感谢你阅读这篇文章！

[](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium-Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership)
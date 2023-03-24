# Python 类装饰器——编写自己的简单指南

> 原文：<https://levelup.gitconnected.com/python-class-decorators-an-easy-guide-to-write-your-own-941fa2ad0ad9>

这篇文章旨在以一种简单实用的方式展示 Python 类装饰器。之后，您应该能够将类定义为装饰器。让我们开始吧…

![](img/fe77dfb57fde254746424a2f09af73d8.png)

# 我们能对类装饰者做些什么？

*   消除重复代码(多个功能共享相同的代码)
*   防止不良输入(数据验证，查看我关于这个主题的另一篇文章[这里](https://medium.com/@caopengau/python-data-pipeline-first-and-foremost-step-data-validation-e15017b7ef8d)
*   日志记录(事实上我们将在本文中构建一个，一个有趣的时间跟踪器[这里是](https://github.com/koaning/memo/blob/main/memo/_util.py))
*   证明

# Python 类装饰器简介

例如，下面的`logger`函数在调用修饰函数之前和之后打印出一些`=`字符:

```
def logger(n):
    def decorate(fn):
        def wrapper(*args, **kwargs):
            print(n*'=')
            result = fn(*args, **kwargs)
            print(result)
            print(n*'=')
            return result
        return wrapper
    return decorate
```

`logger`是一个返回装饰器的装饰器工厂。它接受一个指定要显示的`=`字符数的参数。`*args`和`**kwargs`是特殊关键字，允许函数采用可变长度参数。

下面说明了如何使用`logger`装饰工厂:

```
@logger(5)
def add(a, b):
    return a + b

add(10, 20)
```

输出:

```
=====
30
=====
```

装饰工厂接受一个参数并返回一个可调用的。callable 接受一个参数(`fn`)，这是一个将被修饰的函数。同样，callable 可以访问传递给装饰器工厂的参数(`n`)。

当类实例实现`__call__`方法时，它可以是可调用的。因此，您可以将`__call__`方法作为装饰器。

下面的例子使用一个类重写了`logger`装饰工厂:

```
class logger:
    def __init__(self, n):
        self.n = n
    def __call__(self, fn):
        def wrapper(*args, **kwargs):
            print(self.n*'=')
            result = fn(*args, **kwargs)
            print(result)
            print(self.n*'=')
            return result
        return wrapper
```

您可以像这样使用`logger`类作为装饰器:

```
@logger(5)
def add(a, b):
    return a + b
```

`@logger(5)`返回一个`logger`类的实例。该实例是一个可调用的实例，因此您可以这样做:

```
add = logger(5)(add)
```

所以你可以用可调用的类来修饰函数。

将所有这些放在一起:

```
from functools import wraps

class logger:
    def __init__(self, n):
        self.n = n
    def __call__(self, fn):
        @wraps(fn)
        def wrapper(*args, **kwargs):
            print(self.n*'=')
            result = fn(*args, **kwargs)
            print(result)
            print(self.n*'=')
            return result
        return wrapper

@logger(5)
def add(a, b):
    return a + b

add(10, 20)
```

# 摘要

*   通过实现`__call__`方法，使用可调用类作为装饰器。
*   将装饰器参数传递给`__init__`方法。

**呼吁行动**

如果你觉得这个指南有帮助，请鼓掌并跟我来。通过[链接](https://medium.com/@caopengau/membership)加入 medium，获取我和所有其他优秀作家在 medium 上的优质文章。
# 理解 Python 中的 Callable

> 原文：<https://levelup.gitconnected.com/understanding-callable-in-python-3dd6e4fd38e>

![](img/a12dbaecce803ad2a989a4bec32d0552.png)

函数和类是我们日常开发中最常用的东西。我们召唤它们，传递它们，却从未想过是什么让它们如此神奇。简短的回答是**可调用协议；**长答案继续看！

## 定义📢

定义`__call__`方法的对象被称为可调用的。或者基本上 callable 是任何你可以用括号`()`调用并传递参数给它的东西。是的，我基本上是在说一个函数。

## **__ 调用 _ _ 方法🤙**

`__call__`是 Python 中最有趣的 dunder 方法之一。这是大多数内置函数所利用的。如果我们窥视这些内置函数的`type`，那么我们经常会看到结果是一个`class`。

```
>>> range
<class 'range'>>>> zip 
<class 'zip'>>>> int
<class 'int'>
```

你知道要点了。但是一个类是如何作为一个函数的呢？我的意思是你可以直接`zip(iterable1, iterable2)`得到你的结果，而不需要进一步调用这个类的任何其他方法。

想象一下使用 `**zip**` **就像是一个普通的类。**

```
processor = ['Intel', 'Ryzen', 'Apple Silicon']
year = [2018, 2019, 2020]zipped = zip(processor, year)
zipped.start()
```

这绝对不是直观的工作。那么它是如何工作的呢？

## 使用 __call__ 方法🔨

在幕后，这是因为`__call__`方法。如果我们希望类的**实例是可调用的，那么在类中使用这个方法。**

我这么说是什么意思？让我们以一个打印数字平方的类为例。

```
class Square:

    def __call__(self, num):
            print(num * num)>>> sq = Square() # create an instance 
>>> sq(5) # invoke
25
```

这类似于执行:

```
>>> sq = Square()
>>> sq.__call__(5)
25
```

但是有了邓德方法，我们就不必这么做了。我们可以像普通函数一样直接调用实例。

> 您可能已经注意到，我们不需要使用`__init__`构造函数来传递值。因为类实例的行为类似于函数，所以它可以像函数一样接受参数，而不需要构造函数。

## 测试可调用👨‍🔬

默认情况下，类或函数是可调用的；但是实例只能用`__call__` dunder 方法调用。我们可以通过使用`callable()`功能对此进行检查。

以一个没有`__call__`方法的类为例。

```
class Random:
    pass>>> callable(Random)
True>>> r = Random()
>>> callable(r)
False
```

相反，让我们看看前面例子中的`Square`类。

```
>>> callable(Square)
True>>> callable(sq)
True
```

## 结论🚀

下次当你为某个东西创建一个类的时候，它需要立即返回一个值；您可以使用`__call__`方法。

我希望这篇博客有助于理解函数和类所包含的基本概念。如果你有任何疑问或建议，让我们在评论区讨论。
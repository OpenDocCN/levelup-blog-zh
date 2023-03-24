# 学习 Python:基于对象的编程

> 原文：<https://levelup.gitconnected.com/learning-python-object-based-programming-23a69b85109c>

![](img/698986394611bbb966abdfade8e2b64b.png)

奥斯卡·伊尔迪兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

在本文和接下来的几篇文章中，我将向您介绍 Python 中的面向对象编程。这些文章将向您展示如何创建您自己的用户定义的数据类型，从而将 Python 的有用性扩展到您选择的任何领域。在这第一篇文章中，我将演示如何在 Python 中创建可以用作用户定义类型的简单对象。

# 什么是对象？

在编程中有很多关于对象的混淆。出于我的目的，我将这样定义对象:一个用户定义的数据类型，它定义了类型的属性和类型的行为。

要使用您已经熟悉的编程语言，属性是描述对象某个方面的变量。比如一个人有名字，那么名字就是一个人的属性。汽车有发动机，所以发动机是汽车的一个属性。

行为就像一个描述对象做什么的函数。猫会发出声音，所以声音是猫的一种行为。二维空间中的点可以移动，所以移动是点的行为。

当我们用 Python 编写面向对象的程序时，我们定义具有属性和行为的对象，并将属性和行为打包到一个包中——类。如果我们不能做到这一点，我们就必须为每个属性和每个行为设置单独的变量，这很快就会变得难以管理。

# 在 Python 中创建对象

在本文中，我不打算创建包含所有属性和所有可能行为的完全开发的对象。相反，我将从演示如何创建对象以及如何定义和访问这些对象的一些属性开始。

让我们从定义一个`Point`对象或类开始。在此之前，让我提一下面向对象编程惯例的一个方面。当定义一个新的类时，类名应该以大写字母开头。这有助于区分类对象和其他事物，如变量。

下面是我如何定义一个`Point`类:

```
class Point:
  """Point represents an object in 2-D space"""
```

新的类对象是用关键字 class 后跟类名(以大写字母开头)定义的。后面的字符串叫做 docstring，它可以帮助人们识别一个`Point`对象代表什么。我可以省略它，但是它让类的定义看起来很奇怪。

我可以通过一个叫做实例化的过程来创建一个`Point`对象。它被称为实例化，因为它创建了类的一个实例。例如:

```
pt1 = Point()
```

`pt1`是`Point`类的一个实例，现在可以在程序中使用。

一个点有两个属性，一个 x 坐标和一个 y 坐标，我可以这样定义它们:

```
pt1.x = 2.0
pt1.y = 3.0
```

点运算符用于指定类的属性。

我可以通过从对象中调用属性来检索它们，再次使用点运算符将实例名与属性分开:

```
print("pt1.x:",pt1.x,", pt1.y:",pt1.y)
```

以下是我在一个完整的程序中迄今为止所做的工作:

```
class Point:
  """Point represents an object in 2-D space"""

  pt1 = Point()
  pt1.x = 2.0
  pt1.y = 3.0print("pt1.x:",pt1.x,", pt1.y:",pt1.y)
```

这个程序的输出是:

```
pt1 x: 2.0 , pt1 y: 3.0
```

目前，您不能访问一个类对象的集合属性，例如通过编写:

```
print(pt1)
```

以下是我在 Python shell 中尝试时得到的结果:

```
>>> print(pt1)
<__main__.Point object at 0x00000145E8560AF0>
```

这告诉我在这个内存地址有一个`Point`对象，但是 Python 不会访问这个属性。我必须通过点运算符来访问属性。在后面的文章中，我将向您展示如何让这些代码工作，但是现在我们需要看看其他的解决方案。

一个解决方案是创建一个打印点的函数。这解决了我们眼前的问题，但不是真正的长期解决方案。我将在下一篇文章中讨论这个解决方案。

也就是说，这里有一个打印`Point`对象内容的函数和一个使用该函数的测试程序:

```
class Point:
  """Point represents an object in 2-D space"""def printPt(pt):
  print("x:",pt.x,"y:",pt.y)pt1 = Point()
pt1.x = 3.0
pt1.y = 5.0
printPt(pt1)
```

这个程序的输出是:

```
x: 3.0 y: 5.0
```

这表明我们的用户定义对象可以用作函数的参数。用户定义的对象也可以用作函数返回值。例如，让我们定义一个函数，它可以找到两点之间的中点。下面是函数定义:

```
def midPoint(p1, p2):
  p = Point()
  p.x = (p1.x + p2.x)/2
  p.y = (p1.y + p2.y)/2
  return p
```

下面是测试该功能的完整程序:

```
class Point:
  """Point represents an object in 2-D space"""def printPt(pt):
  print("x:",pt.x,"y:",pt.y)def midPoint(p1, p2):
  p = Point()
  p.x = (p1.x + p2.x)/2
  p.y = (p1.y + p2.y)/2
  return ppt1 = Point()
pt1.x = 1
pt1.y = 3
pt2 = Point()
pt2.x = -3
pt2.y = 4
pt3 = Point()
pt3 = midPoint(pt1, pt2)
printPt(pt1)
printPt(pt2)
print("Midpoint:",end="")
printPt(pt3)
```

以下是该程序的输出:

```
x: 1 y: 3
x: -3 y: 4
Midpoint: x: -1.0 y: 3.5
```

# Python 对象是可变的

您可以通过访问赋值语句中的属性来更改对象的属性。例如:

```
p1 = Point()
p1.x = 1
p1.y = 2
printPt(p1)
p1.x = 4
printPt(p1)
```

这个程序的输出是:

```
x: 1 y: 2
x: 4 y: 2
```

# 当心复制对象

当你使用赋值来复制一个类对象时，你创建了一个原始对象的*浅*副本，有时也被称为*别名*。这意味着，如果您对原始对象进行了更改，复制的对象也会随之更改。这里有一个例子:

```
class Point:
  """Point represents an object in 2-D space"""def printPt(pt):
  print("x:",pt.x,"y:",pt.y)p1 = Point()
p1.x = 1
p1.y = 2
print("p1: ",end="")
printPt(p1)
p2 = p1
print("p2: ",end="")
printPt(p2)
p1.x = 2
print("p1: ",end="")
printPt(p1)
print("p2: ",end="")
printPt(p2)
```

这个程序的输出是:

```
p1: x: 1 y: 2
p2: x: 1 y: 2
p1: x: 2 y: 2
p2: x: 2 y: 2
```

我改变了`p1` 的 x 坐标，当我这样做的时候，`p2`的 x 坐标也改变了。这是因为，在内部，`p2`对象“指向”了`p1` 对象，所以对`p1` 的任何更改也会发生在`p2`上。

解决这个问题的方法是使用 Python 提供的复制模块。模块中有`copy`函数，它将对传递给它的对象进行深层复制。

下面是上面用`copy`函数改写的例子:

```
import copyclass Point:
  """Point represents an object in 2-D space"""def printPt(pt):
  print("x:",pt.x,"y:",pt.y)p1 = Point()
p1.x = 1
p1.y = 2
print("p1: ",end="")
printPt(p1)
p2 = copy.copy(p1)
print("p2: ",end="")
printPt(p2)
p1.x = 2
print("p1: ",end="")
printPt(p1)
print("p2: ",end="")
printPt(p2)
```

这个程序的输出是:

```
p1: x: 1 y: 2
p2: x: 1 y: 2
p1: x: 2 y: 2
p2: x: 1 y: 2
```

现在，当我复制`p1`并更改它的一个或多个属性时，这些更改不会反映在`p2`对象中。

这总结了本文中我想说的关于 Python 对象的所有内容。在我的下一篇文章中，我们将研究如何在类定义中移动函数，以便在类对象和对象使用的函数(在类中定义时称为方法)之间有更紧密的耦合。

感谢阅读。请将您的意见和建议发电子邮件至 mmmcmillan1@att.net[或回复如下。](mailto:mmmcmillan1@att.net)
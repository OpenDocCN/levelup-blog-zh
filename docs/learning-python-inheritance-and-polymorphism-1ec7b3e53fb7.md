# 学习 Python:继承和多态

> 原文：<https://levelup.gitconnected.com/learning-python-inheritance-and-polymorphism-1ec7b3e53fb7>

![](img/ac1602f01591fb6e3492b810476d4be1.png)

照片由[本工程图](https://unsplash.com/@thisisengineering?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 Python 编程中采用面向对象编程(OOP)的一个重要原因是利用了两个关键的 OOP 概念——继承和多态。在本文中，我将讨论如何在 Python 编程中使用这两个概念。

# 继承术语

继承是一个类定义使用已经在另一个类中定义的属性和方法的能力。首先定义一组属性和方法的类被称为*超类*。继承这些属性和方法的类被称为*子类*。

超类及其子类的集合被称为*继承层次*。子类可以从其他子类派生。这被称为多重继承，但我不打算在本文中探讨它。

# 继承基础

子类是通过在子类的定义中提供对超类的引用来创建的。为了演示这一点，我将创建一个`Shape`类，它将作为一组子类的超类，这些子类定义特定类型的形状，如圆形和正方形。

下面是一个`Shape`类定义和一个测试该类的简短程序:

```
class Shape:
  def __init__(self,name,x,y):
    self.name = name

  self.x = x
  self.y = ydef draw(self):
  print("Drawing",self.name,"at origin x:",self.x,"y:",self.y)s = Shape("Shape",1,2)
s.draw()
```

这个程序的输出是:

```
Drawing Shape at x: 1 y: 2
```

现在让我们定义一个继承自`Shape`类的`Rectangle`类。我们从识别`Rectangle`是一个子类开始，在派生类定义的第一行括号中放置子类的名称:

```
class Rectangle(Shape):
```

接下来要做的是定义`__init__`方法。我们想利用继承，不需要重新定义我们可以在子类定义中重用的方法。记住这一点，下面是对`Rectangle`类`__init__`方法的定义:

```
def __init__(self,x,y,height,width):
  Shape.__init__(self,x,y)
  self.name = "Rectangle"
  self.height = height
  self.width = width
```

这里要注意的主要事情是我如何从超类`Shape`中调用`__init__`方法，这样我就不必再次写出那个定义。这是通过继承实现良好代码重用的本质。

接下来我们需要一个“画”矩形的方法。在这里，我决定演示一个被称为*覆盖*的概念，这意味着我将重新定义当从`Rectangle`对象调用 draw 方法时它做什么。定义如下:

```
#overriding super class definition of the draw method
def draw(self):
  print("Drawing",self.name,"at origin x:",self.x,"y:",self.y)
  print("Height:",self.height,"Width:",self.width)
```

现在让我们将所有这些合并到一个程序中，该程序创建一个`Shape`对象和一个`Rectangle`对象，以及这些类的定义:

```
class Shape:
  def __init__(self,x,y):
    self.name = "Shape"
    self.x = x
    self.y = y def draw(self):
    print("Drawing",self.name,"at origin x:",self.x,"y:",self.y)class Rectangle(Shape):
  def __init__(self,x,y,height,width):
    Shape.__init__(self,x,y)
    self.name = "Rectangle"
    self.height = height
    self.width = width #overriding base class definition
  def draw(self):
    print("Drawing",self.name,"at origin x:",self.x,"y:",self.y)
    print("Height:",self.height,"Width:",self.width)sh = Shape(3,4)
sh.draw()
rec = Rectangle(1,2,5,10)
rec.draw()
```

以下是该程序的输出:

```
Drawing Shape at origin x: 3 y: 4
Drawing Rectangle at origin x: 1 y: 2
Height: 5 Width: 10
```

总结一下我们在继承方面的进展，我们已经看到子类可以继承超类的属性和方法，因此这些属性和方法不必在子类中重新定义。这是一个强大的编程特性，允许代码重用，允许程序员遵循“不要重复自己”或 DRY 的原则。

这也是 Python 中类多态的一个例子。系统将计算出它遇到了什么类型的对象，并调用适当的方法。

# Python 中的其他多态性类型

因为 Python 是一种动态类型的编程语言，所以很容易处理类对象的集合。这在像 C++这样的语言中是不正确的，c++是强类型的，需要程序员通过一些限制来正确地处理类对象集合。

在 Python 中，我可以简单地声明一个新的列表，然后随心所欲地向列表中添加对象。当我想访问对象时，我可以简单地遍历列表并执行任何我想要的操作。Python 将知道如何处理每个方法调用

在下面的例子中，我创建了一个基于`Shape`的对象列表，并从列表中“画”出它们。下面是代码和输出:

```
class Shape:
  def __init__(self,x,y):
    self.name = "Shape"
    self.x = x
    self.y = y def draw(self):
    print("Drawing",self.name,"at origin x:",self.x,"y:",self.y)class Rectangle(Shape):
  def __init__(self,x,y,height,width):
    Shape.__init__(self,x,y)
    self.name = "Rectangle"
    self.height = height
    self.width = width #overriding base class definition
  def draw(self):
    print("Drawing",self.name,"at origin x:",self.x,"y:",self.y)
    print("Height:",self.height,"Width:",self.width)class Circle(Shape):
  def __init__(self, x,y,radius):
    Shape.__init__(self,x,y)
    self.name = "Circle"
    self.radius = radius def draw(self):
    print("Drawing",self.name,"at origin x:",self.x,"y:",self.y)
    print("Radius:",self.radius)sh = Shape(3,4)
rec = Rectangle(1,2,5,10)
circ = Circle(5,6,5)
shapes = []
shapes.append(sh)
shapes.append(rec)
shapes.append(circ)
print()
print()
print("Drawing set of shapes:")
for sh in shapes:
  sh.draw()
```

这个程序的输出是:

```
Drawing set of shapes:
Drawing Shape at origin x: 3 y: 4
Drawing Rectangle at origin x: 1 y: 2
Height: 5 Width: 10
Drawing Circle at origin x: 5 y: 6
Radius: 5
```

由于多态性，Python 知道调用哪个 draw 方法，即使所有对象都存储在同一个列表中。

# 功能多态性

当我们谈到多态性时，我还需要介绍一下*函数多态性*。Python 中有很多内置函数多态性的例子。`print`函数就是一个很好的例子。

您可以将一个数字、一个字符串、一个列表和许多其他对象传递给`print`函数，它会知道如何恰当地处理这些对象。`len`功能是另一个例子。如果您将一个字符串传递给`len`函数，它将打印字符串中的字符数。如果你传递一个列表，它会计算列表中的元素。如果你传递给`len`一个字典，它会计算键/值对。这是函数多态性的另一个很好的例子。

我们可以对类对象做同样的事情。以下函数打印传递给它的`Shape`对象的名称:

```
def printName(shape):
  print(shape.name)
```

下面是一个使用函数的程序:

```
sh = Shape(3,4)
rec = Rectangle(1,2,5,10)
circ = Circle(5,6,5)
printName(sh)
printName(rec)
printName(circ)
```

以下是该程序的输出:

```
Shape
Rectangle
Circle
```

这就完成了我想说的关于类继承和多态的内容。现在，您已经对 Python 的主要特性有了一个相当完整但简短的介绍。在我的下一篇文章中，当我结束学习 Python 的这个系列时，我将介绍我在以前的文章中没有涉及的一些特性。

感谢您的阅读，请发电子邮件至[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)告诉我您的意见和建议，或者在下面添加回复。
# 学习 Python:类和方法

> 原文：<https://levelup.gitconnected.com/learning-python-classes-and-methods-ad67f8273ad0>

![](img/cc36ff161d2a9c2af4a7e8b7389b1364.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在我的上一篇文章中，我通过演示如何创建类对象介绍了 Python 类。我展示了如何在对象实例化后向对象添加属性，但是我没有演示如何在类定义中嵌入函数(在类中定义时称为方法)。在本文中，我将演示如何定义和使用类对象的方法。

在我描述如何创建 Python 类方法之前，让我花一点时间来谈谈为什么类方法很重要。类的方法组成了类的接口，这是程序与类交互的方式。通过为一个类定义一组方法，你决定了一个类如何被使用，因为让一个对象做任何事情的唯一方法是通过你定义的方法。

您应该仔细定义一个类接口，只包含那些正确使用该类所必需的方法。类方法也可以作为类属性的入口。通过定义访问类属性的方法，可以控制外部代码如何影响属性。

我之前用过的例子是一个人的年龄。大多数人活不到 85 岁左右。然而，存储 Python 数字的变量可以保存比这个数字大得多和小得多的值(想想负值)。一个好的类接口设计应该包括一些检查方法，以确保为类属性输入了有效的数据。

# 定义类方法

首先，我们需要定义一个类。让我们使用日期对象。下面是一个`Date`对象定义的开始:

```
class Date:
"""Date object"""d1 = Date()
d1.month = 11
d1.day = 16
d1.year = 2020
```

如果我们想打印日期，我们可以编写一个函数，它接受一个`Date`对象并显示存储在该对象中的日期:

```
def printDate(dt):
  print("%2d/%2d/%4d" % (dt.month, dt.day, dt.year))
```

如果我们在上面的程序末尾调用这个函数:

```
printDate(d1)
```

输出是:

```
11/16/2020
```

像这样定义函数的问题是我们违反了面向对象编程的原则之一— *封装*。封装指的是将数据与对该数据进行操作的功能(方法)捆绑在一起。

当我们封装数据和方法时，我们帮助确保从一个对象调用的方法将以正确的方式与该对象的数据一起工作。我们还可以使用类方法来确保我们分配给对象的数据是合法有效的。

这里举个例子会有帮助。假设我们有一个人对象。一个人有一个年龄。现在大多数人至少活到 70 或 80 岁，但是很少有人活到 100 岁以上。然而，当年龄被分配给一个人对象时，它可以是任何合法的 Python 数字。这意味着我们可以指定一个很大的数字作为年龄，比如 1，582，483，或者我们可以指定一个很小的数字作为年龄，比如-212。

然而，当我们定义一种为一个人分配年龄的方法时，我们可以控制为年龄属性分配什么值。我将很快演示它如何处理日期对象。

现在回到通过创建一个类方法来打印日期。要创建一个类方法，只需将定义放在类定义中，就像这样:

```
class Date: def printDate(self):
    print("%2d/%2d/%4d" % self.month, self.day, self.year))
```

现在我们可以使用点操作符直接从一个 `Date`对象调用该方法。下面是一个演示如何做到这一点的程序(我再次包含了类定义):

```
class Date: def printDate():
    print("%2d/%2d/%4d" % (self.month, self.day, self.year))tomorrow = Date()
tomorrow.month = 11
tomorrow.day = 17
tomorrow.year = 2020
tomorrow.printDate()
```

这个程序的输出是:

```
11/17/2020
```

显然，我需要解释关键字`self`在这个定义中的作用。类定义需要一种方法来引用代码正在处理的当前对象。你甚至可以说程序的焦点是当前的对象。`self`关键字提供了这种参考。

如您所见，每当我们编写一个使用当前对象数据的方法时，`self`就变成了参数，因为我们需要一种使用点运算符来检索数据的方法。

# 其他类方法

打印对象属性值的方法很重要，但是一个类可能还需要许多其他方法。例如，`Date`类可能需要一个方法，让你将日期增加一天或其他单位。

下面是一个新的`Date`类定义，它包括一个`increment`方法，以及一个测试该方法的程序:

```
class Date: def printDate(self):
    print("%2d/%2d/%4d" % (self.month, self.day, self.year))

  def increment(self, unit):
    """increment the date by a day or more"""
    self.day = self.day + unitd1 = Date()
d1.month = 11
d1.day = 16
d1.year = 2020
d1.printDate()
d1.increment(1)
d1.printDate()
d1.increment(3)
d1.printDate()
```

这个程序的输出是:

```
11/16/2020
11/17/2020
11/20/2020
```

所有面向对象的编程语言都包含一个特殊的方法，用于在实例化一个新对象时初始化它。这个方法被称为*构造函数*。在大多数语言中，构造函数方法与类同名，但是在 Python 中，构造函数方法被命名为: `__init__`。

方法用来初始化一个类的属性。许多编程语言的构造函数允许您为多个构造函数编写定义，以便可以用不同的方式初始化属性。例如，`Date`对象可以用月、日和年来初始化，但也可以只用月和年来初始化，甚至只用月和日来初始化。一个`Date`也可以不用数据初始化，参数的默认值被赋给属性。

但是，在 Python 中，您用默认参数定义了`__init__`方法。下面是我们的`Date`类的`__init__`方法定义:

```
class Date:

  def __init__(self, month=0,day=0,year=0):
    self.month = month
    self.day = day
    self.year = year # rest of Date definition
```

下面是一些用我们新的`__init__`方法实例化不同的`Date`对象的例子:

```
d1 = Date(11,17,2020)
d1.printDate()
d2 = Date(11,17)
d2.printDate()
d3 = Date()
d3.printDate()
```

这段代码的输出是:

```
11/17/2020
11/17/2020
0/ 0/2020
```

# 使用 __str__ 显示对象状态

在面向对象编程中，引用一个对象的*状态*就是引用在任何给定时间存储在该对象所有属性中的值。很多时候你都想看到一个对象的状态，尤其是在调试期间。Python 提供了显示状态的特殊对象方法:`__str__`方法。对于熟悉 Java 的人来说，`__str__`类似于`to_strin` g Java 方法。

下面是一个`_str__`方法如何寻找`Date`类:

```
def __str__(self):
  return "%2d/%2d/%4d" % (self.month, self.day, self.year)
```

`__str__`定义类似于`printDate`方法，但是它没有调用`print`函数，而是以与`printDat` e 方法相同的格式返回当前日期。

`__str__`的一个很好的特性是，您可以通过简单地引用对象来调用它，而不必直接调用方法。我的意思是:

```
d1 = Date(11,17,2020)
print(d1)
```

哪些输出:

```
11/17/2020
```

正如我前面提到的，大多数情况下 __str__ 方法都是用于调试。您应该编写一个单独的方法来显示可调用的对象属性。

# Python 中的重载运算符

当你重新定义一个操作符的工作方式时，这被称为*操作符重载*。Python 允许重载它的许多操作符。您可以在[这里](https://docs.python.org/3/reference/datamodel.html#emulating-numeric-types)找到您可以重载的操作符的完整列表。文档称之为仿真而不是重载。

运算符重载很重要，因为在处理类对象时使用标准 Python 运算符比调用方法更好。例如，假设我正在处理一个计数的类，我有两个对象想加在一起。下面两行哪一行看起来比较好用？

```
totalCounters = counter1 + counter2
totalCounters = counter1.add(counter2)
```

第一行更直观。

让我们看看如何通过实现我刚刚描述的`Counter`类来重载+运算符。下面是该类的代码以及一个简单的测试程序:

```
class Counter:
  def __init__(self):
    self.count = 0 def increment(self):
    self.count = self.count + 1 def __str__(self):
    return str(self.count)laps = Counter()
for i in range(1,6):
  laps.increment()
print("Laps run by laps:",laps)
moreLaps = Counter()
for i in range(1,4):
  moreLaps.increment()
print("Laps run by moreLaps:",moreLaps)
```

这个程序的输出是:

```
Laps run: 5
Laps run by moreLaps: 3
```

现在让我们看看如何重载`+`操作符。为此，我们在`Counter`类定义中为 `__add__`编写了一个新的定义，告诉 Python 当两个`Counter`对象与`+`操作符组合在一起时该做什么。下面是放置在类定义中的`__add__`的代码:

```
def __add__(self, cntr):
  return self.count + cntr.count
```

`self`参数用于操作符左侧的`Counter`对象，cntr 参数用于操作符右侧的`Counter`对象。为两个参数调用`count`属性，将它们相加，并返回结果。

下面是一个测试程序和输出:

```
laps = Counter()
for i in range(1,6):
  laps.increment()
print("Laps run by laps:",laps)
moreLaps = Counter()
for i in range(1,4):
  moreLaps.increment()
print("Laps run by moreLaps:",moreLaps)
totalLaps = laps + moreLaps
print("Total laps run:", totalLaps)Laps run by laps: 5
Laps run by moreLaps: 3
Total laps run: 8
```

让我们再重载一个操作符:操作符`==`。重载该操作符的代码包括使用`==`操作符来测试存储在两个对象的`count`属性中的值。下面是位于`Counter`类定义中的代码:

```
def __eq__(self, cntr):
  return self.count == cntr.count
```

下面是一个测试我们重载的`==`操作符的程序:

```
laps = Counter()
for i in range(1,6):
  laps.increment()
print("Laps run by laps:",laps)
moreLaps = Counter()
for i in range(1,6):
  moreLaps.increment()
print("Laps run by moreLaps:",moreLaps)
if (laps == moreLaps):
  print("Laps and moreLaps have the same number of laps.")
else:
moreLaps.increment()
print("Laps run by laps:",laps)
print("Laps run by moreLaps:",moreLaps)
if (laps == moreLaps):
  print("Laps and moreLaps have the same number of laps.")
else:
  print("Laps and moreLaps have a different number of laps.")
```

以下是输出:

```
Laps run by laps: 5
Laps run by moreLaps: 5
Laps and moreLaps have the same number of laps.
Laps run by laps: 5
Laps run by moreLaps: 6
Laps and moreLaps have a different number of laps.
```

这就结束了我对用 Python 创建类的讨论。在我的下一篇文章中，我将讨论如何在 Python 中执行继承和多态，这极大地增强了面向对象编程的能力和实用性。

感谢您的阅读，请发电子邮件至[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)告诉我您的意见和建议，或者在下面留下您的回复。
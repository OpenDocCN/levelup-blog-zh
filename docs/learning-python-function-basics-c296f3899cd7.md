# 学习 Python:函数基础

> 原文：<https://levelup.gitconnected.com/learning-python-function-basics-c296f3899cd7>

![](img/7818a8e4b6dad9e1b7faf40d7c939ec5.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我将暂时停止展示模板，花些时间讨论如何用 Python 创建函数。

# 为什么应该使用函数

函数是一种命名的计算，可以用来代替程序中的代码。例如，如果我正在编写一个天气应用程序，并且需要经常将摄氏温度转换为华氏温度，我不会总是在每次需要进行转换时用代码写出公式。相反，我会创建一个执行转换的函数，然后在需要时调用该函数。

函数在编程中很有用，原因有几个。一个原因是因为它们允许您在其他应用程序中重用代码。您可以定义一次函数，然后在可以使用该函数的任何其他程序中使用该函数。

函数也模块化你的程序，使它们更容易理解和调试。当你创建一个函数时，你可以严格地测试这个函数，然后当你在你的程序中使用它时，你就可以确信它会工作。然后，如果程序中确实出现了 bug，你可以相当确定问题不在这个函数上。

或者，如果问题出在函数上，隔离问题并修复它会更容易。您不必搜索代码行，而是可以直接找到函数定义来确定哪里出错了。

# 定义和使用函数

函数是一种命名的计算，通常用于以某种方式转换数据。例如，平方根函数接受一个数字并返回它的平方根。返回数据的函数称为返回值函数。

一个函数可以仅仅为了函数内部语句的效果而编写，这些类型的函数通常被称为过程或子例程，以区别于返回值函数。

函数被定义在程序的顶部。下面是函数定义的语法模板:

*定义函数名(可选-函数-参数):
函数体
返回语句*

当函数需要外部数据来执行计算时，这些数据通过函数参数进入函数。您不必定义带参数的函数，但是您编写的大多数函数都需要外部数据来做任何有趣的事情。

函数定义的第一行称为函数标题。标题包含关键字`def`来表示函数定义即将到来。`def`关键字后面是函数名。函数名应该有意义，就像变量名有意义一样。

函数名后面是参数列表。它被认为是可选的，因为函数不一定要有参数。这对于更多地充当过程或子例程的函数来说是典型的。

函数的参数是保存传递给函数的数据的变量。参数名应该像函数名和变量名一样有意义。当我在下面展示函数示例时，您将看到如何使用参数列表的示例。

现在该举个例子了。假设你正在做一些统计工作，你的程序需要对数字进行多次平方运算。因为你不止一次的平方数字，你应该写一个函数来完成。这个函数是这样的:

```
def square(number):
  return number * number
```

函数名为`square`。参数是要平方的数字。函数体仅由 return 语句组成，该语句将数字乘以自身，然后将该值发送回程序。

# 调用函数

当一个程序使用一个函数时，它被称为函数调用。函数调用可以放在任何需要表达式的地方。例如，函数调用可用于为变量赋值，如下例所示:

```
squared = square(10)
```

函数调用可以用作更大表达式的一部分，如下例所示:

```
val1 = 3
val2 = 5
sum_squares = square(val1) + square(val2)
```

函数调用也可以放在`print`语句中:

```
print(square(100))
```

这就是我说的函数调用可以到任何需要表达式的地方的意思。

可以从其他函数内部调用函数。下面的例子创建了一个`sum_of_squares`函数，它调用上面定义的`square`函数来平方它的两个参数。下面是函数定义和一个测试程序:

```
def square(number):
  return number * numberdef sum_of_squares(number1, number2):
  return square(number1) + square(number2)num1 = 4
num2 = 5
sumSquares = sum_of_squares(num1, num2)
print("The sum of the squares of",num1,"and",
      num2,"is",sumSquares)
```

这个程序的输出是:

```
The sum of the squares of 4 and 5 is 41
```

当调用一个函数时，Python 会检查以确保该函数的实参数量与定义中的实参数量相同。如果参数号和形参号不匹配，就会出错。以下是在这种情况下收到的错误消息的示例:

```
Traceback (most recent call last):
File "C:\python\test.py", line 9, in <module>
sumSquares = sum_of_squares(num1)
TypeError: sum_of_squares() missing 1 required positional argument: 'number2'
```

系统知道`sum_of_square`函数需要两个参数，当它只找到一个时，就会抛出函数调用缺少一个参数的错误。

函数调用中参数的顺序也很重要。函数调用参数必须这个函数应该这样调用:与函数参数的顺序相同。例如，下面是一个将字符串打印指定次数的函数:

```
def printTimes(string, times) {
  for i in range(0, times):
    print(string, end=" ")
```

这个函数应该这样调用:

```
printTimes("hello", 5)
```

字符串参数需要放在数值参数之前。如果颠倒顺序，您会得到以下类型的错误消息:

```
>>> printTimes(5, "hello")
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
File "<stdin>", line 2, in printTimes
TypeError: 'str' object cannot be interpreted as an integer
>>>
```

导致错误的原因是 Python 试图将字符串参数视为数字，而这是行不通的。

# 过程:不返回值的函数

有时，您需要编写不返回值的函数，这些函数仅用于它们执行的代码。我把这些类型的函数称为过程。其他语言称之为 void 函数。

过程可以接受参数，但不返回值。过程的一个常见用途是命名一组一起显示的`print`语句，比如问候语或菜单。过程的语法模板看起来与返回值函数的语法模板相同:

*定义过程名(可选参数列表):
过程体*

以下是显示程序选项菜单的程序示例:

```
def menu():
  print("1\. Enter student information")
  print("2\. Enter test grades")
  print("3\. Enter final grades")
  print("4\. Enter class times")
  print("5\. Enter student documentation")
  print()
  print("6\. Quit")
```

下面是如何在程序中使用该过程:

```
menu()
choice = int(input("Enter your choice: "))
```

下面是程序运行时的样子:

```
1\. Enter student information
2\. Enter test grades
3\. Enter final grades
4\. Enter class times
5\. Enter student documentation
6\. Quit programEnter your choice:
```

过程可以带参数。下面是一个简单的过程，它向作为参数传递给过程的名称打印一个问候语:

```
def greet(name):
  print("Hello,",name)
```

这样称呼的时候:

```
greet("Jane")
```

该程序显示:

```
Hello, Jane
```

从技术上讲，过程返回的是`Non` e 对象，类似于其他语言中的 null 对象。下面是使用 Python shell 的演示:

```
>>> def greet greet("Jane")
(name):
...   print("Hello,",name)
...
>>> what = greet("Joe")
Hello, Joe
>>> print(what)
None
>>>
```

大多数程序员忽略返回值，我们鼓励你也这样做。

# 函数是一个很深的话题

关于在 Python 中使用函数，还有更多需要了解的。在我的下一篇文章中，我将更多地介绍如何使用 Python 函数参数，解释和演示 Python 如何处理范围问题，并解释如何创建和使用递归函数。

感谢您的阅读，请在[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)给我发电子邮件，提出您的意见和建议。
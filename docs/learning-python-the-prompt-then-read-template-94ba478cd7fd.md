# 学习 Python:提示，然后阅读模板

> 原文：<https://levelup.gitconnected.com/learning-python-the-prompt-then-read-template-94ba478cd7fd>

![](img/219fdbfac876112daaee21b4e5756a93.png)

照片由[卢](https://unsplash.com/@riku?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在我的上一篇文章中，我讨论了如何使用 Input-Process-Output 模板作为在 Python 中构建程序的通用指南。回顾一下，大多数 Python 程序都由三个步骤组成——将输入输入到程序中，以某种方式处理输入，以及输出处理结果。

在本文中，我将重点介绍这一步骤的一部分——通过提示用户输入一些数据，然后将数据读入程序，从而将数据输入到程序中。这是一个非常简单的过程，除了在输入数字时必须进行一些数据转换。

# 定义提示

先来定义一下*提示*这个词。提示是给程序用户的消息，告诉他们应该输入什么。提示需要是描述性的，但不必过于详细。

例如，如果您的程序需要用户输入他们的名字，您可以使用这样的提示:

```
Enter your name:
```

但是，如果您希望用户分别输入他们的名字和姓氏，您可能需要更加具体。在这种情况下，更合适的提示可能是两个提示:

```
Enter your first name:
Enter your last name:
```

# 如何将输入输入到程序中

Python 有一个特殊的从用户那里获取输入的函数——input 函数。以下是该函数的第一个语法模板:

*变量名=输入()*

这个函数等待用户输入一些数据，然后按回车键。该函数接受用户键入的任何内容，并将其赋给赋值运算符左侧的变量。

这里有几个例子:

```
name = input()
number = input()
```

对于第一个示例，在键盘上输入的文本被分配给 name 变量。在第二个示例中，在键盘上输入的数字被赋给 number 变量，但是输入的数据被存储为字符串，因为所有的键盘输入都被存储为字符串。

这意味着，如果我们想在 number 变量中存储一个实数，我们必须做一些事情将字符串数据转换成数字数据。在这种情况下，解决方案是使用一个名为`int`的特殊函数。这个函数接受一个字符串值，并将其转换为一个整数。

下面是一个`int`函数工作的例子:

```
number = int(input())
print(number+1)
```

以下是该程序在 Python shell 中以交互方式运行一次的输出:

```
>>> number = int(input())
200
>>> print(number+1)
201
>>>
```

如果我不使用`int`函数，我会得到这个:

```
>>> number = input()
200
>>> print(number+1)
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
TypeError: can only concatenate str (not "int") to str
>>>
```

这个错误告诉我，我只能将字符串连接成字符串。试图在字符串中添加数字是错误的。

如果你想输入一个浮点数(比如 3.14159)，使用`float`函数代替 int 函数。`float`函数接受字符串输入，并将其转换为浮点数。这里有一个例子:

```
pi = float(input())
```

现在，让我们在代码中添加一个提示来真正实现提示，然后阅读模板。下面是一个程序，它提示用户输入一个数字，然后显示用户输入的内容:

```
print("Enter a number: ")
number = int(input())
print(number)
```

`print`功能提供提示:“输入一个数字:”。让我们看看运行程序时的情况:

```
Enter a number:
200
200
```

用户在提示符后的行中输入 200，因为`print`函数总是在输出的末尾添加一个新行。如果您希望提示保持在同一行，您可以像这样编写函数:

```
print("Enter a number: ", end="")
```

这个程序现在看起来像这样:

```
Enter a number: 200
200
```

这更符合大多数用户在遇到提示时的预期。

# 输入功能的第二个版本

还有另一个版本的`input`函数，它消除了在接收用户输入之前调用`print`函数的需要。以下是此版本输入的语法模板:

*变量名=输入("提示")*

在这个版本中，提示作为一个字符串放在`input`函数的括号内。然后首先显示提示，然后等待用户输入他们的响应。

下面是这个版本的`input`函数如何工作的一些例子:

```
name = input("Enter your name: ")
salary = int(input("Enter your yearly salary: "))
print(name,"makes",salary,"dollars per year.")
```

这个程序运行一次的输出是:

```
Enter your name: Bill Gates
Enter your yearly salary: 1000000
Bill Gates makes 1000000 dollars per year.
```

将提示符放在输入函数中删除了一行代码，这看起来可能并不多。但是如果你的程序中有很多提示，那么保存这些单行代码可能会很有意义。你的程序的读者会感激你所做的工作，让他们不必阅读太多的提示语句。

# 提示，然后读取模板摘要

当您希望用户在程序中输入数据时，可以使用“提示，然后读取”模板。提示通过指导用户输入什么来帮助确保用户输入正确的数据。在程序中，没有什么比没有提示的输入更糟糕的了，这让您想知道到底应该输入什么。

使用`prin` t 函数后跟`input`函数，或者仅使用`input`函数，并将提示作为参数放在`input`函数调用中，来实现提示，然后读取模板。

`input`函数以字符串形式返回用户输入，因此如果数据是一个数字，则必须先将其转换为数字，然后再将其赋给变量。

下面是这两种提示类型的另一个示例，然后读取模板实现:

```
print("Enter the number of guests you are inviting: ")
numGuests = int(input())numGuests =
  int(input("Enter the number of guests you are inviting: "))
```

感谢您的阅读，请发送电子邮件至[mmmcmillan1@att.net](mailto:mmmcmillan1@att.net)向我提出意见和建议。
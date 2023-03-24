# 如何让你的 python 代码运行速度提高 10 倍

> 原文：<https://levelup.gitconnected.com/how-to-make-your-python-code-run-10x-times-faster-5690f5d4d7aa>

## 让您的 python 代码运行速度提高 10 倍的简单提示和指南

![](img/cf8aba34f287ed07bae38f5cd8eeb6f4.png)

Python 是一种强大而通用的编程语言，无论你是从事 web 应用程序开发还是数据科学，这种语言已经覆盖了你。在当今世界，Python 正成为顶级编程语言，可以用它构建任何东西。在运行大型代码和程序时，我们需要加速排泄。因为 python 是一种解释型语言，它的速度低于其他编程语言，如 C++，Java 等。

但是我们可以优化我们的代码来加速 python 的排泄，在这篇文章中，我将指导你如何优化你的 Python 代码，使运行比以前更快。有些技巧你已经知道，但有些对你来说是新的，有些影响较小，但有些影响也较大。查看 Python 加速代码技巧列表，并为这篇文章添加书签。

# 1.使用列表理解

列表理解是创建新列表和进行循环工作的最快方法。下面是一个例子，我们将创建一个从 1 到 1000 的偶数列表。

```
# Normal Way
even = []
for i in range(1000):
    if i % 2 == 0:
        even.append(i)
```

下面是列表理解方式。

```
#list Comprehension way
even = [i for i in range(1000) if i % 2 == 0]
print(even)
```

列表理解是做同样工作最快的一行方式。它比 append 方法快得多。所以总是要尝试实现一个列表理解解决方案。

# 2.使用库函数

你不需要创建函数来解决你的问题，大多数函数已经内置在 Python 中了，它们比手动创建的函数更高效、更快。

使用库函数可以使你的代码更简洁，更容易理解，并提高执行速度。尝试用库函数替换你手工创建的函数，并检查程序性能的差异。

# 3.使用 Join 连接字符串

代替使用`“+”`符号来连接字符串，你应该使用`Join`方法来做同样的工作。下面是两种方法的例子和区别。

```
#plus method
Idendity = "I'm" + "a" + "Programmer"
print(Idendity) # I'maProgrammer
```

下面是使用 Join 方法的相同工作的示例代码。

```
#Join method
Identity = " ".join(["I'm", "a", "Programmer"])
print(Identity) # I'm a Programmer
```

Join()方法比简单的连接更快更准确。它会自动在每个单词后添加空格，而在正常的连接中，您需要手动添加空格。因此，这种方法更快，省时，更准确。

# 4.对任何无限循环使用 1

你应该知道无限循环，它永远不会停止，直到循环被打破或面临任何运行时错误。你可以使用 True 使循环无限，使用它没有问题。但是你可以使用`“1”`代替 True 以稍快的速度完成同样的工作。这将减少比较时间，因为它是基于数学计算。

```
#not use this
while True:
    pass#use this 
while 1:
    pass
```

# 5.多重分配

你应该多做几次作业，而不是只做一次。让我们理解多重赋值是如何使你的代码看起来更整洁并提高性能的。下面的示例显示了您正在交换单个赋值和多个赋值的值。

```
#single assignment
a = 5
b = 7
temp = a
a = b
b = temp#multiple assingment
a = 5
b = 7
a, b = b, a
```

此外，如果你用数据声明一个以上的变量，那么你应该使用多重赋值方法一次声明。

```
#normal way
a = 1
b = 4
c = 6#improved way
a, b, c = 1, 4, 6
```

这是用单个赋值操作符“=”声明多个变量的一种稍微快一点的方法。

# 6.使用 C 库

我更喜欢使用 C 语言制作的 Python 库。因为它背后的原因是 Clang 是一种编译机器代码的编译器类型的语言，其执行速度比通常是解释器语言的 Python 快 10 倍。`Numpy`和`ctypes`库是用 C 语言制作的模块的例子。

# 7.更新您的 Python 版本

你应该一直关注新的 python 版本发布，因为它们改进了代码的执行，并修复了一些有时会使你的执行变慢的错误。新版本支持新库的版本特性，因此您应该保持 python 的更新。

他们发布了点版本，这意味着当前的新版本是 3.8，新版本发布后，您可能会得到 3.8.2 或 3.8.3 等。

# 8.正确的数据结构

总是尝试为你的任务使用合适的数据结构。Python 有多种数据结构可以使用，比如列表、元组、字典和集合。但是大多数时候我们使用列表。那不好，你要根据你的任务用数据结构。使用元组或列表，因为从元组迭代比从列表迭代更容易和更快。

# 9.永远不要使用全局变量

在 python 中，全局变量是用全局关键字声明的。但是你不应该在你的代码中使用全局变量，因为全局变量需要更长的操作时间，所以如果没有必要，尽量避免使用。

# 10.避免点操作

使用任何类别的模块时，尽量避免点操作。而不是尝试从库中导入该类。因为当您对库使用点操作时，它首先调用 __getattribute()__，然后使用字典操作，这很费时间。

```
#Don't use that 
import time
time.sleep(10)#Use that 
from time import sleep
sleep(10)
```

# 最后的想法

好吧，这些技巧肯定会提高你的代码执行。但是我有一些其他的建议给你，让你使用`cython`，它将你的代码转换成 C 语言，并使用 python 编译器来编译。否则，如果我错过了任何提高代码速度的重要技巧，请告诉我。

***如果你觉得这篇文章有帮助，请鼓掌，并随时留下你的回复。***

查看我关于 Python 的其他文章，希望它们对你有帮助。

[](/a-beginners-guide-to-tesseract-ocr-using-pytesseract-23036f5b2211) [## 使用 Pytesseract 的 Tesseract OCR 初学者指南

### 光学字符识别或光学字符阅读器(OCR)是电子或机械转换的图像…

levelup.gitconnected.com](/a-beginners-guide-to-tesseract-ocr-using-pytesseract-23036f5b2211) [](/pyqt5-tutorial-learn-gui-programming-with-python-and-pyqt5-df4225d2e3b8) [## PyQt5 教程:用 Python 和 PyQt5 学习 GUI 编程

### Pyqt5 是图形用户界面小部件工具包。它是最强大和最流行的 python 接口之一…

levelup.gitconnected.com](/pyqt5-tutorial-learn-gui-programming-with-python-and-pyqt5-df4225d2e3b8) [](/sending-email-with-python-c6bdc9a07cb5) [## 使用 Python 发送电子邮件

### 在今天的世界里，电子邮件是我们生活的一部分，无论是商业、学校、大学，还是发送电子邮件…

levelup.gitconnected.com](/sending-email-with-python-c6bdc9a07cb5) [](/a-beginners-guide-to-natural-language-processing-in-python-using-nltk-6e4692b825d4) [## 使用 NLTK 的 Python 自然语言处理初学者指南

### 自然语言处理是人工智能的一个分支，它帮助计算机理解自然语言

levelup.gitconnected.com](/a-beginners-guide-to-natural-language-processing-in-python-using-nltk-6e4692b825d4) [](/master-object-oriented-programming-oop-in-python-3-c69a1e8a6d3d) [## 掌握 Python 的面向对象编程(OOP)

### 通过掌握面向对象编程(OOP ),学习用 Python 编写更简洁、更模块化的代码。

levelup.gitconnected.com](/master-object-oriented-programming-oop-in-python-3-c69a1e8a6d3d) [](/how-to-work-with-a-pdf-in-python-a1e0c1d127a4) [## 使用 Python 阅读和编辑 PDF 文档

### 在本文中，我们将了解如何使用 python pdf 模块来读写 pdf 文件。PyPDF2 是一个…

levelup.gitconnected.com](/how-to-work-with-a-pdf-in-python-a1e0c1d127a4) 

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)
# 但是 Python 中的循环是如何工作的呢？

> 原文：<https://levelup.gitconnected.com/but-how-does-looping-work-in-python-af8c8624e80d>

跳下兔子洞去了解迭代的秘密！

数据编码 GIF 由数据营(GIPHY.com)

Python 是一种面向对象的编程语言。

> ePython 中的一切都是对象，但**并非所有对象都是平等的**。

梅里克·加兰听证会 GIF GIPHY 新闻(GIPHY)

有些对象给了我们遍历它们的能力。

这些对象被称为**可迭代对象**或简称为**可迭代对象。**

一些常见的可迭代对象的示例如下:

*   列表
*   字典
*   元组
*   设置
*   用线串

## 他们有什么特别之处？

您是否想知道是什么赋予了这些对象被迭代的能力？

让我们来看看他们隐藏了什么！

Snoop 韩剧 GIF By The Swoon(GIPHY.com)

我们将首先创建一个名为`fruits_list`的列表，并在其上调用内置的`dir`函数。

函数的作用是:返回指定对象的所有属性和方法，不带值。

这将输出到:

注意到`__iter__`方法了吗？

## “__iter__”方法

该方法将**可迭代对象** `fruits_list`转换为**迭代器对象/迭代器。**

这使得`fruits_list`是可迭代的。

注意，同样的操作也可以通过调用另一个名为`iter()`的内置 Python 函数来执行。

![](img/6b9f0e1c625b464b59d89606da22dc7b.png)

由 [Max Duzij](https://unsplash.com/@max_duz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

想知道这些内置函数是从哪里来的？

*结账:*

[](https://bamania-ashish.medium.com/namespaces-scopes-an-easy-lesson-in-python-13d654a95ee) [## 名称空间和作用域:Python 中的一堂简单课

### Python 开发者必备！

bamania-ashish.medium.com](https://bamania-ashish.medium.com/namespaces-scopes-an-easy-lesson-in-python-13d654a95ee) 

继续前进。

计算机工作 GIF(GIPHY.com)

让我们从`fruits_list`创建一个迭代器。

如上所述，有两种方法可以做到这一点。

其输出将是:

让我们看看所有与`fruits_list_iterator`相关的方法。

注意到`__next__`方法了吗？

## “__next__”方法

该方法与一个迭代器对象相关联，该对象有助于在迭代过程中访问它的下一个值。

让我们在迭代器对象`fruits_list_iterator`上调用`__next__`方法。

同样的操作也可以使用内置的 Python 函数`next`来执行，如下所示:

注意到最后一个电话了吗？

## “StopIteration”异常

当我们用完迭代器对象中的元素时，`next` function / `__next__`方法会引发一个`StopIteration`异常。

这太难理解了！我们快完成了！

GIPHY.com 塞斯·梅耶斯深夜与塞斯·梅耶斯的搞笑 GIF

让我们使用`for`循环来迭代我们的`fruits_list`。

让我们终于明白幕后发生了什么！

## 第一步

`for`循环使用`iter()`从`fruits_list`列表中创建了一个迭代器对象。

## 第二步

在每次迭代中，调用`next()`来检索列表中的下一个值。

## 第三步

在每次迭代中，调用`print`语句在控制台上打印每个值。

## 第四步

当对`next()`的调用引发`StopIteration`异常时,`for`循环退出。

这就完成了迭代过程。

这就是 Python 中迭代的工作方式！

代码编码 GIF 由 EscuelaDevRock(GIPHY.com)制作

*感谢您阅读本文！*

[](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium-Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)
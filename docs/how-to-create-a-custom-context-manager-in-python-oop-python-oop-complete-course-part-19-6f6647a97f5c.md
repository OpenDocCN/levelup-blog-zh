# 如何在 Python OOP 中创建自定义上下文管理器:Python OOP 完整教程(第 19 部分)

> 原文：<https://levelup.gitconnected.com/how-to-create-a-custom-context-manager-in-python-oop-python-oop-complete-course-part-19-6f6647a97f5c>

## 了解什么是 Python OOP 中的上下文管理器特殊方法，以及如何覆盖它们。

![](img/973b4a12aa411091555a1bf546a13536.png)

穆罕默德·塔克什在 [Unsplash](https://unsplash.com/s/photos/context) 上拍摄的照片

在我们开始之前，让我告诉你:

*   这篇文章是 Python 面向对象编程完整课程的一部分，你可以在这里找到它。
*   所有资源都可以在下面的“资源”部分找到。

## 介绍

当您听到术语**上下文管理器**时，您必须记住的关键字是`with`

提醒一下，`with`关键字可用于打开文件进行读写，如下所示:

```
with open('file_path', 'w') as file: 
    file.write('hello world !')
```

通常，我们对文件使用`with`关键字来保证**在完成所有需要的处理后关闭**文件。

那么，如何让你的对象能够使用 Python 中的`with`关键字呢？让我们一起来看看。

**目录**

1.  [上下文管理器有哪些特殊的方法？](#aa30)
2.  [可用上下文管理器的特殊方法](#9a52)
3.  如何覆盖上下文管理器的特殊方法？

## 1.上下文管理器有哪些特殊的方法？

一般来说，**上下文管理器**允许设置和清理对象的资源，当它们的创建被一个`with`语句包装时(例如，数据库是在你使用完它们之后需要清理的资源之一。这种清理通过关闭连接来进行)。

特别是，**上下文管理器的特殊方法**是用于定义上下文管理器行为的方法。换句话说，它定义了当您进入该语句的代码块时要做什么，以及当您离开该代码块时要做什么。

现在，让我们检查一下有哪些可用的上下文管理器特殊方法。

## 2.可用的上下文管理器的特殊方法

如前所述，您必须在`with`语句的代码块开始和代码块结束时定义行为。

因此，有两种上下文管理器方法:

*   `__enter__(self)`:定义**上下文管理器**在`with`语句创建的块的开头应该做什么。
*   `__exit__(self, exception_type, exception_value, traceback)`:它定义了**上下文管理器**在其块被执行或终止后应该做什么。

现在让我们看一个具体的例子来学习如何覆盖它们。

![](img/1c5a4d6f9340d9e75416fe4d5aca7abd.png)

由 [Alp Duran](https://unsplash.com/@alpduran) 在 [Unsplash](https://unsplash.com/s/photos/between) 拍摄的照片

## 3.如何覆盖上下文管理器的特殊方法？

首先，让我们举一个例子来说明为什么上下文管理器是有用的。

假设你有一个文件夹，它的名字是`dataset`，在这个文件夹中你有文件`dummy.txt`。

让我们在两种情况下打开这个文件。

*   **不使用(上下文管理器)**。

输出:

```
Hi I am Dummy File
True
```

前面的例子已经被执行，没有任何错误，正如你看到的`file.closed`返回`True`，这意味着文件**被关闭**。

*   现在，如果在你关闭文件之前发生了错误，会发生什么？

输出:

```
IndexError                                Traceback (most recent call last)
<ipython-input-2-9755d8bd0574> in <module>
      4 content = file.read()
      5 a = [1, 2]
----> 6 b = a[10]
      7 file.close()
      8 print(content)IndexError: list index out of range
```

在前面的例子中，您试图从只有两个元素的列表中访问`10th`元素，因此您得到了一个错误。

*   检查文件是否关闭。

```
print(file.closed)
```

输出:

```
False
```

你得到`False`是因为调用函数`file.close()`前出错，文件还没有关闭**。**

*   现在让我们用上下文管理器来尝试前两个例子。

输出:

```
Hi I am Dummy File
True
```

如你所见，它已经运行并且文件已经关闭，因为**上下文管理器**关闭了场景之外的文件。

*   让我们检查一下，如果在关闭文件之前发生了一个`error`，会发生什么。

输出:

```
IndexError                                Traceback (most recent call last)
<ipython-input-6-7d029e3dc81d> in <module>
      3 with open(file_path) as fil:
      4     a = [1, 2]
----> 5     b = a[10]
      6     content = fil.read()
      7IndexError: list index out of range
```

如您所见，您遇到了一个错误。

*   检查文件是否关闭。

```
print(fil.closed)
```

输出:

```
True
```

尽管发生了错误，您还是得到了`True`。

因此，**上下文管理器**帮助你**清理**资源，并且**关闭**文件，即使有错误。

*   现在让我们定义模拟`open()`功能的`File`类。

假设类`File`有三个属性(`file`、`file_path`和`mode`)，并且您想让您的类成为**上下文管理器**，那么您应该覆盖`__enter__()`方法和`__exit__()`方法。

输出:

```
Hi I am Dummy FileThe file has been closed
```

正如您所看到的，该类打开了文件，然后没有任何问题地关闭了它。

*   检查文件是否关闭。

```
print(f.closed)
```

输出:

```
True
```

您得到了`True`，这意味着您的上下文管理器类`File`在文件从`with` 语句体中退出后关闭了该文件。

*   检查在`with`范围内发生异常时会发生什么。

```
with File(file_path, 'r') as f:
    cont = f.read()
    a = [1,2]
    b = a[10]
    print(cont)
    print()
```

输出:

```
The file has been closed---------------------------------------------------------------------------
IndexError                                Traceback (most recent call last)
<ipython-input-11-61758e09ee00> in <module>
      2     cont = f.read()
      3     a = [1,2]
----> 4     b = a[10]
      5     print(cont)
      6     print()IndexError: list index out of range
```

在前面的例子中，您试图从只有两个元素的列表中访问`10th`元素，因此您得到了一个错误。

*   检查文件是否关闭。

```
print(f.closed)
```

输出:

```
True
```

您得到了`True`,这意味着即使发生了错误，文件也是关闭的。

现在，假设您想要访问错误的类型，以便能够处理它。为此，您应该更新`__exit__()`方法。

输出:

```
The file has been closed
exception_type: <class 'IndexError'>
exception_value: list index out of range
traceback: <traceback object at 0x0000000004DD3D88>---------------------------------------------------------------------------
IndexError                                Traceback (most recent call last)
<ipython-input-14-565acbd5e564> in <module>
     22     cont = f.read()
     23     a = [1,2]
---> 24     b = a[10]
     25     print(cont)
     26     print()IndexError: list index out of range
```

正如您所看到的，当一个错误发生在**上下文管理器**范围内时，您可以获得关于这个错误的所有细节，这样您就可以根据您正在处理的用例轻松地处理它。

现在，让我们总结一下我们在这篇文章中学到了什么。

![](img/dd42ce95c035cbd870dff4fd5c710c5a.png)

照片由[安 H](https://www.pexels.com/@ann-h-45017/) 在[像素](https://www.pexels.com/)上拍摄

*   **上下文管理器的特殊方法:**是用于在`with` 语句代码块的开头和结尾定义上下文管理器行为的方法。
*   `enter()`方法:定义**上下文管理器**在`with`语句创建的块的开头应该做什么。
*   `exit()`方法:定义**上下文管理器**在块被执行或终止后应该做什么。

***附言*** :万分感谢您花时间阅读我的故事。在你离开之前，让我快速地提两点:

*   首先，要直接在您的收件箱中获得我的帖子，请在这里订阅[](https://medium.com/@samersallam92/subscribe)*，您可以在这里关注我*[](https://medium.com/@samersallam92)*。***
*   ***第二，作家在媒体上赚了几千美元。为了无限制地访问媒体故事并开始赚钱， [***现在就注册成为媒体会员***](https://medium.com/@samersallam92/membership) ，每月仅需 5 美元。通过注册[***与此链接***](https://medium.com/@samersallam92/membership) ，您可以直接支持我，无需您支付额外费用。***

**![Samer Sallam](img/7d756fa3da76843e747e5ecde71b84d0.png)

萨梅尔·萨拉姆** 

## **Python 面向对象编程的完整教程**

**[View list](https://medium.com/@samersallam92/list/the-complete-course-in-objectoriented-programming-in-python-7b54126a7f4e?source=post_page-----6f6647a97f5c--------------------------------)****24 stories****![A magnifying glass with the Python logo and a set of objects and arrows connecting between them to indicate that everything in Python is an object](img/ce97e46734c67f4f565df3877a88bd11.png)****![A dummy image for better reading and navigation.](img/926249cc12e1f2c807e7711382599fb6.png)****![A dummy image for better reading and navigation.](img/2d5e5c2a38605b91a621b6b26d9ab546.png)**

**要回到上一篇文章，您可以使用以下链接:**

**[第 18 部分:如何创建定制的序列类](https://blog.devgenius.io/how-to-create-a-customized-sequence-class-python-oop-complete-course-part-18-8c47ac58b814)**

**要阅读下一篇文章，您可以使用以下链接:**

**[第 20 部分:如何在 Python 中创建可调用对象](/how-to-create-callable-objects-in-python-python-oop-complete-course-part-20-15fe46e3e2c3)**

## **资源:**

*   **GitHub [这里](https://github.com/samersallam/The-Complete-Course-in-Object-Oriented-Programming-in-Python/tree/main/Context%20Managers%20Special%20Methods)。**
# 如何让你的类对象支持不同的操作符:Python OOP 完整教程—第 14 部分

> 原文：<https://levelup.gitconnected.com/operators-special-methods-python-oop-complete-course-part-14-b5acd58f1fdf>

## 了解什么是 Python OOP 中的运算符特殊方法以及如何覆盖它们。

![](img/7adba84fb2b60b04fa0e125286736c59.png)

由 [geralt](https://pixabay.com/users/geralt-9301/) 在 [Pixabay](https://pixabay.com/illustrations/search/method/?pagi=2&) 上拍摄的照片

在我们开始之前，让我告诉你:

*   这篇文章是 Python 面向对象编程完整课程的一部分，你可以在这里找到它。
*   所有资源都可以在下面的“资源”部分找到。
*   这篇文章在 YouTube 上也有视频[点击这里](https://youtu.be/DLHCWCcOCn4)。

[https://youtu.be/DLHCWCcOCn4](https://youtu.be/DLHCWCcOCn4)

## 介绍

Python 是一种丰富的编程语言，具有不同类型的运算符，您很可能将这些运算符用于内置数据类型，如数字和字符串。

现在的问题是:**您能否将这些操作符用于用户定义的类**？如果答案是肯定的，**如何定义这些操作符与这些类的对象一起使用时的行为**？

要回答这些问题，让我们开始吧。

**目录**

1.  [运营商的特殊方法有哪些？](#2071)
2.  [可用的操作员特殊方法](#aab5)
3.  [如何覆盖运算符的特殊方法？](#4879)

## **1。运营商的特殊方法有哪些？**

当您对用户定义的类的对象使用操作符时，会调用这些方法，例如算术操作符`(+ — * /)`、比较操作符`(> >= < <=)`或 Python 中任何其他支持的操作符。

下一节将展示 Python 中所有可用的操作符的特殊方法。

## 2.可用的操作员特殊方法

这些方法可以分为以下几类:

*   算术运算符的特殊方法
*   比较运算符的特殊方法
*   一元运算符和函数特殊方法
*   赋值运算符的特殊方法

Python 中有大量这样的特殊方法。为了保持文章简洁明了，所有这些方法的定义都被转移到一个独立的 pdf 文件中，你可以从这里下载。

现在，让我们学习如何覆盖这些方法。

![](img/1cd2831c83fe4ff8a97a49cebbcb92cc.png)

迪特尔马·贝克尔在 [Unsplash](https://unsplash.com/s/photos/comparison?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

## 3.如何重写运算符的特殊方法？

假设您有一个包含两个实例属性(`name`和`price`)的`Course`类，并且您想让这个类对象支持`add +`操作符。

首先，让我们在不覆盖任何特殊方法的情况下定义类。

```
class Course:

    def __init__(self, name, price):
        self.name = name
        self.price = price
```

现在，让我们从这个类中定义两个对象，并尝试将其中一个添加到另一个中。

```
course1 = Course('Math', 10)
course2 = Course('Physics', 25)
print(course1 + course2)
```

输出:

```
**TypeError**                                 Traceback (most recent call last)
**~\AppData\Local\Temp/ipykernel_2568/3239797223.py** in <module>
      1 course1 **=** Course**('Math',** **10)**
      2 course2 **=** Course**('Physics',** **25)**
**----> 3** print**(**course1 **+** course2**)**

**TypeError**: unsupported operand type(s) for +: 'Course' and 'Course'
```

我们得到了**类型错误**,因为当`+`操作符被用于来自`Course` 类的对象时，Python 解释器不知道该做什么。

要解决这个问题，您可以覆盖 `__add__`方法，并定义当`+`操作符与`Course`类对象一起使用时的正确行为(在这种情况下，我假设我们通过对两个对象的价格求和来对它们求和)。

现在，让我们看一下覆盖这个方法后的前一个例子。

输出:

```
The total price is :  35
```

如您所见，您现在没有得到任何错误。

**注意** : `self`和`other`参数，在前面例子的`__add__`方法中，代表`+`操作符的左右操作数。

现在，让我们看另一个例子。假设您有一个具有 `name, age, major,` 和`cgpa`实例属性的`Student`类，并且您想让这个类支持`==`操作符(在这种情况下，我假设两个学生是相等的，如果他们具有相等的`cgpa`和相等的`major`)。

您可以简单地通过覆盖`__eq__`方法来实现。

输出:

```
True
```

如你所见，你得到了**真值**，因为很明显两个学生有相同的`major`和相同的`cgpa`。

> 注意:默认情况下，预定义的行为支持一些特殊的方法。通过使用 dir()函数，您可以获得所有这些方法的列表。此外，您可以像覆盖任何其他特殊方法一样覆盖它们。

**现在，让我们总结一下我们在这篇文章中学到的知识:**

![](img/ccb7fc677cd37fec2b5d19924bb08e77.png)

照片由[安 H](https://www.pexels.com/@ann-h-45017/) 在[像素](https://www.pexels.com/)上拍摄

*   **操作符的特殊方法:**是在使用算术操作符`(+ — * /)`、比较操作符`(> >= < <=)`或任何其他类型的操作符时被调用的方法。
*   在 Python 中，有大量这样的特殊方法来表示所有可用的操作符。

***附:*** *:非常感谢您花时间阅读我的故事。在你离开之前，让我快速地提两点*

*   *首先，要直接在您的收件箱中获得我的帖子，请在这里订阅*[](https://medium.com/@samersallam92/subscribe)*，*并且您可以在这里关注我*[](https://medium.com/@samersallam92)**。***
*   ***第二，作家在媒介上制造了几千个****$*$***。为了无限制地访问媒体故事并开始赚钱，* [***现在就注册成为媒体会员***](https://medium.com/@samersallam92/membership)**，其中***每月只需花费 5 美元。报名* [***有了这个链接***](https://medium.com/@samersallam92/membership) *，就可以直接支持我，不需要你额外付费。*****

**![Samer Sallam](img/7d756fa3da76843e747e5ecde71b84d0.png)

[萨梅尔萨拉姆](https://medium.com/@samersallam92?source=post_page-----b5acd58f1fdf--------------------------------)** 

## **Python 面向对象编程的完整教程**

**[View list](https://medium.com/@samersallam92/list/the-complete-course-in-objectoriented-programming-in-python-7b54126a7f4e?source=post_page-----b5acd58f1fdf--------------------------------)****24 stories****![A magnifying glass with the Python logo and a set of objects and arrows connecting between them to indicate that everything in Python is an object](img/ce97e46734c67f4f565df3877a88bd11.png)****![A dummy image for better reading and navigation.](img/926249cc12e1f2c807e7711382599fb6.png)****![A dummy image for better reading and navigation.](img/2d5e5c2a38605b91a621b6b26d9ab546.png)**

**要回到上一篇文章，您可以使用以下链接:**

**[第 13 部分:初始化和构造特殊方法](https://blog.devgenius.io/initialization-and-construction-special-methods-python-oop-complete-course-part-13-78b62305101)**

**要阅读下一篇文章，您可以使用以下链接:**

**第 15 部分:类表示特殊方法**

## **资源:**

*   **GitHub [这里**这里**](https://github.com/samersallam/The-Complete-Course-in-Object-Oriented-Programming-in-Python/tree/main/Operators%20Special%20Methods)**
*   **操作员特殊方法 [pdf 文件](https://samersallam.gumroad.com/l/dbbdu)**
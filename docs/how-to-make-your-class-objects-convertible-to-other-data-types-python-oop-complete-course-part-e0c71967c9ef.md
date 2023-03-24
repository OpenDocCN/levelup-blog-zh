# 如何将你的类对象转换成其他数据类型:Python OOP 完整教程—第 16 部分

> 原文：<https://levelup.gitconnected.com/how-to-make-your-class-objects-convertible-to-other-data-types-python-oop-complete-course-part-e0c71967c9ef>

## 了解什么是 Python OOP 中的类型转换特殊方法，以及如何覆盖它们。

![](img/96f008d2d5ae7048d01a88fdada5cb43.png)

照片由[提婆达尔山](https://unsplash.com/@darshan394)在 [Unsplash](https://unsplash.com/s/photos/ways) 上拍摄

在我们开始之前，让我告诉你:

*   这篇文章是 Python 面向对象编程完整课程的一部分，你可以在这里找到它。
*   所有资源都可以在下面的“资源”部分找到。
*   这篇文章在 YouTube 上也有视频[点击这里](https://youtu.be/wM1nCsVan9c)。

[https://youtu.be/wM1nCsVan9c](https://youtu.be/wM1nCsVan9c)

## 介绍

在本文中，您将学习如何将类对象转换成其他数据类型。

假设您有一个浮点值为`a = 3.14`的变量。如果你决定把这个变量转换成一个整数，你所要做的就是使用`int()`函数:`b = int(a)`。

这种转换是可能的，因为 Python 知道如何将浮点变量转换成整数。

但是，如果您想将类中的一个对象转换为整数，该怎么办呢，如下所示:

```
obj = DummyClass()
int_obj = int(obj)
```

在这种情况下，你会得到一个错误，因为解释器不知道如何把这个对象转换成一个整数。

因此，要学习如何将你的类对象转换成其他数据类型，请继续阅读…

**目录**

1.  [什么是类型转换特殊方法？](#ddcc)
2.  [可用类型转换的特殊方法](#42d8)
3.  如何覆盖类型转换的特殊方法？

## 1.什么是类型转换特殊方法？

当您想要将对象数据类型从一种类型转换为另一种类型时，会调用这些方法。

例如，(`__int__()`、`__float__()`等……)。

因此，这些方法将定义如何将对象转换成所需的数据类型。

## 2.可用的类型转换特殊方法

Python 是一种可及的编程语言，具有如下特殊方法

*   `__int__()`:这个方法定义了用你的类的对象调用函数`int()`时的行为。
*   `__float__()`:这个方法定义了当用你的类的对象调用函数`float()`时的行为。

为了保持文章简洁明了，所有这些方法的定义都被移到了这个 [pdf 文件](https://samersallam.gumroad.com/l/lmbgs)中。

![](img/604e6d1f5163232e6f5ccfcb15e90145.png)

照片由[伊丽莎白·凯](https://unsplash.com/@elizabeth_kay)在 [Unsplash](https://unsplash.com/s/photos/compare) 上拍摄

## 3.如何重写类型转换特殊方法？

*   首先，让我们开始谈论`__int__()` 的方法

回到上一篇文章中定义的`Student` 类。让我们试着把它的对象`student`转换成整数，看看结果。

输出:

```
**TypeError**                                 Traceback (most recent call last)
**<ipython-input-11-1b9d40f01ca0>** in <module>
**----> 1** print**(**int**(**student**))**

**TypeError**: int() argument must be a string, a bytes-like object or a number, not 'Student'
```

如您所见，您得到了一个`TypeErrorr`，因为`int()`函数不支持`Student` 数据类型。

要解决前面的错误，您可以覆盖`__int__()`方法。

现在**，**让我们试着覆盖`__int__()`方法，例如，让它返回你的`Student` 对象的`age` 。参考下面代码中的最后一个方法定义…

输出:

```
25
```

没有任何错误，你得到了`25`，这是对象的年龄。

*   现在我们来谈谈`__float__()`法

使用同一个`Student` 类，让我们试着用`student` 对象调用`float()`函数，看看结果…

输出:

```
**TypeError**                                 Traceback (most recent call last)
**~\AppData\Local\Temp/ipykernel_6068/718096445.py** in <module>
**----> 1** print**(**float**(**student**))**

**TypeError**: float() argument must be a string or a number, not 'Student'
```

如您所见，您得到了相同的`TypeError`，因为`float()`函数不支持`Student`数据类型。

要解决前面的错误，您可以覆盖`__float__()`方法。

现在，让我们重写`__float__()`方法，比如让它返回你的`Student` 对象的`cgpa` 。参考下面代码中的最后一个方法定义…

输出:

```
95.5
```

**总结:**当你覆盖一个类型转换特殊方法时，你决定了当你想把一个对象从对象类类型转换成另一个数据类型时它应该返回什么。

现在，让我们总结一下我们在这篇文章中学到了什么。

![](img/ccb7fc677cd37fec2b5d19924bb08e77.png)

照片由[安 H](https://www.pexels.com/@ann-h-45017/) 在[像素](https://www.pexels.com/)上拍摄

*   **类型转换特殊方法**是当你想将一个对象从你的类转换成另一种数据类型时被调用的方法。
*   Python 中每个可用的数据类型都有一个特殊的方法。

***附:*** *:非常感谢您花时间阅读我的故事。在你离开之前，让我快速地提两点:*

*   *首先，要想直接在你的收件箱里看到我的帖子，请在这里订阅*[](https://medium.com/@samersallam92/subscribe)*，*并且你可以在这里关注我*[](https://medium.com/@samersallam92)**。***
*   ***第二，作家在媒介上制造了数以千计的****$****。为了获得无限制的媒体故事并开始赚钱，* [***现在就注册成为媒体会员***](https://medium.com/@samersallam92/membership)**，其中* *每月只需花费 5 美元。通过此链接*[](https://medium.com/@samersallam92/membership)**报名，可以直接支持我，不需要你额外付费。*****

**![Samer Sallam](img/7d756fa3da76843e747e5ecde71b84d0.png)

[萨梅尔萨拉姆](https://medium.com/@samersallam92?source=post_page-----e0c71967c9ef--------------------------------)** 

## **Python 面向对象编程的完整教程**

**[View list](https://medium.com/@samersallam92/list/the-complete-course-in-objectoriented-programming-in-python-7b54126a7f4e?source=post_page-----e0c71967c9ef--------------------------------)****24 stories****![A magnifying glass with the Python logo and a set of objects and arrows connecting between them to indicate that everything in Python is an object](img/ce97e46734c67f4f565df3877a88bd11.png)****![A dummy image for better reading and navigation.](img/926249cc12e1f2c807e7711382599fb6.png)****![A dummy image for better reading and navigation.](img/2d5e5c2a38605b91a621b6b26d9ab546.png)**

**要回到上一篇文章，您可以使用以下链接:**

**[第 15 部分:类表示特殊方法](https://medium.com/mlearning-ai/class-representation-special-methods-python-oop-complete-course-part-15-2bdfed7f417)**

**要阅读下一篇文章，您可以使用以下链接:**

**[第 17 部分:当你获取、设置或删除一个实例属性时，场景之外会发生什么](/what-happens-beyond-the-scene-when-you-get-set-or-delete-an-instance-attribute-d88be5610456)**

## **资源:**

*   **GitHub [**这里**](https://github.com/samersallam/The-Complete-Course-in-Object-Oriented-Programming-in-Python/tree/main/Type%20Conversion%20Special%20Methods)**
*   **类型转换特殊方法定义 [pdf 文件](https://samersallam.gumroad.com/l/lmbgs)。**
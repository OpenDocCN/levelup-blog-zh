# Python 中多重处理的实用指南

> 原文：<https://levelup.gitconnected.com/a-practical-guide-to-multiprocessing-in-python-3e10195eec23>

## 了解如何使用 Python 中的进程池来加速您的程序。

![](img/de62bc5fde3e49c09872d25aca108750.png)

照片由 [Olivier Collet](https://unsplash.com/@ocollet?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 介绍

在本文中，您将学习如何通过使用 Python 中的`multiprocessing`模块和`concurrent.futures`模块并行运行 CPU 大量任务来加速您的程序。

**目录**

1.  [执行的正常流程](#dc55)
2.  [什么是多重处理？](#bb53)
3.  [如何给目标函数传递一个参数？](#a9f0)
4.  [流程池执行者](#7a30)
5.  [线程和多重处理的区别](#267c)

## 1.正常的执行流程

参考本文，我在其中更详细地讨论了程序执行的正常流程。

现在，让我们看一个代码示例。假设我们有做一些数学计算(CPU 扩展任务)的`CPU_extensive()`函数，假设你想调用它 5 次。我们将通过`time.sleep()`函数模拟长时间运行。

```
def CPU_extensive():
    """
    Doing mathematical calculations
    """
    time.sleep(2)
    print('Done')
```

输出 **:**

```
Done
Done
Done
Done
Done
the execution  finished in 10.028165700000045 seconds
```

它花费了大约 10 秒钟，因为每次迭代花费了 2 秒钟。

现在，让我们来理解什么是多重处理，以及它如何加快处理时间。

## 2.什么是多重处理？

它是一种允许您的程序通过同时使用多个 CPU 内核并行运行的技术。它用于显著地加速你的程序，特别是当它有很多 CPU 扩展任务的时候。

在这种情况下，多个功能可以一起运行，因为每个功能将使用不同的 CPU 内核，这反过来将提高 CPU 利用率。

要在 Python 中使用多重处理，你可以使用`multiprocessing`模块，它是一个内置模块，所以你不需要安装任何东西。

要使用多个进程，您必须使用类`multiprocessing.Process()`创建一个进程对象，并确定您希望使用该进程执行哪个函数。

现在，让我们在测量执行时间的同时，使用多重处理重复前面的例子。

**注意** : `process.join()`已经用于让主程序等待进程完成执行。

输出:

```
Done
Done
Done
Done
Done
the program has finished in 2.6050616 seconds
```

只花了大约 2 秒钟，因为现在所有的功能都已并行执行，无需相互等待。

**注意:**如果你机器中的内核数量少于你想要运行的进程数量，你的电脑会想办法把这些进程分散开来运行。

在前面的例子中，我们看到了一个没有参数的函数。现在的问题是，如果函数有参数，如何传递函数参数？

## 3.如何向目标函数传递一个参数？

假设目标函数`CPU_extensive()`有一个将在数学计算中使用的参数。您可以使用`args`参数向 `multiprocessing.Process()`对象中的这个参数传递一个值。

```
def CPU_extensive(x):
    """
    Doing mathematical calculations
    """
    time.sleep(2)
    print(f'Done {x}')
```

然后，您可以像下面这样传递参数:

输出:

```
Done 0
Done 500
Done 1000
Done 1500
Done 2000
the program has finished in 2.622033 seconds
```

现在，让我们继续，看看在 Python 中使用多处理的更优雅的方式。

## 4.进程池执行器

这是一种更简单、更高效的并行运行进程的方式。Python3.2 及以上版本支持。为了使用它，我们需要内置模块`concurrent.futures`。

为了理解如何使用这个池，让我们先看看下一个代码示例，然后让我来解释这是怎么回事。

```
def CPU_extensive(x):
    """
    Doing mathematical calculations
    """
    time.sleep(2)
    return f'Done {x}'
```

输出:

```
Done 0
Done 500
Done 1000
Done 1500
Done 2000
the program has finished in 4.5337259 seconds
```

首先，您可以看到类`ProcessPoolExecutor`已经被导入并用于定义一个对象，该对象可以用作带有`with`语句的上下文管理器。

接下来我们来说说`submit()`法。这个方法简单地调度一个要执行的函数，并返回一个代表函数执行状态的`Future`对象。该方法**不会按照函数执行的顺序返回结果。**

最后，要获得这个函数返回的值，您可以使用`as_completed()`函数，它给出了一个迭代器，您可以循环遍历这个迭代器以产生完整函数的结果。注意数值(0–500–1000–1500–2000)是**而不是**排序的。

另一种调用函数的方法是使用`map()`方法。该方法可以替代`for-loop`执行多个进程。该方法按照函数的执行顺序返回结果。

让我们看一个使用`map()`方法的例子。

输出:

```
Done 1
Done 2
Done 3
Done 4
Done 5
the execution has finished in 4.5361406 seconds
```

请注意，函数结果是按照它们被调用的顺序排序的。

**注意**:当你从创建一个对象时，你可以通过将所需的数量传递给`ProcessPoolExecutor`类来控制创建的进程的数量。默认情况下，这个数字等于机器上的核心数。

在结束本文之前，让我们快速比较一下多线程和多重处理。

## 5.线程和多重处理的区别

*   线程只在 CPU 的一个内核上运行，它们共享 CPU 的资源，而在多重处理中，每个进程保留一个单独的内核。
*   多线程对于 IO 扩展任务是有效的，而多处理对于 CPU 扩展任务是有效的。
*   在多处理中，任务同时运行(**并行**)，而在线程中，它们似乎只同时运行(**并发**)。

[](/a-practical-guide-to-multithreading-in-python-adae78df1fac) [## Python 多线程实用指南

### 了解如何使用 Python 中的线程池来加速您的程序

levelup.gitconnected.com](/a-practical-guide-to-multithreading-in-python-adae78df1fac) 

**就是这样！！现在让我们继续讨论文章摘要。**

![](img/dfe3babda390356c9538b69c6f042deb.png)

照片由[安 H](https://www.pexels.com/@ann-h-45017/) 在[像素](https://www.pexels.com/)上拍摄

在本文中，您已经了解到:

*   通过并行运行多个 CPU 密集型任务，您可以使用多重处理来加速程序执行。
*   您可以使用`multiprocessing`模块创建和管理流程。
*   您可以使用`concurrent.futures`模块中的流程池执行器以更好的方式创建和管理流程。

***附言*** *:万分感谢您花时间阅读我的故事。在你离开之前，让我快速地提两点*

*   *首先，要想直接在你的收件箱里看到我的帖子，请在这里订阅*[](https://medium.com/@samersallam92/subscribe)**并且你可以在这里关注我*[](https://medium.com/@samersallam92)**。***
*   ***第二，作家在媒介上制造了数以千计的****$****。为了无限制地访问媒体故事并开始赚钱，* [***现在就注册成为媒体会员***](https://medium.com/@samersallam92/membership)**其中* *每月只需花费 5 美元。通过此链接* *报名* [***，可以直接支持我，不需要你额外付费。***](https://medium.com/@samersallam92/membership)***

**![Samer Sallam](img/7d756fa3da76843e747e5ecde71b84d0.png)

萨梅尔·萨拉姆** 

## **Python 初学者到专家的完整课程**

**[View list](https://medium.com/@samersallam92/list/python-complete-beginner-to-expert-course-32d3a941c05e?source=post_page-----3e10195eec23--------------------------------)****21 stories****![A white desk with a laptop, its screen shows some lines of code, phone, cup of coffee, a screen and a white lamp on it.](img/a990b710cc6c33085894453e6e0b10b9.png)****![A dummy image for better reading and navigation.](img/6423d57aa5d56c05169d2738a1592214.png)****![A dummy image for better reading and navigation.](img/f8ba979ab77abc24ff23c97dc0499b45.png)****![Samer Sallam](img/7d756fa3da76843e747e5ecde71b84d0.png)

萨梅尔·萨拉姆** 

## **Python 面向对象编程的完整教程**

**[View list](https://medium.com/@samersallam92/list/the-complete-course-in-objectoriented-programming-in-python-7b54126a7f4e?source=post_page-----3e10195eec23--------------------------------)****24 stories****![A magnifying glass with the Python logo and a set of objects and arrows connecting between them to indicate that everything in Python is an object](img/ce97e46734c67f4f565df3877a88bd11.png)****![A dummy image for better reading and navigation.](img/926249cc12e1f2c807e7711382599fb6.png)****![A dummy image for better reading and navigation.](img/2d5e5c2a38605b91a621b6b26d9ab546.png)**
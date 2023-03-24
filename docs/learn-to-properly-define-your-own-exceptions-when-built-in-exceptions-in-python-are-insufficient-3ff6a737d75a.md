# 当 python 中的内置异常不足时，要学会正确定义自己的异常

> 原文：<https://levelup.gitconnected.com/learn-to-properly-define-your-own-exceptions-when-built-in-exceptions-in-python-are-insufficient-3ff6a737d75a>

## pythons 内置的异常很棒，但还不够好…

![](img/0216fc94f1e359eb56667f75d4fe0e96.png)

照片由 [Unsplash](https://unsplash.com/photos/Vy2cHqm0mCs) 上的[罗姆森·普里查维特](https://unsplash.com/@woodies11)拍摄

N ew 程序员经常对[异常处理](https://docs.python.org/3/tutorial/errors.html)的主题感到困惑，并寻找在他们现有的代码中采用异常处理的最佳实践，但是如果没有关于异常处理如何工作的正确知识，它可能会导致他们的程序行为异常。

在本文中，我将给出一个简单的用例，在这个用例中，使用内置 python 异常是不够的，并导致一个模糊的错误消息，我将解决这个问题，并尝试从最初使用内置异常开始进行改进，然后逐渐定义我们自己的自定义异常，以演示程序员在编写程序时的一个有点现实的思考过程

# 让我们从例子开始

这个程序试图计算一个三角形的面积。如果你还记得中学几何课上的话，如果三角形的任意两条边之和小于第三条边之和，那么这个三角形就是无效的。

下面是这个函数的实现

如果您在终端中运行两次，首先使用有效的边三角形(3，4，5)，然后使用无效的边三角形(3，4，10)

您得到一个 **ValueError** 内置异常，带有一个消息有效负载“**数学域错误”**。如果我是这个程序的用户，我不知道这个错误意味着什么，也不知道如何纠正我的输入

让我们修改这个程序，并尝试在这里提出一个更具体的异常，在它的有效载荷中携带更多有用的信息来帮助我们的程序用户。定义一个继承自 **Exception** 类的 **TriangleError** 异常是一个好的开始。

这看起来是一个非常简单的定义，确实足够了，因为它从基类**异常继承了 *__init__* 、 *__str__* 和 *__repr__* 的实现。**

> 如果我们只是想要一个独特的异常类，它有一些基本的功能，并且可以与其他异常类型分开处理和引发，那么这个定义就足够了，而且功能齐全

现在让我们修改我们的 triangle_area 函数来抛出这个异常

用同样的输入再次运行程序，我们得到

该输出更好，因为用户可以清楚地识别出错误 **TriangleError** 与三角形几何有关，并且我们正在创建无效的三角形。但是请注意，有效载荷信息没有提供任何关于侧面的信息，所以让我们继续努力

在这里，我们重写了基类 *__init_* _、 *__str__* 和 *__repr_ 的所有关键方法。* *超级()。__init__(text)* 将有效载荷消息存储到基类**异常**中。在 *__str__* 和 *__repr__* 中，我们通过 *self.args[0]检索来使用存储在基类中的有效负载消息。sides* 被定义为一个属性，所以我们的实例变量可以像公共属性一样被访问。在这里使用属性[的更多优势](https://www.freecodecamp.org/news/python-property-decorator/)

让我们修改我们的 *triangle_area* 函数来使用这个改进的错误类

我们所要做的就是将 *sides* 参数添加到 **TriangleError** 类中。现在让我们运行这个例子

查看错误报告。看起来棒极了…不是吗？不仅用户得到了正确的错误类型，而且有效载荷信息也清楚地表明双方的输入是错误的。不仅如此，我们现在还可以在程序中处理这个异常，并检查它的有效载荷，以获得错误的边长。另外，请注意我们是如何访问 sides 属性的。这可以通过将边定义为属性来实现

# 结论

内置异常有时不如自定义异常有用。您可以让您的程序传达与领域相关的信息，这在大型库或程序中非常有用

# 快速链接

> *P* [*属性装饰器使用*](https://www.freecodecamp.org/news/python-property-decorator/)
> 
> [*python 中的异常处理*](https://docs.python.org/3/tutorial/errors.html)
> 
> [*无效三角形*](https://www.geeksforgeeks.org/check-whether-triangle-valid-not-sides-given/#:~:text=Approach:%20A%20triangle%20is%20valid,three%20conditions%20should%20be%20met.)
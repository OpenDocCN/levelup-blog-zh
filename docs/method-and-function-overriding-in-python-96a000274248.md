# Python 中的方法和函数覆盖

> 原文：<https://levelup.gitconnected.com/method-and-function-overriding-in-python-96a000274248>

![](img/031a625671bf2182913ac42091f4053d.png)

来自[像素](https://www.pexels.com/photo/woman-coding-on-computer-3861958/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的[摄影](https://www.pexels.com/@thisisengineering?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

## 使用@functools.singledispatch 和 type 批注。

方法和函数重写是一种非常有用的技术。它允许您在代码中多次定义同一个方法，但是每个方法都采用不同类型的参数。

## 术语注释，给那些关心的人！

方法在类上定义。当子类重新定义父类中可用的方法时，就会发生重载。因此我们称这个 ***方法重载*** 。我不会在本文中讨论方法重载。

函数不是在类对象上定义的(正常情况下)。当我们定义一个函数的多个版本时，称为 ***函数覆盖*** 。这篇文章是关于 Python 中的函数覆盖的。

## 一个没有函数覆盖的例子

让我们用一个圆形类和一个方形类的标志性例子。

这里没什么可看的。正方形有一个实例属性:它的边长，而圆定义了一个半径属性。每个实例的`__init__`方法取一个初始化值，默认值为 0。这是面向对象的 Python 101，目前为止没什么特别的。我们以后会需要这个代码。

## **计算物体面积的函数。**

正方形面积的计算不同于圆形面积的计算。我的函数，`area`，可以取一个正方形或者圆形物体，并计算其面积。它知道计算这两种几何形状面积的不同方法。将来，我可能想添加对其他形状的支持。

这个函数使用一个`if … elif`构造来识别对象是圆形还是方形，然后使用不同的计算方法。此函数不使用覆盖。它接受任何类型的参数，并计算出如何处理它们。

这是函数的驱动因素:

这是我终端的输出:

```
113.09733552923255
144.0
```

你可以在 Github 上找到这个例子。

## 使用函数覆盖的示例

到目前为止,`area`功能运行得很好——没有什么问题。然而，当我增加对越来越多的几何形状的支持时，它会变得臃肿。随着它的成长，测试也将变得更加困难。

让我们使用覆盖将函数分解成更具体的变量。用以下代码替换之前的`area`函数:

事情是这样的:

*   第 1 行:从 functools 模块导入`singledispatch`。这就是“奇迹”发生的地方。
*   第 2 行:在名为`area.`的函数上使用装饰器
*   第 3-#4 行:基础函数 area 没有实现，并返回一个错误。
*   第 7 行:*寄存器*一个用于`area`函数的覆盖，该函数带有一个`Circle`参数
*   第 12 行:*寄存器*一个用于`area`函数的覆盖，该函数带有一个`Square`参数
*   第 8 行和第 9 行:注册的函数被简单地命名为 _ 因为它们不会被直接调用。

正如您所看到的，我们已经注册了两个特定的处理程序来分派(或处理)函数的单个参数的变化。这就是为什么装修工叫`singledispatch`的原因。这是在 Python 版本 3.4 中引入的。

## **为什么在基本函数上引发 NotImplementedError？**

这是一点惯例，但非常有用。如果我正在写一个库供其他人使用，那么他们可能会定义一些形状，比如五角星形，这我不知道。我们选择引发一个异常供客户端处理，而不是默默地返回 0。我们已经明确定义了`area`不知道如何返回作为参数传入的对象的计算值。

运行这段代码，我得到了和以前完全一样的输出！你可以在 Github 上看到完整的例子。

## 改为使用类型批注

到目前为止，我们已经使用以下装饰器注册了基本方法的特定变体，并显式指定了类型:

```
@area.register(Circle)
@area.register(Square)
```

有一个更聪明的方法，使用类型注释！从 3.7 版本开始，Python 非常乐意使用函数参数上的类型注释来推断分派函数的正确类型。

下面是相同代码的一个示例，更新后使用了类型批注:

正如你在第 7 行和第 12 行看到的，我们没有指定注册函数的类型。相反，正确的类型是从第 8 行和第 13 行的注释中推断出来的。

```
*def* _(***any_object***: Circle): ...
*def* _(***any_object***: Square): ...
```

在注册调度处理程序时使用注释使得代码自文档化，因此更易于维护和理解。

## 了解将调用哪个调度程序

如果将不同的类型传递给函数，那么确定将调用哪个调度程序有时会很有用。

以下示例显示了不同的函数如何注册为基本函数的调度程序:

这是我的终端中的结果—您将获得不同的地址。

```
<function _ints at 0x10b2a0dc0>
<function _lists at 0x10b2a0e50>
<function _floats at 0x10b2a0ee0>
<function fancy_print at 0x10b2a0af0>
```

## 使用@singledispatchmethod 重写实例方法

实例方法总是有一个附加的，第一个参数，通常命名为*。functools 模块提供了一个名为 **singledispatchmethod** 的 singledispatch 的替代品，用于实例方法。它可以处理初始自身参数。*

*这是一个关于狗类的人为例子。它会叫，有不同的品质。发送整数值会让狗叫多次。发一串也会改变狗叫声的质量。*

*这是我的终端的输出:*

```
*BARK BARK BARK BARK BARK 
quiet bark*
```

*从这个例子中可以看出，通过使用`@singledispatchmethod` decorator 可以覆盖一个类的实例方法。*

## *结论和警告*

*在 functools 模块中，Python 从 3.4 版本开始提供了两个装饰器:@singledispatchmethod 用于实例方法，而@singledispatch 用于函数。从版本 3.7 开始，这些已经能够使用类型注释。*

*这是一项了不起的能力——但它有其局限性。这些装饰符只能用于覆盖方法或函数签名中的第一个参数。如果您需要能够覆盖多个参数，您将不得不去别处寻找。也许 PyPi 模块 [multipledispatch](https://pypi.org/project/multipledispatch/) 正是您所需要的！*

*如果您想了解更多关于 Python 中面向对象编程的知识，可以看看我的其他文章！*

*[](https://medium.com/@quinn.richard/objects-classes-instances-in-python-for-beginners-4dc35b39c785) [## 如何有效地使用 Python 类

### 让我们探索 Python 中强大的面向对象编程概念！

medium.com](https://medium.com/@quinn.richard/objects-classes-instances-in-python-for-beginners-4dc35b39c785)*
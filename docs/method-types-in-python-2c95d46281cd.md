# Python 中的 cls 与 self 方法类型

> 原文：<https://levelup.gitconnected.com/method-types-in-python-2c95d46281cd>

![](img/099a0b80ff6f1f24a0b800bb3933046a.png)

[https://real python . com/instance-class-and-static-methods-demystified/](https://realpython.com/instance-class-and-static-methods-demystified/)

你有没有想过为什么在 python 中一些方法接收作为参数的 ***self*** 关键字，其他的 ***cls*** *，*和其他的什么都没有？

为了理解这些关键字以及 python 为什么要求程序员将它们作为参数发送，我先解释一下类和实例的区别。

## 什么是课？

类是创建对象的代码模板(就像建造房子的蓝图)。类定义了变量和所有与描述对象相关的不同功能。在 python 中，类是由关键字 class[1]创建的。

## 什么是实例？

使用类的构造函数创建对象。这个对象将被称为类[1]的实例。

# python 中不同的方法类型

在 python 中有三种不同的方法类型。静态方法、类方法和实例方法。每一种都有不同的特点，应该用在不同的场合。

## 静态方法

python 中的静态方法必须通过用 *@staticmethod* 修饰来创建，以便让 python 知道这个方法应该是静态的。静态方法的主要特征是它们可以在没有实例化类的情况下被调用。这些方法是自包含的，这意味着它们不能访问任何其他属性或调用该类中的任何其他方法。

当你有一个类，但你不需要一个特定的实例来访问这个方法时，你可以使用一个静态方法。例如，如果你有一个名为 **Math** 的类和一个名为 **factorial** 的方法。您可能不需要特定的实例来调用该方法，因此您可以使用静态方法。

静态方法的示例:

Python 上使用阶乘方法的数学类示例

## 分类方法

必须用 decorator *@classmethod* 创建方法，这些方法与静态方法有一个共同的特点，即它们可以在没有类实例的情况下被调用。区别在于访问其他方法和类属性的能力，而不是实例属性[2]。

## 实例方法

只有当类已经实例化时，才能调用此方法。一旦创建了该类的对象，实例方法就可以被调用，并且可以通过保留字 **self** 访问该类的所有属性。实例方法能够创建、获取和设置新的实例属性，并调用其他实例、类和静态方法[2]。

## 不同方法类型的示例

Python 中不同方法类型的示例

# self vs cls

关键字 **self** 和 **cls** 的区别仅在于方法类型。如果创建的方法是实例方法，那么必须使用保留字**self****，**，但是如果方法是类方法，那么必须使用关键字 **cls** 。最后，如果方法是一个静态方法，那么这些词都不会被使用，因为如前所述，静态方法是自包含的，不能访问实例或类变量，也不能访问实例或类方法。

## 参考

1.  [-https://www . hackere earth . com/practice/python/object-oriented-programming/classes-and-objects-I/tutorial/](https://www.hackerearth.com/practice/python/object-oriented-programming/classes-and-objects-i/tutorial/)
2.  [https://real python . com/instance-class-and-static-methods-demystified/](https://realpython.com/instance-class-and-static-methods-demystified/)
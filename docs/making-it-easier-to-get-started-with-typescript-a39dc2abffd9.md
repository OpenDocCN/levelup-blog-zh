# 使开始使用 TypeScript 变得更加容易

> 原文：<https://levelup.gitconnected.com/making-it-easier-to-get-started-with-typescript-a39dc2abffd9>

## TypeScript 中的函数定义、类和继承

![](img/0ebb92e35a866a1342f899937f252551.png)[](/typescript-learning-notes-i-installing-compilation-and-basic-types-3720dd9bc494) [## TypeScript 学习笔记 I:安装编译和基本类型

### TypeScript 是微软开发的开源编程语言，TypeScript 是一个超级 Javascript，遵循…

levelup.gitconnected.com](/typescript-learning-notes-i-installing-compilation-and-basic-types-3720dd9bc494) 

# 函数定义

让我们从一些容易理解的例子开始，来解释在 TypeScript 中定义函数的几种方法。

1.  函数声明方法

2.匿名函数方法

3.定义方法来传递参数，并为参数和方法名声明类型。

4.匿名函数为参数和方法名传递带有声明类型的参数。

5.没有返回值的方法。

6.在 TypeScript 中，形参和实参必须相同，如果不相同，就必须配置可选参数，add？签名。

7.在 TypeScript 中设置默认参数。

8.TypeScript 中 ES6 剩余参数的用法。

9.TypeScript 中的函数重载，其中两个或多个同名函数被重载，并且根据参数的数量或类型有选择地执行其中一个函数，从而导致不同的结果。

当 ES5 中有同名函数时，后面声明的函数会覆盖前面声明的函数，在 TS 中是这样写的。

上述方法的一个变体也可以写成如下形式。

# 类和继承

TypeSctipt 中的类和继承方法与 ES6 中的基本相同，编写如下。

1.  定义类别

2.继承类

3.与 C++一样，TypeScript 中的类修饰符主要如下。

**(1)公共**

可以在类内部、子类内部和类外部访问它。如果没有添加修饰符，默认情况下属性是公共的。

**(2)受保护**

它可以在类和子类内部访问，但不能在类外部访问。

**(三)私人**

它可以在类内部访问，但不能在子类或外部访问。

这一系列文章致力于通过简单的例子帮助您开始使用 TypeScript。如果你对我的文章感兴趣，可以关注我的 [Medium](https://hyhwell.medium.com/) 或者 [Twitter](https://twitter.com/Maxwell_hyh) 。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级达人集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)
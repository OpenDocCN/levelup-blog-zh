# 学习 Go: for 循环和“做指定次数的事情”模板

> 原文：<https://levelup.gitconnected.com/learning-go-for-loops-and-the-do-something-a-specified-number-of-times-template-4c3c16b7f383>

![](img/482a205d085d1748c57c491c4c2d707f.png)

由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在我的上一篇文章中，我讨论了如何使用 Go for 循环的一个版本来编写通用循环，该版本的工作方式类似于 while 循环在其他编程语言中的工作方式。在本文中，我将向您展示如何使用更特殊形式的 for 循环，就像其他语言中的 for 循环一样。

# 对于循环

在大多数编程语言中，当您想要执行某个任务特定次数时，会使用`for`循环。正如我在上一篇文章中演示的，您可以在 Go 中使用 for 编写一个更通用的循环，其工作方式类似于`while`循环。该表单的语法模板是:

*for(条件){
语句；
}*

这种形式的`for`循环可以在其他语言中使用`while`循环的情况下使用。for 循环的其他形式的语法模板如下:

*为初始化变量；条件；修改变量{
语句；
}*

注意，虽然在 Go 中我们通常不在语句末尾使用分号，但是在`for`循环中我们必须使用分号，因为在同一行中有多个语句。

下面是 Go 中一个简单的`for`循环的例子，它打印数字 1 到 10:

```
func main() {
  const stop = 10;
  for i:=1; i <= stop; i++ {
    fmt.Println(i)
  }
}
```

# 做某事指定次数模板

计算机编程中有许多问题需要程序将一组语句重复设定的次数。您可能需要编写一个程序来计算银行帐户 20 年的利息，或者编写一个程序来将用户的 10 个值存储到一个数组中。这些程序中的每一个都利用编程模板*做某件事情指定次数*。

该模板的伪代码如下:

*按设定次数执行以下操作:
执行一个或多个动作*

现在让我们用这个模板来解决一些问题。第一个问题将是我前面提到的利息计算问题。问题是这样的:假设一个储蓄账户有 1000 美元，每年只赚 1%的利息，没有额外的钱存入账户。20 年后账户余额会是多少？

以下是解决方案:

```
func main() {
  const rate = 1.01
  const numYears = 20
  balance := 1000.00
  for i:=1; i <= numYears; i++ {
    balance *= rate
  }
  fmt.Printf("After %d years, the balance is %.2f.",
             numYears, balance)
}
```

这里有一个更难的问题。编写一个程序，绘制一个直方图，显示三家零售店以 100 美元为单位的销售额。直方图将由星号组成。每家店的销售额是:store 1——2400 美元；store 2–3400；和 store 3–2200。使用 for 循环创建直方图。

以下是解决方案:

```
func main() {
  const store1 = 240
  const store2 = 3400
  const store3 = 2200
  var bar string
  const star = "*"
  for i:=1; i <= store1/100; i++ {
    bar += star
  }
  fmt.Println("Store1: " + bar)
  bar = ""
  for i:=1; i <= store2/100; i++ {
    bar += star
  }
  fmt.Println("Store2: " + bar)
  bar = ""
  for i:=1; i <= store3/100; i++ {
    bar += star
  }
  fmt.Println("Store3: " + bar)
}
```

# 开始循环

本文展示了在实现指定次数的*做某事*编程模板中`for` 循环的更多传统用法。Go 对`for` 循环的实现很有趣，因为它可以用来代替`while`循环，并且它与其他语言中实现`for`循环的方式有一些其他的语法差异。

在我看来，Go 的`for`循环 make 的特性要优于其他语言的`for`循环。我之前提到过这一点，但是只有一个循环结构可以让 Go 成为一种更容易使用的语言。诸如此类的约束不应该让您感到受限，而是应该让您不必决定使用哪种循环结构。如果你用 Go 编程，你的循环结构将会是一个`for` 循环，不管你试图解决什么问题。

感谢您阅读这篇文章，如果您有任何意见或建议，请发邮件给我。
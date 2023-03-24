# Scala 中的代数数据类型及其通用表示

> 原文：<https://levelup.gitconnected.com/algebraic-data-types-and-its-generic-presentation-in-scala-d2c1ce7740fe>

![](img/5e055853515bf5934285b9f8ec853cac.png)

编程的本质是以一种有用的方式编码、解码和操作数据。因此，基于不同的编程理念，有多种方法来表示数据。在函数式编程中，数据表示通常是代数数据类型。

今天，我想简单解释一下在 Scala 中表示 ADT 的两种方式。首先是`sealed trait`和`case class`。另一种在描述数据时不常讨论，但如果您想进行更一般化的编程，它是一般化数据表示的一种替代方式。

# 访问 ADT(代数数据类型)

代数数据类型(ADT)是函数式编程概念，对使用“与”和“或”的数据表示有一个好听的名字。

在 ADT 术语中，“ands”通常被命名为产品，“ors”是联产品。我们也可以说余积是和的类型。如果你有兴趣了解更多关于 ADT 的信息，可以看看我之前关于 ADT 的[帖子。](https://edward-huang.com/functional-programming/2019/12/30/what-is-an-adt-algebraic-data-types/)

让我们来看一个如何表示以下描述的数据的示例:

1.  支付方式可以是信用卡**或** Paypal
2.  信用卡有前四个**和后六个**
3.  Paypal 有一个用户名**和**密码

# 用 Case 类封装特征表示

在 Scala 中，我们将把乘积表示为`case class`，并将其协积为`sealed trait`，这意味着与`case class`的“与”和与`sealed trait`的“或”。

```
sealed trait PaymentMethod
case class CreditCard(name:String, expiryDate:Instant) extends PaymentMethod
case class Paypal(username:String, password:String) extends PaymentMethod
```

ADT 的优点是类型安全。这意味着，编译器完全知道数据类型是什么，使我们能够编写完整的、正确类型的方法，包括我们的类型(基本上更容易模式匹配):

```
def derivePaymentMethod(paymentMethod:Payment): String =  paymentMethod match {
  case CreditCard(name, expiryDate) => s"Creditcard name ${name}"
  case Paypal(username, password) => s"Paypal user $username"
}
```

# 替代数据编码

`sealed trait`和`case class`无疑是最方便的数据表示方式。一个是它更容易推理和理解，并且使编码数据类型安全。但是，在 Scala 中还有其他方法可以将数据编码到 ADT 中。在 Scala 标准库中，我们可以将乘积表示为*元组*，将余积表示为*或者*。

让我们举一个上面的例子:

```
type PaymentMethod2 = Either[CreditCard, Paypal]

type CreditCard = (String, Instant)

type Paypal = (String, String)

def derivePaymentMethod(paymentMethod: PaymentMethod): String = paymentMethod match {
  case Right((username,password)) => s"Paypal user $username"
  case Left((name, expiryDate)) => s"Creditcard name ${name}"
}
```

使用*元组*和*比第一个例子中的 *case 类*和 *sealed trait* 可读性差，但是两者具有相同的理想属性。*

*然而，*支付方式 2* 比*支付方式*更通用。任何使用一对`String`和`Instant`操作的代码也可以使用*信用卡*，反之亦然。*

# *结论*

*作为 scala 开发人员，我们更喜欢用 *case class* 和 *sealed trait* 在语义上表示数据，而不是泛型的*元组*和*或者*。但是，在某些情况下，泛型是可取的。例如，如果我们想将数据序列化为 HTML 组件，我们不关心字符串或 Paypal 对。我们把这两个数字写成 HTML 格式，然后就完事了。通用编程还有助于用很少的代码解决各种类型的问题，避免各种类型之间的重复。*

*了解更多关于[不成形的通用编程](https://books.underscore.io/shapeless-guide/shapeless-guide.html)。*

**原载于 https://edward-huang.com*[](https://edward-huang.com/scala/functional-programming/shapeless/2020/06/21/algebraic-data-types-and-its-generic-presentation-in-scala/)**。***
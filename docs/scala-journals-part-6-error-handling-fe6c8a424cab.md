# Scala 日志—错误处理

> 原文：<https://levelup.gitconnected.com/scala-journals-part-6-error-handling-fe6c8a424cab>

![](img/02fce2a6f75f6a95774b7855d3e8cebb.png)

我们来谈谈错误处理。在函数式编程中，我们不喜欢副作用。错误就是那些能够(并且将会)发生的事情！)走错了。如何用函数的方式处理 Scala 中的错误？

我在第一篇文章中提到，当我想到 Scala /函数式编程时，我会想到代数。对于你这样解方程，你的数学老师会怎么说:

```
x = 3 
y = 0 
x / y = error
```

…或者…

```
x = 3 
x / y = null
```

如果函数方式像代数一样，那么我们不能允许**空值**或**异常**。空值破坏了参照透明性规则，换句话说，它们破坏了函数的纯洁性。例外也是如此(在某种程度上)。让我们澄清一下。

# 什么是参照透明和纯函数？

我之前提到过，但是**纯函数**是这样一个函数:

*   它的返回值不是`Unit` -想想看:`Unit`强烈暗示我们正在做一些不纯的事情(比如`println`)也许？)
*   它的返回值只依赖于它的参数:
    `def add(x: Int, y: Int) = x + y`
*   该函数的返回值在任何时候都是相同的，没有例外:

```
def add(x: Int, y: Int) = x + y 
add(1, 4) // 5 
add(1, 4) // 5 again, this could never evaluate to anything else
```

*   这个函数的返回值可以和这个函数调用互换使用

```
def add(x: Int, y: Int) = x + y 
add(1, 5) == 6 // true at all times as add(1, 5) is the same as 6 at all times
```

**引用透明**指的是最后一点——在简化中，它意味着你总是可以用它的返回值替换一个函数调用。

# 没有空听起来很粗略

如果你曾经调试过`NullPointerException`,你将会体会到无空规则。如果不是，就认为 null 不是一个值——它表示没有值。而这种东西在代数中是不存在的。自从我开始函数式编程之旅以来，我只在使用一些 Java 库的环境中遇到过使用 null，这些库的*可能会返回 null。尽管如此，我的 Scala 代码不会使用 null。*

Scala 不像 Java 那样有检查异常的概念，在 Java 中编译器会提醒你(在一定程度上)你的错误处理——所以在 Scala 中，无论你告诉你的函数将返回什么，编译器都会假设你没有说谎。

# 为什么异常“在某种程度上”破坏了参照透明性？

例外应该根据它们的名字来使用——例外。当一个情况是一个真正的例外。被零除真的是例外吗？不会，如果你的递归函数意外炸栈，是真的异常吗？大概是的。(尽管这也意味着你的测试很丢人！)

这导致了(也许是有争议的)一种说法，即只有当异常被显式地观察和执行时，它们才会破坏参照透明性。这意味着—如果您编写显式抛出或捕获异常的代码，您就破坏了引用透明性。如果代码“自己”做到了——我个人认为没问题。异常将在真正异常的情况下抛出。这显然并不意味着我们完全忽略错误处理！让我们看看 Scala 提供了什么。

# 期权及其在现实世界中的应用

建议一个可选值——一个可能存在也可能不存在的值。如果它存在，它被包裹在`Some`中，如果它不存在，它就变成了`None`。这非常适合模式匹配。

让我们看看下面的基本用法——想象一下注册中的可选字段，比如“生日”。`calculateAge`函数获取出生日期并计算年龄。

```
def calculateAge(birthday: Option[Date]): Option[Int] =        
       birthday match {         
            case Some(bday) => Some(calculateAge(bday))         
            case None => None     
} private def calculateAge(bday: Date): Int = 21 
// everyone is 21 obviously ;) 
```

如果我们的客户添加了他们的出生日期，他们的年龄将被计算出来，并以`Some`的形式返回。如果客户错过了他们的出生日期，甚至不会调用`calculateAge`，他们的年龄将作为`None`返回。现在就看打电话的人处理了。

选项**被大量使用**。一些实际例子:

*   函数中的可选参数:

```
def optionalStrings(str: Option[String]) = ...
```

*   初始化时不存在的参数

```
case class Customer(name: String, age: Option[Int])  
// age can be updated/added later
```

*   当您不关心错误消息时

```
def divide(one: Int, two: Int): Option[Int] =    
      two match {     
          case t if t == 0 => None     
          case t => Some(one/two)    
       }
```

## 摘要

`Option` ==可能缺少数据或者我们不关心错误消息

# 以及它在现实世界中的用法

`Either`与选项非常相似，不同之处在于它允许检索错误消息。可能是`Left`也可能是`Right` - `Left`表示有问题(类似于`Option`中的`None`),`Right`表示一切正常(类似于`Some`表示`Option`)。

让我们看看上面使用`Either`实现的生日示例。

```
// type Age is only a type alias to make below Either more explicit and easier to understand 
type Age = Int // this will hold our error detail case class CalculationProblem(problem: String) def calculateAge(birthday: Option[Date]): Either[CalculationProblem, Age] =    
birthday match {         
    case Some(bday) => Right(calculateAge(bday))         
    case None => Left(CalculationProblem("Birthday date missing")    } private def calculateAge(bday: Date): Int = 21
```

`Either`用的很多，我会说和`Option`差不多。当然有很多更好的方法来实现`calculateAge`——这是毫无疑问的，但是让我们用它来想出一些在现实生活中如何以及何时使用`Either`的好主意。

*   当您想要获取错误消息并对其采取行动时，您可以将其打包在`Left`中。`Right`将代表幸福之路。
*   使用`Either`而不是抛出异常——你可以创建一个 case 类(或者整个层次结构，有点像异常)来保存你的错误信息，就像我上面做的那样(这也是一个非常常见的做法)
*   确保你的`Either`是可以理解的，并且在它出现的上下文中容易推理。这意味着有时创建类型或别名并使用`Either[DatabaseConnectionProblem, WriteSuccess]`而不是`Either[Problem, Unit]`更好——想想在你的代码所做的上下文中放置第一个`Either`和第二个`Either`有多容易或多难。

## 摘要

`Either` ==操作可能失败，我们关心错误信息

# Try 及其在现实世界中的用法

最后但并非最不重要的(虽然在我今天提到的所有三种类型中使用最少的)是`Try`。你可以认为`Try`是`Either`的一种特殊类型，其中`Left`不是`Left`而是`Throwable`。这意味着`Try`是处理抛出异常的代码的一个很好的选择(想想 Java 库)。构造函数自动“捕捉”应该抛出的异常，并将它们放在`Try`的`Left` - `Failure`的等效位置。快乐路径结果进入`Success`。

将`Try`和`calculateAge`一起使用有点大材小用，所以让我们再来看看分工:

```
def divide(one: Int, two: Int): Try[Int] = Try(one/two) 
divide(1, 0) // Failure(java.lang.ArithmeticException: / by zero) divide(1, 1) // Success(1)
```

…想想模式匹配处理这种情况会有多容易，这种方法可以避免出现异常:

没有灵丹妙药，但是一般来说`Try`比`Either`和`Option`用得少一点，原因很简单，我们一般不希望在 Scala 代码中看到异常。`Try`非常适合:

*   抛出异常的代码(例如 Java 库)

## 摘要

`Try` ==包装在`Try`中的代码可能会抛出异常

# 那么我应该抛出异常吗？

如上所述——只有在**真正**的例外情况下。在定义异常时，我是一个纯粹主义者(在可抛出的意义上)——如果你知道它会在你的代码中发生，它就不是异常，它只是一条不愉快的路径。由于在 Scala 代码中抛出异常对我来说是一件大事，我记得非常清楚，在我的职业生涯中，只有一次在我工作的 Scala 代码库中允许出现显式异常。

# 错误处理摘要

仔细想想，盲目地遵循参照透明性最终会让我们认为我们不能有任何 IO 操作/数据存储，甚至不能有随机数或时间/日期操作。那编程有什么用？

正如我在其他帖子中多次提到的，这完全取决于你想成为一个多么纯粹的人。在我的代码中，我遵循这些规则:

*   我不使用空值。永远不会。如果我不关心为什么某件事可能是`None`，我使用`Option`，如果我想知道失败的原因，我使用`Either`
*   我不抛出异常。我用`Either`代替。
*   我不尝试/捕捉可能抛出异常的代码(例如 Java 库)，而是将有问题的代码封装在`Try`中，并在`Success`或`Failure`上进行模式匹配——这是异常停止冒泡的地方
*   我认为异常和空值是邪恶的——我尽一切可能不使用它们，即使这意味着从我的高级同事那里寻求我可能不会使用的替代方法的帮助(还没有！)要意识到。

# (不那么)可怕的东西结束

还记得我在关于理解的文章中说过单子是可以平面映射的东西吗？我说:

> 因为理解对于处理和编写单子来说是很棒的。

猜猜是什么— `Try`、`Option`和`Either`(虽然要么只来自 Scala 2.12！)也是单子。这意味着你可以用它们来理解。

参考资料:
*Scala 食谱*作者 a .亚历山大
*Scala 中的编程:简体*作者 a .亚历山大
[https://www.garysieling.com/scaladoc/](https://www.garysieling.com/scaladoc/)
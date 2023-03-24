# 使用接收函数在 Go 中进行依赖注入

> 原文：<https://levelup.gitconnected.com/dependency-injection-in-go-using-receiver-functions-d76b7e541ecd>

## 在 golang 中实现依赖注入的简单而强大的技术

![](img/63773156eff78ad1cd04b4e7d300162b.png)

由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

依赖注入是分离代码的伟大技术，使它更加稳定、可测试和健壮。该技术包括设计您的代码来接收依赖项。在面向对象的语言中，我们通常使用类构造函数来实现。所以当有人创建你的对象的实例时，所有的依赖关系都会被注入其中。这非常有意义，因为依赖关系不应该是接口签名的一部分，否则所有的实现都必须接收依赖关系作为完成工作的参数。但是每个实现基于它们所做的事情可以有不同的依赖关系。你明白问题所在，对吧？

太棒了，所以在一个面向对象的世界里，你只需要设计你的代码来接收对构造函数的依赖，你就有了一个非常解耦的代码，可以用 mocks 等工具进行测试。那交易是什么？

Go 没有类，只有 structs，不支持构造函数。那么在这种情况下，我们如何使用依赖注入呢？

一种技术是利用接收器功能来实现。

## 接收器功能/方法

接收器函数也称为方法，是绑定到类型的一种特殊类型的函数。您可以识别它们，因为它们在关键字`func`之后、函数名之前包含接收者。例如:

```
type text stringfunc **(t text)** print() {
   fmt.Println(t)
}
```

当你想调用这个方法时，你可以调用`variable.method()`

```
func main() {
   var t text
   t = "a"
   t.print()
}
```

很简单，这看起来就像在 Java 和 C#等语言中调用方法一样。

所以如果我的函数有任何依赖关系，我可以创建一个包含所有依赖关系的结构，并通过这个结构注入它们。这样我就不必更改任何接口签名或类似的东西。这非常类似于通过构造函数注入依赖关系。在一些场景中，函数的签名必须非常具体，这种模式被证明非常有用。我将展示其中的两个。

## Web 服务器处理程序

处理函数必须是`func(ResponseWriter, *Request)`类型，所以我们不能传递更多的参数给它。这里有一个如何使用接收器函数注入这些内容的快速示例:

这是一个虚拟的例子，但是它展示了如何注入依赖来完成`HandleRequest`功能。我们只需要从我们的`dependencies`结构中填充`externalService`(第 25 行)。我们实际上可以为模拟`externalService`的`HandleRequests`函数编写单元测试，以准确返回我们测试所需的数据。

## AWS 函数

同样的模式也适用于 aws lambda 函数。根据 aws [文档](https://docs.aws.amazon.com/lambda/latest/dg/golang-handler.html)，函数的签名需要匹配以下之一:

*   `func ()`
*   `func () error`
*   `func (TIn), error`
*   `func () (TOut, error)`
*   `func (context.Context) error`
*   `func (context.Context, TIn) error`
*   `func (context.Context) (TOut, error)`
*   `func (context.Context, TIn) (TOut, error)`

为了从 SSM 获得一些配置，或者从 Dynamo DB 获取一些东西，以及许多其他情况，您经常需要在 lambda 中与其他 AWS 解决方案进行交互。你可以像上面的例子一样注入它们。AWS 为您可能需要的服务提供了接口。这意味着，如果你依赖于它们而不是它们的实际实现，你可以很容易地出于测试目的而模仿它们。利用这一点。值了。让我们创建一个例子来描述 Lambda 函数中 DynamoDB 上的一个表。

这里我们可以看到与之前完全相同的模式。这一次我们创建一个 lambda，它为 **DynamoDB** 中的一个表名返回 **ARN** (亚马逊资源名)。重要的是我们在`main`函数上注入了`DynamoDB`服务。在我们的`dependencies`结构上，我们也依赖于一个接口，在本例中是由 AWS 提供的，但是我们实际上可以创建自己的接口，只定义我们正在使用的方法。这样我们可以很容易地为`HandleRequest`函数编写单元测试。这是另一个要点。

现在我们正在创建`mockDynamoDBAPI`结构(在第 12 行)并且只实现我们想要模仿的方法`DescribeTable`(在第 16 行)。然后我们可以创建一个简单的测试用例，在`DescribeTable`方法返回错误时测试`HandleRequest`函数。我们都完成了！

## 结论

如果使用得当，依赖注入是一项伟大的技术。在 go 中实现它的方式与面向对象语言略有不同，但这仍然是可能的，而且非常简单。
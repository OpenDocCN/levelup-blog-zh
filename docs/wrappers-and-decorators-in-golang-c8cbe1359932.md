# Golang 的包装工和装饰工

> 原文：<https://levelup.gitconnected.com/wrappers-and-decorators-in-golang-c8cbe1359932>

编写可重用和可理解的代码

![](img/6ff26219504c258af36b35a6761594b1.png)

照片由 [Didssph](https://unsplash.com/@didsss?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

包装函数是维护程序单一责任和分离的一种非常简单快捷的方法。最基本的函数包装形式是将您想要包装的函数作为参数传递给另一个函数。

```
package mainimport (
 "fmt"
)func main() {
 print(1, 2, sum)
}func sum(a, b int) int {
 return a + b
}func print(a, b int, f func(int, int) int) {
 fmt.Printf("inputs: %d, %d\n", a, b)
 res := f(a, b)
 fmt.Printf("result: %d\n", res)
}
```

这是一个非常愚蠢的例子，但它说明了我们在这里试图实现的想法。在这个特定的例子中，我们打印内部函数的参数和结果。如果我们需要为不同的函数做同样的事情，比如说`multiply`，我们可以通过创建新函数并以同样的方式调用它来实现:

```
func multiply(a, b int) int {
 return a * b
}
```

然后就用`print(1, 2, multiply)`打电话。

另一个选择是使用**闭包**来实现类似的功能。闭包函数是在另一个函数的作用域内创建的函数。通常使用这种技术是为了让内部函数可以重用外部函数中的变量和上下文。让我们重构原始代码，使用相同的输入调用多个操作，使用闭包实现减法的新函数:

```
package mainimport (
 "fmt"
)func main() {
 a := 1
 b := 2
 fmt.Println("Sum:")
 print(a, b, sum) fmt.Println("\nMultiply:")
 print(a, b, multiply) msg:="variable from outer scope"
 s := func(x, y int) int {
  fmt.Println(msg)
  return x - y
 } fmt.Println("\nSubtract:")
 print(a, b, s)
}func sum(a, b int) int {
 return a + b
}func multiply(a, b int) int {
 return a * b
}func print(a, b int, f func(int, int) int) {
 fmt.Printf("inputs: %d, %d\n", a, b)
 res := f(a, b)
 fmt.Printf("result: %d\n", res)
}
```

# 依赖注入

这项技术的另一个很好的用例是依赖注入。我们经常需要使用带有固定签名的函数作为参数或字段。如果我们需要接收一个额外的依赖项，我们可以将我们想要执行的函数包装在另一个函数中，该函数将依赖项作为参数接收，返回一个带有我们需要的确切签名的函数。这种模式的一个很好的例子可以在人们如何为 HTTP 服务器创建中间件中找到。在最基本的形式中，您可以包装用于处理附加行为请求的函数。[这里有一个例子](https://gowebexamples.com/basic-middleware/)。当我们需要将依赖项添加到函数中，但我们仍然需要返回`http.Handler`类型(通常与`[http.HanlderFunc](https://pkg.go.dev/net/http#HandlerFunc)`结合)时，这很有用

处理依赖注入的另一种方法可以在我不久前写的这篇文章中找到。

包装函数非常有用和简单。然而，在一长串包装函数中导航可能会很棘手。或者了解它们在不同的包中是如何被使用的。解决这个问题的另一种方法是创建接口并使用装饰模式。

# 装饰图案

装饰模式允许在运行时向对象添加功能。它最初是在 [*设计模式中引入的:可重用的面向对象软件*](https://en.wikipedia.org/wiki/Design_Patterns) *的元素。*

这种模式的经典实现是有一个接口，公开您想要调用的方法和多个实现，通常一个实现用于实际行为(通常称为装饰)，多个实现用于任何附加行为(在装饰行为之前或之后调用)，称为装饰者。装饰者依赖于他们正在实现的接口，这样我们可以安全地将行为添加到我们正在装饰的接口的任何实现中。这允许程序组成装饰链，根据装饰的顺序改变行为。

我创建了这个 repo[来实现一些基本的装饰模式。最简单的例子就是`runner`接口。有三种类型实现该接口，`defaultRunner`包含获取数据的实际代码(这是修饰的)，以及两个用于选择输出策略的修饰器(互斥的，这意味着我们只能使用一个来修饰):`dryRunner`将输出设置为`stdout`和`fileRunner`，将输出设置为文件。](https://github.com/RicardoLinck/decorators)

从类型定义中，我们可以看到装饰器包含一个我们正在装饰的接口的字段。在下一个要点中，我们可以看到我们如何与之互动:

这只是 decorator 模式的一个简单例子，即使这两个 decorator 不能组合，你也可以编写新的 decorator，在它的基础上增加更多的功能，比如一个记录执行时间的日志 decorator，它不会关心使用的是哪种输出策略。我想说明这种模式也可以和其他模式一起使用，比如 Strategy 模式，并且仍然为解耦提供了很好的价值。

在`[cache/cache.go](https://github.com/RicardoLinck/decorators/blob/main/cache/cache.go)`中实现了另一个例子，这是一个内存缓存的基本实现，用于我们从远程服务获取的键。有趣的是，接口是在这个包中定义的，被修饰的实现驻留在`[service/service.go](https://github.com/RicardoLinck/decorators/blob/main/service/service.go)`中，完全不知道它正在被修饰，这要感谢 golang 的[隐式接口实现系统](https://go.dev/tour/methods/10)。这给这种模式带来了更多的灵活性，因为您可以在不同的包中定义您想要装饰的接口，而不是您正在装饰的功能的实际实现。
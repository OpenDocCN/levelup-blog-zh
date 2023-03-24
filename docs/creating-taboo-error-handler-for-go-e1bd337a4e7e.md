# 为 Go 创建禁忌错误处理程序

> 原文：<https://levelup.gitconnected.com/creating-taboo-error-handler-for-go-e1bd337a4e7e>

![](img/e8e4c74f5e5a5317e8af65b0d9e771f0.png)

*Unsplash 上@hhh13 的照片*

我为 Golang 创建了[这个模块](https://github.com/anon-org/taboo)来帮助我处理错误。当我的一个同事考虑将`context`从处理程序传递给服务，传递给存储库以更详细地跟踪错误日志时，这个想法就出现了。我不同意他的观点，因为我认为那不是`context`的目的。也许是我错了，或者是他错了，或者是我们都错了，因为这是我们第一个投入生产的 Golang 项目。

尽管我们对`context`有不同的看法，但我们都认为 Golang 的错误处理过于冗长和庞大。它让我们阅读更多的错误处理，而不是系统流程本身。然后我记得当我使用 Java/Kotlin 编码时，我总是使用`throws`、`throw`和`try-catch block`来处理任何错误。

嗯…

我为什么不为 Golang 创作呢？

然后我为 Golang 创建了这个名为`taboo`的`try-catch block`模块。因为我知道这个东西在 Golang 开发者中引起争论，但是，那我想我为什么不试一试呢？

对于设计本身，我的灵感来自于[这篇文章](https://hackthology.com/exceptions-for-go-as-a-library.html)，但整个实现都是根据我当前的需求进行调整的。这个模块不是基于`error`，而是基于`panic`和`recover`，所以只要在错误的条件下使用，就会非常危险。

让我们举个例子:

```
package mainfunc div(a, b int) int {
  return a / b
}func main() {
  div(10, 0)
}
```

当`b`被`zero`填满时，会引起恐慌

```
panic: runtime error: integer divide by zerogoroutine 1 [running]:
main.div(...)
        /tmp/anon-org/taboo/cmd/example.go:4
main.main()
        /tmp/anon-org/taboo/cmd/example.go:8 +0x12Process finished with exit code 2
```

因此，我们可以像这样使用`taboo`来处理它:

```
taboo.Try(func() {
  div(10, 0)  
}).Catch(func(e *taboo.Exception) {
  fmt.Println(e.Error())
}).Do()
```

`taboo`将捕获`panic`并尝试`recover`它，并生成一个名为`taboo.Exception`的错误堆栈，以便更详细地跟踪错误。所以程序是这样结束的:

```
main.div:9 runtime error: integer divide by zeroProcess finished with exit code 0
```

很方便吧？

那么，如果我想将错误抛出或再次抛出给第一个调用者呢？

```
package mainimport (
  "errors"
  "fmt"
  "github.com/anon-org/taboo/pkg/taboo"
)func div(a, b int) int {
  if b == 0 {
    taboo.Throw(errors.New("division by zero detected"))
  }
  return a / b
}func callDiv() int {
  var result inttaboo.Try(func() {
    result = div(10, 0)
  }).Catch(func(e *taboo.Exception) {
    e.Throw("callDiv rethrow this error")
  }).Do()return result
}func callCallDiv() int {
  var result inttaboo.Try(func() {
    result = callDiv()
  }).Catch(func(e *taboo.Exception) {
    e.Throw("callCallDiv rethrow this error")
  }).Do()return result
}func main() {
  taboo.Try(func() {
    callCallDiv()
  }).Catch(func(e *taboo.Exception) {
    fmt.Println(e.Error())
  }).Do()
}
```

`e.Throw(message)`将包装上一个异常，并再次抛出给上一个调用者。所以打印出来的错误会是这样的:

```
main.callCallDiv:34 callCallDiv rethrow this error caused by:
  main.callDiv:22 callDiv rethrow this error caused by:
    main.div:11 division by zero detected

Process finished with exit code 0
```

我认为它就像 Java/Kotlin 中的`try-catch block`,但是有很多缺陷。这个模块仍然是一个实验，我自己不打算在生产中使用这个模块。或者也许我应该？

*最初发布于*[*https://clavinjune . dev*](https://clavinjune.dev/en/blogs/creating-taboo-error-handler-for-go/)*。*
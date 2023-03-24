# 在围棋中打造自己的未来

> 原文：<https://levelup.gitconnected.com/build-your-own-future-in-go-f66c568e9a7a>

![](img/0da3a697e4995dd334060be5c842e2ed.png)

照片由[g 摄影](https://unsplash.com/@gdtography?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Go 编程语言的一个主要特点就是它的同名`go`语句。不过，在我看来，`go`语句也是它的主要缺点之一。不仅仅是我。

与表达式不同，语句不产生任何结果。在围棋中，开始一个新的 goroutine 超级容易。但是你如何得到它的结果呢？你怎么知道它是否出错了呢？你如何等待它完成？如果你不再需要它的结果，你如何取消它？

熟悉围棋的人会说:嗯，很明显，用渠道。但是围棋中的通道仍然是一个低层次的构造。首先，要有一个产生结果或错误的 goroutine，并且也是可取消的，你需要三个。你可能认为`Context`有助于满足第三个要求。但是`Context`还是暴露了渠道:`Done()`只是`<-chan struct{}`

那有什么问题？频道越多，问题就越多。通道可能会死锁。渠道会恐慌。甚至在开始编写业务逻辑之前，您就已经有了所有需要处理的底层问题。

而且只写一次是不够的。你必须一遍又一遍地重复它。因为最有可能的是，两个不同的 goroutines 会返回两种不同类型的结果。也就是说，两种不同的频道类型。这意味着，如果没有泛型，你要么多次复制你的代码，要么求助于使用`interface{}`和运行时强制转换，这完全打破了类型安全的概念。您必须在两个糟糕的解决方案之间做出选择，因为`go`是一个语句，而不是一个表达式。

在 Go 语言引入泛型作为实验性特性之前，情况确实如此。我已经简要地写过一次了，这一次，我想展示一下泛型是如何帮助我们解决围棋设计中的一个最大的缺陷。

我们将实现一个延迟值设计模式，在不同的语言和框架中，它被称为 Future、Promise 和一堆其他的名字。我会把它叫做未来，就像 Java 那样。

延期值不是急，就是懒。这意味着它们要么一创建就开始执行，要么只有在某些事情触发它们时才执行。既然陈述的本质是急切的，我会赞成急切的执行。

我们的未来将有以下方法:

*   `Get()`阻塞当前的 goroutine，直到获得未来的结果
*   `Cancel()`那停止了我们未来的执行

用 Go 术语来说，它将是一个具有两种方法的接口:

```
type Future[type T] interface {
   Get() Result[T]
   Cancel()
}
```

*注意，我将使用方括号来表示泛型类型。他们没有在提案中记载，但是*[*go2 playground*](https://go2goplay.golang.org)*支持他们，事实我是从* [*这篇文章*](https://medium.com/capital-one-tech/generics-are-the-generics-of-go-3e0ef0cb9e04) *中得知的。我发现这种类似 Scala 的语法比圆括号更容易混淆。*

`Result`是另一个接口，它包装了`S`类型的`Success`，或者是`Failure`:

```
type Result[type S] interface {
   Success() S
   Failure() error
}
```

为了支持结果，我们需要一个结构来保存它的数据:

```
type result[type S] struct {
   success S
   failure error
}
```

看看这个结构，实现这两个结果接口方法应该很简单:

```
func (this *result(S)) Success() S {
   return this.success
}

func (this *result(S)) Failure() error {
   return this.failure
}
```

我们可以简单地将结构本身公开，避免使用接口，节省几行代码，但是接口提供了更干净的 API。

将来，打印结果的内容也很方便，因此我们将为此实现 Stringer 接口:

```
func (this *result(S)) String() string {
   if this.failure != nil {
      return fmt.Sprintf("%v", this.failure)
   } else {
      return fmt.Sprintf("%v", this.success)
   }
}
```

到目前为止，应该很简单。现在让我们讨论一下支持`Future`的结构需要什么数据。

具有以下结构:

```
type future[type T] struct {
    ...
}
```

关于`Future`的状态我们需要知道什么？

首先，我们想把结果保存在某个地方:

```
type future[type T] struct {
   result   *result[T]
   ...
}
```

知道未来是否已经完成也是有用的:

```
type future[type T] struct {
   ...
   completed bool
   ...
}
```

如果它还没有完成，我们需要一个等待它的方法。在 Go 中，一种常见方法是使用通道:

```
type future[type T] struct {
   ...
   wait     chan bool
   ...
}
```

我们最后的要求是能够取消`Future`。为此，我们将使用`Context`，它返回一个函数，我们需要调用它来取消它:

```
type future[type T] struct {
   ...
   cancel   func()
}
```

但是引用`Context`本身也是有用的:

```
type future[type T] struct {
   ...
   ctx      context.Context
   cancel   func()
}
```

就这样，这就是我们的`Future`现在需要的所有数据。

```
type future[type T] struct {
   result   *result[T]
   complete bool
   wait     chan bool
   ctx      context.Context
   cancel   func()
}
```

现在让我们实现这两个`Future`方法。

由于我们正在使用`Context`，取消`Future`变得微不足道:

```
func (this *future[T]) Cancel() {
   this.cancel()
}
```

现在让我们讨论一下我们的`Get()`应该处理什么情况。

1.  `Future`已经完成了它的工作。那么我们应该简单地返回结果，不管是成功还是失败
2.  `Future`还没有完成它的工作。然后我们应该等待，阻塞调用的 goroutine，当结果准备好了，我们应该返回它
3.  在此期间`Future`被取消了。我们应该返回一个错误，指出

映射这三种情况后，我们得出以下方法:

已经完成的未来的例子很简单。我们只是返回缓存的结果。

如果它还没有完成，我们使用`wait`通道来等待它。

也可能有这样一种情况，我们的未来被取消了上下文。检查`ctx.Done()`频道就知道了。

这就是实现处理结果的不同用例。

接下来，让我们看看我们如何构建我们的未来。

我们的未来需要执行任意一段代码。代码本身可能返回泛型类型的结果或错误。我们的构造函数将简单地返回相同泛型类型的 Future。

```
func NewFuture[type **T**](f func() (**T**, error)) Future[**T**] {
    ...
}
```

请注意泛型是如何允许我们定义输入和输出类型之间的强大关系的。我们的`Future`保证返回与我们提供给构造函数的任意函数相同的类型。不再需要使用`interface{}`和不安全施法。

接下来，我们要初始化我们的`Future`:

```
fut := &future[T]{
   wait: make(chan bool),
}
fut.ctx, fut.cancel = context.WithCancel(context.Background())
...

return fut
```

为了使我们的`Future`可取消，我们创建了一个`Context`和一个通道，这样我们可以等待它以并发方式完成。

*你可能要考虑把* `*Context*` *传递给* `*Future*` *的构造函数，而不是自己创建。为了示例的简洁，我省略了这一点。*

最后，我们需要对我们推迟的任意一段代码做一些事情:

```
go func() {
   success, failure := f()

   fut.result = &result[T]{success, failure}
   fut.completed = true
   fut.wait <- true
   close(fut.wait)
}()
```

这里我们在一个新的 goroutine 中执行函数，获取它的结果，并将我们的`Future`标记为完成。

通道应该只使用一次，所以关闭它是个好主意。

*根据您的用例，您可能想要考虑使用一个工人池，而不是为每一个未来生成 goroutine。*

现在让我们看看它是如何工作的。

首先，我们希望看到我们的未来能够回报一个结果:

```
f1 := NewFuture(func() (string, error) {
   time.Sleep(1000)
   return "F1", nil
})

fmt.Printf("ready with %v \n", f1.Get()) 
// Need to wait...
// ready with F1
```

到目前为止，看起来不错。

但是，如果我们再次尝试得到结果呢？

```
fmt.Printf("trying again with %v \n", f1.Get()) 
// trying again with F1
```

注意，它现在不打印“需要等待”，因为结果已经被记忆。

如果函数返回一个错误，我们的未来会怎样？

```
f2 := NewFuture(func() (string, error) {
   time.Sleep(1000)
   return "F2", fmt.Errorf("something went wrong")
})

fmt.Printf("ready with %v \n", f2.Get())
// Need to wait...
// ready with something went wrong
```

很好，看起来错误也能被正确处理。

最后，取消怎么办？

```
f3 := NewFuture(func() (string, error) {
   time.Sleep(100)
   fmt.Println("I'm done!")
   return "F3", nil
})
f3.Cancel()

fmt.Printf("ready with %v \n", f3.Get())
// Need to wait...
// ready with context canceled
```

注意“我完成了！”是永远不会被打印出来的，因为我们丢弃了这个未来的结果。

# 结论

即将到来的泛型可能有助于解决`go`作为一个声明，而不是一个表达式所引起的许多问题。

多亏了它们，我们可以像许多其他语言一样，使用延迟值作为并发原语。这意味着我们现在可以:

*   轻松访问 goroutine 结果和错误
*   编写类型安全的代码，仍然是可重用的和通用的
*   停止搞乱低级并发原语，如通道
*   可以完全停止使用`go`语句

## 脚注

完整的代码示例可以在这里找到:【https://github.com/AlexeySoshin/go2future 
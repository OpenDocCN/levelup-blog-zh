# Golang 2.0 草图特征-错误处理

> 原文：<https://levelup.gitconnected.com/golang-2-0-draft-feature-error-handling-c0a2332b9162>

Golang 目前是 1.17 版本(2022 年 1 月)。尽管还不清楚 Golang 2.0 将于何时发布，以及它将包含哪些功能，但有几个有趣的草案，很有可能至少其中一些草案功能将成为 Golang 2.0 的一部分。在这个系列中，我将总结 Golang 2.0 中的草稿特性，我们将从错误处理开始。

![](img/c2c6dcc82b8090da60722e735879f179.png)

由 [Sarah Kilian](https://unsplash.com/@rojekilian?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/error?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 错误处理

Golang 中的错误处理是讨论最多的话题之一。现在 Golang 强迫你——这很好——在调用任何产生书面结果的函数后检查错误

```
if (err != nil) {
    return err // or do something else
}
```

在我看来，Golang 有这样一个显式的错误检查是件好事，因为它能让你意识到错误的处理。然而，这也导致了重复编写相同的代码，这是不理想的。

Golang 2.0 草案通过引入一个新的关键字`check`和`handler`函数来解决这个问题。不是显式地检查每个错误，而是在函数前面添加关键字`check`,表明这些返回值应该通过处理函数来检查。一个小例子看起来像这样

```
func foobar(a, b int) error {
    handle err { return err } // This is our handler x := check foo(a, b) // check invokes the handle func
    y := check bar(a, b) // this on as well if (x > y) {
        return fmt.Errorf("This is an error")
    }
}
```

让我们一行一行地过一遍。首先，我们定义函数 foobar，它将 a 和 b 作为输入，并返回一个错误。在下一行中，我们定义了由关键字`handle`指示的错误处理函数。处理函数接受 error 类型的参数，并具有与封闭函数相同的返回签名。因此，当 foobar 只返回一个错误时，处理程序只能返回一个错误。如果 foobar 将返回`(int, error)`，处理函数将需要返回例如`0,err`。

关键字`check`表示该函数应该使用预定义的错误处理函数进行检查。所以与其写作

```
x, err := foo(a, b)
if (err != nil) {
    return err
}
```

我们只写`x := check foo(a, b)`。这执行相同的代码流，但不会产生任何代码开销。

处理程序的一个好处是你可以链接处理程序。让我们看看下面的代码。

```
func foobar(a, b int) error {
    handle err { return err } // This is our handler x := check foo(a, b) // check invokes the first handle func
    y := check bar(a, b) // this on as well if (x > y) {
        handle err { err = fmt.Errorf("doSomething with %x and %y, x, y) } // second handler
        check doSomething(x, y) // invokes second handler and then the first
    }
    check doSomething(y, x) // invokes first handler
    return nil}
```

如上面的代码所示，如果我们定义了多个处理函数，它们将以相反的顺序执行。请注意，处理函数只对定义它们的作用域有效。所以在`if`块中定义的处理函数将只在作用域内被调用。

错误处理功能提案看起来很有前途。尽管我现在很喜欢 Golang 的显式错误处理，但我们可以减少代码开销，尤其是在大型项目中，这很好。我仍然不确定这是否会带来更多的复杂性而不是可读性，但我期待着测试它。

如果您对更多 Golang 文章感兴趣，请查看以下内容

[https://it next . io/the-power-of-golang-keyword-defer-b 31 bde cf 10b 6](https://itnext.io/the-power-of-golang-keyword-defer-b31bdecf10b6)

# 资源

[https://go . Google source . com/proposal/+/master/design/go 2d draft . MD](https://go.googlesource.com/proposal/+/master/design/go2draft.md)
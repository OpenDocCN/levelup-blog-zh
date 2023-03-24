# Golang 错误检查—以编程方式处理错误

> 原文：<https://levelup.gitconnected.com/golang-error-inspection-handle-errors-programmatically-67595035d66>

![](img/a2b5cc0ed1684dd7c363460a493f983d.png)

Photo by [傅甬 华](https://unsplash.com/@hhh13?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/error?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) (This is just to catch your attention)

Golang 目前处于 1.19 版本(2022 年 9 月)——该树已经重新开放用于 Go 1.20 开发。也就是说，Golang 2.0 离发布还很遥远。然而，对于 Golang 2.0，有一些有趣的设计草案目前正在社区中讨论。我已经写了一篇关于“错误处理草案设计”的小文章——如果你对 Golang 2.0 *可能*的发展方向感兴趣，也可以随意看看这篇文章。

[](/golang-2-0-draft-feature-error-handling-c0a2332b9162) [## Golang 2.0 草图特征-错误处理

### Golang 目前的版本是 1.17。本文介绍了 Golang 2.0 的错误处理草稿特性。

levelup.gitconnected.com](/golang-2-0-draft-feature-error-handling-c0a2332b9162) 

这篇文章是关于 Golang 2.0 草案的一个特性，这个特性很久以前就已经被合并到 Golang 树中了:错误检查。在 Go 1.13 中，Golang 增加了改进的错误检查功能。

## 错误检查

与*错误处理*相反，错误检查是以编程方式测试错误并对错误做出反应的能力。特别是对于较大的代码库来说，这一点非常重要——我刚刚参与了一个大型代码库的单元测试编写工作，这个代码库在 Go 1.13 之前已经开发了好几年了——因此涉及到了大量的遗留错误处理和检查代码。目前，有四种不同的方法来检查错误:

**使用** `**errors.Is**`
检查特定的标记错误当您定义自定义错误时，您可以使用`errors.Is`检查包装错误链中的任何错误是否与目标匹配。

```
var MyCustomError = errors.new("Bad Input")[...]err := someFunctionThatReturnsAnError()
if errors.Is(err, MyCustomError) {
    fmt.Println("Bad Input Error")
}
```

`errors.Is`是 Go 1.13 中增加的功能之一，用于改进错误检查。它遍历包装器错误链，并检查是否有任何错误与给定的目标匹配。如果您正在检查非常具体的错误，例如，如果您正在编写单元测试并且确实想要引发一个具体的错误，那么应该使用这种方法。然而接下来我最喜欢的是。

**使用** `**errors.As**`检查错误类型与`Is()`相反，`As(err error, target interface{}) bool`检查包装器错误链中的任何错误是否具有*特定类型*而不是特定错误。

```
type CustomError struct {
    msg string
}var MyCustomError = CustomError{msg: "Bad Input"}[...]
err := someFunctionThatReturnsAnError()
if errors.As(err, &CustomError) {
    fmt.Println("%s\n", err.msg)
}
```

使用`errors.As`,您可以检查更大范围的错误。显然，为了更大的范围，你牺牲了准确性——但是我发现大多数时候这是可以的。

**使用类似** `**os.IsNotExist**`的检查另一种检查错误的方式是使用类似`os.IsNotExist`的功能进行特别检查。这些特别的功能用于检查特定的错误和类型，例如`os.IsNotExist`检查“ErrNotExist 以及一些系统调用错误”。这已经很模糊了，再加上文件已经说这是在`errors.Is`之前——所以**不应该再用**了。

**在** `**err.Error()**`
报告的错误文本中进行子串搜索。如果您没有定制的错误类型，这可能是广泛使用的方法，因此`errors.As`不是一个选项——或者您需要支持许多遗留代码(并且不想重写它们)——或者只是没有预先考虑错误检查(并且也不想重写它)。然而，这很可能也是最不精确的检查错误的方法，应该只是你的最后一道防线。

## 资源

[https://go . Google source . com/proposal/+/master/design/go2 draft . MD](https://go.googlesource.com/proposal/+/master/design/go2draft.md)
# Golang 中的错误处理风格指南

> 原文：<https://levelup.gitconnected.com/error-handling-style-guide-in-golang-9761a5a1434d>

错误处理是 Golang 中的一项基本技术。这篇文章将讨论 Golang 中的错误处理代码风格指南。

![](img/97ecaed891ede41788a09eaa290ad420.png)

马克·弗莱彻·布朗在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 声明错误

错误内置接口类型是表示错误条件的常规接口，零值表示没有错误。

```
type error interface {
    Error() string
}
```

声明错误的选项很少。

## `**error.New**`

函数`New`返回一个格式为给定文本的错误。即使文本完全相同，对它的每次调用都会返回不同的错误值。

```
func bar() error {
    return errors.New("error: an error occurred")
}
```

## fmt。错误 f

函数`Errorf`根据格式说明符格式化，并返回满足`error`的字符串值。

```
func foo() error {
    return fmt.Errorf("foo: %s", "an error occurred")
}
```

如果格式说明符包含带有错误操作数的%w 动词，则返回的错误将实现返回操作数的 **Unwrap** 方法。

```
func foo() error {
   return fmt.Errorf("foo: %w", errors.New("an error occurred"))
}
```

## 自定义错误类型

有时候，我们需要创建一个自定义的`error`来处理错误。

```
type NotFoundError struct {
  File string
}func (e *NotFoundError) Error() string {
  return fmt.Sprintf("file %q not found", e.File)
}
```

# 风格指南

在为您的用例选择最佳错误选项之前，请考虑之后的[。](https://github.com/uber-go/guide/blob/master/style.md#errors)

*   调用者需要匹配错误以便处理它吗？如果是，我们必须通过声明顶级错误变量或自定义类型来支持`[errors.Is](https://golang.org/pkg/errors/#Is)`或`[errors.As](https://golang.org/pkg/errors/#As)`函数。
*   错误消息是静态字符串，还是需要上下文信息的动态字符串？对于前者，我们可以使用`[errors.New](https://golang.org/pkg/errors/#New)`，但对于后者，我们必须使用`[fmt.Errorf](https://golang.org/pkg/fmt/#Errorf)`或自定义错误类型。
*   我们是在传播下游函数返回的新错误吗？如果是这样，请使用错误包装。

```
+-----------------+---------------+-------------------------------+
| Error matching? | Error Message |           Guidance            |
+-----------------+---------------+-------------------------------+
| No              | static        | errors.New                    |
| No              | dynamic       | fmt.Errorf                    |
| Yes             | static        | top-level var with errors.New |
| Yes             | dynamic       | custom error type             |
+-----------------+---------------+-------------------------------+
```

发现这篇文章很有帮助👏？看看我下面的其他文章吧！

[](/error-handling-functions-in-golang-159ae73164b3) [## Golang 中的错误处理函数

### 在本文中，我将演示在 Go 中处理错误函数的基础知识以及如何使用它们。

levelup.gitconnected.com](/error-handling-functions-in-golang-159ae73164b3) [](/error-handling-strategies-in-go-7759b3f73d0) [## 围棋中的错误处理策略

### 在 Go 中，当函数调用返回错误时，调用者有责任检查错误并采取适当的措施…

levelup.gitconnected.com](/error-handling-strategies-in-go-7759b3f73d0) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)
# Python 中完全类型安全的错误处理

> 原文：<https://levelup.gitconnected.com/completely-type-safe-error-handling-in-python-3307c5d4c096>

![](img/49e4aecb8dcb9e9881200ba3c20dd67a.png)

在我关于使用 [pfun](https://pfun.dev/) 进行 Python 函数式编程的系列文章中，我们将看看 Python 的类型系统，以及如何使用它使使用`pfun`的错误处理完全类型安全。在本系列的[上一篇文章中，我们介绍了`pfun.effect.Effect`类型，并讨论了它如何模拟成功计算(通过称为*的`A`类型参数模拟成功类型*)和错误(通过称为*的`E`类型参数模拟错误类型*)。](https://dev.to/suned/purely-functional-python-with-static-types-41mf)

回想一下`Effect`代表的功能是:

*   只取一个类型为`R`的参数
*   返回类型为`A`的结果或引发类型为`E`的错误
*   可能会也可能不会产生副作用

让我们用一个小例子来研究这种抽象如何实现完全类型安全的错误处理。为了理解`Effect`如何启用这个特性，让我们重新检查一下将`Effect`与返回新的`Effect`的函数链接在一起的方法`and_then`:

我们已经省略了实现，因为它不是特别重要(如果你愿意，你可以在[之前的文章](https://dev.to/suned/purely-functional-python-with-static-types-41mf)中看到细节)。*重要的是`and_then`的*签名*。要了解`and_then`的类型如何启用类型安全错误处理，请考虑以下代码:*

当在最后一行中调用`last`时，它调用`first`，这可能会以类型`E`的值失败。如果`first`成功，`last`调用`f`，结果为`first`。`f`返回`second`，也被`last`调用。但是`second`可能会因类型为`E2`的值而失败，这最终意味着`last`可能会以两种方式失败:

因此，`last`的正确错误类型必须是`Union[E, E2]`，这就是你将在`and_then`的签名中发现的。

在`and_then`的返回类型中使用`Union`意味着通过将效果与简单错误类型*自动组合*来构建复杂错误类型。这是非常有用的，因为它允许我们在类型检查器(如 [MyPy](https://mypy.readthedocs.io/en/stable/) )的帮助下推理组合效果的复杂错误，而不需要我们做任何特别的努力:我们可以简单地用简单的错误类型描述效果，将它们与`and_then`(或接受多种效果的`pfun.effect` api 中的任何其他函数)组合，并让类型检查器做复杂的工作，跟踪当调用`Effect`时可能会引发哪些错误。

如果我们不能通过类型安全处理错误类型来消除它们，那么自动跟踪错误类型就不是特别有用。`pfun.effect.Effect`提供了[几种处理错误](https://pfun.dev/effectful_but_side_effect_free/#error-handling)的方式。我们将通过下面的例子来研究其中一个:

假设您想使用`pfun`调用一个 HTTP api，并将结果解析为函数式的 JSON。要发出 HTTP 请求，您可以使用:

(当然，您不必键入`call_api`的类型，因为它可以由`MyPy`推断出来，我们在这里这样做只是因为查看类型是有益的)。

因为不是所有的字节串都是有效的 JSON 数据，所以让我们编写一个函数`parse_json`，将 JSON 数据解析为一个`Effect`。我们可以使用`effect.success`函数创建一个除了返回参数之外什么也不做的效果，使用`effect.error`函数创建一个除了参数失败之外什么也不做的效果:

或者简单地使用`effect.catch`装饰器:

有了`parse_json`在手，我们的整个程序看起来像:

现在，假设我们想要确保在`program`被赋值时所有的错误都被处理。我们用来消除误差的函数叫做`[Effect.recover](https://pfun.dev/effect_api/#pfun.effect.Effect.recover)`。这个函数允许我们传入一个函数来检查错误并返回一个新的效果(它可能会以新的方式失败)。

让我们通过操作`HTTPException`来演示一下。这可以通过调用备份 api 来完成。在本例中，我们将简单地想象我们可以使用默认的备份响应

`typing.NoReturn`是一种特殊类型，表示`handle_http_error`不能为`Effect`的`E`类型变量返回值，这是让类型检查器验证`handle_http_error`不能失败。

我们当然可以以类似的方式继续操作`JSONDecodeError`，但我想你已经明白了。

总之，`pfun.effect`通过纯粹的函数式编程实现了类型安全错误处理，而无需开发人员付出任何特别的努力。这很有用，因为它使我们能够使用类型检查器来验证我们的错误处理是正确的(至少在类型方面)。要了解更多关于`pfun`的信息，请查看[文档](https://pfun.dev/)或前往 [github 库](http://github.com/suned/pfun)。

*最初发布于 dev。* [*至*](https://dev.to/suned/completely-type-safe-error-handling-in-python-3apg)
# 解码 Python 布尔运算符

> 原文：<https://levelup.gitconnected.com/decoding-python-boolean-operators-83019fbe0e60>

## Python 布尔操作符可能非常紧凑，但是有点混乱

![](img/311ac4268edd88810ba54cfad673e731.png)

基于 [Michael Dziedzic](https://unsplash.com/@lazycreekimages?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/math?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我们大多数人都熟悉 python 中的`and`和`or`，它们在条件表达式中充当逻辑运算符。例如:

然而，它们还有一个不太常见的用例，为了简洁起见，这些关键字可以在非条件上下文中使用。许多纯粹主义者并不认为`and`和`or`的这种用法非常 pythonic 化，不管是否简洁。我相信这样想的原因是，这些表达往往没有使用更传统的`if` … `else`结构清晰。

## 它们是如何工作的

`and`、`or`和`not`有时被称为短路布尔运算符。您可以在 [python 文档](https://docs.python.org/3/library/stdtypes.html#boolean-operations-and-or-not)中找到对它们的简要描述。

一般来说，你只需要记住`or`将返回第一个“真”值或者最右边的值，如果没有值是真的话。

`and`将以相反的方式工作，返回第一个“Falsy”值，否则返回语句中的最后一个值。

`not`也属于这一类，尽管它有点离群，因为`and`和`or`是二元操作，`not`是后缀一元操作符，这意味着它只接受在关键字后传递的单个参数。`not`的不同之处还在于，它不返回它的参数作为响应，而是评估它的真值并返回逆布尔值。

如果我们使用 dis，我们可以使用这些操作符来查看一些简单的语句。

“2”是行号

注意这些操作符的操作顺序也很重要。`or`先来，然后`and`，最后`not`。

## 例子

虽然到目前为止大多数例子都是“玩具”例子——我相信在某些情况下这些操作符，特别是`or`，是有用的。

考虑一个例子，您收到了一些 json，并希望解析出一个“last_updated_by”字段用于审计。让我们假设我们的 json 对象看起来像这样。

在这个例子中，如果对象还没有被更新，它将不会有一个`updated_by`字段，在这种情况下，我们想回到`created_by`，这恰好是一个可空的字段。使用传统的`if` … `elif` … `else`语法似乎可以通过以下方式获得该值。

但是如果我们利用布尔运算`or`,这可以被压缩成一个简洁的单行，易于阅读和理解。虽然`and`有点违反直觉，但是`or`的这种 n 进制用法对我来说读起来很好，就像 javascript 中的双管(||)操作符一样。

## 效率

除了简洁之外，您可能想知道使用操作符与条件表达式相比，是否有其他副作用或效率。首先让我们看看上面使用`or`的例子的字节码。

显然，布尔操作符的字节码要短得多，这对于效率来说是个好兆头。查看代码时，您会注意到，如果值在字典中，布尔方法将少进行一次字典查找，因为它可以直接返回字典响应，而不是在评估条件后进行最终的字典查找。

让我们开始计时吧。在这两种情况下，`or`方法都稍微快一些，这可能是因为少了一个`get`调用。然而，我不认为结果是确凿无疑的。您的里程可能会有所不同。

## 结论

虽然我发现自己很少使用`and` / `or`布尔运算符，但是当你在代码审查中遇到它们时，知道它们的意思是非常有用的。对于所有寻找高效、简洁解决方案的 leetcoders 来说，它们也很方便。
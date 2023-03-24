# 用 Currying 和 TypeScript 实现更好的 JavaScript 获取功能

> 原文：<https://levelup.gitconnected.com/better-javascript-fetch-functions-with-currying-and-typescript-6c41d9d17282>

![](img/0e7022ed2d570e2a35fb20949ec909dc.png)

斯科特·艾伦在 [Pluralsight](https://www.pluralsight.com/courses/learning-programming-abstractions-python) 上的乐高积木抽象

JavaScript fetch API 公开了一个有效处理 HTTP 请求和响应的接口。借助窗口作用域的`fetch`函数，请求资源可以被异步调用。因此，函数本身将返回一个`Promise`，可以与`.then()`和`.catch()`链接，以指示成功或错误解决。

在整个 JavaScript 代码库中，可以找到带有附加逻辑的类似代码段。

在考虑工程原理时，消除重复和抽象通用部件是至关重要的。这些部分可以依次组合在一起，产生我们想要的结果。

# 一些重要的考虑

随着`async`函数的引入(*在创作*时具有出色的浏览器支持)，我们可以用更短、更易读的形式编写`Promise`结算。然而，当我们考虑错误处理和请求取消时，同样的可重用性原则也将适用。

此外，我们可以选择预构建包，如`Axios`。虽然，外部软件包有其自身的优点和缺点。我们将使用 fetch API，因为它在所有现代浏览器中都有本机可用性。

# 构建块抽象

我们将生成的抽象层应该是无偏见的和分离良好的。因此允许以非强制方式交换较低级别的逻辑功能。

我们将从诊断 fetch API 的核心部分开始，并将它们提取到模块化实体中。

## 承诺解决的回调

我们的抽象实现的消费者将需要一种在成功和失败上下文中执行代码的方法。为此，我们公开了一个回调函数，带有两个可接受的参数，如下所示:

```
type CallbackType = (response: any, error?: string | null) => void
```

当我们成功地解析了 Promise 时，响应被转发给第一个回调参数。如果失败，我们将`null`作为第一个参数传递，将拒绝错误消息作为第二个参数传递。或者，我们可以使用两个函数`successCallback`和`errorCallback`在不同的分辨率状态下具体触发，以便更好地分离关注点。

```
type SuccessCallbackType = (response: any) => void;type ErrorCallbackType = (error: string) => void;
```

*根据您的用例，您可以确定正确的功能粒度和分离级别。*

此外，我们将实现 Promise chaining 来传输由`.json()`返回的结果，从而避免多层嵌套的异步调用。

我们新创建的`retrieve`函数用三个参数简化了`Promises`的繁琐。

## 请求对象中间件

进一步考虑可重用性，`retrieve`函数的第二个参数将具有需要在多个请求中标准化的键值对。

我们可以截取请求对象，并创建一个中间件函数来标准化我们的对象是如何构造的。我们可以传递对象本身并改变其结构属性，或者选择参数化方法:

与我们的`retrieve`功能代码示例不同，这种方法并不适合所有用例。这个例子展示了这个想法，而不是一个被抽象以适应多用途范围的实现。

*请考虑如何扩大您的中间件功能，并包含仅用于特定用例的不必要部分。*

# 将零件固化成复合材料

我们已经成功地积累了抽象的片段，我们可以将它们组合成更简单的函数形式。

通过利用 Currying，我们可以创建一个强大的复合函数，包含我们已经生成的较低层次的抽象。

`compactFetch`函数将拥有几乎完全处理构建请求类型所需信息的参数。

然而，封装的函数将处理额外的参数，这些参数又将被转发给我们前面提到的`retrieve`和`request`函数。

## *获取* &帖子示例

# 最后的想法

记住插入功能粒度，并根据您的需求引入关注点分离。考虑您的系统将要与之交互的接口和冗长的含义。希望您现在可以创建更好的 JavaScript 获取函数。

# 进一步阅读

[](https://developer.mozilla.org/en-US/docs/Glossary/Abstraction) [## 抽象- MDN Web 文档词汇表:Web 相关术语的定义

### 计算机编程中的抽象是一种降低复杂性的方法，允许在计算机编程中进行高效的设计和实现。

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Glossary/Abstraction) [](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) [## 使用获取 API-Web API | MDN

### Fetch API 提供了一个 JavaScript 接口，用于访问和操作 HTTP 管道的各个部分，例如…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) [](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await) [## await - JavaScript | MDN

### await 表达式会导致异步函数暂停执行，直到一个承诺被兑现(即，完成或…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await) [](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/async_function) [## 异步函数表达式- JavaScript | MDN

### async function 关键字可用于在表达式中定义异步函数。您还可以定义异步…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/async_function) 

# 如果你觉得这篇文章有用，请与他人分享。一些掌声👏🏻下面多多帮忙！

通过鼓掌，你帮助其他人发现这些内容，并激发更多关于可访问性、设计、反应和 JavaScript 的文章的写作！
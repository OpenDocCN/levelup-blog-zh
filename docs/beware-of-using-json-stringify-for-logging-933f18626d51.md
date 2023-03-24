# 当心使用 JSON.stringify()进行日志记录

> 原文：<https://levelup.gitconnected.com/beware-of-using-json-stringify-for-logging-933f18626d51>

![](img/a1b62fb96540bdb666bd637e3c2d80b6.png)

罗西·克尔在 [Unsplash](https://unsplash.com/) 上的照片

最近，我在一个基于 AWS Lambda 和 Node.js 的遗留系统上工作。它使用 console 对象和 JSON.stringify()将消息和数据记录到 AWS CloudWatch。

日志消息通常包含有助于解决问题的有用线索，尤其是当它们只出现在生产环境中时。

通过运行下面的示例并扩展输出，您可以看到在日志消息中包含 JSON 数据是多么有用。Sumo Logic 等日志工具可以处理日志消息中嵌入的 JSON 数据。

单击绿色的 run 按钮，然后通过单击灰色小三角形展开输出

然而，当与 JavaScript 错误一起使用时，特别是从[错误类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error)继承的对象。这项技术弊大于利。错误地使用 JSON.stringify()的输出是一个空对象！

回到我工作的遗留系统。我正在调查用户报告的一些问题。在浏览日志时，我注意到许多错误没有什么有用的信息，而且很难理解。这些错误消息是使用 JSON.stringify()记录的。

那么，为什么一种对记录普通对象非常有效的技术对记录错误完全无效呢？这是因为 JSON.stringify()只包含可枚举的属性，而[错误类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error)没有任何属性，因此输出{}。

运行下面的示例演示了常规对象和错误的属性之间的差异。

您是否注意到常规对象的属性将 enumerable 设置为 true，而错误对象的属性为 false？

去掉错误的所有属性对我们的日志来说是灾难性的。有什么技巧可以解决这个问题吗？

一种选择是使用[模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)，而不使用 JSON.stringify()。

虽然它确实显示了错误名称和消息，但是它有不输出堆栈属性的缺点。

另一种选择是简单地记录错误，根本不使用任何文本或 JSON.stringify()。

消息和堆栈属性都包含在日志输出中。运行上述示例时，您需要在堆栈跟踪查看器和属性查看器之间切换，以查看错误属性中的所有信息。

Util.format()可用于输出错误的名称、消息和堆栈属性。

它不是 JSON，但是它包含了所有重要的属性。

您可以创建一个对象，该对象从错误中复制感兴趣的属性，并使用 JSON.stringify()代替原始错误。

与前面提到的技术相比，它的优势在于它保留了 JSON 格式。

您可以使用 JSON.stringify()的 replacer 参数来指定一个函数，并修改被序列化的内容。

使用 Object.getOwnPropertyNames()来动态获取属性名，允许在 JSON 中包含未知属性。例如，AWS SDK 抛出的错误通常包含一组更丰富的属性。

或者，replacer 参数也可以是属性名的白名单。

如果您只关心名称、消息和堆栈属性，或者确切地知道哪些属性存在于将要抛出的错误中，这可能是序列化它们的一种简单方法。

这些是记录错误的一些方法。每一种都有利弊。有些可以像 JSON 一样记录错误，有些则不能。然而，所提到的选择并不详尽，还有更多选择。如果你想更深入地挖掘，你可以考虑在你的错误上添加一个 [toJSON()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify#toJSON_behavior) 方法，研究一下 [serialize-error](https://www.npmjs.com/package/serialize-error) 包或者利用一个日志框架。

所以请记住，当使用 JSON.stringify()进行日志记录时，它非常适合记录自定义数据对象。但是在错误的情况下使用时要特别小心。

如果你喜欢阅读这样的文章，并想支持我成为一名作家，可以考虑[注册成为一名媒体成员](https://john-mills.medium.com/membership)。每月 5 美元，你可以无限制地阅读媒体上的故事。如果你用我的链接注册，我会赚一点佣金。

[](https://john-mills.medium.com/membership) [## 通过我的推荐链接-约翰·米尔斯加入媒体

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

john-mills.medium.com](https://john-mills.medium.com/membership)
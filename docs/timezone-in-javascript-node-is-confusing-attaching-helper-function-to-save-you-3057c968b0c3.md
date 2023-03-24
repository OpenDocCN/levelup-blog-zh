# Javascript/Node 中的时区令人困惑，这里有一个帮助函数可以帮助你

> 原文：<https://levelup.gitconnected.com/timezone-in-javascript-node-is-confusing-attaching-helper-function-to-save-you-3057c968b0c3>

这篇文章旨在展示`new Date()`的内在问题，并提供脚本和如何解决它的解释。

![](img/11eea60364d1b2846cb029424c3a707e.png)

**问题** `**new Date()**` **:** 它采用运行代码的机器的时区，并自动将其转换为 UTC

例如，如果你和我在不同的时区，用`2022–11–08T14:00:00.000`喂养它会产生不同的输出

```
console.log(new Date("2022-11-08T14:00:00.000"))// in Australia/Melbourne, the output is:
// Date: "2022-11-08T03:00:00.000Z"// in UTC
// Date: "2022-11-08T14:00:00.000Z"// in US/Eastern
// Date: "2022-11-08T19:00:00.000Z"
```

# **有什么解决办法？**

我们必须假设，无论是什么样的日期，都是他们想从系统中提取的时间。

这听起来很奇怪，但它完成了任务

## 第一部分。确保尾随的“Z”

在我们对日期时间字符串使用`new Date()`之前，我们想给日期时间字符串添加一个结尾‘Z ’,以确保代码将时间视为 UTC。

```
const dateTimeString = dateTimeString.endsWith('Z') ? dateTimeString : dateTimeString + 'Z'
```

那么所有时区的日期时间都是相同的

```
console.log(new Date("2022-11-08T14:00:00.000Z"))// in Australia/Melbourne, the output is:
// Date: "2022-11-08T14:00:00.000Z"// in UTC
// Date: "2022-11-08T14:00:00.000Z"// in US/Eastern
// Date: "2022-11-08T14:00:00.000Z"
```

然后，我们可以在我们的数据库中存储这个本地日期时间，但采用 UTC 格式。

## **第二部分。读取和解析**

现在，让我们将本地时间和处理过的日期时间返回给系统，我们需要将它解析为 UTC，并自动应用时区将其返回给系统。

```
const localTimeInUtc = "2022-11-08T14:00:00.000Z" // from db
const localTime = localTimeInUtc.endsWith("Z")? localTimeInUtc.slice(0, -1) : localTimeInUtc;
```

这样，我们可以将`localTime`返回给任何时区的任何系统，发送给我们原始的本地日期时间。

如果你想查看和测试时区相关的代码，请点击这里查看 github repo。我关于如何正确进行单元测试的分步指南[在这里](https://medium.com/@caopengau/unit-testing-properly-with-sinon-js-a-step-by-step-guide-32655b19ead5)。

**行动呼吁**

如果你觉得这个指南有帮助，请鼓掌并跟我来。通过[链接](https://medium.com/@caopengau/membership)加入 medium，获取我和所有其他优秀作家在 medium 上发表的所有优质文章。
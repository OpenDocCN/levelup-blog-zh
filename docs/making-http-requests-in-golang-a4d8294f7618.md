# 用 Golang 发出 HTTP 请求

> 原文：<https://levelup.gitconnected.com/making-http-requests-in-golang-a4d8294f7618>

![](img/34ad724e3bd8a62ce5fa9880776009f9.png)

[杭牛](https://unsplash.com/@niuhang?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

这是一个小的帮助说明，适用于那些想找到使用 go standard library net/http 内置包进行 HTTP 请求的方法的人

http 包为发出 HTTP 请求提供了 GET 和 POST 等方便的功能。我们将看到这些函数的快速代码示例。

**得到**

在上面，我们称之为 http。Get 返回一个响应和一个错误值。如果在这个请求过程中有一个错误，err 变量将是非空的，我们将记录这个错误并退出。如果没有错误，我们可以推迟 resp 的执行。Body.Close()将在函数结束时运行，当您有一个响应体，并且忘记关闭响应体会导致长时间运行的程序中的资源泄漏时，这个函数非常有用。

然后，我们读取响应体并记录它。resp。Body 实现 io。阅读器接口，它将允许我们一个数据块一个数据块地读取数据，并将其作为数据流进行处理。

**发布**

请求是进行 POST 调用，在这个请求中，我们将发送一个 JSON 有效负载作为请求体。我们首先整理一个 map，如果成功，我们将得到一个[]字节的信息，我们处理错误，然后调用 http。Post 方法。

http。Post 函数接受 URL、内容类型(在本例中为 JSON)和 io.Reader 的一个实例。读者界面。所以，我们用字节。基于我们的字节数组给我们一个字节缓冲区。

然后，我们读取响应体并记录它。resp。Body 实现 io。阅读器接口，它将允许我们一个数据块一个数据块地读取数据，并将其作为数据流进行处理。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)
# 用 Golang 和 Gin Gonic 发送 HTTP 错误代码的简单方法

> 原文：<https://levelup.gitconnected.com/sending-http-error-codes-with-golang-and-gin-gonic-d915d1dd0166>

## 处理 API 中错误的简单方法

![](img/8ab1c8dbb103c88d3abf4281433b5306.png)

Go 是一种非常流行的语言，这是有道理的。它是一种简单的语言，性能与 C++这样的低级语言一样好。Golang 在 web 开发中的接受度正在高速增长。

Gin web 框架使用定制版本的[http outer](https://github.com/julienschmidt/httprouter)，它在 API 路径中的导航速度比大多数框架都要快。杜松子酒号称比马丁尼酒快 40 倍。

在为您的服务开发 API 时，您可能需要一种方法来处理错误/异常，并向客户端返回适当的 HTTP 错误代码和消息。下面的方案使处理这种情况变得非常容易。

## 响应调度员

响应调度程序的实现如下所示:

上述方法接受概括响应的*上下文*和*响应*结构。

此方法可用于向客户端发送各种 HTTP 错误代码和错误消息。调度程序的用法如下所示:

## 包装它

从 API 发送错误响应的方法有成千上万种，这是其中之一。每当我开始一个新项目时，我更喜欢这样的方法来使我的生活变得简单。
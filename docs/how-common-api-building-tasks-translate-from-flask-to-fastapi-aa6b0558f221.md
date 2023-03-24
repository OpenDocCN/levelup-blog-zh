# 常见的 API 构建任务如何从 Flask 转换到 FastAPI

> 原文：<https://levelup.gitconnected.com/how-common-api-building-tasks-translate-from-flask-to-fastapi-aa6b0558f221>

## 这个在烧瓶里，那个在法斯塔皮

![](img/c933897de1983658870f1d2d735c2e16.png)

爱丽丝·山村在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

python API 开发领域一直在快速变化。早些时候，这个空间属于 Flask，除了 API 构建能力之外，它还拥有与 [Jinja](https://jinja.palletsprojects.com/en/3.0.x/) 模板引擎的集成。因此，使用 flask 开始了网站开发的浪潮。

几年前 [FastAPI](https://fastapi.tiangolo.com/) 发布，它为桌面带来了过多的特性，这使得构建 API 成为一项相对容易的任务。FastAPI 具有可选的异步功能，因此即使有人正在从 flask 过渡，他们也可以使用普通的同步编程。它与 [Pydantic](https://pydantic-docs.helpmanual.io/) 和 [Startlette](https://www.starlette.io/) 的集成使它在众多产品中脱颖而出。

当应用程序非常依赖 I/O 操作，并且由于 I/O 操作的阻塞性质，其他代码片段的执行也被阻塞时，FastAPI 是最适合的。因此，使用异步编程，我们可以在等待长时间运行的任务被执行的同时服务于其他请求。

接下来，我们将直接切入主题。

## 1.应用程序初始化

Flask 类接受 *__name__* 作为参数，而 Fastapi 不接受。flask 中的 *__name__* 主要用于定位模板和静态文件。

## 2.创建路线

在这两个库中声明路由的关键区别在于，flask 需要将 HTTP 方法描述为 route 函数的参数。另一方面，FastAPI 需要用户将 HTTP 方法声明为装饰器的名称。

## 3.访问查询参数

当 FastAPI 收到带有查询参数的请求时，它会解析这些参数，并将它们传递给修饰路由函数。Flask 也解析查询参数，但它不是将它们作为 kwargs 传递，而是使它们在 request.args 对象中可用。

## 4.获取请求正文

在两个库中，将请求体解析为 json 惊人地相似。与 flask 不同，fastapi 不允许直接使用请求模块来访问请求数据，而是需要将其传递给 route 函数。

## 5.启动-关闭事件

Flask 不提供任何基于事件(如启动和停止应用程序)来执行一段代码的方法。然而，FastAPI 确实提供了两个事件，即启动和关闭事件。

在启动时启动数据库连接并在关闭时使连接正常失效的情况下，这种类型的触发器尤其重要。

## 6.数据有效性

Flask 不附带任何数据验证模块。FastAPI 利用 Pydantic，这是一个现代的包，它强制类型提示遵循某些用户定义的数据验证协议。

除了确保数据的结构，pydantic 还实现了验证器，通过它可以应用自定义检查来确保数据的同质性。

## 7.JSON 响应

有了 fastapi，用户不必担心在返回值之前对数据进行 JSON 编码，fastapi 对每个返回值都应用了 JSON 编码器。在 flask 中，用户应该使用 jsonify()来指定一个 JSON 响应。

## 8.处理 CORS

在构建与不同来源的其他应用程序通信的 API 时，CORS 很重要。Flask 并不执行开箱即用的 CORS，它需要一个名为 [flask_cors](https://flask-cors.readthedocs.io/en/latest/) 的扩展库。

在 Fastapi 中，通过其中间件功能，CORS 被完美地处理。不需要安装任何其他模块来支持。

# 谢谢！

我很高兴你读完这篇文章。请不要犹豫，在评论区讨论您对 fastapi、flask 或本文的看法。一会儿见:)
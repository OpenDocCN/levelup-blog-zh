# 编写 Fx 模块

> 原文：<https://levelup.gitconnected.com/writing-fx-modules-517193b9c4f0>

![](img/3199996f3fff99f4f304d184d0a89ed1.png)

来源:[戈朗周刊](https://res.cloudinary.com/cpress/image/upload/w_1280,e_sharpen:60/rp1osul9qflgxvec52fo.jpg)

## *本文是* [*GoLang:构建 Web 服务器*](https://sumit-agarwal.medium.com/golang-building-a-web-server-2d34d4f90fa1) 系列文章的一部分

在[上一篇文章](https://medium.com/swlh/dependency-injection-in-go-using-fx-6a623c5c5e01)中，我们探索了如何使用 [Fx](https://pkg.go.dev/go.uber.org/fx) 库将手动连接的 HTTP web 服务器转换成基于阿迪的 web 服务器。现在，一旦我们准备好了，我们的下一步就是给我们的服务器添加功能，也就是业务逻辑。但是在我们开始之前，我们需要先了解如何为我们的 Fx 服务器编写模块。这些模块将作为我们的应用程序的构建模块。

## 什么是模块？

一般来说，模块是一段独立且可互换的程序，因此每个模块包含执行所需功能的一个方面所需的一切。(来源:[维基百科](https://en.wikipedia.org/wiki/Modular_programming))。

当我们写一行代码时，我们实现了一个小目标。这可能是我们的业务逻辑，从字符串中删除尾随空格，或者获取当前系统时间，等等。这些案例中的每一个都是独一无二的，都可以独立存在，都有它自己的意义，那么我们要不要继续下去，用它来创建一个模块呢？我不这么认为。

我们在定义模块的时候要深思熟虑。创建一个模块的理想场景是当你想封装一些行为，这样它就可以在应用程序中重用，有时也可以在应用程序之外重用。通常，这种行为是使用接口在模块外部公开的。例如，创建模块的一些好的用例是*日志模块、度量模块、授权模块等。*

> 请确保你理解库、框架和模块之间的区别。

## 什么是 Fx 模块？

正如我们在上一篇文章中看到的，Fx 所做的就是将一个对象注入另一个对象。为了将对象 A 注入到对象 B 中，它需要预先将对象 A 放在适当的位置。那么它首先从哪里得到对象 A 呢？

迷惑…对吧？

让我们试着简化它，当 Fx 创建一个新对象(我们称之为 OBJ1，以下简称 OBJ1)时，它查找 obj 1 需要的依赖项。

情况 1:不需要依赖关系，OBJ1 是在应用程序上下文中创建的。

案例 2: Fx 在应用程序上下文中找到需要的依赖项，注入它们，OBJ1 就创建了。

这一切都是在哪里发生的？它发生在一个`fx.provide`调用中，该调用是一个模块的一部分，该模块的职责是在 Fx 生命周期中调用`New`方法时创建 OBJ1 并将其提供给应用程序上下文。([外汇文件](https://pkg.go.dev/go.uber.org/fx))

Fx 是根据[模块化编程](https://en.wikipedia.org/wiki/Modular_programming)的概念设计的。每当你在一个使用 Fx 实现 DI 的应用程序中编写一行代码时，它总是某个模块的一部分(除非手动连接)。当您将应用程序转换为 Fx 应用程序时，您已经在模块中编写了代码。

## 我们的第一个 Fx 模块:LoggerFx

我们将尝试将日志添加到我们到目前为止构建的 web 服务器中。为此，我们将使用 [zap](https://pkg.go.dev/go.uber.org/zap) 作为日志库(由 uber 构建)。我们添加一个名为`loggerfx.go`的文件

```
// ProvideLogger to fx
func ProvideLogger() *zap.SugaredLogger {
   logger, _ := zap.NewProduction()
   slogger := logger.Sugar()

   return slogger
}

// Module provided to fx
var Module = fx.Options(
   fx.Provide(ProvideLogger),
)
```

然后将`main.go`更新为:

```
func main() {
   fx.New(
      fx.Provide(http.NewServeMux),
      fx.Invoke(server.New),
      fx.Invoke(registerHooks),
      loggerfx.Module,
   ).Run()
}

func registerHooks(
   lifecycle fx.Lifecycle, mux *http.ServeMux, logger *zap.SugaredLogger,
) {
   lifecycle.Append(
      fx.Hook{
         OnStart: func(context.Context) error {
            logger.Info("Listening on localhost:8080")
            go http.ListenAndServe(":8080", mux)
            return nil
         },
         OnStop: func(context.Context) error {
            return logger.Sync()
         },
      },
   )
}
```

我们已经准备好运行应用程序，该应用程序将在“/”路由上提供“Hello World”响应。但这次我们也在控制台上看到了`Listening on localhost:8080`日志。

> `*go run main.go*`

瞧啊。用 Fx 写模块就是这么简单。请在 [Github](https://github.com/sumiet/medium_webserver_series/tree/master/3) 上找到代码。

*请注意，我们使用了 Zap* ***库*** *，在 Fx* ***框架*** *里面创建了 LoggerFx* ***模块*** *。希望这能帮助你更好地理解这三个术语的区别。*

现在，您获得 Fx 模块实践经验的任务是将 HTTP 处理程序转换成一个模块。编码快乐！

在下一篇文章中，我们将探索远程过程调用，并为我们的服务添加一个 RPC 处理器模块。

RPC 解释道

C **oding 有趣的事实:**计算机“bug”这个词的灵感来自于一个真实的 bug。它是由[格蕾丝·赫柏](https://gocoderz.com/blog/great-women-of-stem/)于 1947 年创建的。

***请求:*** *请在评论中添加您的宝贵反馈，这将真正帮助我提高内容的质量，并使其符合您的期望。*
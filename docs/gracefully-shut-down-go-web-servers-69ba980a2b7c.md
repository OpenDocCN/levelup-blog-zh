# 优雅地关闭 Go Web 服务器

> 原文：<https://levelup.gitconnected.com/gracefully-shut-down-go-web-servers-69ba980a2b7c>

![](img/f41528f28359fa7c7e2d7b453f7c1f2a.png)

[https://github.com/MariaLetta/free-gophers-pack](https://github.com/MariaLetta/free-gophers-pack)

正常关机是 web 服务器的一项常见任务，也是一项非常重要的功能。我们将研究如何在 Golang 做到这一点。让我们开始吧。

# 什么是优雅关机？

如果您的 web 服务器在关闭服务器之前等待您的活动请求得到处理，这意味着您正在优雅地关闭服务器。

## 为什么重要？

想象你正在逛商店。他们晚上 10 点关门。晚上 9 点 55 分和 10 点的时候你都在里面。他们将关闭商店，不接受任何新的顾客，但帮助里面的顾客，为他们服务，然后他们就会回家。他们优雅地关闭了商店。

对于 web 服务器来说，这也是同样的想法。你得到一个关闭网络服务器的信号。然后，您将不会接受任何新的请求，服务于活动的请求，然后关闭 web 服务器。

## 例子

让我们创建一个运行在端口 8000 上的 web 服务器。然后创建一个 goroutine 来运行 web 服务器，这样它就不会阻塞代码。

## 运行 web 服务器

srv。ListenAndServe 将在端口 8000 上运行 web 服务器。如果有任何错误，我们将记录错误。

您可能会注意到额外的检查错误。就是(呃，http。ErrServerClosed)。当您关闭或关闭服务器时，http。ErrServerClosed 将被返回，所以在运行 web 服务器时它不是一个错误，所以我将忽略这个错误。

## 为什么使用渠道？

> 从一个信道取回接收是一个阻塞操作。

我定义了一个`os.Signal`通道来阻塞应用程序，直到收到来自操作系统的信号。当程序中断时，`signal.Notify`将向`quit`通道发送信号。用`ctrl + c`就可以了。

## 为什么上下文超时？

假设我们的代码中有一个 bug，我们正在关闭服务器以使用修复这个 bug 的新版本。想象一下，这个 bug 让我们在几分钟内处理一个活动请求。所以我们不应该等那么久。在下面的代码中，我说，嘿，不要等待超过 30 秒。关闭服务器，即使 30 秒后有任何活动请求。这就是使用超时上下文的原因。`srv.Shutdown`会在 30 秒内优雅地关闭服务器。

```
package main

import (
 "context"
 "errors"
 "log"
 "net/http"
 "os"
 "os/signal"
 "time"
)

func main() {
 srv := &http.Server{
  Addr: ":8000",
 }
 go func() {
  if err := srv.ListenAndServe(); err != nil && !errors.Is(err, http.ErrServerClosed) {
   log.Fatal(err)
  }
 }()
 quit := make(chan os.Signal, 1)
 signal.Notify(quit, os.Interrupt)
 <-quit
 ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
 defer cancel()
 if err := srv.Shutdown(ctx); err != nil {
  log.Fatal(err)
 }
}
```

当您关闭 web 服务器时，不要忘记优雅地关闭它。
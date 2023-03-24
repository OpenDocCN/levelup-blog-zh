# 用 Rust 处理同一个端口上的 websocket 和 http

> 原文：<https://levelup.gitconnected.com/handling-websocket-and-http-on-the-same-port-with-rust-f65b770722c9>

![](img/86627b5c18ee897dd16e73728ed7df50.png)

## 介绍

我将通过我所能找到的最简单、最灵活的方法来为异步服务器创建脚手架，该服务器能够在 Rust 中监听一个端口的同时服务于 websocket 连接和 http 请求。这个例子将使用未加密的 websocket 连接。您可以在 https://github.com/tsidea/http-ws-server-rs 找到与本文相关的完整运行示例。

在我们开始之前，我们需要了解一些关于 websockets 的细节，特别是它们是如何创建的。它们最初只是一个简单的 http 请求。当您在 Javascript 中构造一个 [Websocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket) 对象时，它首先向提供的 url 发送一个 http 请求，并要求服务器使用[升级头](https://en.wikipedia.org/wiki/HTTP/1.1_Upgrade_header)将连接升级到 Websocket。如果请求包含所有必要的头，服务器通过发回包含其他特定头的响应来表示同意。这叫做 [websocket 握手](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers#the_websocket_handshake)。握手之后，http 连接保持打开，服务器和客户端都可以对其进行读写。websocket 协议还指定了客户端和服务器都需要应用的某些消息帧，以便进行通信。这意味着该连接不仅仅是一个原始的 TCP 连接，它在 TCP 之上使用了一些帧。即便如此，从 websockets 读写的开销也是最小的，因此它[为我们提供了一个非常高性能的通信层，而不是 http](https://blog.feathersjs.com/http-vs-websockets-a-performance-comparison-da2533f13a77) 。

## 初始设置

为了测试我们的服务器设置，我们需要用`curl`来测试 http 部分，用`[websocat](https://github.com/vi/websocat)`来测试 websocket 部分。你也可以使用任何带有 websocket 客户端扩展的浏览器，比如 Firefox 的简单 Websocket 客户端。我假设您已经安装了`curl`，那么让我们使用下面的命令安装`websocat`:

```
cargo install websocat
```

没错，`websocat`可以用`cargo`安装。

现在让我们像这样创建我们的 rust 项目:

```
cargo new http-ws-server-rs
```

接下来，我们需要将本例中使用的依赖项添加到`Cargo.toml`的依赖项部分:

```
[dependencies]
futures = { version="0.3" }
tokio = { version="1.0", features=["rt","macros"] }
hyper={ version="0.14", features=["server","http1","http2","tcp"] }
tungstenite={ version="0.12", default-features=false }
tokio-tungstenite={ version="0.13" }
```

因为我们正在编写一个异步服务器，所以我们需要`[futures](https://docs.rs/futures/0.3.12/futures/)`机箱。我们还需要一个异步服务器的运行时，由`[tokio](https://docs.rs/tokio/1.1.1/tokio/)`提供。为我们提供 http 构建模块的机箱是`[hyper](https://docs.rs/hyper/0.14.2/hyper/)`。然后`[tungstenite](https://docs.rs/tungstenite/0.12.0/tungstenite/)`为我们提供了 websocket 握手实现，而`[tokio-tungstenite](https://docs.rs/tokio-tungstenite/0.13.0/tokio_tungstenite/)`给了我们一个`[WebsocketStream](https://docs.rs/tokio-tungstenite/0.13.0/tokio_tungstenite/struct.WebSocketStream.html)`实现。我只选择了构建我们的示例所需的[机箱特征](https://doc.rust-lang.org/cargo/reference/features.html)，您可能需要更多的特征。

## 使用 [hyper](https://hyper.rs/guides/) 的 HTTP 服务器

我们将使用`[hyper](https://hyper.rs/guides/server/hello-world/)` [自己的例子](https://hyper.rs/guides/server/hello-world/)使用`hyper`来构建一个简单的 web 服务器。下面是我们将从`src/main.rs`文件开始的代码:

如果你看一下`[hyper](https://hyper.rs/guides/server/hello-world/)` [的例子](https://hyper.rs/guides/server/hello-world/)，你会发现这段代码几乎是一样的，只有一些小的不同。第一个区别是添加到 main 的属性:

```
#[tokio::main(flavor = "current_thread")]
```

[这指示](https://docs.rs/tokio/1.1.1/tokio/attr.main.html) `[tokio](https://docs.rs/tokio/1.1.1/tokio/attr.main.html)` [在当前线程](https://docs.rs/tokio/1.1.1/tokio/attr.main.html)上运行。构建自己的服务器时，您必须决定服务器是多线程的还是单线程的。我们只是让这个例子简单一些。

另一个区别是，在第 22 行和第 26 行，你可以看到我们将远程地址传递给了我们的处理函数。这绝对不是必需的，这只是为了展示一种情况，在这种情况下，您可能希望在处理程序中方便地保存这些信息，以备记录之需。

现在让我们运行这个服务器，看看会发生什么:

```
**~/http-ws-server-rs**$ cargo run 
 **Finished** dev [unoptimized + debuginfo] target(s) in 0.02s 
 **Running** `target/debug/http-ws-server-rs` 
Listening on 127.0.0.1:3000 for http or websocket connections.
```

现在服务器启动了，让我们使用`curl`来连接它:

```
**~/http-ws-server-rs**$ curl localhost:3000 
Hello there connection 127.0.0.1:34830
```

正如我们所预料的那样，我们收到了一个 hello 问候和发出请求的地址。我们现在可以继续增强处理程序的功能。

## 处理 http 与 websocket

现在我们有了处理函数，我们需要做的就是弄清楚什么时候请求实际上是一个 websocket 握手请求，而不仅仅是一个普通的 http 请求。正如简介中提到的，我们可以只查看[升级头](https://docs.rs/hyper/0.14.2/hyper/header/constant.UPGRADE.html)来判断客户端是否试图启动 websocket 连接。自然，我们将为此使用一个`[match](https://doc.rust-lang.org/rust-by-example/flow_control/match.html)`模块。

如您所见，`match`块匹配一个由 uri 路径和一个告诉我们升级头是否存在的布尔值组成的[元组](https://doc.rust-lang.org/rust-by-example/primitives/tuples.html)。如果 Upgrade 头存在，那么我们可以假设该请求是一个 websocket 握手请求。从`match`块可以看出，我们只接受第一个分支上的 websocket 连接，这与`/ws_echo`上的 uri 路径相匹配。

## Websocket 握手响应

接受连接的第一步是用握手响应来回答握手请求。为此，我们将使用`tungstenite` crate 方法`[create_response_with_body](https://docs.rs/tungstenite/0.12.0/tungstenite/handshake/server/fn.create_response_with_body.html)`，它将创建一个正确的握手响应，并验证该请求是否为正确的 websocket 握手请求。下面是能够创建握手响应的块:

最后你会看到这一切是如何联系在一起的。现在只需注意我们匹配了`create_response_with_body`的返回值。如果方法返回`Ok`，那么我们可以启动 websocket 连接。如果该方法返回`Err`，这意味着该请求不是有效的 websocket 握手请求。

## 启动 websocket 流

一旦我们收到来自`create_response_with_body`的包含握手响应的`Ok`，我们就可以继续使用请求来创建我们的 websocket 连接，如下所示:

我们要做的第一件事是[生成一个任务](https://docs.rs/tokio/1.1.1/tokio/task/fn.spawn.html)，该任务将在 websocket 连接的剩余生命周期中处理它。然后，我们使用`[hyper::upgrade::on](https://docs.rs/hyper/0.14.2/hyper/upgrade/fn.on.html)`方法将请求升级到持久连接。在这一部分，我们将请求转换成流，以便能够对其进行读写。`hyper::upgrade::on`方法返回一个[未来](https://doc.rust-lang.org/std/future/trait.Future.html)，所以我们必须`[await](https://doc.rust-lang.org/std/keyword.await.html)`它来接收结果。成功后，我们得到一个带有`[Upgraded](https://docs.rs/hyper/0.14.2/hyper/upgrade/struct.Upgraded.html)`对象的`Ok`，我们将通过从它创建一个`WebSocketStream`来使用它作为我们的`[WebSocketStream](https://docs.rs/tokio-tungstenite/0.13.0/tokio_tungstenite/struct.WebSocketStream.html)`的基础。您可以将`Upgraded`对象或多或少地视为请求的底层 TCP 连接的句柄。除了`Upgraded`对象之外，我们还需要`WebSocketStream`，因为`WebSocketStream`能够添加和删除【websocket 消息所需的帧和开销。它还能给我们一个[流](https://docs.rs/futures/0.3.12/futures/stream/trait.Stream.html)，带有`Upgraded`对象所缺少的所有[花里胡哨](https://docs.rs/futures/0.3.12/futures/stream/trait.StreamExt.html)。

现在我们有了这个东西，我们可以用它做任何我们想做的事情。在这个例子中，我们只是将拆分为[接收器](https://docs.rs/futures/0.3.12/futures/sink/trait.Sink.html)和[流](https://docs.rs/futures/0.3.12/futures/stream/trait.Stream.html)，这样我们就可以[将流](https://docs.rs/futures/0.3.12/futures/stream/trait.StreamExt.html#method.forward)转发到接收器，从而实现回声。一旦连接关闭，转发将失败，整个任务将结束。

## 完成请求处理程序

现在让我们把所有这些放到一个完整的处理程序中，看看它是什么样子的。

现在我们只需用这个函数替换`src/main.rs`文件中最初的一行程序`handle_request`函数。包括所有必要的`use`语句。

## 测试最终处理器

用上面的完整处理程序实现更新初始处理程序后，我们可以启动改进的服务器:

```
**~/http-ws-server-rs**$ cargo run 
 **Finished** dev [unoptimized + debuginfo] target(s) in 0.02s 
 **Running** `target/debug/http-ws-server-rs` 
Listening on 127.0.0.1:3000 for http or websocket connections.
```

现在让我们看看它如何处理`curl`请求和`websocat`连接。

```
**~/http-ws-server-rs**$ curl localhost:3000 
This / url doesn't do much, try accessing the /ws_echo url instead. 
**~/http-ws-server-rs**$ curl localhost:3000/whichever_path 
This /whichever_path url doesn't do much, try accessing the /ws_echo url instead. 
**~/http-ws-server-rs**$ curl localhost:3000/ws_echo 
Getting even warmer, try connecting to this url using a websocket client. 
**~/http-ws-server-rs**$ websocat ws://127.0.0.1:3000/ws_echo 
hello, my name is... 
hello, my name is...
```

成功！我们有一个服务器在同一个端口上处理 http 和 weboscket。
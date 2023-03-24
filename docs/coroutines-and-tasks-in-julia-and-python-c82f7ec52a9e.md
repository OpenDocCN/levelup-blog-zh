# Julia 和 Python 中的协程和任务

> 原文：<https://levelup.gitconnected.com/coroutines-and-tasks-in-julia-and-python-c82f7ec52a9e>

![](img/b66cb88bc0b1f5f79a7a6e5e724f002f.png)

[咖啡极客](https://unsplash.com/@coffeegeek?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这是我在谈论 Julia 和 Python 中的[生成器和迭代器时提到的一个话题。在这里，我们将更深入地了解基础知识，以及如何使用它来实现并发性。](https://medium.com/@Jernfrost/generators-and-iterators-in-julia-and-python-6c9ace18fa93)

比较 Julia 和 Python 是很有挑战性的，因为虽然它们都使用协程和任务，但它们呈现给用户的界面却完全不同。

许多相同的概念在两个地方都存在，但通常存在于不同的抽象层次。

## 朱莉娅的例子

这是一个 TCP/IP 服务器和客户端的例子。这是服务器的代码，它监听来自客户端的连接，然后将从客户端收到的文本大写并写回客户端。

```
using Sockets

@async begin
   server = listen(2001)
   while true
       sock = accept(server)
       @async while isopen(sock)
           write(sock, uppercase(readline(sock, keep=true)))
       end
   end
end
```

在 REPL 上，我们可以连接到服务器

```
julia> clientside = connect(2001)
```

为了捕捉来自服务器的响应，我们将创建一个任务来监听回复。

```
julia> @async while isopen(clientside)
           write(stdout, readline(clientside, keep=true))
       end
```

最后，我们将向服务器发送一条文本。

```
julia> println(clientside,"Hello World from the Echo Server")
```

它会反馈:

```
HELLO WORLD FROM THE ECHO SERVER
```

对于那些没有读过我关于生成器的故事的人，让我解释一下宏是如何工作的。

如果我们不使用宏，代码必须写成这样:

```
function echo_server()
   server = listen(2001)
   while true
       sock = accept(server)
       function writer()
           while isopen(sock)
               write(sock, uppercase(readline(sock, keep=true)))
           end           
       end
       writer_task = Task(writer)
       schedule(writer_task)
   end
end

server_task = Task(echo_server)
schedule(server_task)
```

我们创建一个任务来监听连接。实现`listen`函数是为了调用`wait`函数。`wait`类似于 Python 的`await`。然而在 Julia 中，它通常不公开，而是一个实现细节。它用于等待各种可等待的对象，如通道和套接字。

当`wait`被调用时，调度程序将选择另一个准备运行的任务。

在`writer_task`中，`readline`将调用`wait`，T8 将控制传递给其他任务，直到在正在读取的套接字上接收到一些数据。

## 假想的 Python 示例

比较语言的一个问题是，API 是如此不同，以至于我们试图比较的关键特性很容易淹没在所有其他差异中。

因此，我冒昧地写了一个代码示例，在这里，我想象 Python 可以像 Julia 一样访问类似的网络 API。

然后我们用 Python 的并发概念来使用它。

```
async def echo_server():
   server = listen(2001)
   while True:
       sock = await server.accept()
       async def writer():
           while sock.isopen():
               data = await sock.readline()
               await sock.write(data.upper())
       await writer()

asyncio.run(echo_server())
```

我们没有使用`@async`宏，而是用`async`关键字来标记我们的函数。这使得该方法的运行返回一个协程，类似于朱莉娅`Task`。

`accept`、`readline`、`write`的设计与茱莉亚不同。Julia 版本在内部调用`wait`，并返回常规函数值。Python 风格返回协程(Julia 任务)。运行并等待协程完成并返回值是通过`await`完成的。所以`await`采用协程作为参数。

这解释了为什么我们不能下一个函数调用并做类似于 Julia 的事情:

```
await sock.write(sock.readline().upper())
```

因为`readline`实际上并不返回数据，而是一个协程。或许我们可以这样做:

```
await sock.write((await sock.readline()).upper())
```

## 真实的 Python 例子

当然，在现实世界中，Python 网络 API 有些不同。

我们的协程从客户端读取数据并以大写形式发送回，它必须接受一个`reader`和`writer`流对象，用于读取和写入客户端。

```
import asyncio
async def echo_server(reader, writer):
    while True:
        data = await reader.readline()
        writer.write(data.upper())
        await writer.drain() 
    writer.close()
```

另外`write`没有阻塞，因为我假设它只是写入缓冲区。它正在耗尽阻塞的缓冲区。所以`drain`将返回一个协程，我们必须等待它完成。

启动服务器有点不同。我们不处理套接字对象。

```
async def main():
    server = await asyncio.start_server(
        echo_server, '127.0.0.1', 2001)
    await server.serve_forever()

asyncio.run(main())
```

启动服务器的函数需要一个协程作为第一个参数。将为每个连接的客户端调用这个协程。

我不知道你如何让 Python 与 REPL 异步运行，所以调用`assynio.run`会冻结你的 REPL 提示符，不像 Julia。因此，为了测试这一点，您必须在另一个 python REPL 中设置一个客户端连接。或者您可以在命令行上使用 Netcat:

```
$ nc localhost 2001
Hello
HELLO
```

现在你可以写一堆东西，让它大写。按 Ctrl+C 退出。

## 关于 Julia 和 Python 并发性之间差异的思考

Python 方法把协程放得到处都是，期望它们作为参数。

在 Julia APIs 中很少需要直接提供任务对象。虽然可能主观上受我与 Julia 相处时间的影响，但我忍不住觉得这方面的 Python APIs 更难理解。

为了进行比较，我研究了在 Go 编程语言中这是如何实现的。[这个](https://coderwall.com/p/wohavg/creating-a-simple-tcp-server-in-go)显示了与我们的 Julia 示例逻辑非常相似的代码。

这也恰好遵循与 Swift 编程语言的 [SwiftSocket](https://github.com/swiftsocket/SwiftSocket) 库相似的逻辑。

[](https://gitconnected.com/learn/julia) [## 学习朱莉娅-最佳朱莉娅教程(2019) | gitconnected

### 12 大朱莉娅教程-免费学习朱莉娅。课程由开发人员提交并投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/julia)
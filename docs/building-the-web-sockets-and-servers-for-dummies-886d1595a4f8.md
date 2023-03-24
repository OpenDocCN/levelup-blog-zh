# 构建网络:虚拟的插座和服务器

> 原文：<https://levelup.gitconnected.com/building-the-web-sockets-and-servers-for-dummies-886d1595a4f8>

![](img/673b0827bd242570c34e00c2355937e2.png)

由[泰勒维克](https://unsplash.com/@tvick?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

*什么是 web 服务器？*

你可能熟悉这样的解释:**web 服务器是运行在计算机上的一个进程，它接受 HTTP 请求并发回 HTTP 响应**。但是什么样的过程呢？它跑向哪里？运行情况如何？还有最重要的: ***我如何构建一个？***

在本文中，我将剖析一个简单 web 服务器的基本软件组件——这些概念与语言无关，并使用我们的操作系统提供的一些核心基础设施。我将专门使用 python 的库，但是大多数编程语言都有某种访问套接字的方式。

在我们开始之前，我恳求你跟随你自己的终端。通过在您自己的 python 环境中构建 web 的基本组件，您将会发现更多。我会让这变得非常简单:只需打开一个终端并启动一个 python shell。(你可以用 python 2，但我更喜欢 python 3)

```
$ python3
```

在我们进入 web 服务器之前，我们必须从您可能听说过，但从未真正理解的东西开始:

# 插座

一般来说，网络通信和计算机通信的核心是不起眼的**插座**。套接字是一种软件抽象，用于表示和控制通信端点。它可以监听通信，连接到另一个套接字，发送响应，关闭这些连接，等等…

如果这看起来太抽象，现在，你可以想象一个理论上的邮箱。在 python 中，我可以非常简单地创建一个新的套接字。

```
>>> import socket
>>> sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
```

这创建了一个套接字的**实例**，但是没有地址。不是很有用。这就像一个邮箱漂浮在真空中，没有人能找到它。为了给它一个地址，我**将**我的套接字绑定到一个地址*(例如:127.0.0.1:8888)。*你们中的一些人可能认识这个地址，这是一个用于本地 web 服务器的通用端口，尤其是节点服务器。现在让我们将套接字绑定到该地址。

```
>>> sock.bind(('', 8888))
```

`bind`接受主机名和端口的元组。`''`表示绑定到所有接口。

现在，我的邮箱位于本地机器的 8888 端口。问题是，它关门了——不接受邮件。任何发送消息的人都会发现他们无法连接。检查一下，打开一个新的 shell 窗口:

```
$ curl localhost:8888
```

过一会儿，您的请求应该超时:“无法连接到本地主机端口 8888”。那是因为你的插座关了。

为了允许传入的连接，您需要将您的套接字转换成一个**监听套接字:**

```
>>> REQUEST_QUEUE_SIZE = 5
>>> sock.listen(REQUEST_QUEUE_SIZE)
```

现在你的邮箱已经打开，可以接收邮件了。您的套接字正在侦听传入的请求。但是仍然有一个问题:没有人检查它。尝试通过 curl 再次连接到您的插座:

```
$ curl localhost:8888 
```

它应该永远挂着。注意，您没有连接失败——事实上，您已经成功地向套接字发送了请求！问题是套接字没有发送任何响应，它只是在侦听，就像你的前女友收件箱一样。但是请记住，我们的套接字一次只能容纳队列中的 5 条消息，所以我们需要快速开始处理这些消息！

这就是`socket.accept`的作用。Accept 告诉您的套接字等待队列中的下一封邮件(或者如果它已经在那里就抓取它)并使用**客户端地址**打开**一个新的套接字**。这有点令人困惑，但你会明白我的意思。在您的 python shell 中亲自尝试一下:

```
>>> client_connection, client_address = sock.accept()
>>> client_address
('127.0.0.1', 52095)  # your address won't be quite the same
```

如您所见，`socket.accept()`返回一个 client_connection *，它实际上是一个全新的套接字*，以及该客户端的地址。在本例中，我们看到客户端来自本地主机(127.0.0.1)的端口 52095。这是一个短暂的端口——计算机为了发出 curl 请求，凭空创建了它。请记住，这个新套接字是原始套接字端口和新客户端地址之间的一个**连接**。试试这个:

```
>>> client_connection.getsockname()
('127.0.0.1', 8888)
>>> client_connection.getpeername()
('127.0.0.1', 52095  # your address won't be quite the same
```

注意，这个套接字仍然位于端口 8888。这种类型的插座称为**连接**插座**。**您最初的**监听套接字**仍然存在——它的工作是继续监听更多的请求(例如，您可以打开另一个终端，发出一个新的 curl 请求，然后再次调用`socket.accept`，您将拥有另一个连接)

让我们通过套接字向 curl 客户端发回一条消息:

```
>>> client_connection.recv(1024)  # read the request
>>> response = b"""HTTP/1.1 200 OK\n\nHello World!"""
>>> client_connection.sendall(response)
>>> client_connection.close()
```

“b”表示这是一个字节串。您应该会在 curl 终端中收到一条消息！**万岁！！**

这种通过套接字发送 HTTP 的想法是服务器和 web 应用程序领域的开端，我迫不及待地想告诉大家这一切——但首先，让我们快速回顾一下:

*   套接字是绑定到地址的通信端点
*   一个**监听插座**监听特定地址的通信
*   **连接套接字**由监听套接字在接受请求时创建，负责管理连接、完成请求并发送响应。
*   您可以通过套接字接受 HTTP 请求和发送 HTTP 响应，等等。记住，套接字是一个通用的通信通道。你可以发送你想要的任何字节的数据。

# 一个简单的网络服务器

您可能已经开始预料到这一点，但是 **web 服务器**本质上是一个开放的套接字，在一个无限循环中监听和接受请求，就像这样:

```
server_socket = ...
<instantiate_socket_here>  # pseudocodewhile true:  # do forever
    client, _ = server_socket.accept()
    request = client.recv(1024)  # read 1024 bytes from the request

    # run the request through your web framework of choice
    response = run_web_application(request) # send the response back to the client
    client.sendall(response)
    client.close()
```

服务器接受连接、处理请求、发送响应，然后关闭连接并接受队列中的下一个请求。

还记得我们说过客户端连接实际上是与 **server_socket** 不同的**套接字——这真的很有帮助，因为这意味着监听套接字(server_socket)不必处理通信。它可以自由地立即转到下一条消息，因此我们的简单架构有可能并发处理请求。我们需要一些魔法来实现并发性，但是我们正在超越自己。**

另一件值得注意的事情是:一旦连接打开，服务器没有**和**来关闭它。当您听到“web 套接字”时，您可能会想到这一点—您可以拥有一个直接与服务器通信的开放通道。这就是聊天应用程序给你实时更新信息的方式。每个人都与聊天服务器有一个开放的连接(每个人都有自己的服务器套接字)。当您关闭聊天时，您的连接也将关闭。

跟随这个示例，看看保持与服务器的开放连接是什么样子的:

1.  确保前面示例中的`sock`正在监听端口 8888。如果您不确定，请打开一个新的 python shell 并运行以下命令:

```
>>> import socket
>>> SERVER_ADDRESS = (HOST, PORT) = '', 8888
>>> REQUEST_QUEUE_SIZE = 5
>>> sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
>>> sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
>>> sock.bind(SERVER_ADDRESS)
>>> sock.listen(REQUEST_QUEUE_SIZE)
```

*您现在应该打开了一个监听套接字并监听端口 8888*

2.用新插座连接到您的监听插座。为此，在新的终端中打开第二个 python shell:

```
>>> import socket
>>> connect_sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
>>> connect_sock.connect(('localhost', 8888))
```

3.接受原始套接字上的连接，并开始监听消息

```
>>> connection, _ = sock.accept()
>>> while True:
...    connection.recv(1024) # constantly read from the socket 
```

4.开始从连接的套接字发送消息

```
>>> connect_sock.sendall(b'Hello World, this is my first live msg!')
>>> connect_sock.sendall(b'Second msg, this is still fun!')
```

5.观察它们出现在您原来的套接字连接中。**wheeee！**

这是位于 [Javascript 的 WebSocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API) 核心的 OS 级软件，以及流行 web 框架的相应 WebSocket 实现。而你是用 Python 从头开始构建的！

## HTTP 服务器

为了完善示例，我将使用 python 的 socket API 为您提供一个简单 HTTP 服务器的完整实现:

**webserver.py** (盗自 [RuslansPivak](https://ruslanspivak.com/lsbaws-part3/) )

```
import socket
import timeSERVER_ADDRESS = (HOST, PORT) = '', 8888
REQUEST_QUEUE_SIZE = 5# Handle a request to the socket
def handle_request(client_connection):
    request = client_connection.recv(1024) #read from request stream
    print(request.decode())
    http_response=b"""HTTP/1.1 200 OK\n\nHello World!"""
    client_connection.sendall(http_response)def serve_forever():
    listen_sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    listen_sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
    listen_sock.bind(SERVER_ADDRESS)
    listen_sock.listen(REQUEST_QUEUE_SIZE)
    print('Serving HTTP on port {port}'.format(port=PORT))

    while True:
        # wait for the next connection and process it
        client_conn, _ = listen_sock.accept()
        handle_request(client_conn)
        client_conn.close()if __name__ == '__main__':
    serve_forever()
```

要让这个基本的 web 服务器工作，您只需运行:

```
>>> python webserver.py
Serving HTTP on port 8888
```

你可以用 curl 来测试它:

```
curl localhost:8888
Hello World!
```

恭喜你，你刚刚用套接字做了一个 web 服务器。当然，它所做的只是打印 hello world，但是基本架构已经存在——只需在上面插入您选择的 python web 框架，它就会做更多的事情。

真正的开源 python 服务器，如 [Gunicorn](https://gunicorn.org/) ，基本上与你构建的服务器结构相似，但它们实现了一个标准接口，称为 **(Python) Web 服务器网关接口(WSGI)** 。这就是为什么 Gunicorn 是一个“WSGI”服务器。类似 WSGI 的好处是所有流行的 web 框架(Django、Flask、Pyramid)都符合 WSGI——这意味着任何 WSGI 服务器都可以使用这些框架来处理请求，不会有任何问题。

将我们的服务器转变成有效的 WSGI 服务器实际上并不难；如果你想了解更多，我推荐[这篇博文](https://ruslanspivak.com/lsbaws-part2/)，它教会了我很多关于 python 服务器的知识。

# 松散的末端

我之前提到了并发性，但这是一个大问题。它本质上包括通过`os.fork`复制套接字过程，并处理该决定的所有含义。那是另一个时间的故事，但是你可以在这里阅读更多。

很难想象像 HTTP 这样建立在 sockets 这样的简单架构之上的简单协议能够创造出广阔的网络景观，但是我们做到了。套接字还可以做的不仅仅是 web:这个简单的抽象足够强大，可以控制我们如何通过蓝牙、USB、HTTP 或者您将来设计的一些应用程序进行通信。软件之所以如此强大，是因为有多少个这样简单的部分组合在一起，可以创建一个丰富而复杂的塑造世界的应用程序生态系统。

**以下是我们所学内容的回顾**

*   套接字是用于多种目的的通信抽象
*   套接字是操作系统级的抽象，但是我们可以使用 python 的**套接字**库来访问它们
*   一个**监听** **套接字**存储&管理一个连接请求队列
*   `socket.accept()`接受其中一个请求并打开一个新的**连接的套接字**
*   一个**连接的套接字**使数据能够在客户端&服务器之间来回发送
*   HTTP 服务器是一个监听套接字，它快速接受传入的请求，并打开新的套接字来处理这些请求。连接套接字返回 HTTP 响应，并关闭连接。

*巨大的呐喊以鲁斯兰的*[](https://ruslanspivak.com/lsbaws-part1/)**博文为灵感**

*如果你想看更多这样的作品，请留言告诉我！感谢阅读。*
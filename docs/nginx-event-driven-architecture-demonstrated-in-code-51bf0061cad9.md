# Nginx 事件驱动架构——代码演示

> 原文：<https://levelup.gitconnected.com/nginx-event-driven-architecture-demonstrated-in-code-51bf0061cad9>

![](img/191ca4a5e5a789257da44795d45a10bd.png)

资料来源:nginx.com

## **背景**

Nginx 是一个开源的 web 服务器，可以用作备用代理、负载平衡器、HTTP 缓存和邮件代理。它能够支持大量的并发连接，这是它的旗舰特性之一。Nginx 网站上有一篇博文[1]解释了其出色的可扩展性背后的事件驱动架构。虽然这是一个很好的深入探索，但核心工程师可能仍然感到不满意，因为我们总是在寻找涉及实际代码的理解水平。

Nginx 的源代码虽然可以公开获得，但阅读起来很有挑战性，因为它巨大的尺寸和单薄的注释。所以为了演示核心架构，我用 C 写了一个简单的服务器，复制了 Nginx 的事件驱动设计。我的代码在 Github [2]上。请随意检查回购和服务器周围玩。在这篇博文中，我将从实现的角度带你了解事件驱动架构。

## **准备好插座**

首先，我们需要创建一个套接字，将它绑定到一个网络地址和端口，并开始监听套接字。一系列的`socket`、`bind`和`listen`系统调用是如此的标准，以至于几乎所有的 web 服务器都从它们开始。Nginx 也不例外。有一点需要注意的是，我们希望将监听套接字设置为非阻塞的——原因很快就会清楚。

## **事件循环**

下一步是 Nginx 与其他 web 服务器(如 Apache)的区别。当客户端连接进来时，在调用`accept`之后，可以产生一个新的线程来处理请求。这使得教科书中的并发请求处理成为可能。我记得我在学校也是这样被教导的。然而，在真实的生产环境中，这并不是最具可伸缩性的方式。线程虽然比进程轻，但在大多数现代操作系统中却太重了。当我们有成百上千个线程，每个线程代表一个客户端连接时，开销是巨大的。相反，规范的方法是使用事件循环。

不同的操作系统有不同的事件循环接口(例如，`select`、`epoll`、`epoll_wait`、`evpoll`、`kqueue`)。但它们都是一样的。实际上，应用程序要求操作系统监控一组文件描述符，并在一个或多个文件描述符发生 IO 事件时进行报告。因此，我们的服务器将监听套接字放入事件结构，并在循环中调用`epoll_wait`(我使用的是 linux ),而不是阻塞`accept`直到有客户端连接。

```
int efd = epoll_create1 (0);
// Call register_fd() to register the
// listening socket to the event
// struct represented by efd.
// See the snippet below.
for ;; {
  int n = epoll_wait(
    efd,
    events /* Empty events that will be filled on return */,
    EVENTS_LEN,
    1000 /* timeout in ms */
  );
  // Error check.
  for (int i = 0 ; i < n; i ++) {
    // Process events[i]. It has IO updates.
  }
}
```

当监听套接字的 IO 事件触发时，一个新的客户端连接可用于`accept`。然后我们`accept`连接，并将从`accept`获得的客户端套接字描述符放回事件循环。当客户端发送数据时，客户端套接字描述符的事件将被触发，我们将在下一轮`epoll_wait`中捕捉到这个事件。随着我们`accept`新的连接，我们将在事件观察列表中不断积累客户端套接字描述符，直到我们完成它们，在这种情况下，我们关闭客户端套接字描述符并停止轮询它们的 IO 事件。

对于一个`epoll_wait`调用，可能会返回来自多个套接字(监听套接字和客户端套接字)的 IO 事件。这很好，也在意料之中。我们只是一个接一个地回应。这是最关键的部分。我们不会仅仅为了处理 IO 事件而产生新的线程。我们以单线程的方式依次浏览它们。显然，我们需要将所有的套接字描述符设置为非阻塞的，这样单个线程就不会被特定的 IO 通道阻塞。这样，我们避免了创建数千个线程，然后只让它们阻塞等待 IO。这种模式在现代 web 架构中被广泛采用，因为单线程现在通常足够强大，可以进行多路复用。事实证明，这通常比拥有多个线程要高效得多。Redis 也在利用完全相同的模式[3]。

注意一个细节:在监听插座上设置电平触发(`EPOLLIN`)而不是边沿触发(`EPOLLET`)很重要。

```
void register_fd(int efd, int fd) {
  struct epoll_event event;
  event.data.fd = fd;
  event.events = EPOLLIN;
  epoll_ctl(efd, EPOLL_CTL_ADD, fd, &event);
  // Error check.
}
```

Edge-trigger 只为更改触发事件。当`epoll_wait`返回时，可能会有多个客户端连接在监听队列中。如果没有新的客户端连接，即使队列中有旧的客户端连接，`epoll_wait`也不会为 edge-trigger 返回事件。人们可以将`accept`包装在一个嵌套的循环中，试图耗尽现有的客户端连接，但是这会导致更加复杂和微妙的竞争情况(例如，太多连续的客户端连接；客户端连接在`accept`完成之后、下一个`epoll_wait`之前进入。同样的原理也适用于客户端套接字。

## **多流程**

到目前为止，所有的逻辑都在一个线程上运行。我们还没有利用大多数现代机器中的多核。为此，我们可以(Nginx 也这样做)`fork`在初始服务器套接字上调用`listen`之后。

监听套接字可以在多个进程之间共享。当连接事件在监听套接字上触发时，所有调用`epoll_wait`的进程都会知道。他们都会冲向`accept`。它们中只有一个能够从监听队列中获取连接——操作系统保证了这一点。`accept`将向其他进程返回`AGAIN`或`EWOULDBLOCK`错误。这些进程将知道所有的客户端连接在这一轮都被接受了。通过一些特殊的标志控制，一些操作系统足够聪明，可以只通知一个进程。某些操作系统支持跨进程分割监听套接字，并且只唤醒那些分配了新连接事件的进程。从操作系统的角度来看，这样效率更高。但是不利的一面是，如果一个特定的进程很慢，所有分配给它的连接事件都必须等待，并且不能被其他进程获得。

## **不要阻塞事件循环**

我们的服务器处理事件循环中的所有业务逻辑。这很好，因为我们的演示客户机只是向服务器发送“hello world”消息，服务器读取并打印出这些消息。许多现实世界的系统具有复杂的逻辑，当在单线程框架中处理时，可能会停止事件循环。例如，Node.js 在接受连接后将繁重的工作卸载到一个单独的线程池[4]。Golang 对轻量级线程——Go 例程(我们可以创建任意多的线程)提供了本机支持，它将由几个核心底层操作系统线程执行。

> **参考文献**

[1][https://www . nginx . com/blog/inside-nginx-how-we-designed-for-performance-scale/](https://www.nginx.com/blog/inside-nginx-how-we-designed-for-performance-scale/)

[2]https://github.com/eileen-code4fun/NginxEventLoop

[3][https://medium . com/swlh/use-the-source-redis-internal-tricks-5a 8b 735 B9 ef 0](https://medium.com/swlh/use-the-source-redis-internal-tricks-5a8b735b9ef0)

[4][https://nodejs . org/en/docs/guides/don-block-the-event-loop/](https://nodejs.org/en/docs/guides/dont-block-the-event-loop/)
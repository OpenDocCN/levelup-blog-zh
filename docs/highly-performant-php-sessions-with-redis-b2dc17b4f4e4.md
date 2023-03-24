# Redis 的高性能 PHP 会话

> 原文：<https://levelup.gitconnected.com/highly-performant-php-sessions-with-redis-b2dc17b4f4e4>

![](img/e68dd8500d76d11220d9d722173bf764.png)

[绿色变色龙](https://unsplash.com/@craftedbygc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/writing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

网络是无状态的，但我们构建的应用程序却不是。为了方便 web 应用程序中的状态，PHP 提供了一种会话处理机制。默认情况下，会话是关闭的，通过“session_start()”函数启用。

在 https://www.php.net/manual/en/book.session.php 的[阅读更多关于 PHP 会话处理的信息](https://www.php.net/manual/en/book.session.php)

如果您以前使用过 PHP 会话，您可能已经注意到由“session_start()”函数引起的性能问题。在小范围内，你的应用可能还可以，但是当你扩大规模时，问题就会出现。特别是，如果应用程序在页面加载期间对服务器进行几次 ajax 调用，就会出现问题。这是因为 PHP 将其会话作为文件存储在驱动器上。在每个请求期间，PHP 打开会话文件进行读写。文件在请求过程中被锁定。这意味着如果有三个 ajax 请求发送到服务器，请求二和请求三将被阻塞，等待前面的请求。

举例来说，假设 web 服务器对单个请求的响应时间为 300 毫秒，我们有一个初始请求，其中有三个异步运行的 ajax 请求。

加载时间应该是:
*300 ms 初始请求+ 300 ms ajax 请求= 600 ms*

当使用 PHP 的默认会话时，加载时间变成:
*300 ms 初始请求+ 300 ms ajax 请求一+ 300 ms ajax 请求二+ 300 ms ajax 请求三= 1,200 ms 或大约 1.2 秒！*

这是一个简单的例子。对于带有内置 API 的内容系统(如 WordPress ),在单个页面加载期间向服务器发送许多请求已经成为惯例。

为了解决文件锁定问题，我们必须改变会话存储机制。PHP 提供了编写[定制会话处理程序](https://www.php.net/manual/en/session.customhandler.php)的能力。我们可以这样做，或者我们可以接入 Redis 会话处理程序。Redis 没有锁定问题，并且已经被设置为高度可伸缩的。基于文件的会话充其量是可伸缩的。

要让它运行，我们需要做的就是更新 php.ini 文件中的两行，以指示 php 使用 Redis 以及在哪里可以找到 Redis 服务器。在本文中，我不会讨论如何设置 Redis 服务器。

打开 php.ini 文件，添加或更新以下两个配置值:

*   会话.保存处理程序
*   会话.保存路径

session.save_path 值指示 PHP 在哪里找到 Redis 服务器。

例如，配置值可能如下所示:

```
session.save_handler = redis
session.save_path = tcp://1.2.3.4:6379
```

重启 PHP 服务，您将看到会话存储在 Redis 中，而不是文件系统中。

如果您无法访问 php.ini 文件，请不要放弃希望！也可以在应用程序运行时使用以下 PHP 代码设置这些值:

```
ini_set('session.save_handler', 'redis');
ini_set('session.save_path', 'tcp://1.2.3.4:6379');
```

确保这两行在调用 session_start()函数之前运行。

根据服务器配置，也可以在. htaccess 文件中进行设置。

恭喜你！您的用户现在将体验到相当大的性能提升，并且您已经朝着构建一个高度可伸缩和高性能的站点又迈进了一步。
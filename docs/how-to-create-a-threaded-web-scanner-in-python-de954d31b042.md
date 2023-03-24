# 如何用 Python 创建线程化 Web 扫描器

> 原文：<https://levelup.gitconnected.com/how-to-create-a-threaded-web-scanner-in-python-de954d31b042>

## 一个简单的项目，大约 100 行代码就可以完成。

Python 的伟大之处在于，它让开发人员的生活变得简单——导入几个库来为您完成困难的工作，然后您就可以开始比赛了。这适用于创建能够进行多个并发请求的线程化 web 扫描器——使用 Python 很容易在很短的开发时间内完成。

![](img/d0983e50002ceb276a909e43c0e0d420.png)

在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [amirali mirhashemian](https://unsplash.com/@amir_v_ali?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

在这篇文章中，我将解释如何用 Python 创建一个使用 [urllib3](https://urllib3.readthedocs.io/en/latest/) 的线程 web 扫描器，这是一个强大的线程安全 HTTP 客户端，可以通过 pip 安装。在这里，我将主要关注这个库的用法以及如何实现线程。像参数解析和 IO 这样普通的方面可以在我将在底部分享的完整脚本中看到。所有这些都可以在大约 100 行内完成，这使得它成为一个快速完成的项目。

该脚本将接受一个参数或主机列表，加上一个要搜索的路径列表，然后输出基于目标 [HTTP 状态代码](https://www.restapitutorial.com/httpstatuscodes.html)找到匹配的结果。这使得查看给定站点上存在(HTTP 200)或缺少(HTTP 404)什么文件和文件夹变得容易。这有助于在迁移网站后或作为日常维护的一部分来检测断开的链接和丢失的资源。

Web 扫描也有安全应用程序，其中扫描器可用于检测公众不应访问的资源，执行应用程序指纹识别，甚至通过向端点发出多个带参数的请求来检测像 [SQL 注入](https://www.w3schools.com/sql/sql_injection.asp)这样的事情，直到抛出 HTTP 500，通过触发错误来指示成功的注入。

基本上，如果您想要一种自动化且高效的方式来对 web 服务器执行操作，这就是您的起点。

# 管理连接

在 urllib3 中，到单个主机的连接由一个[连接池](https://urllib3.readthedocs.io/en/1.5/pools.html)管理。多个池由一个[池管理器](https://urllib3.readthedocs.io/en/1.5/managers.html)管理。这些较高级别的抽象让您可以提供传递给较低级别的 kwargs，从而使整个堆栈易于从 PoolManager 级别进行实例化。控制并发性、超时、标头、代理配置等的参数。都可以在一个地方完成。如果您想深入了解可以配置的内容或了解特定参数的作用，请参考[文档](https://urllib3.readthedocs.io/en/latest/reference/index.html)。

我将使用一个名为`THREADS`的常量来指定可以同时活动的池`num_pools`和同时连接`maxsize`的(最大)数量。我还设置了`block=True`，这将导致这些限制在线程场景中被全局强制执行。这样做可以有效地定义可以同时处于活动状态的最大并发连接数。这将使我们的扫描器高效，无论它是向十台主机发出一个请求，还是向一台主机发出十个请求，或者是两者的大倍数(不会导致泛滥)。

让我们看看到目前为止我们在代码中讨论了什么:

我们现在已经配置了我们的`http`池管理器，这样它将自我管理到多个主机的并发连接，我们还设置了超时，允许每次尝试连接重试一次，并指定我们不希望它跟随重定向。我们将通过这个池开始我们所有的连接。

# 提出请求

提出请求相当简单。我将把逻辑放在一个简单的函数中，该函数接受一个 URL 并返回 URL 的元组和结果状态代码。我们将在线程化时调用这个函数:

首先，我对异常处理非常宽容。请求可能会因为主机关闭、TLS 错误*、超时等而失败。随意实现 urllib3 拥有的许多异常类型。您还可以采用一种更通用的方法[,这种方法仍然可以提供一个健壮的错误输出。在开始扫描以忽略任何无法到达的主机之前，检查所有目标主机是否都已启动也是有意义的。您也可以在出现一定数量的错误后放弃扫描主机——只要确保您以](https://stackoverflow.com/questions/9823936/python-how-do-i-know-what-type-of-exception-occurred)[线程安全的方式](http://effbot.org/pyfaq/what-kinds-of-global-value-mutation-are-thread-safe.htm)实现这一点。

其次，请注意，我正在使用`functools`将`flush=True`参数分配给脚本中所有的`print()`调用。这是为了有效地关闭输出缓冲，以便我们在线程化环境中看到扫描的实时进度。

总而言之，一个简单的函数——使用`http` PoolManager 来处理我们的连接，捕捉一些错误，然后返回结果。现在是并行运行的时候了。

**如果您特别想忽略证书错误，请查看*[](https://stackoverflow.com/questions/18061640/ignore-certificate-validation-with-urllib3)**`*urllib3.disable_warnings()*`*，并注意，由于 urllib3 处理 kwargs 的方式，您可能需要将 HTTP 和 HTTPS 连接分成两个不同的池。***

# **穿线**

**让我们直接进入代码:**

**我喜欢 Python。那不是很容易吗？一次导入，三行代码，我们从上面并行运行我们的`request()`函数。幸运的是，urllib3 足够智能，可以根据我们初始化它的方式来控制和管理来自线程的 web 请求。注意，上面的`THREADS`常量在这里再次被用来设置工人计数。`executor.map`有效地为`urls`列表中的每个条目调用`request()`，并产生一个生成器，该生成器将输出保留调用顺序的结果。最后一行将导致我们的脚本等待线程完成后再继续。**

**应该注意的是，如果试图通过 ctrl+c 提前中止扫描，脚本将有效地挂起，直到完成和/或您终止相关的进程。这个[可以搞定](https://stackoverflow.com/questions/29177490/how-do-you-kill-futures-once-they-have-started)，但是 TL；它的缺点是有点尴尬，并且会涉及一些重构——我选择不麻烦。**

# **将它投入使用**

**首先，你如何知道这是否正常工作。它实际上是在并行运行`THREADS`个连接吗？我做的是启动一个简单的 PHP 服务器，上面有一个`sleep.php`文件，它会休眠 5 秒钟然后返回。给定`request()`中的`print()`调试语句，很容易在扫描运行时跟踪进度，调整一些变量，并实时查看发生了什么。您可以通过为`example0.com, example1.com… example9.com`创建本地 DNS 条目来模拟多个主机，所有条目都指向一个本地主机服务器，这样您就不会以调试流量淹没一个活动站点而告终。您还可以通过多次请求同一个睡眠文件，在单个示例主机上测试线程。**

**下面是一个完整的脚本扫描 10 个“主机”的例子，每个主机扫描两个文件，一个不存在，另一个休眠 5 秒。它被设置为只报告找到的文件(即 HTTP 200，省略 HTTP 404):**

```
**$ time python pywebscan.py hosts.txt paths.txt
Scanning 10 host(s) for 2 path(s) - 20 requests total...------ REQUESTS ------[http://example0.com/pywebscan-test/does_not_exist.txt](http://example0.com/pywebscan-test/does_not_exist.txt) 404
[http://example2.com/pywebscan-test/does_not_exist.txt](http://example2.com/pywebscan-test/does_not_exist.txt) 404
...
[http://example0.com/pywebscan-test/sleep.php](http://example0.com/pywebscan-test/sleep.php) 200
[http://example3.com/pywebscan-test/sleep.php](http://example3.com/pywebscan-test/sleep.php) 200
...------ RESULTS ------[http://example0.com/pywebscan-test/](http://example0.com/pywebscan-test/)
---
[http://example0.com/pywebscan-test/sleep.php](http://example0.com/pywebscan-test/sleep.php) 200...[http://example9.com/pywebscan-test/](http://example9.com/pywebscan-test/)
---
[http://example9.com/pywebscan-test/sleep.php](http://example9.com/pywebscan-test/sleep.php) 200------ SCAN COMPLETE ------real    0m5.193s
user    0m0.000s
sys     0m0.000s**
```

**整个过程只用了 5 秒多一点的时间就完成了，您会注意到请求响应并不是基于主机名的顺序响应，所以线程正在工作。由于睡眠，如果连续进行，可能需要 50 多秒。将我们的`THREADS` const 设置为 1 证实了这一点:**

```
**$ time python pywebscan.py hosts.txt paths.txt
Scanning 10 host(s) for 2 path(s) - 20 requests total......------ SCAN COMPLETE ------real    0m50.309s
user    0m0.000s
sys     0m0.015s**
```

**概念得到验证。请随意测试其他场景(如大量请求和各种请求组合)，以确保您的实现一切正常。**

# **完整的脚本**

**这里可以找到[。它很简单，只有两个参数——主机或主机文件，外加一个路径文件。线程计数、超时等参数。只是硬编码到脚本中——很容易根据需要转换成 CLI 参数。有一些逻辑来处理主机和路径解析、输出格式化，并没有涉及太多其他内容。根据需要精简和适应。](https://github.com/kld87/pywebscan/blob/master/pywebscan.py)**

# **后续步骤和高级用法**

**到目前为止，我已经提到了几个可以改进的地方:更好的错误处理，优雅的中止行为，以及更多通过参数定制的功能。以下是需要考虑的其他几点:**

1.  **处理和跟踪重定向——为了简单起见，我已经把它们关掉了。许多水疗应用程序可能会使用。htaccess 将所有“未找到”的请求通过管道传输到单个入口点，这在扫描期间允许重定向时会导致一些奇怪的结果。重定向也可以用于将 HTTP 流量发送到 HTTPS，并且有多种类型的重定向和重写类型可以使用。对于您的用例，请记住这一点。**
2.  **Python 使得反向 dns 查找[变得相当容易。将 IP 转换成主机名。如果您使用 IP 列表，尤其是涉及虚拟主机和/或 HTTPS 时，这可能会很有用。](http://searchsignals.com/tutorials/reverse-dns-lookup/#pythonic-reverse-ip-lookups)**
3.  **`request()`函数获得整个响应，而不仅仅是状态代码。你可以解析它，搜索内容，提取链接等等。**
4.  **您可以将 urllib3 配置为[使用代理](https://urllib3.readthedocs.io/en/latest/advanced-usage.html#proxies)，如果需要，还可以指定用户代理。**
5.  **您可以调整`THREADS`参数来增加并发连接的数量——只是在这样做的时候要注意套接字/资源的使用——并且尽量不要一次将太多的连接发送到一台主机。**

# **包扎**

**现在你有了它——一个线程化的 Python web 扫描器，大约有 100 行代码，其中大部分是用来处理执行流的。无论您的使用情形是什么，它都是通用的并且易于扩展。我们已经看到 [urllib3](https://urllib3.readthedocs.io/en/latest/) 非常强大而且易于使用，Python 的 [ThreadPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#concurrent.futures.ThreadPoolExecutor) 使线程化变得轻而易举。如果你对 Python 中的并行性感兴趣，我推荐你阅读这篇文章，这篇文章从较高的层面对理论和不同的方法进行了分解。**

**如果你喜欢这篇文章，我最近写了一篇类似的文章，关于如何[创建一个 DIY 的 web scraper](/how-to-build-a-diy-web-scraper-in-any-language-1104ac0713cd) 来抓取和提取你可能会发现有用的网站信息。**
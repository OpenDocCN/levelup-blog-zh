# 作为反向代理的 Apache 2.4

> 原文：<https://levelup.gitconnected.com/configuring-apache-2-4-as-a-reverse-proxy-af7db55860a8>

## 将 Apache web 服务器配置为反向代理

![](img/0099dd9c8a5fc445878cdb39003dc465.png)

德文·艾弗里在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

那么，我们为什么要使用反向代理呢？

*   安全合规性阻止对后端服务器的直接访问
*   负载平衡传入的 HTTP 请求
*   通过监控和记录流量来提高安全性
*   SSL 卸载避免在后端服务器上安装证书
*   代表后端服务器提供静态内容
*   URL 重写，然后再转到后端服务器
*   将不同的网站合并成一个 URL 空间

本教程是[您还可以确认模块是否正确加载。](https://medium.com/p/baab2be02542# service apache restart</span></pre><p id=)

```
# /opt/SP/apache-2.4/bin/apachectl -M | grep proxy
**proxy**_module (shared)
**proxy**_http_module (shared)
```

[下一步将会失败，因为 web 服务器仍然在监听 **HTTP 端口 80** ，但是我们接下来将会更改它，并确认它能按预期工作。](https://medium.com/p/baab2be02542# service apache restart</span></pre><p id=)

```
# curl http://127.0.0.20:8080
^C
```

第一个重要的步骤是**确保防火墙规则打开**并调整为允许 **HTTP 端口 8080** 连接到 web 服务器。如果你使用的是 Amazon EC2，找到保护你的 web 服务器的**安全组**，并确保 **HTTP 端口 8080** 从你的反向代理打开。您可能需要包括私有 IP **10.0.0.20** 和弹性 IP(如果您分配了一个的话)。

在 web 服务器上，编辑**/opt/SP/Apache-2.4/conf**中的 **httpd.conf** 文件，并更新以下行:

```
Listen 8080
```

为了演示反向代理功能，我们希望使用一个独立于反向代理的端口，但它们不必不同。在这种情况下，反向代理将使用 **HTTP 端口 80** ，web 服务器将使用 **HTTP 端口 8080** 。

您需要重新启动 Apache 以使更改生效。

```
# service apache restart
```

您可以确认 web 服务器是这样工作的:

```
# curl [http://localhost:8080](http://localhost:8080)
<html><body><h1>It works!</h1></body></html>
```

现在，返回反向代理，并确认它也在那里工作:

```
# curl [http://10.0.0.20:8080](http://localhost:8080)
<html><body><h1>It works!</h1></body></html>
```

现在来看看您是否将一个 **HTTP port 80** 请求定向到反向代理，这可行吗？

```
# curl [http://10.0.0.1](http://localhost:8080)0
<html><body><h1>It works!</h1></body></html>
```

是的，它是！你可能会想，反向代理和 web 服务器都在 **HTTP 端口 80** 上有标准构建列表，为同一个**index.html**文件提供服务，那么你怎么知道它已经加载了正确的文件，这就足够公平了。

我在 web 服务器上编辑了**/var/SP/httpd/htdocs/index . html**文件，改成了“**它工作了！**【to】**app 好用！**”并再次运行测试。

```
# curl [http://10.0.0.1](http://localhost:8080)0
<html><body><h1>The app works!</h1></body></html>
```

所以你有它。您可以将一个 **HTTP 端口 80** 请求定向到反向代理，反向代理将该请求定向到 **HTTP 端口 8080** 上的 web 服务器。

如果您有任何意外的问题，您可以在以下位置找到反向代理和 web 服务器上的日志文件:

```
/opt/SP/apache-2.4/logs/access_log
/opt/SP/apache-2.4/logs/error_log
```

# 迈克尔·惠特尔

*   ***如果你喜欢这个，请*** [***跟我上媒***](https://whittle.medium.com/)
*   ***更多有趣的文章，请*** [***关注我的刊物***](https://medium.com/trading-data-analysis)
*   ***有兴趣合作吗？*** [***让我们连线 LinkedIn 上的***](https://www.linkedin.com/in/miwhittle/)
*   ***支持我和其他媒体作者*** [***在此报名***](https://whittle.medium.com/membership)
*   ***请别忘了为文章鼓掌:)←谢谢！***
# 节点中的多域 SSL。带有 Certbot 的 JS

> 原文：<https://levelup.gitconnected.com/multi-domain-ssl-in-node-js-with-certbot-8f0df5331d47>

## 从一个简单的 Node.js 应用程序通过 HTTPS 服务多个网站。

![](img/c142e19a9cea3eb7333e203524a9ce8a.png)

节点中的多域 SSL。JS 本文插图作者[金彩云](https://medium.com/u/ab43c160e8e6?source=post_page-----8f0df5331d47--------------------------------)

使用 Node.js 为 web 应用程序或 API 提供服务既简单又容易。最重要的是，您需要一个解决方案来获得 SSL 证书，使您的 web 或 API 可以通过 *HTTPS* 访问。好消息是，您可以从单个 Node.js 应用程序为不同目的的多个域提供服务。这将帮助你节省精力、时间和金钱。即使您只想为单个域创建 SSL，也可以遵循本指南。

## 所以，让我们开始吧！

# 步骤 1:创建一个简单的 Node.js-Express 服务器⚡

*如果您已经有了现有的 Node.js 应用程序，请转到步骤 2。*

浏览到您的项目文件夹，并使用`npm init`命令启动 Node.js 应用程序。然后，用`npm install express`命令安装 express 模块。

```
**$** **npm init
$ npm install express**
```

# 步骤 2:安装 Certbot🤖

*如果您已经拥有包含所有证书的 certbot，请转到步骤 3。*

在这一步中，我们将在您的服务器上安装 certbot。在此之前，请确保您已经在服务器上安装并更新了 Python。以下命令将把 certbot 正确安装到您的系统中。

```
**$ sudo apt-get update
$ sudo apt-get install software-properties-common
$ sudo add-apt-repository universe
$ sudo add-apt-repository ppa:certbot/certbot
$ sudo apt-get update
$ sudo apt-get install certbot**
```

# 步骤 3:为您的域生成 SLL 证书🔒

现在，有了 certbot，就可以为您的域生成证书了。

```
**$ sudo certbot certonly --manual** 
```

运行该命令后，将提示您输入您的电子邮件，接受服务条款，输入您的域名(不含 [www。)，](http://www.),)随后是您必须在"/送达文件的验证。已知/acme-challenge/<xxxx>"包含服务器上的质询字符串。在这个验证步骤中，请保持 shell 窗口打开，并在服务器上托管完 acme-file 后返回。您可以通过让您的 express 应用程序通过 *HTTP* 提供静态文件来做到这一点，如下面的 server.js 示例所示。

然后，回到您的 shell 并按 enter 键。您将得到确认，您的域的认证。

对于要在 node.js 应用程序中托管的所有域名，您可以多次重复此步骤

# 步骤 4:为多域 SSL 设置您的 Express 应用程序。⛅

简而言之，只需转到你的 server.js 文件并使用`https`模块启动 *HTTPS* 服务器。要设置许多域，只需使用`addContext()`函数向 Node.js 应用程序添加额外的域。您可以按照我在下面创建的示例脚本来激活多域 SSL。

**多域 SSL 快速应用的 Node.js 示例。**(作者)

如果你使用上面的脚本，别忘了把 domain1.com、domain2.com 和你的域名分别改成。

如果您一直坚持到这一点，您将能够使用 SSL 托管多个域。尽管如此，此时从所有域返回给客户机的内容将是相同的。因此，您可以向 Node.js 应用程序添加一些规则，根据请求主机名返回不同的内容，如下面的脚本所示。

**示例 Express 规则根据请求主机名返回不同的响应。**(作者)

恭喜你！大概就是这样。

我希望你喜欢这篇文章，并发现它对你的日常工作或项目有用。如果您有任何问题或意见，请随时给我留言。

关于我&查看我所有的博客内容:[链接](https://joets.medium.com/about-me-table-of-content-bc775e4f9dde)

安全**和健康**吗**！💪**

**感谢您的阅读。📚**

[![](img/a6077d45ce05991c2da29e83848923e8.png)](https://medium.com/@JoeTS)

[https://medium.com/@JoeTS](https://medium.com/@JoeTS)
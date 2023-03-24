# 如何将 HTTPS 流量代理到您的开发服务器

> 原文：<https://levelup.gitconnected.com/how-to-proxy-https-traffic-to-your-development-server-63c9980d5899>

启用临时 SSL 支持的单个命令

![](img/0858fdf93075330c14d8b761209c0d49.png)

照片由[飞:D](https://unsplash.com/@flyd2069?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/security?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

通过阅读这篇文章，您将了解到只需一个命令就可以将 HTTPS 流量代理到任何现有的 HTTP 服务器。这很方便，尤其是当你在一个远程机器上开发一个只在安全环境下工作的原型时(HTTPS)。

假设您正在构建一个在前端应用程序中使用`MediaDevices.getUserMedia()`的流媒体平台。只有当且仅当应用程序通过 HTTPS 提供服务或通过本地主机访问时，此功能才起作用。对于位于远程机器上的开发服务器，您可以利用`[ssl-proxy](https://github.com/suyashkumar/ssl-proxy)`作为临时解决方案，并将剩余的精力和时间用于开发。

根据官方文献记载，`[ssl-proxy](https://github.com/suyashkumar/ssl-proxy)`是一个

> “…将 SSL 添加到在虚拟机上运行的东西的方便而简单的方法—无论是您的个人 jupyter 笔记本电脑还是您的团队 jenkins 实例。ssl 代理在一个命令中自动生成 SSL 证书，并将 HTTPS 流量代理到现有的 HTTP 服务器。”

请注意，这种方法非常适合远程测试您的开发服务器。对于生产级应用程序，您应该使用标准的部署方法。这包括:

*   运行在 Nginx 之后
*   跑在 CDN 后面
*   使用由[生成的证书和私钥运行，让我们加密](https://letsencrypt.org/)。

让我们继续下一节的设置和安装。

# 设置

您可以选择:

*   根据您的操作系统下载预构建的二进制文件
*   从源代码构建包

## 预建二进制

您可以根据您的操作系统从[官方存储库](https://github.com/suyashkumar/ssl-proxy/releases/)下载预构建的二进制文件:

*   [ssl-proxy-darwin-amd64.tar.gz](https://github.com/suyashkumar/ssl-proxy/releases/download/v0.2.6/ssl-proxy-darwin-amd64.tar.gz)
*   [ssl-proxy-linux-amd64.tar.gz](https://github.com/suyashkumar/ssl-proxy/releases/download/v0.2.6/ssl-proxy-linux-amd64.tar.gz)
*   [SSL-proxy-windows-amd64 . exe . zip](https://github.com/suyashkumar/ssl-proxy/releases/download/v0.2.6/ssl-proxy-windows-amd64.exe.zip)

只需解压缩文件，您就可以通过以下方式正常使用它:

```
./ssl-proxy
```

除此之外，您可以使用`wget`来获取基于您的操作系统的正确的二进制文件:

```
wget -qO- "[https://getbin.io/suyashkumar/ssl-proxy](https://getbin.io/suyashkumar/ssl-proxy)" | tar xvz
```

或者，`curl`命令可以用来直接拉取特定的二进制文件

```
curl -LJ "[https://getbin.io/suyashkumar/ssl-proxy?os=linux](https://getbin.io/suyashkumar/ssl-proxy?os=linux)" | tar xvz
```

它接受以下选项:

*   达尔文
*   窗子
*   Linux 操作系统

## 从源代码构建

如果您的操作系统中已经安装了`docker-compose`，您可以按照如下方式构建它:

```
docker-compose -f docker-compose.build.yml up
```

它将在当前工作目录下的`build`文件夹中创建一个新的二进制文件。

你可以选择用`make`和`dep`在本地建造。您所需要做的就是克隆存储库并运行`make`命令来构建它。

# 履行

一旦您的工作目录中有了`ssl-proxy`二进制文件，您就可以选择运行它:

*   自动自签名证书
*   让我们加密 SSL 证书

## 自签名证书

假设在当前工作目录中有 Linux 预构建的二进制文件，运行以下命令开始代理 HTTPS 流量:

```
./ssl-proxy-linux-amd64 -from 0.0.0.0:4430 -to 127.0.0.1:8000
```

*   `from`:表示 HTTPS 路由为反向代理
*   `to`:指你的开发应用(服务器、jupyter 笔记本等)的 HTTP 路由。)

在这种情况下，它会将运行在端口`4430`上的外部 URL 代理到运行在端口`8000`上的网络 URL。

只需根据自己的需求修改网址即可。您可以省略`http/https`前缀，因为它将默认为以下内容:

*   `from` : https
*   `to` : http

在初始运行期间，它将在当前目录中生成一个自签名证书和一个私钥:

*   cert.pem
*   key.pem

它将在后续运行中重复使用相同的文件。

此外，您还可以通过以下参数提供自己的证书和私钥:

*   确实的事情
*   键

例如:

```
./ssl-proxy-linux-amd64 -cert cert.pem -key key.pem -from 0.0.0.0:4430 -to 127.0.0.1:8000
```

## 让我们加密 SSL 证书

通常，您不应该使用此工具在多个服务器或生产服务器上进行负载平衡。如果需要临时 SSL 支持，请确保您有以下物品:

*   为您的应用程序保护一个域(运行并可公开访问)
*   可以绑定到端口 443

完成后，您可以运行以下命令来获取和提供加密证书(假设您的域位于`mydomain.com`):

```
./ssl-proxy-linux-amd64 -from 0.0.0.0:443 -to 127.0.0.1:8000 -domain=mydomain.com
```

# 试验

假设您正在运行以下命令，通过自签名证书为您的应用程序提供服务:

```
./ssl-proxy-linux-amd64 -from 0.0.0.0:4430 -to 127.0.0.1:8000
```

您应该在终端上看到以下输出:

```
Assuming -to URL is using http://
Proxying calls from https://0.0.0.0:4430 (SSL/TLS) to http://127.0.0.1:8080
...
```

然后，您可以通过(`hostname`指远程机器的公共 IP)正常访问您的应用程序:

```
https://<hostname>:4430
```

在初始运行期间，您应该会看到“`Your Connection is Not Secure`”用户界面。只需点击`Advanced`按钮，接受证书。如果一切顺利，应用程序应该完全在安全的环境中运行(HTTPS)。

# 结论

让我们回顾一下你今天所学的内容。

本文首先简要介绍了问题陈述以及如何使用`ssl-proxy`来解决问题。

之后，它继续一步一步的指导，以获得基于操作系统的可执行二进制文件。

接下来是实现部分，分为自签名证书和加密证书。

最后，它讲述了在浏览器上访问和测试您的应用程序。

感谢阅读。祝你有美好的一天！

# 参考

1.  [Github — ssl 代理](https://github.com/suyashkumar/ssl-proxy)
# 使用 Node.js 创建一个 Web 服务器来服务简单的 HTML

> 原文：<https://levelup.gitconnected.com/create-a-web-server-node-js-to-serve-simple-html-5d064133fcce>

## 通过从头开始构建一个 web 服务器来理解它是如何工作的。

![](img/9fd8c90120a63899d62eafe9a5afcbb9.png)

来源:[https://level up . git connected . com/introduction-to-nodejs-fb9 a6f 540 be 9](/introduction-to-nodejs-fb9a6f540be9)

Node.js 是什么？

*   Node.js 是一个开源的 web 服务器环境
*   它可以在浏览器之外运行 JavaScript。

在这篇文章中，让我们使用 Node.js 编写一个 web 服务器来服务简单的 HTML 文件。

## 要求:安装 Node.js

推荐 LTS 版本。

![](img/6e32b9c10933f1951d1fbd37422c26ba.png)

# 方法 1:使用内置的“http”模块创建服务器

## 步骤 1:创建要服务的 index.html

![](img/95041a6d153aa250494297d079da0cce.png)

## 步骤 2:使用 http 模块编写服务器代码

![](img/9e175817099aceff10045d2b6401a47b.png)

## 步骤 3:运行服务器

```
$ node server.js
```

然后您的`index.html`页面将在 URL:[http://localhost:8080](http://localhost:8080/)上可用

![](img/8086307e03770758f0cd8d2d7dccb52a.png)

我们的节点服务器可以根据不同的 HTTP 请求提供不同的 HTML 内容。所以我们可以这样写我们的服务器:

![](img/459151bf5c5ce9e916e970b30e4a3636.png)

结果如下:

![](img/cad3bb60efd798ee6a2b165850ad77ae.png)

本地主机:8080/

![](img/366f03ee1bc9227c184bb7ee094008f9.png)

本地主机:8080/关于

![](img/f3c2cbac33441e9e3f98935a768fed71.png)

未处理的 http 请求

# 方法 2:使用现有的服务器(http-server)

## 第一步:安装`http-server`

```
$ sudo npm install http-server$ cd node_modules/
```

![](img/c5a7b796fff72cf0819aa20f364e5759.png)

$ CD http-服务器

![](img/f5fd59fe3e81931c23b499ee54e71db0.png)

## 步骤 2:在目录:`node_modules/http-server/bin`下创建一个 HTML 文件

```
$ cd bin
$ vim index.html 
```

![](img/f81ffd6841c66bf548fac47a8d740d77.png)![](img/97e7edf463d361f3891629537c6fa4bf.png)

## 步骤 3:运行服务器

```
$ node http-server . /* run server in current directory*/
```

![](img/719575c4fdd99587a49f9a53b862bf43.png)

你可以通过以上三个地址访问 HTML 文件。例如:

![](img/ef8f7fb19c49f06f7e23022341ae70e3.png)

此外，您的计算机名作为主机名。就我而言:

```
$ hostname
```

![](img/526e143a3cd94757166ddf77f818ae74.png)![](img/1af47a6122bfe2bbad94912c710567f9.png)

# 摘要

除了 html 文件，节点 web 服务器还可以提供 JavaScript 代码。
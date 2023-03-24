# Node.js 基础—文件和 HTTP

> 原文：<https://levelup.gitconnected.com/node-js-basics-files-and-http-14cc2626cc07>

![](img/ea0a56b1b8afc5d51806a486d1c7843c.png)

[伊万·耶维奇](https://unsplash.com/@ivanjevtic?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

JavaScript 是世界上最流行的编程语言之一。为了有效地使用它，我们必须了解它的基本知识。

在本文中，我们将了解 Node.js 的基础知识，包括使用文件、创建 web 服务器和请求制作服务器。

# 文件系统模块

Node.js 标准库附带了`fs`模块，让我们可以操作文件系统上的项目。

例如，我们可以编写以下代码从计算机中读取文件:

```
const { readFile } = require("fs");
readFile("foo.txt", "utf8", (error, text) => {
  if (error) throw error;
  console.log(text);
});
```

我们从`fs`模块中导入了`readFile`方法。

然后我们用文件路径、编码调用它，用错误或文件内容回调它。

`error`遇到错误。

同样，我们使用`writeFile`方法将文件写入文件系统。

例如，我们可以写:

```
const { writeFile } = require("fs");
writeFile("bar.txt", "hello", (error) => {
  if (error) {
    throw error;
  }
});
```

我们传入文件路径、文件内容和一个在试图写入文件后调用的回调。

`error`遇到错误。

没有必要指定编码。

`writeFile`将假设当它把一个字符串写入一个文件时。

这些方法有一些很有前途的版本，可以使链接变得更容易。

例如，我们可以通过编写以下代码来使用`readFile`的 promise 版本:

```
const {readFile} = require("fs").promises;(async () => {
  const text = await readFile("foo.txt", "utf8");
  console.log(text);
})()
```

上面的代码需要`readFile`的 promise 版本。

然后，我们可以使用异步函数来读取文件，而不是使用回调。

还有一个同步版本的`readFile`叫做`readFileSync` /

例如，我们可以如下使用它:

```
const { readFileSync } = require("fs");
console.log(readFileSync("foo.txt", "utf8"));
```

`readFileSync`只返回文件内容，而不解析为值或用文件内容调用回调。

# HTTP 模块

Node.js 还附带了一个 HTTP 模块，我们可以用它来构建自己的 HTTP 服务器。

要创建一个基本的 HTTP 服务器，我们可以使用`createServer`方法:

```
const {createServer} = require("http");
const server = createServer((request, response) => {
  response.writeHead(200, {"Content-Type": "text/html"});
  response.write(`hello ${request.url}`);
  response.end();
});
server.listen(8000);
```

我们用具有`request`和`response`参数的回调函数调用了`createServer`。

它们分别包含请求和响应对象。

`response`对象可用于做出响应。

`request`包含 URL、正文、标题等内容。

一旦我们创建了服务器，我们就必须用一个端口来监听请求，以便客户端可以向它发出请求。

如果我们转到`localhost:8000`，应该会显示`hello /`。

`request.url`有相对网址。

`writeHead`写入响应报头。200 是状态码，代表 OK。

`write`写回复正文。

我们必须调用`end`来结束响应。

要使用 Node.js 应用程序向服务器发出请求，我们可以使用`request`方法来完成。

例如，我们可以写:

```
const { request } = require("http");
const requestStream = request({
  hostname: "example.com",
  path: "/",
  method: "GET",
  headers: { Accept: "text/html" }
}, response => {
  console.log(response.statusCode);
});
requestStream.end();
```

我们从`http`模块中获取`request`方法。

我们将一个对象传入带有`hostname`的`request`函数，这是主机名。

`path`具有相对于主机名的路径。

`method`是请求方法。

`headers`有我们想要设置的标题。

然后我们传入一个带有`response`参数的回调，该参数有响应。

`statusCode`有状态码。

在`https`模块中还有一个`request`函数，让我们发出 HTTPS 请求。

![](img/9dd066b0669e2f45980eaa4408f8403c.png)

菲尔·古德温在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 结论

我们可以通过使用`fs`模块来操作文件系统。

函数有承诺、回调和同步版本。

HTTP 模块让我们创建 web 服务器。

它还有一个发出请求的功能。

使用`request`模块，我们可以通过传递 URL、头部和主体来发出请求。

然后，我们可以通过传入回调来获得请求的响应。
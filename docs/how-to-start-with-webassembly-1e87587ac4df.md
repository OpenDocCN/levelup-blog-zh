# 如何开始使用 WebAssembly

> 原文：<https://levelup.gitconnected.com/how-to-start-with-webassembly-1e87587ac4df>

![W](img/d891666cc9ebd9231a574f9c9cb6a45e.png)WebAssembly(Wasm)是一种用于在网络上执行代码的低级二进制格式。它被设计成高效和快速的，并打算用作 C、C++和 Rust 等语言的编译目标，然后可以在 web 上以接近本机的性能运行。

WebAssembly 旨在作为 JavaScript 的补充，而不是替代。它可以在 web 应用程序中与 JavaScript 一起使用，以利用其性能优势，并运行在 web 上难以或不可能运行的代码。

WebAssembly 的主要优势之一是它可以在现代 web 浏览器中运行，而不需要插件或附加软件。它还被设计成可移植的，可以在多种设备和平台上运行。

WebAssembly 仍然是一项相对较新的技术，但它已经被用于各种应用程序，如游戏、多媒体应用程序、科学模拟等。它有可能显著提高 web 应用程序的性能和功能，并使在 web 上运行更复杂和要求更高的应用程序成为可能。

在本文中，我们将创建一个示例项目来帮助您使用 WebAssmbly。

这只是众多关于 JavaScript 和软件开发的文章中的一篇。欢迎关注我们，获取更多关于 JavaScript 和软件开发的精彩内容。我们尝试一周发布多次。请确保不要错过我们的任何精彩内容。

![](img/e0ddac8e17632a2b80fc4a5f71df959c.png)

苏珊·霍尔特·辛普森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这篇文章最初发表在我之前的博客上。

最近我想深入研究 Web 组装。我在网上找到的例子不管用，或者信息丢失。我会花几个小时去弄明白。为了节省你的时间，我创建了这个帖子。

我被[这个帖子](https://medium.freecodecamp.org/get-started-with-webassembly-using-only-14-lines-of-javascript-b37b6aaca1e4#--respond)所吸引(但是这个项目不会为我运行)。因此，我根据那篇文章创建了自己的博客。

文件结构将如下所示:

> wasmDemo
> | _ index . html
> | _ scripts . js
> | _ server . js
> | _ squarer . wasm

所以，所有东西都在一个没有子文件夹的目录中。

为了编写一个 C++程序，首先进入下面的页面:

[https://mbebenita.github.io/WasmExplorer/](https://mbebenita.github.io/WasmExplorer/)

让我们写一个平方程序:

```
int squarer((int i) {
 return i * i;
}
```

粘贴到上面的网站。然后编译它并下载`. wasm '文件。将该文件移到我们的文件夹“wasmDemo”中。

接下来创建一个 html 文件:

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>WASM Demo</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
  </head>
  <body>
    <h1>WASM Demo</h1>
    <script src="./scripts.js"></script>
  </body>
</html>
```

然后创建一个 js 文件:

```
let squarer;
function loadWebAssembly(fileName) {
  return fetch(fileName)
    .then(response => response.arrayBuffer())
    .then(buffer => WebAssembly.compile(buffer))
    .then(module => {
      return new WebAssembly.Instance(module);
  });
}
loadWebAssembly('./squarer.wasm').then(instance => {
  squarer = instance.exports._Z7squareri;
  console.log('Finished compiling! Ready when you are…');
});
```

因为我们必须在服务器上运行它，所以让我们为节点服务器创建一个 server.js 文件:

```
var http = require('http');
var fs = require('fs');
var path = require('path');
const PORT = 8080;
http
  .createServer(function(request, response) {
    var filePath = '.' + request.url;
    if (filePath == './') {
      filePath = './index.html';
    }
    var extname = String(path.extname(filePath)).toLowerCase();
    var mimeTypes = {
      '.html': 'text/html',
      '.js': 'text/javascript',
      '.css': 'text/css',
      '.wasm': 'application/wasm'
    };
    var contentType = mimeTypes[extname] || 'application/octet-stream';
    fs.readFile(filePath, function(error, content) {
      if (error) {
        response.writeHead(404);
        response.end('404 Not Found');
      } else {
        response.writeHead(200, { 'Content-Type': contentType });
        response.end(content, 'utf-8');
      }
    });
  })
  .listen(PORT);
```

如果一切正常，那么用“node server.js”启动应用程序

现在你可以访问 squarer 函数，我们用 C++写的，也可以在 js 文件或控制台中使用。

![](img/5c7fdb823e2c7f4190f716ff6bed224c.png)

就是这样。您刚刚创建了您的第一个 WebAssembly 应用程序。没那么难。尝试一下，让它成为你自己的，然后发布你创造的其他东西。我们很想知道你对此有何看法。

如果你喜欢这个教程，那么别忘了分享，鼓掌，关注我们。我们每周发表多篇文章。所以，不要错过任何一个。再见
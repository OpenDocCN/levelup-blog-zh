# 节点中的 HTTP/2。JS 核心

> 原文：<https://levelup.gitconnected.com/http-2-innode-js-core-91eb25e296e8>

![](img/e15c798b5d9cb4a51d5ee227fa49d6cc.png)

[http 2 的崛起| Daniela Matos de Carvalho | YLDio | empire js Conf 2017](https://youtu.be/yToHjxhCeYM)

HTTP/2 开始被越来越多地使用(它从今年年初的 11%跃升至网络总使用量的 18%)。如果你还记得我们上一篇关于 HTTP/2 的博客文章，比如 [HTTP/2:展望 web 的未来](https://blog.yld.io/2017/01/10/http-2-a-look-into-the-future-of-the-web)、[HTTP/2 的替代方案](https://blog.yld.io/2017/02/08/alternatives-to-http)或[用服务器推送和服务工作者进行优化](https://blog.yld.io/2017/03/01/optimize-with-http-2-server-push-and-service-workers)，你可能还记得 HTTP/2 协议中的一些细节以及与版本 1 的区别。如果你还没有的话，就去看看吧，因为我们不会在这篇博文中讨论 RFC 的细节。

在我们最新的 blogposts 中，我们提到 HTTP/2 的一个版本开始在 [nodejs/http2 repository](https://github.com/nodejs/http2) 下实现，但当时还没有决定它是要合并到 Node.js 核心模块中，还是作为一个外部模块。拥有一个新实现背后的主要原因是其他实现并不完全遵守 [HTTP/2 RFC](https://tools.ietf.org/html/rfc7540) 。

最终，提交了一个[拉请求](https://github.com/nodejs/node/pull/14239)以将其添加到核心。它在 8 月([版本 8.4.0](https://github.com/nodejs/node/blob/master/doc/changelogs/CHANGELOG_V8.md#2017-08-15-version-840-current-addaleax) )被合并并包含在一个标志(`--expose-http2`)下作为实验。

在 Twitter 宣布该标志将在 8.x LTS 版本中被移除后，Node.js 最终在 8.8.0 版本中移除了该标志。HTTP/2 仍然被认为是实验性的，尽管事实上它还没有 100%完成，你已经可以开始用它做实验并查看 [HTTP/2 API 文档](https://nodejs.org/api/http2.html)。

# 四处玩耍

创建一个 HTTP/2 服务器应该没有那么难。事实上，如果你已经有了 Node.js 的经验，它应该和你所熟悉的非常相似。看看下面的例子:

```
**const** http2 = require('http2'),  
  fs = require('fs');**const** options = {  
  key: fs.readFileSync('keys/server.key'),
  cert: fs.readFileSync('keys/server.crt')
};**const** server = http2.createSecureServer(options);  
server.on('stream', (stream, requestHeaders) => {  
  stream.respond({
    'content-type': 'text/html',
    ':status': 200
  });
  stream.end('<h1>Hi, and welcome to YLD blog!</h1>');
});
server.listen(3000);
```

主要的区别是我们需要“http2”并向`http2.createSecureServer`函数发送凭证选项。浏览器只在 HTTPS 下实现 HTTP/2，确保你正在建立一个 TLS 连接。

你可能已经注意到我们到处都在使用流。然而，有一个[兼容 API](https://nodejs.org/api/http2.html#http2_compatibility_api) 允许从 HTTP/1 轻松迁移到 HTTP/2。这是您设置请求和响应处理程序的方式，因为您已经习惯了:

```
// ...**const** onRequestHandler = (req, res) => {  
  **const** { socket: { alpnProtocol } } = req.httpVersion === '2.0' ? req.stream.session : req;
  res.writeHead(200, `Hi, and welcome to YLD blog! The server you are talking with supports ${alpnProtocol} `);
}**const** server = http2.createSecureServer(  
  options,
  onRequestHandler
).listen(3000);
```

请求和响应处理程序将按预期工作，尽管事实上它们已经有了 HTTP/2 额外的信息，例如连接是否使用了 [ALPN](https://tools.ietf.org/html/rfc7301) (仅用于 HTTP/2)。

HTTP/2 中最有趣的特性之一是服务器推送，推送文件非常简单。在下一个示例中，我们可以看到，每次请求索引路由时，都会推送一个 CSS 文件:

```
**const** onRequestHandler = (req, res) => {  
  **const** currentUrl = url.parse(req.url); **if** (currentUrl.pathname === '/') {
    **const** cssFile = {
      path: '/style.css',
      filePath: './style.css',
      headers: {
        'content-type': 'text/css'
      }
    };
    pushAsset(res.stream, cssFile); // ...
```

您可以如下实现`pushAsset`:

```
**const** pushAsset = (stream, file) => {  
  **const** filePath = path.join(__dirname, file.filePath);
  stream.pushStream({ [HTTP2_HEADER_PATH]: file.path }, (pushStream) => {
    pushStream.respondWithFile(filePath, file.headers);
  });
}
```

事实上，Node.js API 允许您使用 [respondWithFile](https://nodejs.org/api/http2.html#http2_http2stream_respondwithfile_path_headers_options) 或使用 [respondWithFD](https://nodejs.org/api/http2.html#http2_http2stream_respondwithfd_fd_headers_options) 的文件描述符来推送文件。`file.path`在这里非常相关，因为这将是将要被请求的路径。例如，如果`/styles/style.css`中的窗口请求该文件，则需要相应地修改路径。在我们的情况下，它只是`/style.css`。请注意，我们可以推送尽可能多的文件，您也可以推送您认为与索引页面相关的内容。查看[完整示例](https://github.com/sericaia/http2-examples-empireconf/tree/master/04-server-push)。

您还可以像我们在[使用服务器推送和服务工作程序优化](https://blog.yld.io/2017/03/01/optimize-with-http-2-server-push-and-service-workers)中所做的那样，使用服务器推送和服务工作程序进行测试。查看一个使用 HTTP/2 Node.js 核心实现的例子。

# 框架和预期，敬请关注！

坏消息是，像 [Express](https://expressjs.com/) 或 [Hapi.js](https://hapijs.com/) 这样的框架还不支持 HTTP/2。好消息是，这即将实现，它应该与用于 [spdy](https://www.npmjs.com/package/spdy) 或 Node.js 的其他旧 HTTP/2 模块的实现非常相似

如果您想了解这方面的最新信息，请查看以下帖子:

快递:

*   [https://github.com/expressjs/express/issues/3388](https://github.com/expressjs/express/issues/3388)
*   [https://github.com/expressjs/express/pull/3390](https://github.com/expressjs/express/pull/3390)
*   [https://github.com/nodejs/node/issues/15203](https://github.com/nodejs/node/issues/15203)

Hapi.js:

*   [https://github.com/hapijs/hapi/issues/2510](https://github.com/hapijs/hapi/issues/2510)
*   [https://github.com/hapijs/hapi/issues/3584](https://github.com/hapijs/hapi/issues/3584)

# 想了解更多？

10 月 13 日，我在 [EmpireConf](http://2017.empireconf.org/) 做了一个关于这个话题的演讲。如果你对此感兴趣，如果你想了解更多关于 HTTP/2 的知识，请查看我的演讲[**HTTP/2 的兴起**](https://www.youtube.com/watch?v=yToHjxhCeYM) **，我用管弦乐队的比喻解释了 HTTP/2！你也可以在 github.com/sericaia/http2-examples-empireconf 知识库中找到代码示例。**

由[丹妮拉·马托斯·德·卡瓦略](https://twitter.com/sericaia)出版——YLD[的软件工程师](https://twitter.com/YLDio)
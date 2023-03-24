# Node.js 提示—请求 URL、解析请求正文、上传文件

> 原文：<https://levelup.gitconnected.com/node-js-tips-request-urls-parsing-request-bodies-upload-9caaa7f8446e>

![](img/27a29e9be550135518ac0307d84281c7.png)

照片由[王巍](https://unsplash.com/@1875patricia?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何类型的应用程序一样，当我们编写节点应用程序时，有一些困难的问题需要解决。

在本文中，我们将研究一些编写节点应用程序时常见问题的解决方案。

# 从请求中获取路径

我们可以通过使用`request.url`解析请求 URL 来从请求中获取路径。

例如，我们可以写:

```
const http = require("http");
const url = require("url");const onRequest = (request, response) => {
  const pathname = url.parse(request.url).pathname;
  console.log(pathname);
  response.writeHead(200, { "Content-Type": "text/plain" });
  response.write("hello");
  response.end();
}http.createServer(onRequest).listen(8888);
```

我们所要做的就是获取`request.url`属性来获取 URL。

然后我们可以用`url`模块解析它。

然后我们可以用`pathname`来获得相对路径。

# 将相对路径转换为绝对路径

我们可以通过使用`path`模块的`resolve`方法将相对路径转换为绝对路径。

例如，我们可以写:

```
const resolve = require('path').resolve
const absPath = resolve('../../foo/bar.txt');
```

我们用相对路径调用`resolve`来返回文件的绝对路径。

# 获取以字节为单位的字符串长度

我们可以通过使用`Buffer.byteLength`方法获得以字节为单位的字符串长度。

例如，我们可以写:

```
const byteLength = Buffer.byteLength(string, 'utf8');
```

我们传入一个`string`到`Buffer.byteLength`来获得字符串的字节长度。

# 获取从 Express 中的表单传递的数据

要从 Express 中的表单获得数据，我们可以使用`body-parser`包来完成。

然后为了得到解析的结果，我们可以从`req.body`属性中得到它。

我们可以写:

```
const bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({ extended: true }));app.post('/game', (req, res) => {
  res.render('game', { ...req.body });
});
```

我们调用`app.post`来创建一个 POST route，它通过`req.body`获取请求体，因为我们调用了`bodyParser.urlencoded`来解析 URL 编码的有效负载。

`extended`设置为`true`让`body-parser`接受表单数据中类似 JSON 的数据，包括嵌套对象。

有了这个选项，我们不必像传统的 HTML 表单发送那样发送键值对。

# 在同一个端口上运行多个 Express 应用

我们可以使用`app.use`合并多个 Express 应用程序，并在同一个端口上运行它们。

例如，我们可以写:

```
app
  .use('/app1', require('./app1').app)
  .use('/app2', require('./app2').app)
  .listen(8080);
```

我们要求`app1`和`app2`在一个应用程序中使用他们的路线。

然后我们调用`listen`来监听端口 8080 中的请求。

# 用 NodeJs 子进程改变工作目录

我们可以用`cwd`选项改变工作目录。

例如，我们可以写:

```
const exec = require('child_process').exec;exec('pwd', {
  cwd: '/foo/bar/baz'
}, (error, stdout, stderr) => {
  // work with result
});
```

我们用一个带有`cwd`属性的对象调用`exec`来设置当前的工作目录。

然后我们在回调中得到结果。

# 使用 AWS SDK for Node.js 将二进制文件上传到 S3

通过使用 AWS 包，我们可以使用 Node.js AWS SDK 将二进制文件上传到 S3。

例如，我们可以写:

```
const AWS = require('aws-sdk');
const fs = require('fs');AWS.config.update({ accessKeyId: 'key', secretAccessKey: 'secret' });const fileStream = fs.createReadStream('zipped.tgz');fileStream.on('error', (err) => {
  if (err) { 
    console.log(err); 
  }
}); fileStream.on('open', () => {
  const s3 = new AWS.S3();
  s3.putObject({
    Bucket: 'bucket',
    Key: 'zipped.tgz',
    Body: fileStream
  }, (err) => {
    if (err) { 
     throw err; 
    }
  });
});
```

我们使用`aws-sdk`包。

首先，我们向`AWS.config.update`认证。

然后我们用`createReadStream`从文件中创建一个读取流。

然后，我们通过给`'open'`事件附加一个监听器来监听打开的文件。

然后我们创建一个`AWS.S3`实例。

我们调用`putObject`来上传文件。

`Bucket`是桶名。

`Key`是文件的路径。

`Body`是我们创建的文件的读取流。

我们还监听`error`事件，以防遇到任何错误。

![](img/76e7e87f539ccb7212977c02242f151e.png)

Robina Weermeijer 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 结论

如果我们使用`http`模块来监听请求，我们可以用`request.url`属性获得请求的路径。

要将相对路径转换成绝对路径，我们可以使用`path`模块的`resolve`方法。

我们可以通过创建一个读取流并调用`putObject`来用 S3 上传文件。

我们可以用`exec`设置当前工作目录。
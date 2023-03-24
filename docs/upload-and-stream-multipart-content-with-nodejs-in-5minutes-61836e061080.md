# 如何在 5 分钟内用 Node JS 上传和流式传输多部分内容

> 原文：<https://levelup.gitconnected.com/upload-and-stream-multipart-content-with-nodejs-in-5minutes-61836e061080>

![](img/c266c8a7481f180cb461b0210b57cd1d.png)

在 Node js 中，开始流式传输上传的内容非常简单。在这个例子中，我们将看看如何使用 request.js 来实现这一点，这是一个非常漂亮的 HTTP 请求实用程序，应该是您的实用工具中的必备工具。如果你打算长期使用 Node js 的话。对于不熟悉流概念的人来说。将大文件加载到内存中的问题是，您实际上可能会耗尽内存，导致应用程序崩溃。如果这是一个可能是一场噩梦的生产应用程序，您会想尽一切办法避免它。

流的美妙之处在于，它们通过对大块数据进行操作来帮助您解决内存问题，从而允许您最小化内存占用。你可以想象一条小溪，就像你厨房水槽里的水龙头。一旦一个流被打开，数据就以块的形式从源头流向使用它的进程，理论上，你可以传输无限量的数据。

在我们开始编码之前，确保你已经安装了 [Node.js 和](https://nodejs.org/en/download/)。

一旦安装了节点，打开终端窗口并输入以下命令，检查它是否按预期工作

```
node -v
```

创建一个名为`myapp`的目录，切换到该目录并运行`npm init`。按照屏幕上的说明给你的包一个默认的名字，版本，作者，测试脚本。按回车键预选默认选项。然后安装`express`作为依赖项。Express js 将用于说明我们的服务器，它将负责处理多部分上传的内容

```
$ mkdir myapp && cd $_$ npm init$ npm install express --save
```

为了说明流的作用，我们创建了客户端代码来执行内容的上传，我们可以将文件命名为 **index.js** 。

```
touch index.js
```

我们的 client.js 代码利用了[请求](https://www.npmjs.com/package/request)模块——节点内置 [http](https://nodejs.org/api/http.html) 模块的包装器。因此，我们需要通过运行以下命令来确保我们已经安装了它

```
npm install requests --save
```

将下面的代码附加到 index.js，该代码用作负责执行 formData 上载的客户端代码，其中 my_file 属性是指向本地计算机上的文件的链接。`[FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData)`对象允许您编译一组键/值对，并使用`[h](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)ttp request`发送。它主要用于发送表单数据，但也可以独立于表单使用，以传输键控数据。如果表单的编码类型设置为`multipart/form-data`，传输数据的格式与表单的`[submit()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormElement/submit)`方法发送数据的格式相同。

```
var request = require('request');
var fs = require('fs');var formData = {
    my_field: 'file',
    my_file: fs.createReadStream('C:\\temp\\recording.mp4')
};
request.post({
    url: '[http://localhost:5000/api/v1/uploads'](http://localhost:5000/api/v1/uploads'),
    formData: formData
}, function optionalCallback(err, httpResponse, body) {
    if (err) {
        return console.error('upload failed:', err);
    }
    console.log('Upload successful!  Server responded with:', body);
});
```

在服务器端，我们将使用 busboy npm 包来实际处理上传内容的流。附加包 http-status-codes 包含静态 http 代码，而 [morgan](https://github.com/expressjs/morgan) 是一个日志工具，任何在 Node.js 中使用 HTTP 服务器的人都应该学会使用它。morgan 是一个中间件，它允许我们轻松地将请求、错误等记录到控制台。

对于我们的用例，它有助于注销所有传入的 HTTP 请求。

```
npm install busboy --save
npm install http-status-codes --save
npm install morgan --save
```

服务器背后的想法是模拟一个面向公众的服务，你需要上传多部分内容。你要记住的一点是，当使用 Busboy 时，它只解析 application/x-www-form-urlencoded 和 multipart/form-data 请求。创建名为 server.js 的新文件

```
touch server.js
```

在下面附加以下代码

```
var express = require('express'),
    HttpStatus = require('http-status-codes'),
    morgan = require('morgan'),
    packageConfig = require('./package.json'),
    path = require('path'),
    fs = require('fs'),
    Busboy = require('busboy');
express()
    .use(morgan('combined'))
    .get('/api/v1', function(req, res) {
        res.status(HttpStatus.OK).json({
            msg: 'OK',
            service: 'File Server'
        })
    })
    .post('/api/v1/uploads', function(req, res) {
        var busboy = new Busboy({
            headers: req.headers
        });
        console.log(req.headers);
        busboy.on('file', function(fieldname, file, filename, encoding, mimetype) {
            file.on('data', function(data) {
                console.log('File [' + fieldname + '] got ' + data.length + ' bytes');
            });
            file.on('end', function() {
                console.log('File [' + fieldname + '] Finished');
            });
            var saveTo = path.join(__dirname, 'dir', path.basename(filename));
            var outStream = fs.createWriteStream(saveTo);
            file.pipe(outStream);
        });
        busboy.on('finish', function() {
            res.writeHead(HttpStatus.OK, {
                'Connection': 'close'
            });
            res.end("That's all folks!");
        });
        return req.pipe(busboy);
    }).listen(process.env.PORT || 5000, function() {
        var address = this.address();
        var service = packageConfig.name + ' version: ' + packageConfig.version + ' ';
        console.log('%s Listening on %d', service, address.port);
    });
```

您需要将客户端作为单独的节点应用程序运行，而服务器在单独的节点应用程序上运行。点击查看完整源代码
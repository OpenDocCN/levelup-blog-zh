# Node.js 基础— Socket.io

> 原文：<https://levelup.gitconnected.com/node-js-basics-socket-io-e19053a4441b>

![](img/2d1d0eb4d98a7ce5ace0626d4368bc11.png)

[Tekton](https://unsplash.com/@tekton_tools?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 使用 Socket.io 进行实时通信

Socket.io 是一个实时通信库，它将 HTTP 请求与 WebSockets 结合起来，以实现我们的应用程序中的实时通信。

例如，我们可以用 Socket.io 编写一个简单的应用程序:

`index.js`

```
const content = require('fs').readFileSync(__dirname + '/index.html', 'utf8');const httpServer = require('http').createServer((req, res) => {
  res.setHeader('Content-Type', 'text/html');
  res.setHeader('Content-Length', Buffer.byteLength(content));
  res.end(content);
});const io = require('socket.io')(httpServer);io.on('connect', socket => {
  console.log('connect');
});httpServer.listen(3000, () => {
  console.log('go to http://localhost:3000');
});
```

`index.html`

```
<!DOCTYPE html>
<html lang="en"><head>
  <meta charset="UTF-8">
  <title>Minimal working example</title>
</head><body>
  <ul id="events"></ul><script src="/socket.io/socket.io.js"></script>
  <script>
    const $events = document.getElementById('events'); const newItem = (content) => {
      const item = document.createElement('li');
      item.innerText = content;
      return item;
    }; const socket = io(); socket.on('connect', () => {
      $events.appendChild(newItem('connect'));
    });
  </script>
</body></html>
```

在`index.js`，我们提供`index.html`文件。

此外，我们监听`connect`事件，以监听来自客户端的任何连接。

在`index.html`中，我们建立了联系。

当我们连接到 Socket.io 服务器时，我们调用`socket.on`来监听`connect`事件以添加项目。

# 快速安装

我们可以通过 Express 应用程序使用 Socket.io。

为此，我们可以写:

`index.js`

```
const express = require('express');
const app = express();
const server = require('http').createServer(app);
const options = { /* ... */ };
const io = require('socket.io')(server, options);io.on('connection', socket => {
  console.log('connect')
});app.use(express.static('public'));app.get('/', (req, res) => {
  res.sendFile(`${__dirname}/index.html`);
});server.listen(3000);
```

`index.html`

```
<!DOCTYPE html>
<html lang="en"><head>
  <meta charset="UTF-8">
  <title>Minimal working example</title>
</head><body>
  <ul id="events"></ul> <script src="/socket.io/socket.io.js"></script>
  <script>
    const $events = document.getElementById('events');
    const newItem = (content) => {
      const item = document.createElement('li');
      item.innerText = content;
      return item;
    };
    const socket = io();
    socket.on('connect', () => {
      $events.appendChild(newItem('connect'));
    });
  </script>
</body></html>
```

我们使用和以前一样的`index.html`文件。

唯一的区别是我们用快递服务。

我们使用 Express app 实例调用`http`模块的`createServer`方法，而不是使用回调函数。

然后我们用`res.sendFile`方法提供静态文件。

# 命名空间

我们可以创建名称空间来隔离通信流量。

例如，我们可以写:

`index.js`

```
const express = require('express');
const app = express();
const server = require('http').createServer(app);
const options = { /* ... */ };
const io = require('socket.io')(server, options);io.on('connection', socket => {
  console.log('connect')
});const adminNamespace = io.of('/admin');adminNamespace.use((socket, next) => {
  console.log(socket);
  next();
});adminNamespace.on('connection', socket => {
  socket.on('delete user', (data) => {
    console.log(data);
  });
});app.get('/', (req, res) => {
  res.sendFile(`${__dirname}/index.html`);
});server.listen(3000);
```

`index.html`

```
<!DOCTYPE html>
<html lang="en"><head>
  <meta charset="UTF-8">
  <title>Minimal working example</title>
</head><body>
  <ul id="events"></ul> <script src="/socket.io/socket.io.js"></script>
  <script>
    const socket = io('/admin');
    socket.emit('delete user', 'foo')
  </script>
</body></html>
```

在`index.js`文件中，我们用以下内容创建了`/admin`名称空间:

```
const adminNamespace = io.of('/admin');
```

然后，在监听到名称空间的连接之前，我们用自己的中间件函数调用`use`方法。

我们可以在`use`回调中添加允许或拒绝连接的逻辑。

我们调用`next`来完成连接。

`adminNamespace.on`方法监视`connection`事件，以监视到名称空间的连接。

然后我们用想要监听的事件名调用`socket.on`，然后从`data`参数中获取数据。

在`index.html`中，我们调用`io('/admin')`连接到`'/admin'`名称空间。

然后我们调用`socket.emit`，将事件名称作为第一个参数，将事件数据作为第二个参数。

# 结论

我们可以很容易地用 Socket.io 进行实时通信。
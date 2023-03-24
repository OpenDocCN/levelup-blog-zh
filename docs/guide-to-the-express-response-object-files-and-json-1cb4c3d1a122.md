# 快速响应对象指南—文件和 JSON

> 原文：<https://levelup.gitconnected.com/guide-to-the-express-response-object-files-and-json-1cb4c3d1a122>

![](img/26d4c10f8429aa8e2cd8479d8eda36a6.png)

维克多·塔拉舒克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Express response 对象允许我们向客户端发送响应。

可以发送各种响应，比如字符串、JSON 和文件。此外，除了主体之外，我们还可以向客户端发送各种标头和状态代码。

在本文中，我们将研究 response 对象的各种属性，包括文件和 JSON 响应。

# 方法

## res .下载

`res.download`向服务器发送文件响应。

例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res) => {
  res.download('./public/foo.txt');
})app.listen(3000, () => console.log('server started'));
```

然后，当一个请求被发送到这个路径时，我们就会下载一个文件。

我们可以用不同于服务器上的文件来保存文件，方法是:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res) => {
  res.download('./files/foo.txt', 'foobar.txt');
});app.listen(3000, () => console.log('server started'));
```

来自服务器的文件`foo.txt`将被客户端保存为`foobar.txt`。

在下载响应失败的情况下，`download`方法还采用了一个错误处理程序。

例如，我们可以使用内置的错误处理程序来处理错误，如下所示:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res, next) => {
  res.download('./files/foo.txt', 'foobar.txt', err => next(err));
});app.listen(3000, () => console.log('server started'));
```

我们用传入的错误对象调用了`next`,将它传递给错误处理程序。

## RES . end([数据] [，编码])

`res.end`结束响应过程。该方法来自`http`模块的`response.end()`。

我们可以用它来结束响应，而不发送任何数据。用数据来回应，可以用`res.send()`或者其他方法。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res) => {
  res.status(404).end();
});app.listen(3000, () => console.log('server started'));
```

然后，我们向客户端发送了一个没有数据的 404 响应。

## 分辨率格式(对象)

`res.format`方法从请求对象获取`Accept` HTTP 头，并使用`res.accepts`为请求选择一个处理程序。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res) => {
  res.format({
    'text/plain': () => {
      res.send('hi');
    }, 'text/html': () => {
      res.send('<p>hi</p>');
    }, 'application/json': () => {
      res.json({ message: 'hi' });
    }, 'default': () => {
      res.status(406).end();
    }
  })
});app.listen(3000, () => console.log('server started'));
```

如果我们向`/`发送一个 GET 请求，并将`Accept`头的值设置为`text/plain`，我们将得到`hi`。如果我们发送`text/html`作为`Accept`头的值，我们得到`<p>hi</p>`，依此类推。

`default`指定没有匹配的响应类型时的默认响应。

即使我们将 MIME 类型名称缩写成更短的名称，它也能工作:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res) => {
  res.format({
    text: () => {
      res.send('hi');
    }, html: () => {
      res.send('<p>hi</p>');
    }, json: () => {
      res.json({ message: 'hi' });
    }, default: () => {
      res.status(406).end();
    }
  })
});app.listen(3000, () => console.log('server started'));
```

然后我们得到和以前一样的东西。

![](img/c884fee54d6343a7bbd372aa534442f0.png)

安德鲁·庞斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 资源获取(字段)

`get`方法返回由`field`指定的 HTTP 响应头。匹配不区分大小写。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res) => {  
  res.send(res.get('Content-Type'));
});app.listen(3000, () => console.log('server started'));
```

## res.json([body])

`res.json`让我们向客户端发送一个 JSON 响应。该参数可以是任何 JSON 类型，包括对象、数组、字符串、布尔值、数字或 null。

例如，如果我们有:

```
const express = require('express')
const app = express()app.use(express.json())
app.use(express.urlencoded({ extended: true }))app.get('/', (req, res) => {
  res.json({ message: 'hi' });
})app.listen(3000, () => console.log('server started'));
```

然后我们得到:

```
{"message":"hi"}
```

作为回应。

## res.jsonp([body])

我们可以用它来发送一个支持 JSONP 的 JSON 响应。它返回一个传入 JSON 主体的回调。

例如，我们可以如下使用它:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res) => {
  res.jsonp({ user: 'foo' });
});app.listen(3000, () => console.log('server started'));
```

如果我们用`?callback=cb`作为查询字符串发送一个 GET 请求，那么我们得到:

```
/**/ typeof cb === 'function' && cb({"user":"foo"});
```

作为回应。如果我们忽略`callback`搜索参数，那么我们会得到一个普通的 JSON 响应:

```
{
    "user": "foo"
}
```

# 结论

使用 response 对象，我们可以轻松地发送各种响应。

要发送文件，我们可以用`download`。

为了根据`Accept`请求头发送不同的东西，我们可以使用`format`方法。

最后，为了发送 JSON，我们可以使用`json`或`jsonp`方法，这取决于我们是否需要在客户端调用回调函数。
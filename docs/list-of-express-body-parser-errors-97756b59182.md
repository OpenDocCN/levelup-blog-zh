# Express 主体分析器错误列表

> 原文：<https://levelup.gitconnected.com/list-of-express-body-parser-errors-97756b59182>

![](img/aee76aa37bf816fdc63d7370ae662516.png)

安妮·尼加德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

默认情况下，Express 4.x 或更高版本没有提供任何解析请求体的功能。因此，我们需要添加一个库来处理这个问题。

是一个中间件，我们可以用它来解析各种类型的请求体。它抛出了几种我们需要管理的错误。在本文中，我们将看看它抛出了什么错误。

# 错误

当`body-parser`抛出一个错误时，它发送一个响应代码和一个错误消息。错误的`message`属性有错误消息。

下列错误是引发的常见错误。

## 不支持内容编码

当请求有一个包含编码的`Content-Encoding`头，但是`inflation`选项被设置为`false`时，就会抛出这个错误。

响应的状态将是 415m，并且`type`属性被设置为`encoding.unsupported`。`charset`属性将被设置为不支持的编码。

例如，我们可以复制它，如果我们有:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();
const options = {
  inflate: false,
};app.use(bodyParser.urlencoded(options));app.post('/', (req, res) => {
  res.send(req.body);
});app.listen(3000);
```

然后，当我们向`Content-Encoding`设置为`abc`并且`Content-Type`设置为`application/x-www-form-urlencoded`的`/`发送 POST 请求时，我们得到:

```
content encoding unsupported
```

和 415 状态码。

## 请求中止

当客户端在主体读取完成之前中止请求时，会出现此错误。

`received`属性将被设置为请求中止前接收的字节数。

响应的`status`将是 400，并且`type`属性被设置为`request.aborted`。

## 请求实体太大

当请求正文大于`limit`选项中指定的内容时，将会出现错误。

将发送一个 413 错误响应，并将`type`属性设置为`entity.too.large`。

例如，如果我们将`limit`设置为 0，如下所示:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();
const options = {
  inflate: false,
  limit: 0
};app.use(bodyParser.json(options));app.post('/', (req, res) => {
  res.send(req.body);
});app.use((err, req, res, next) => {
  res.status(400).send(err);
});app.listen(3000);
```

然后，当我们用包含内容的 JSON 主体向`/`发出 POST 请求时，我们得到:

```
{"message":"request entity too large","expected":20,"length":20,"limit":0,"type":"entity.too.large"}
```

![](img/b5f1296e722851b92067f7aac718726f.png)

本杰明·伊尔希曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 请求大小与内容长度不匹配

请求的长度与来自`Content-Length`头的长度不匹配。

当请求格式不正确时，会出现这种情况。`Content-Length`通常是根据字符而不是字节来计算的。

返回的状态将是 400，并且`type`属性被设置为`request.size.invalid`。

例如，如果我们有:

```
const express = require('express');
const bodyParser = require('body-parser');
const app = express();
const options = {
  inflate: false,  
};app.use(bodyParser.urlencoded(options));app.post('/', (req, res) => {
  res.send(req.body);
});app.listen(3000);
```

我们向`/`发送一个 POST 请求，将`Content-Length`头设置为-1，将`Content-Type`头设置为`application/x-www-form-urlencoded`，我们得到一个 400 响应。

## 不应设置流编码

`req.setEncoding`方法先于`body-parser`方法被调用会导致此错误。

我们在使用`body-parser`的时候不能直接调用`req.setEncoding`。

状态将为 500，并且`type`属性设置为`stream.encoding.set`。

## 参数太多

当请求体中的参数数量超过`parameterLimit`中设置的数量时，就会出现错误。

响应状态将为 413，并且`type`属性设置为`parameters.too.many`

## 不支持的字符集“伪”

当请求的`Content-Encoding`头包含不支持的编码时，会出现这种情况。编码包含在消息和`encoding`属性中。

`status`将被设置为 415。`type`将被设置为`encoding.unsupported`，并且`encoding`属性被设置为不支持的编码。

# 结论

`body-parser`引发了多种错误。

它们包括发送错误的标头或不被它接受的数据，或者在读取所有数据之前取消请求。

各种 400 系列错误状态代码将作为响应与相应的错误消息和堆栈跟踪一起发送。
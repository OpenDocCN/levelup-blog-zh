# 跟踪快递应用程序响应的响应时间

> 原文：<https://levelup.gitconnected.com/tracking-response-time-of-express-app-responses-8c0e16145a5f>

![](img/9da497ff3569e30fa8b9d8105ad16a38.png)

由 [Austin Distel](https://unsplash.com/@austindistel?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

为了衡量我们的应用程序的性能，我们需要测量我们的 API 的响应时间。

要使用我们的 Express 应用程序完成这项工作，我们可以使用`response-time`包。

# 装置

`response-time`作为节点包提供。我们可以通过运行以下命令来安装它:

```
npm install response-time
```

然后我们可以通过编写以下内容来导入它:

```
const responseTime = require('response-time');
```

# 响应时间([选项])

我们可以通过向可选的`options`参数传递一个对象来设置各种选项。

它将创建一个中间件，为响应添加一个`X-Response-Time`头。

`options`对象具有以下属性:

## 数字

`digits`属性是包含在输出中的固定位数，总是以毫秒为单位。默认值为 3。

## 页眉

要设置的标题的名称。默认为`X-Response-Time`。

## 后缀

`suffix`属性是一个布尔属性，用于指示测量单位是否应该添加到输出中。默认值为`true`。

# 响应时间(fn)

该函数创建了一个记录请求响应时间的中间件，并将其提供给我们自己的函数`fn`。`fn`具有签名`(req, res, time)`，其中`time`是以毫秒为单位的数字。

# 例子

## 简单的例子

我们可以使用`responseTime`包，而不传递任何选项或函数:

```
const express = require('express');
const bodyParser = require('body-parser');
const responseTime = require('response-time')
const app = express();
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.use(responseTime());app.get('/', (req, res) => {
  res.send('foo');
});app.listen(3000);
```

然后我们得到一个值为 0.587 毫秒的`X-Response-Time`响应头。

我们可以在像 Postman 这样的 HTTP 客户端上检查响应头。

# 传入选项

我们可以更改与响应一起返回的头的选项。例如，我们可以编写以下代码来更改发送的位数:

```
const express = require('express');
const bodyParser = require('body-parser');
const responseTime = require('response-time')
const app = express();
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.use(responseTime({
  digits: 5
}))app.get('/', (req, res) => {
  req.id = 1;
  res.send('foo');
});app.listen(3000);
```

然后我们得到一个值为 0.71987ms 的`X-Response-Time`响应头。

![](img/19d8a991d1d30302c82d7a483899955f.png)

由[蒂姆·范德奎普](https://unsplash.com/@timmykp?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 传入我们自己的函数

我们可以将一个函数传递给`responseTime`函数，如下所示:

```
const express = require('express');
const bodyParser = require('body-parser');
const responseTime = require('response-time')
const app = express();
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.use(responseTime((req, res, time) => {
  console.log(`${req.method} ${req.url} ${time}`);
}))app.get('/', (req, res) => {
  req.id = 1;
  res.send('foo');
});app.listen(3000);
```

然后我们会得到这样的结果:

```
GET / 2.9935419999999997
```

从`console.log`开始。

如果我们想要操作响应时间数据或自己记录它，这是很有用的。

# 结论

我们可以在带有`response-time`包的请求的响应头中获得响应时间。

它有一个`responseTime`函数，该函数返回一个中间件，我们可以通过`express`或`express.Router()`的`use`方法使用该中间件。

该函数可以采用一个`options`对象和/或一个带有`req`、`res`和`time`参数的函数来分别获取请求、响应和响应时间。
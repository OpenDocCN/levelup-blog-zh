# Node.js 最佳实践—错误处理

> 原文：<https://levelup.gitconnected.com/node-js-best-practices-error-handling-50719c42504f>

![](img/afa680d1984cbbf7110c0a2835351b4b.png)

理查德·布拉金斯-泰勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Node.js 是编写应用程序的流行运行时。这些应用程序通常是许多人使用的生产质量应用程序。为了使维护它们变得更容易，我们必须制定一些准则供人们遵循。

在本文中，我们将研究 Node.js 应用程序中错误处理的最佳实践。

# 仅使用内置的错误对象

在 JavaScript 中，我们可以创建 error 对象的子类，因此不需要创建自定义类型或返回一些错误值来抛出错误。

我们可以实例化`Error`类或`Error`类的子类。这样，会增加一致性，减少混乱。对每个看代码的人来说就少了很多头痛。

例如，我们可以通过编写以下代码抛出一个`Error`实例:

```
throw new Error('error');
```

我们可以通过使用如下的`catch`块来捕捉错误并优雅地处理它们:

```
try {
  foo();
} catch (e) {
  if (e instanceof RangeError) {
    console.error(`${e.name} - ${e.message}`)
  }
}
```

上面的代码只处理`RangeError`实例。

我们可以从`Error`实例中获得错误名称和堆栈跟踪。此外，我们可以通过子类化`Error`类来创建自己的错误类:

```
class CustomError extends Error {
  constructor(foo = 'bar', ...params) {
    super(...params)
    if (Error.captureStackTrace) {
      Error.captureStackTrace(this, CustomError)
    } this.name = 'CustomError'    
    this.foo = foo
    this.date = new Date()
  }
}
```

然后我们可以抛出一个`CustomError`实例，并通过编写以下代码来捕获它:

```
try {
  throw new CustomError('baz');
} catch (e) {  
  if (e instanceof CustomError) {
    console.error(`${e.name} - ${e.foo}`)
  }
}
```

# 区分操作错误和程序员错误

用户操作失误应由用户自行处理。例如，如果输入了无效的输入，我们应该通过向他们发送错误消息来处理它。

另一方面，如果错误是程序员的错误，那么我们应该修复它们。保持程序员错误不被修复不是一个好主意，因为它会导致糟糕的用户体验和数据损坏。

对于用户错误，我们应该向客户端发送 web 应用程序的 400 系列响应代码。400 表示输入错误，401 表示未授权，403 表示禁止。

# 集中处理错误，而不是在 Express 中间件中

在 Express 应用程序中，错误处理应该集中在自己的中间件中完成，而不是分散在多个中间件中。

例如，我们应该编写类似下面的代码来处理错误:

```
const express = require('express');
const bodyParser = require('body-parser');const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res, next) => {
  try {
    throw new Error('error')
    res.send('hello')
  } catch (err) {
    next(err)
  }
});app.use((err, req, res, next) => {
  res.send('error occurred')
})app.listen(3000, () => console.log('server started'));
```

在上面的代码中，我们在应用程序的末尾添加了一个错误处理程序，以便它在所有路由调用`next`时运行。

这样，我们就不必编写多段代码来处理错误。在上面的代码中:

```
app.use((err, req, res, next) => {
  res.send('error occurred')
})
```

是错误处理器中间件。GET route 中的`next`函数调用错误处理程序，因为它抛出了一个错误。

![](img/0d1c608a4e59dd294b723ecfb679a5ea.png)

照片由[迦勒·马丁](https://unsplash.com/@cmart10?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 使用 Swagger 记录 API 错误

文档非常重要，因为它让人们知道如何使用 API，并确保对 API 的更改不会意外地产生不良行为。

例如，将 Swagger 添加到 Express 应用程序很容易，因为我们有 Swagger UI Express 包来为我们做这件事。我们可以通过编写以下代码来安装 Swagger UI Express 包:

```
npm install swagger-ui-express
```

然后我们可以如下创建一个`swagger.json`文件:

```
{
  "openapi": "3.0.0",
  "info": {
    "title": "Sample API",
    "description": "Optional multiline or single-line description in [CommonMark](http://commonmark.org/help/) or HTML.",
    "version": "0.1.9"
  },
  "servers": [
    {
      "url": "https://UnwittingRudeObservation--five-nine.repl.co",
      "description": "Optional server description, e.g. Main (production) server"
    },
    {
      "url": "https://UnwittingRudeObservation--five-nine.repl.co",
      "description": "Optional server description, e.g. Internal staging server for testing"
    }
  ],
  "paths": {
    "/": {
      "get": {
        "summary": "Returns a hello message.",
        "description": "Optional extended description in CommonMark or HTML.",
        "responses": {
          "200": {
            "description": "A JSON string message",
            "content": {
              "application/json": {
                "schema": {
                  "type": "string",
                  "items": {
                    "type": "string"
                  }
                }
              }
            }
          }
        }
      }
    }
  }
}
```

然后在我们的应用程序中，我们可以写:

```
const express = require('express');
const bodyParser = require('body-parser');
const swaggerUi = require('swagger-ui-express');
const swaggerDocument = require('./swagger.json');
const app = express();app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res, next) => {
  res.send('hello')
});app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerDocument));
app.listen(3000, () => console.log('server started'));
```

我们可以去[https://editor.swagger.io/](https://editor.swagger.io/)编辑我们的 Swagger API 文档。

# 结论

如果我们想在节点应用程序中抛出错误，我们应该抛出一个`Error`的实例或者`Error`的子类。捕捉错误可以在节点应用程序的 catch 块中完成。对于 Express 应用程序，它也应该由中央错误处理程序来处理。

为了使使用和更改 API 变得容易，我们应该使用 Swagger 来记录我们的 API 拥有的路由。对于 Express 应用程序，我们可以使用 Swagger UI Express 以一种易于阅读的格式将我们的`swagger.json`文件作为一个单独的页面。

对于用户错误，我们应该将错误发回给他们。程序员的错误应该由我们来解决。
# Node.js 最佳实践—安全攻击

> 原文：<https://levelup.gitconnected.com/node-js-best-practices-security-attacks-999a82fe5c36>

![](img/f8a6e66dac8d76d1188669145f0a452b.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Node.js 是编写应用程序的流行运行时。这些应用程序通常是许多人使用的生产质量应用程序。为了使维护它们变得更容易，我们必须为人们设定一些准则来遵循。

在本文中，我们将了解编写节点应用程序时需要注意的一些基本安全实践。

# 使用 ORM/ODM 库防止查询注入漏洞

我们不应该将用户输入的字符串直接传入我们的应用程序，以防止 SQL 或 NoSQL 注入攻击。在将输入传递给数据库查询之前，应该对其进行验证和清理。

所有著名的数据访问库，如 Sequelize、Knex 和 Mongoose，都有针对脚本注入攻击的内置保护。

如果不进行排序，未排序的字符串很容易破坏数据，并暴露给未授权方。

# 通用安全最佳实践的集合

我们应该与一般的安全最佳实践保持同步，以便在开发和运行应用程序时实施它们。

# 调整 HTTP 响应头以增强安全性

我们可以使用像`helmet`这样的模块来保护头部，以防止攻击使用常见的攻击，如跨站点脚本与我们的应用程序。

为了添加`helmet`并使用它，我们运行:

```
npm i helmet
```

然后按如下方式使用它:

```
const express = require('express');
const bodyParser = require('body-parser');
const helmet = require('helmet');
const app = express();
app.use(helmet());app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.get('/', (req, res) => {
  res.send('hello');
});app.listen(3000, () => console.log('server started'));
```

头盔自动保护我们免受跨站点脚本，实现严格的传输安全，并防止客户端从响应中嗅探 MIME 类型。

`X-Powered-By`头也被从响应中移除，这样攻击者就不会知道我们的应用是一个 Express 应用。

# 不断自动检查易受攻击的依赖项

在投入生产之前，我们可以使用`npm audit`或 snyk 来检查具有易受攻击的依赖项的包。否则，攻击者可能会利用这些漏洞实施攻击。

# 避免使用 Node.js 加密库来处理密码，请使用 Bcrypt

`bcrypt`提供哈希和盐功能。因此，它比内置的加密库更适合处理秘密。也更快。

我们不希望攻击者能够通过字典攻击来暴力破解密码和令牌。

# 转义 HTML、JS 和 CSS 输出

我们应该避开这些类型的代码，这样攻击就不能用我们的应用程序运行恶意的客户端代码。专用库可以明确地将数据标记为纯内容，永远不应该执行。

![](img/5cd3aa7a58347c5ae844e33cc4f80a27.png)

由[侯赛因·巴德沙阿](https://unsplash.com/@badshah05?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 验证传入的 JSON 模式

应该对 JSON 模式进行验证，以确保传入请求负载包含有效数据。例如，我们可以使用`jsonschema`库来验证发送的 JSON 的结构和值。

我们可以通过快速应用程序使用`jsonschema`库，如下所示:

```
const express = require('express');
const bodyParser = require('body-parser');
const Validator = require('jsonschema').Validator;
const v = new Validator();
const app = express();const addressSchema = {
  "id": "/SimpleAddress",
  "type": "object",
  "properties": {
    "address": { "type": "string" },
  },
  "required": ["address"]
};const schema = {
  "id": "/SimplePerson",
  "type": "object",
  "properties": {
    "name": { "type": "string" },
    "address": { "$ref": "/SimpleAddress" },
  },
  "required": ["name", "address"]
};v.addSchema(addressSchema, '/SimpleAddress');
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));app.post('/person', (req, res) => {  
  if (v.validate(req.body, schema).errors.length) {
    return res.send(400)
  }
  res.send('success');
});app.listen(3000, () => console.log('server started'));
```

在上面的代码中，我们需要`jsonschema`库并使用它的验证器。然后我们定义了被`/SimplePerson`模式引用的`/SimpleAddress`模式。

我们用以下内容添加了`/SimpleAddress`模式:

```
v.addSchema(addressSchema, '/SimpleAddress');
```

在`/SimplePerson`中引用。

然后我们可以根据我们的模式检查我们的请求体:

```
v.validate(req.body, schema).errors.length
```

然后，如果请求体验证失败，我们就停止请求的处理。

# 支持将 jwt 列入黑名单

用于恶意用户活动的 JSON Web 令牌(jwt)应该被撤销。因此，我们的应用程序需要一种方法来撤销这些令牌。

# 结论

我们应该通过检查漏洞和撤销用于恶意目的的令牌来保护我们的应用程序。此外，我们需要采取措施，通过净化任何地方的数据来防止恶意软件在客户端和服务器端运行。

最后，我们应该验证请求体，以确保有效的数据被提交到我们的应用程序。
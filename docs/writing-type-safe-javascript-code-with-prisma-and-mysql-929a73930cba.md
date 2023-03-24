# 用 Prisma 和 MySQL 编写类型安全的 Javascript 代码

> 原文：<https://levelup.gitconnected.com/writing-type-safe-javascript-code-with-prisma-and-mysql-929a73930cba>

![](img/a21eed5024fb40b7a4f8eb40340bfb31.png)

照片由[波普&斑马](https://unsplash.com/@popnzebra?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[挡泥板](https://unsplash.com/s/photos/safety?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)上拍摄

本教程深入研究了如何使用 [JavaScript](https://www.javascript.com/) 、 [Node.js](https://nodejs.org/) 、 [Express](https://expressjs.com/) 、 [Joi](https://joi.dev/) 、 [Prisma](https://www.prisma.io/) 和 [MySQL](https://www.mysql.com/) 创建类型安全的 API 服务器。您将学习如何使用 Express 创建服务器，使用 Joi 验证请求，以及使用 Prisma 在数据库上执行 CRUD 操作。

如果你不熟悉，CRUD 代表 **c** reate、 **r** ead、 **u** pdate、 **d** elete。这些是 API 实现的操作，使程序员能够访问和修改数据。您的 Express API 服务器将是功能完整的。

# 在开始之前

在本教程中，您将学习如何:

*   使用 Prisma 和验证对 Javascript API 实施类型安全。
*   使用 Express、Prisma 和 MySQL 创建一个实用的现代后端。
*   添加 CRUD 操作和 HTTP 请求。
*   用 Prisma 为您的数据建模。

# 什么是类型安全？

类型安全是编程语言为减少或消除类型错误而实施的标准。例如，如果一个方法需要整数参数，而程序员要传递一个字符串列表，那么类型安全语言就会抛出一个错误。

JavaScript 本质上不是一种类型安全的语言，但是您可以强加某种级别的类型安全。Prisma 是类型安全的，因为它生成的数据模型是强类型的。但是如果没有验证，您最好使用 Joi 来验证您的输入，并使用 Prisma 将经过验证的输入写入 MySQL 数据库。

# Prisma 是什么？

Prisma 是 JavaScript 和 TypeScript 的查询生成器。它与许多不同的 SQL 服务器兼容，包括 MySQL、Postgres 和 SQLite，以及预发布的 MongoDB 和 Microsoft SQL server。Prisma 简化了数据库操作，并在查询中加强了类型安全。

Prisma 的工作原理是自动生成一个类型感知的、特定于您的项目的客户端。它还能够通过从数据库服务器获取信息来自动创建模式。Prisma 还具有一个名为 Prisma Studio 的可视化编辑器，以及一个用于与数据库交互的命令行界面(CLI)。

# 用 Prisma 编写类型安全的 API

下面的例子使用 Prisma Client 和 Node.js 创建一个 API，它将与 MySQL 数据库服务器通信。它将使用为面包店创建的数据库模式，特别是描述面包店商业供应商的地址簿表。

# 设置项目

首先，您需要为您的项目创建一个目录。这个例子将使用一个名为`javascript-prisma-mysql`的项目目录。使用以下命令创建目录并导航至该目录:

```
mkdir javascript-prisma-mysql
cd javascript-prisma-mysql
```

一旦进入正确的目录，就应该初始化 Node.js 项目。

# 初始化 Node.js 项目

在启动服务器之前，您必须初始化项目。在项目的根目录下运行此命令:

```
npm init
```

按下 enter 键后，您将看到一个交互式向导已经启动。它会问一些关于你的项目的问题。可以点击“回车”并接受每行的默认值。运行带有`-y`参数的命令将跳过该向导，并在不需要输入的情况下生成您的项目。

![](img/d83d4c72e3077142944354484718c790.png)

npm 初始化交互式向导。

当向导完成时，它将生成节点运行应用程序所需的文件。现在，您可以继续安装项目依赖项了。

# 安装项目依赖项

在开始编写代码之前，您需要安装一些软件包。对于这个项目，您将使用以下 Node.js 包:

Express 是您将用来构建 API 服务器的框架。它提供了处理 [HTTP 请求和路由](https://expressjs.com/en/guide/routing.html)所需的机制。它是由快递包裹提供的。

Prisma CLI 将允许您使用 npx 调用 Prisma 特定的命令。它需要单独安装，作为一个开发依赖，从 prisma 包。

Prisma client 是一个查询生成器，使用 Prisma CLI 生成的模式与 MySQL 数据库进行通信。Prisma 将确保类型安全的 API 响应。你将从`@prisma/client`包中安装它。

Joi 是一个数据验证框架，您将使用它来检查传入的 HTTP 请求的类型是否正确。您将从 joi 包中安装它。

Joi validation for Express 是将 Joi 数据验证规则应用到 Express API 请求的中间件。由`express-joi-validation`包提供。

确保安装了 Node.js，然后在项目目录中运行以下命令:

```
npm install express joi express-joi-validation @prisma/client
npm install prisma — save-dev
```

安装完成后，创建一个新文件，以匹配您在上一步中指定的项目入口点。如果你完全按照步骤操作，它将需要被命名为`index.js`。

# 从 Prisma 开始学习 Express API

您应该通过编辑`index.js`来创建一个 API 服务器。在本例中，服务器运行在端口 3000 上，并显示一条 hello 消息来确认它正在工作。

# 第一个代码

首先，您可以创建一个 Hello World 应用程序，并定义您的第一条路线，如下所示:

```
*/** Required Node.js modules */*const Express = require(‘express’);*/** Initialize Express */*const app = Express();app.use(Express.json());const port = 3000;*/** Routes */*app.get(‘/’, (req, res) => {res.send(“Hello, API server!”);});*/** Server */*app.listen(port, () => {console.log(`API listening on localhost: ${port}`);});
```

上面的代码实现了一个 Express API 并定义了一条路由。首先使用语句`const Express = require(‘express’);`导入所需的模块。

然后为 Express 实例和 API 将监听的端口定义变量。您将通过一个函数调用来定义 Express API 的根路由。在这种情况下，您使用`[app.get()](https://expressjs.com/en/api.html#app.get)`函数为 HTTP get 请求定义一个处理程序。

`app.get()`的第一个参数指定 Express 将在哪个路径上监听请求，第二个参数是一个回调，包含 req 和 res 对象，它们分别包含 HTTP 请求和响应。

您将把路由处理器逻辑放在`app.get()`的主体中。为了演示功能，当用户访问根目录时，您的 API 服务器会显示一条 hello 消息。

最后，调用 Express listen 方法并记录一些关于服务器的信息。在这个例子中，一条短消息被打印到控制台，说明服务器正在运行，并显示 API 服务器正在监听的端口。

你可能注意到这个例子还没有包括 Prisma 和 Joi。没关系，重点是在添加任何东西之前启动服务器。

现在您的代码已经编写好了，您可以使用以下命令启动服务器:

```
node index.js
```

您将看到我们记录到控制台的消息:

![](img/e110dfe154cc6d031c2534c73f4ca4e5.png)

正在运行的节点服务器的输出。

当您打开浏览器并访问 [http://localhost:3000](http://localhost:3000/) 时，您应该会看到 hello 消息:

![](img/57140e161ae8bab1168ffd1453b846b3.png)

运行应用程序时您将看到的欢迎消息。

一旦你达到了那个阶段，你可以在控制台中用 Ctrl+C 关闭服务器，并重新运行`node index.js`来重启它。

# 为类型安全 API 介绍 Joi 和 Prisma

为了说明 API 中类型安全的重要性，下面是一个不安全 API 服务器的示例:

```
const Express = require('express');*/** Initialize Express */*const app = Express();app.use(Express.json())const port = 3000;*/** Routes */*app.post('/unsafe_add', (req, res) => { let answer = Number(req.body.integer) + 10; res.send(String(answer));});*/** Server */*app.listen(port, () => { console.log(`API listening on localhost: ${port}`);});});
```

如您所见，代码类似于前面的例子。这个 API 服务器实现了一个名为`unsafe_add`的路由，它将给定的数字加 10。这个路由的逻辑将一个名为 integer 的主体参数转换为一个数字，然后将其加到 10，并将结果作为一个字符串发送回来。

下面是使用 [Postman](https://www.postman.com/home) 的请求和响应示例:

![](img/544827e10e916c930260d776e4315d2f.png)

乍一看，它似乎工作正常。但是如果你不小心传入了一个不能被强制转换为数字的值呢？例如，单词“二十五”:

![](img/c66c6e6f4fb4c9c1f960e87ab74d844d.png)

这一次，API 返回非数字(NaN)值，因为它无法成功执行转换。想象一下在没有类型安全的大型生产代码库中您可能面临的问题。

# 用 Prisma 和 Joi 实现类型安全 API

既然您已经看到了拥有类型安全 API 服务器的价值，那么是时候关注实现了。

## 导入和实例化新的 Prisma 客户端

首先，您需要将 Prisma 所需的模块添加到源代码中，并实例化一个新的 Prisma 客户端:

```
*/** Required Node.js modules */*const Prisma = require('prisma/prisma-client');*/** Instantiate Prisma client */*const prisma = new Prisma.PrismaClient();
```

现在客户机已经被导入并实例化，但是在 Prisma 初始化之前它还不能使用。在您的项目目录中运行此命令:

```
npx prisma init
```

当该命令完成时，您将会注意到项目目录中的新文件。Prisma 在您的根目录下创建了一个名为。存储环境变量的 env。它还创建了一个名为`prisma`的目录，并在该目录中创建了一个名为`schema.prisma`的文件。

编辑您的`.env`文件并指定关于您的数据库连接的信息。如果您还没有在本地运行的 MySQL 数据库，您可以购买托管数据库。

为了使 Prisma 能够连接到您的数据库，您必须更改存储在`.env`中的 URI 以匹配您的设置:

```
DATABASE_URL=localhost://username:password@localhost:3306/bakery_demo
```

在上面的 URL 中，您应该编辑主机、用户名、密码和表以匹配您的设置。有关 URL 格式的更多信息，请参见 MySQL 的 [Prisma URL 连接指南](https://www.prisma.io/docs/reference/database-reference/connection-urls)。

您还需要为您的 API 编写一个 [Prisma 模式](https://www.prisma.io/docs/concepts/components/prisma-schema)。这个模式将描述存储在数据库中的对象，这样 Prisma 可以生成一个客户机来存储和检索它们。本例将使用 [Prisma bakery 演示](https://github.com/chief-wizard/prisma-fastify-bakery)中的供应商表作为模式的模型。

通过编辑文件`prisma/schema.prisma`创建您的模式。首先，您的模式将包含有关数据库连接的信息:

```
datasource db { provider = "mysql" url      = env("DATABASE_URL")} generator client { provider = "prisma-client-js"}
```

提供者与您的数据库相匹配是很重要的。Prisma 数据库连接器默认为 postgresql，但是对于这个项目，您应该将其更改为 mysql。查看 [Prisma 数据库连接器](https://www.prisma.io/docs/concepts/database-connectors#overview)的完整列表，了解更多信息。

指定正确的数据库连接器后，将数据库模型添加到 prisma/prisma.schema 中 Prisma 模式的末尾。本演示中的供应商模型包含一个表示供应商 id 的整数字段，其余字段为字符串:

```
model Supplier { id        Int    @id @default(autoincrement()) name      String @unique contact   String email     String phone_num String address   String}
```

运行以下命令将您的模式推送到数据库，并为您的 API 生成一个 Prisma 客户端:

```
npx prisma db push
```

生成客户端后，您将能够使用它来查询您的数据库。由于客户端对于您的项目是唯一的，因此您将能够使用 IntelliSense 访问数据库中的字段。

现在是时候设置 Joi 来验证传入 API 的请求了。

# 导入 Joi 并创建 Joi 模式

设置 Joi 的第一步是导入模块。在代码的顶部，添加以下几行:

```
const Joi = require(‘joi’)
const joiMiddleware = require(‘express-joi-validation’).createValidator({})
```

第一行将导入核心 Joi 模块，并允许您编写验证规则。第二行导入 Joi 模块，该模块包含用于 Express APIs 的中间件。

Joi 中间件是传递给路由的一个函数，它在路由逻辑之前被调用。如果验证失败，路由逻辑永远不会执行。

Joi 使用自己的模式来验证传递给 API 的输入。您可以基于`prisma/schema.prisma`中的 Joi 模式。您将需要对相同的字段建模，以便 Joi 可以正确地验证供应商对象。将验证器模式添加到您的`index.js`文件中:

```
*/** Supplier validation schema for Joi */*const supplierSchema = Joi.object({ Id: Joi.number().greater(0), name: Joi.string().alphanum().min(1).max(191).required(), contact: Joi.string().alphanum().min(1).max(191).required(), email: Joi.string().email().max(191).required(), phone_num: Joi.string().length(10).pattern(/*^*[0-9]+*$*/).required(), address: Joi.string().alphanum().min(1).max(191).required(),});
```

如您所见，Joi 的验证模式是由链接在一起的方法组成的。id 字段将只接受大于 0 的数字作为输入。其他字段必须至少有一个字符长。

该示例使用最多 191 个字符作为字符串，因为这是 MySQL 模式中每个字段的最大长度。默认情况下，Prisma 将模型中的[字符串映射到 MySQL 中的 varchar(191)字段。](https://www.prisma.io/docs/reference/api-reference/prisma-schema-reference#model-field-scalar-types)

以下是 Joi 更多验证方法的列表:

*   `bool()`。用于验证布尔对象的 Joi 类型。
*   `bool().falsy()`。如果布尔值为假或等于假的值，如 0，则有效。
*   `bool().truthy()`。如果布尔值为真或等于真的值，如 1，则有效。
*   `bool().validate(value)`。如果布尔值与值匹配，则有效，值可以是真或假。
*   `date()`。用于验证日期或时间对象的 Joi 类型。
*   `date().greater(date)`。如果日期早于以 MM-DD-YYY 格式指定的日期，则有效。
*   `date().less(date)`。如果日期晚于以 MM-DD-YYY 格式指定的日期，则有效。
*   `date().timestamp([options])`。如果时间戳与指定选项的格式相同，则有效，可以是 unix 或 javascript。
*   `number()`。用于验证数字对象的 Joi 类型。
*   `number().greater(value)`。如果数字大于指定值，则有效。
*   `number().less(value)`。如果数字小于指定值，则有效。
*   `number().max(value)`。如果数字小于最大指定值，则有效。
*   `number().min(value)`。如果数字大于指定的最小值，则有效。
*   `number().positive()`。如果数字为正数，则有效。
*   `number().negative()`。如果数字为负数，则有效。
*   `number().validate(value)`。如果数字与指定值匹配，则有效。
*   `string()`。用于验证字符串对象的 Joi 类型。
*   `string().alphanum()`。如果字符串只包含字母数字字符，则有效。
*   `string().creditCard()`。如果使用 [Luhn 算法](https://en.wikipedia.org/wiki/Luhn_algorithm)发现字符串是信用卡号，则有效。
*   `string().domain([options])`。如果字符串是域名，则有效。可以指定一系列选项。
*   `string().email([options])`。如果字符串是电子邮件地址，则有效。可以指定一系列选项。
*   `string().length(value)`。如果字符串长度与指定值匹配，则有效。
*   `string().lowercase()`。如果字符串是小写的，则有效。默认情况下，非小写的字符串将被强制转换为小写。
*   `string().min(value)`。如果字符串长度超过最小指定值，则有效。
*   `string().max(value)`。如果字符串短于最大指定值，则有效。
*   `string().pattern(regex)`。如果字符串与 regex 中指定的正则表达式匹配，则有效。
*   `string().uppercase()`。如果字符串是大写的，则有效。默认情况下，非大写的字符串将被强制转换为大写。

Joi 模式验证方法的完整列表可以在 [Joi API 文档](https://joi.dev/api/)中找到。为了清楚起见，下面是到目前为止的源代码:

```
*/** Required Node.js modules */*const Express = require('express');const Joi = require('joi')const joiMiddleware = require('express-joi-validation').createValidator({})*/** Supplier validation schema for Joi */*const supplierSchema = Joi.object({ id: Joi.number().greater(0), name: Joi.string().alphanum().min(1).max(191), contact: Joi.string().alphanum().min(1).max(191), email: Joi.string().email().max(191), phone_num: Joi.string().length(10).pattern(/*^*[0-9]+*$*/), address: Joi.string().alphanum().min(1).max(191),});*/** Initialize Express */*const app = Express();app.use(Express.json());const port = 3000;*/** Routes */*app.get('/', (req, res) => { res.send("Hello, API server!");});*/** Server */*app.listen(port, () => { console.log(`API listening on localhost: ${port}`);});
```

# 实施 CRUD

完成设置并编写好验证器后，现在可以专注于用 Prisma 编写类型安全的 Javascript 了。在本节中，您将学习为您的 API 编写所有的 CRUD 方法。

每个 CRUD 操作都将在 Routes 部分用一个新方法编写。在生产环境中，这些文件通常被分割成它们自己的源文件。但是出于演示的目的，这个例子将把所有东西都留在`index.js`中。

# 用 Prisma 和 MySQL 创建记录

记录通常是通过 POST 请求创建的，因此您需要遵守这个约定。以下示例包括以下处理记录创建的途径:

```
*/** Create a new supplier and write it to MySQL */*app.post("/supplier/create", joiMiddleware.query(supplierSchema), async (req, res) => { *try* { */** Use Prisma to write the data to our MySQL database */* res.body = *await* prisma.supplier.create({ data: { name: req.body.name, contact: req.body.contact, email: req.body.email, phone_num: req.body.phone_num, address: req.body.address, } }); */** Send a response telling the user our data has been written */* res.status(200).send({"message": "OK"}); } */** If Prisma fails to write to the MySQL database */* */** Catch the error and send it as our response */* *catch* (err) { res.status(500).send(err); }});
```

查看上面的代码，您可以在路由定义中看到对 Joi 中间件的查询。但是 Prisma 正在进行自己的验证，它抛出的任何错误都由 catch 块处理。例如，Prisma 模型指定每个供应商需要一个唯一的名称。您可以使用 Postman 来查看 Prisma 如何处理重名:

![](img/8dbcdcec555b3eb2aed667a8591a4af7.png)

在 Postman 接口中显示的具有重复名称的 API 请求的 500 内部服务器错误状态。

请注意 500 Internal Server 错误，以及 JSON 响应中的 target 字段，指出 Prisma 需要一个惟一的名称。

# 用 Prisma 从 MySQL 中读取记录

这是另一个使用 Prisma 的方法，但是这次您将返回一个与给定名称匹配的 Supplier 对象。

因为您将根据名称返回有关供应商的所有信息，所以这是您需要验证的唯一变量。有了新模式后，编写代码返回您的供应商对象:

```
*/** Get a supplier by name, return the object in our response */*app.get('/supplier', async (req, res) => { *try* { const supplier = *await* prisma.supplier.findUnique({ where: { name: req.body.name, } }); /** Send the Supplier object as our response */ res.status(200).send({"object": supplier}); } */** When Prisma fails to read from database, catch the error and send it as our response */* *catch* (err) { res.status(500).send(err); }});
```

当一个有效的名称被传递给 API 服务器上的 `/supplier`路径时，供应商对象作为一个 JSON 数组返回:

![](img/078080f6f624462ec688cec5d7c80c94.png)

成功请求中的供应商对象 JSON。

您可以在左边看到查询，在右边看到响应。Prisma 能够使用它的`[findUnique()](https://www.prisma.io/docs/reference/api-reference/prisma-client-reference#findfirst)` [方法](https://www.prisma.io/docs/reference/api-reference/prisma-client-reference#findfirst)找到供应商对象。

# 用 Prisma 更新 MySQL 中的记录

接下来，尝试构建更新方法。在本例中，您将使用供应商 id 来更新相应的对象。一旦找到正确的供应商，Prisma 将修补更新的数据，并返回一条表示成功的消息:

```
*/** Update an existing supplier */*app.patch("/supplier/update", joiMiddleware.query(supplierSchema), async (req, res) => { *try* { */** Use Prisma to write the data to our MySQL database */* *await* prisma.supplier.update({ where: { id: req.body.id, }, data: { name: req.body.name, contact: req.body.contact, email: req.body.email, phone_num: req.body.phone_num, address: req.body.address, } }); */** Send a response telling the user our data has been written */* res.status(200).send({"message": "OK"}); }*/** When Prisma fails to write to database, catch the error and send it as response */* *catch* (err) { res.status(500).send(err); }})
```

如果仔细观察，您可能会注意到 update 方法看起来像是 read 和 create 的组合。该示例首先通过 id 进行查询，然后使用 Prisma 来覆盖 Supplier 对象中的 name、contact、email、phone_num 和 address 字段。

您可能会惊讶地看到该示例在所有字段中都通过了测试。很容易想象 MySQL 可能会用空字符串覆盖数据库中的信息。但是 JSON 对象会自动删除未定义的字符串，所以更新整个对象是安全的。

现在检查数据库，看看更新是否有效。下面的例子使用 [Prisma Studio](https://www.prisma.io/studio) 浏览数据库。以下是更新前的数据:

![](img/8486690f60087d7f2148a27fe58b236c.png)

Prisma Studio 界面中显示的原始供应商记录。

下面是更新请求后的数据:

![](img/b74bb7f0cace63aff521c875c55bb5ce.png)

Prisma Studio 界面中显示的更新后的供应商记录。

如您所见，联系人姓名和电子邮件地址按预期进行了更改。

# 用 Prisma 从 MySQL 中删除记录

删除供应商对象与读取非常相似。在这种情况下，可以使用 delete()方法，而不是使用 Prisma 的 findFirst()方法。以下是删除操作的代码:

```
*/** Delete a supplier with a given name */*app.delete('/supplier/delete', joiMiddleware.query(supplierSchema), async (req, res) => { *try* { const supplier = *await* prisma.supplier.delete({ where: { name: req.body.name, } }); res.status(200).send({"message": "deleted OK"}); } */** If Prisma fails to delete the object, catch the error and send it as response */* *catch* (err) { res.status(500).send(err); }});
```

您可以在收到预期响应时确认对象已删除:

![](img/3b7aabd3581c264f1c387321134311fe.png)

对象删除的确认。

实现了 delete 方法之后，所有的 CRUD 操作都实现了。最终的 API 很稀疏，但是很完整。

# 使用 Prisma 进行类型安全的 Javascript API 调用

从代码中可以看出，通过 Prisma 和仔细的验证，可以在 Javascript 中获得某种程度的类型安全。另一个解决方案可能是使用 Typescript 实现这个 API，这在默认情况下是类型安全的。但是对于许多项目来说，Prisma 提供的类型安全级别已经足够了。

下面是完整的源代码供参考:

# 改进和变化

如果您对使用 Javascript 和 Prisma 进行后端编程感兴趣，您可能想要花一些时间来完善这个 CRUD 实现。这个例子是基于为了保持事情简单而做出的决定。

您可能会对[路线和控制器逻辑](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/routes)进行更改。它们应该被完全分割成它们自己的文件。生产 API 以模块化的方式路由和控制器。

# Prisma 为 Javascript 带来了类型安全

这个例子只涉及到 Prisma 的一些特性。它提供了一种简单的、声明性的方法来建模大型或复杂的数据库。因为您只建模了一个表，所以您可能没有意识到它的伸缩性有多好。

Prisma 提供的不仅仅是类型安全的数据库查询。Prisma Studio 和 CLI 简化了数据库管理和迁移。

对于使用 Prisma 的深入 API 实现，看一看[用 TypeScript、PostrgreSQL 和 Prisma](https://www.prisma.io/blog/series/modern-backend-bdes2ps5kibb) 构建一个现代后端。如果需要指导，可以[加入 Prisma Slack 频道](https://slack.prisma.io/)。
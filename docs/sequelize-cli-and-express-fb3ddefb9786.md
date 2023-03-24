# 排序 CLI 和 Express

> 原文：<https://levelup.gitconnected.com/sequelize-cli-and-express-fb3ddefb9786>

![](img/021463944331716371fec71e4ce6afef.png)

Sequelize 是一个流行的、易于使用的 JavaScript 对象关系映射(ORM)工具，可用于 SQL 数据库。如果你习惯于使用 Sequelize 模型查询数据库，那么将 Sequelize 与 Express 结合起来就非常简单了。

在本演练中，我们将从头开始一个 Sequelize 项目，然后合并 Express 来构建将从您的数据库中传递信息的基本路线。

让我们从在一个新的项目文件夹中安装 Postgres、Sequelize 和 Sequelize CLI 开始:

```
mkdir express-sequelize-api
cd express-sequelize-api
npm init -y && npm install sequelize pg &&  npm install --save-dev sequelize-cli
```

接下来，我们将初始化一个 Sequelize 项目，然后在代码编辑器中打开它:

```
npx sequelize-cli init
code .
```

> 要了解以下任何顺序化 CLI 命令的更多信息，请参见:
> [**顺序化 CLI 入门**](https://medium.com/@brunopgalvao/getting-started-with-sequelize-cli-c33c797f05c6)

让我们配置我们的 Sequelize 项目来使用 Postgres。在`/config`目录中找到`config.json`,确保`development`部分如下所示:

```
"development": {
    "database": "products_development",
    "host": "127.0.0.1",
    "dialect": "postgres"
  }
```

很好，现在我们可以告诉 Sequelize CLI 创建数据库了:

```
npx sequelize-cli db:create
```

接下来，我们将创建一个`Product`模型:

```
npx sequelize-cli model:generate --name Product --attributes title:string,description:string,price:integer
```

运行`model:generate`会自动创建一个模型文件和一个带有我们指定属性的迁移。现在，你不需要担心(或编辑)这些来制作下面的简单快速应用程序。

> 看看其他可以和 Sequelize 一起使用的数据类型:[https://sequelize.org/master/manual/data-types.html](https://sequelize.org/master/manual/data-types.html)

现在我们将执行迁移，在数据库中创建`Products`表:

```
npx sequelize-cli db:migrate
```

现在让我们创建一个种子文件:

```
npx sequelize-cli seed:generate --name products
```

您将在`/seeders`目录中看到一个新文件。在该文件中，粘贴下面的代码，这将在您的数据库中为以下每个不可抗拒的产品创建一个条目:

```
'use strict';module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.bulkInsert('Products', [{
      title: 'Apple AirPods',
      description: "https://www.apple.com/airpods",
      price: 199,
      createdAt: new Date(),
      updatedAt: new Date()
    },
    {
      title: 'Apple iPhone Pro',
      description: "https://www.apple.com/iphone-11-pro",
      price: 1000,
      createdAt: new Date(),
      updatedAt: new Date()
    },
    {
      title: 'Apple Watch',
      description: "https://www.apple.com/watch",
      price: 499,
      createdAt: new Date(),
      updatedAt: new Date()
    },
    {
      title: 'Vespa Primavera',
      description: "https://www.vespa.com/us_EN/vespa-models/primavera.html",
      price: 3000,
      createdAt: new Date(),
      updatedAt: new Date()
    },
    {
      title: 'New Balance 574 Core',
      description: "https://www.newbalance.com/pd/574-core/ML574-EG.html",
      price: 84,
      createdAt: new Date(),
      updatedAt: new Date()
    },
    {
      title: 'Tribe Messenger Bike 004',
      description: "https://tribebicycles.com/collections/messenger-series/products/mess-004-tx",
      price: 675,
      createdAt: new Date(),
      updatedAt: new Date()
    },
    {
      title: 'Stumptown Hair Bender Coffee',
      description: "https://www.stumptowncoffee.com/products/hair-bender",
      price: 16,
      createdAt: new Date(),
      updatedAt: new Date()
    }], {});
  }, down: (queryInterface, Sequelize) => {
    return queryInterface.bulkDelete('Products', null, {});
  }
};
```

一旦我们保存了种子文件，让我们执行它:

```
npx sequelize-cli db:seed:all
```

进入`psql`并查询数据库，看看`Products`表中有什么:

```
psql products_development
SELECT * FROM "Products";
```

你应该会看到一辆时髦的 Vespa，一些浓咖啡，一些苹果产品，还有其他东西。太好了！

# 设置 Express

现在我们的数据库已经设置好了，让我们通过 Node.js 服务器合并 Express 来交付这些项目。Express 是一个高度可定制的框架，具有许多功能，但对于这个示例，我们将快速设置一些基本路线。

首先，让我们在项目目录中安装 Express 并创建一个文件来保存我们的服务器代码:

```
npm install express
touch server.js
```

快速启动并运行不需要太多配置。在您的`server.js`文件中，只需添加以下内容:

```
const express = require('express')
const PORT = process.env.PORT || 3000const app = express()app.listen(PORT, () => {
  console.log(`Express server listening on port ${PORT}`)
});app.get('/', (req, res) => {
  res.send("This is root!")
})
```

第一行拉入 Express 库，第二行设置默认端口(`3000`)，第三行启动 Express。现在`app`指的是你的跑步快递 app，我们可以用`.listen()`和`.get()`之类的方法告诉它做什么！

现在启动你的应用程序…

```
node server.js
```

…并在网络浏览器中打开`localhost:3000`。您应该看到“这是 root！”—这是因为我们在代码中使用了`app.listen()`来监听那个端口，并且我们使用了`app.get()`来在`'/'`建立路由。(在终端中按`ctrl` + `c`退出。)

# 在 Express 中使用 Sequelize

厉害！现在我们可以将我们的服务器连接到 Sequelize。首先，我们需要从`/models`文件夹中引入我们的`Product`模型。在`server.js`顶部附近添加此行:

```
const { Product } = require('./models')
```

现在，我们可以创建一条路线来显示我们的整个产品列表。我们将使用`app.get()`在`'/products'`设置一条路线，并使用我们的 Sequelize 模型的`.findAll()`方法返回每个`Product`:

```
app.get('/products', async (req, res) => {
    const products = await Product.findAll()
    res.json(products)
})
```

通过在您的终端中键入`node server.js`来重新启动服务器，然后通过转到[http://localhost:3000/products](http://localhost:3000/products)在您的浏览器中测试路由。您应该会看到一个 JSON 响应，其中包含了从 Apple Watch 到 messenger bike 的所有对象。

好的，太好了！但是，如果我们希望看到一个特定的产品呢？假设我们在浏览器中导航到[http://localhost:3000/products/2](http://localhost:3000/products/2)。我们可以让我们的 API 只响应`id`为`2`的产品——为此我们将在 Express 中使用`req.params`对象:

```
app.get('/products/:id', async (req, res) => {
  const { id } = req.params
  const product = await Product.findById(id)
  res.json(product)
})
```

[Express 中的](https://expressjs.com/en/api.html#req) `[req](https://expressjs.com/en/api.html#req)` [对象](https://expressjs.com/en/api.html#req)代表用户的 HTTP 请求。`req.params`属性包括我们在路由中命名的参数——这些参数用冒号设置，就像上面的`'/products/:id'`中的`:id`。有了`req.params.id`，我们可以使用 Sequelize 的`.findById()`方法只获取其`id`与我们的 URL 匹配的对象。

好吧，但是如果数据库中不存在`id`呢？目前，我们的用户将得到一个 JSON 响应，上面只写着`null`。我们可以通过使用 try/catch 块来发送更有用的错误消息:

```
app.get('/products/:id', async (req, res) => {
    try {
        const { id } = req.params
        const product = await Product.findByPk(id)
        if (!product) throw Error('Product not found')
        res.json(product)
    } catch (e) {
        console.log(e)
        res.send('Product not found!')
    }
})
```

有用吗？重新启动服务器并测试路由。

```
node server.js
```

在浏览器中打开[http://localhost:3000/products/2](http://localhost:3000/products/2)。嘿，这是 iPhone！(您还可以通过导航到/products/987654321 来测试错误处理。)

# 使用 Express 和 Sequelize 更进一步

现在您知道了如何在 Express 中定义端点，这些端点通过一个简单的序列模型从您的数据库中传递数据——并且您可以通过使用带有不同序列查询的[其他 Express 请求方法](https://expressjs.com/en/starter/basic-routing.html)来扩展您的项目。

但是，当您修补您的 Express 项目时，每次更改都要重新启动服务器可能是一件痛苦的事情。让我们通过安装 [nodemon](https://nodemon.io/) 来解决这个问题吧！

```
npm install nodemon --save-dev
```

修改您的`package.json`文件:

```
....
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "nodemon server.js"
  },
....
```

现在，当您从终端运行`npm start`时，nodemon 将监视您的 JavaScript 文件的变化，并自动重启您的服务器——这样，当您使用其他顺序查询方法 **设计新的路线 [**时，您就不必再回到您的终端了！**](/using-the-sequelize-cli-and-querying-4ba8d0ac4314)**

> 本文是与纽约市的软件工程师、编辑和作家杰里米·罗斯(Jeremy Rose)合著的。

# 有关 Sequelize CLI 的更多信息:

*   【Sequelize CLI 入门
*   [使用顺序命令行界面并查询](https://medium.com/@brunopgalvao/using-the-sequelize-cli-and-querying-4ba8d0ac4314)
*   [使用序列 CLI 创建序列关联](https://medium.com/@brunopgalvao/creating-sequelize-associations-with-the-sequelize-cli-tool-d83caa902233)
*   [使用 Faker 开始 Sequelize CLI](https://medium.com/@brunopgalvao/getting-started-with-sequelize-cli-using-faker-824b3f4c4cfe)
*   [使用序列 CLI 和快速路由器构建快速 API](https://medium.com/@brunopgalvao/build-an-express-api-with-sequelize-cli-and-express-router-963b6e274561)
*   使用序列 CLI 和单元测试构建 Express API！
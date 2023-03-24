# 使用序列 CLI 和单元测试构建 Express API！

> 原文：<https://levelup.gitconnected.com/building-an-express-api-with-sequelize-cli-and-unit-testing-882c6875ed59>

![](img/021463944331716371fec71e4ce6afef.png)

单元测试对于软件开发人员来说是一个有用的习惯。对于任何代码可能变得更加复杂的项目，单元测试有助于确保应用程序的核心功能得到维护，即使发生了更改。

即使对于相对小规模的 Node.js 应用程序，也可以通过使用像 Jest 和 SuperTest 这样的 npm 包来包含单元测试。在本演练中，我们将使用 Sequelize 和 Express 构建一个基本的 API，然后添加单元测试以确保我们的 CRUD 端点保持不变。

# 创建顺序化应用程序

我们将尽快完成初始设置，以便进行单元测试——但是我们会在过程中为那些想了解更多有关使用 Sequelize CLI with Express 的人提供注释。

让我们从安装 Postgres、Sequelize 和 [Sequelize CLI](https://github.com/sequelize/cli) 开始，在一个新的项目文件夹中，我们称之为`express-api-unit-testing`:

```
mkdir express-api-unit-testing
cd express-api-unit-testing
git init
npm init -y && npm i sequelize pg && npm i --save-dev sequelize-cli
```

让我们添加一个`.gitignore`文件，以便于以后的部署:

```
echo "
/node_modules
.DS_Store
.env" >> .gitignore
```

接下来，我们将初始化一个 Sequelize 项目，然后在代码编辑器中打开目录:

```
npx sequelize-cli init
code .
```

> 要了解有关以下序列化 CLI 命令的更多信息，请参见:
> [**序列化 CLI 入门**](https://medium.com/@brunopgalvao/getting-started-with-sequelize-cli-c33c797f05c6)

让我们配置我们的 Sequelize 项目来使用 Postgres 数据库。在`/config`目录中找到`config.json`，并修改代码，如下所示:

```
{
  "development": {
    "database": "wishlist_api_development",
    "dialect": "postgres"
  },
  "test": {
    "database": "wishlist_api_test",
    "dialect": "postgres"
  },
  "production": {
    "use_env_variable": "DATABASE_URL",
    "dialect": "postgres",
    "dialectOptions": {
      "ssl": {
        "rejectUnauthorized": false
      }
    }
  }
}
```

> 注:对于`*production*`，我们使用`*use_env_variable*`和`*DATABASE_URL*`。我们将把这个应用程序部署到 Heroku。Heroku 很聪明，他用生产数据库取代了 T7，我们将在稍后的 *r.* 看到它的实际应用

现在我们可以告诉 Sequelize CLI 创建 Postgres 数据库:

```
npx sequelize-cli db:create
```

# 定义模型和添加种子数据

我们的演示应用程序将用户与愿望清单上的项目相关联。让我们从使用 Sequelize CLI 创建一个`User`模型开始:

```
npx sequelize-cli model:generate --name User --attributes firstName:string,lastName:string,email:string,password:string
```

运行`model:generate`会自动创建一个模型文件和一个带有我们指定属性的迁移。现在我们可以执行迁移，在数据库中创建`Users`表:

```
npx sequelize-cli db:migrate
```

现在让我们创建一个种子文件:

```
npx sequelize-cli seed:generate --name users
```

您将在`/seeders`目录中看到一个新文件。在该文件中，粘贴以下代码，这将在您的数据库中为三个用户创建条目:

```
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.bulkInsert('Users', [{
      firstName: 'Bruno',
      lastName: 'Doe',
      email: 'bruno@doe.com',
      password: '123456789',
      createdAt: new Date(),
      updatedAt: new Date()
    },
    {
      firstName: 'Emre',
      lastName: 'Smith',
      email: 'emre@smith.com',
      password: '123456789',
      createdAt: new Date(),
      updatedAt: new Date()
    },
    {
      firstName: 'John',
      lastName: 'Stone',
      email: 'john@stone.com',
      password: '123456789',
      createdAt: new Date(),
      updatedAt: new Date()
    }], {});
  }, down: (queryInterface, Sequelize) => {
    return queryInterface.bulkDelete('Users', null, {});
  }
};
```

在我们的 API 中，这些用户中的每一个都可以有许多愿望清单项目。让我们创建一个`Item`模型，让这些用户有所期待:

```
npx sequelize-cli model:generate --name Item --attributes title:string,link:string,userId:integer
```

现在，我们将创建两个模型之间的关联。

> 要了解有关创建序列关联的更多信息，请参阅:
> [**使用序列 CLI 创建序列关联**](https://medium.com/@brunopgalvao/creating-sequelize-associations-with-the-sequelize-cli-tool-d83caa902233)

首先，在`/models`子目录中找到`item.js`，将代码替换为:

```
module.exports = (sequelize, DataTypes) => {
  const Item = sequelize.define('Item', {
    title: DataTypes.STRING,
    link: DataTypes.STRING,
    userId: {
      type: DataTypes.INTEGER,
      references: {
        model: 'User',
        key: 'id',
        as: 'userId',
      }
    }
  }, {});
  Item.associate = function (models) {
    // associations can be defined here
    Item.belongsTo(models.User, {
      foreignKey: 'userId',
      onDelete: 'CASCADE'
    })
  };
  return Item;
};
```

现在在同一个目录中找到`user.js`,将代码替换为:

```
module.exports = (sequelize, DataTypes) => {
  const User = sequelize.define('User', {
    firstName: DataTypes.STRING,
    lastName: DataTypes.STRING,
    email: DataTypes.STRING,
    password: DataTypes.STRING
  }, {});
  User.associate = function(models) {
    // associations can be defined here
    User.hasMany(models.Item, {
      foreignKey: 'userId'
    })
  };
  return User;
};
```

执行迁移以在 Postgres 数据库中创建`Items`表:

```
npx sequelize-cli db:migrate
```

让我们为我们的用户创建一些想要的项目:

```
npx sequelize-cli seed:generate --name items
```

您将在您的`/seeders`子目录中看到一个以`items.js`结尾的新文件。将该文件中的代码更改为以下内容:

```
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.bulkInsert('Items', [{
      title: 'Moped',
      link: 'https://detroitmopedworks.com',
      userId: 1,
      createdAt: new Date(),
      updatedAt: new Date()
    },
    {
      title: 'iPad Mini',
      link: 'https://www.apple.com/ipad-mini',
      userId: 3,
      createdAt: new Date(),
      updatedAt: new Date()
    },
    {
      title: 'Electric Scooter',
      link: 'https://swagtron.com/electric-scooter',
      userId: 1,
      createdAt: new Date(),
      updatedAt: new Date()
    },
    {
      title: 'Monitor',
      link: 'https://www.asus.com/us/Monitors/MB168B',
      userId: 2,
      createdAt: new Date(),
      updatedAt: new Date()
    }], {});
  }, down: (queryInterface, Sequelize) => {
    return queryInterface.bulkDelete('Items', null, {});
  }
};
```

现在，我们将运行两个种子文件，将我们的用户和我们的意愿列表项目添加到数据库中:

```
npx sequelize-cli db:seed:all
```

确保数据存在于数据库中:

```
psql wishlist_api_development
SELECT * FROM "Users" JOIN "Items" ON "Users".id = "Items"."userId";
```

# 设置 Express

太好了，我们的项目已经准备好了。现在，我们可以合并 Express 并设置路由来服务我们的数据。首先，让我们安装 Express，连同 [nodemon](https://nodemon.io/) 来监控我们文件中的变化，以及 [body-parser](https://www.npmjs.com/package/body-parser) 来处理来自用户请求的信息:

```
npm install express --save
npm install nodemon -D
npm install body-parser
```

现在，让我们通过创建两个新目录和三个新文件来设置体系结构:

```
mkdir routes controllers
touch server.js  routes/index.js controllers/index.js
```

现在我们将修改`package.json`文件来支持 nodemon。此外，我们可以通过创建一个新命令来促进开发:`npm run db:reset`。我们将设置删除数据库、创建数据库、运行迁移，并在需要时重新播种！

```
....
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "nodemon server.js",
    "db:reset": "npx sequelize-cli db:drop && npx sequelize-cli db:create && npx sequelize-cli db:migrate && npx sequelize-cli db:seed:all"
  },
....
```

现在让我们开始构建我们的 Express 应用程序。在`server.js`文件中，添加以下内容:

```
const express = require('express');
const routes = require('./routes');
const bodyParser = require('body-parser')const PORT = process.env.PORT || 3000;const app = express();app.use(bodyParser.json())app.use('/api', routes);app.listen(PORT, () => console.log(`Listening on port: ${PORT}`))
```

在这里，我们创建了一个基本的 Express 服务器集来监听端口 3000。但是我们没有在这个文件中定义路由，而是添加了`app.use('/api', routes)`来引用任何以`api`开头的请求到我们的`/routes`子目录中的`index.js`文件。

> 要了解更多有关序列式快速基本设置的信息，请参见:
> [**序列式 CLI 和快速**](/sequelize-cli-and-express-fb3ddefb9786)

# 快速路由器和控制器

我们将从设置根路由开始。打开`./routes/index.js`文件并添加以下代码:

```
const { Router } = require('express');
const controllers = require('../controllers');
const router = Router();router.get('/', (req, res) => res.send('This is root!'))module.exports = router
```

测试路线:

```
npm start
```

现在在浏览器中打开根端点:[http://localhost:3000/API/](http://localhost:3000/api/)

很好，我们的 Express 应用程序可以工作，但现在我们需要让它从 Sequelize 中传递数据。我们将通过创建一个控制器来处理我们所有的逻辑——我们创建新用户和项目、更新用户等的途径。

> 要了解更多有关使用带序列和快速路由器的控制器的信息，请参见:
> [**使用序列和快速路由器构建快速 API**](/build-an-express-api-with-sequelize-cli-and-express-router-963b6e274561)

现在打开`./controllers/index.js`并添加以下内容:

```
const { User } = require('../models');const createUser = async (req, res) => {
    try {
        const user = await User.create(req.body);
        return res.status(201).json({
            user,
        });
    } catch (error) {
        return res.status(500).json({ error: error.message })
    }
}module.exports = {
    createUser
}
```

这里，我们结合了在 Sequelize 中定义的`User`模型，根据 API 请求中的信息创建一个新的数据库条目。为了实现这一点，我们将在服务器上创建一个路由来连接请求和控制器:

在`./routes/index.js`中，在您的“这是 root！”后添加新的一行路线:

```
router.post('/users', controllers.createUser)
```

这将`/api/users`处的`POST`请求导向我们控制器中的`createUser`功能。为了测试它，你需要使用一个 REST 客户端(比如[邮递员](https://www.postman.com/)或者[失眠](https://insomnia.rest/))。使用`POST`方法将下面的 JSON 主体发送到[http://localhost:3000/API/users](http://localhost:3000/api/users):

```
{
  "firstName": "Jane",
  "lastName": "Smith",
  "email": "jane@smith.com",
  "password": "123456789"
}
```

所以现在我们已经使用路由器和控制器将数据从 Sequelize 传递给我们的 API 用户。我们可以使用相同的策略将任何顺序查询连接到一个 Express 端点。

> 要了解有关自定义序列查询的更多信息，请参见: [**使用序列 CLI 和查询**](/using-the-sequelize-cli-and-querying-4ba8d0ac4314)

让我们添加控制器来执行另外四个任务:获取所有用户及其相关的意愿列表项、获取特定用户和意愿列表、更新用户和删除用户。

用以下内容替换您当前在`./controllers/index.js`中的内容:

```
const { User, Item } = require('../models');const createUser = async (req, res) => {
    try {
        const user = await User.create(req.body);
        return res.status(201).json({
            user,
        });
    } catch (error) {
        return res.status(500).json({ error: error.message })
    }
}const getAllUsers = async (req, res) => {
    try {
        const users = await User.findAll({
            include: [
                {
                    model: Item
                }
            ]
        });
        return res.status(200).json({ users });
    } catch (error) {
        return res.status(500).send(error.message);
    }
}const getUserById = async (req, res) => {
    try {
        const { id } = req.params;
        const user = await User.findOne({
            where: { id: id },
            include: [
                {
                    model: Item
                }
            ]
        });
        if (user) {
            return res.status(200).json({ user });
        }
        return res.status(404).send('User with the specified ID does not exists');
    } catch (error) {
        return res.status(500).send(error.message);
    }
}const updateUser = async (req, res) => {
    try {
        const { id } = req.params;
        const [updated] = await User.update(req.body, {
            where: { id: id }
        });
        if (updated) {
            const updatedUser = await User.findOne({ where: { id: id } });
            return res.status(200).json({ user: updatedUser });
        }
        throw new Error('User not found');
    } catch (error) {
        return res.status(500).send(error.message);
    }
};const deleteUser = async (req, res) => {
    try {
        const { id } = req.params;
        const deleted = await User.destroy({
            where: { id: id }
        });
        if (deleted) {
            return res.status(204).send("User deleted");
        }
        throw new Error("User not found");
    } catch (error) {
        return res.status(500).send(error.message);
    }
};module.exports = {
    createUser,
    getAllUsers,
    getUserById,
    updateUser,
    deleteUser
}
```

我们还将设置每条路线来击中正确的控制器。将`/routes/index.js`文件更改为如下所示:

```
const { Router } = require('express');
const controllers = require('../controllers')
const router = Router();router.get('/', (req, res) => res.send('This is root!'))router.post('/users', controllers.createUser)
router.get('/users', controllers.getAllUsers)
router.get('/users/:id', controllers.getUserById)
router.put('/users/:id', controllers.updateUser)
router.delete('/users/:id', controllers.deleteUser)module.exports = router;
```

测试这些端点中的一些，在 Postman 中发出`GET`、`POST`、`PUT`或`DELETE`请求，以及每个端点的适当请求体。例如，在[http://localhost:3000/users/3](http://localhost:3000/users/3)上的`PUT`请求的主体可能如下所示:

```
{
    "firstName": "Johnny",
    "lastName": "Stone",
    "email": "john.e@stone.com"
}
```

如果您的手动测试进展顺利，是时候开始构建自动化单元测试了！

# 记录

不过，首先，这是集成更好的日志记录的好时机。现在，如果我们在点击[http://localhost:3000/API/users/2](http://localhost:3000/api/users/2)端点时检查我们的终端，我们将看到被执行的原始 SQL。出于调试目的和更好的日志记录，让我们安装一个名为 [morgan](https://www.npmjs.com/package/morgan) 的快速中间件:

```
npm install morgan
```

修改您的`server.js`文件以使用 Morgan(并添加`module.exports = app`，我们将在稍后的测试中使用):

```
const express = require('express');
const bodyParser = require('body-parser');
const logger = require('morgan');const routes = require('./routes');const PORT = process.env.PORT || 3000;const app = express();
app.use(bodyParser.json())
app.use(logger('dev'))app.use('/api', routes);app.listen(PORT, () => console.log(`Listening on port: ${PORT}`))module.exports = app
```

让我们看看结果:

```
npm start
open [http://localhost:3000/api/users/2](http://localhost:3000/api/users/2)
```

现在，您应该在终端中看到类似这样的内容:

```
GET /api/users/2 304 104.273 ms
```

那是摩根。

# 单元测试

现在我们将为单元测试配置 Express JSON API。让我们安装[Jest](https://jestjs.io/)——一个令人愉快的专注于简单性的 JavaScript 测试框架:

```
npm install jest --save-dev
```

我们还将使用[超级测试](https://github.com/visionmedia/supertest#readme)在我们的 Express API 上测试我们的 HTTP 端点:

```
npm install supertest --save-dev
```

我们需要在`package.json`中做两处修改来配置 Jest。首先，在`"scripts"`属性下，让我们编辑我们的测试命令以使用 Jest:

```
...
"test": "jest",
...
```

我们需要 Jest 忽略我们的`./node_modules`文件夹，所以我们还需要添加以下代码片段:

```
....
 "jest": {
    "testEnvironment": "node",
    "coveragePathIgnorePatterns": [
      "/node_modules/"
    ]
  },
....
```

太好了！让我们编写一个简单的测试来确保我们的设置有效。首先，我们将为我们的测试创建一个新目录，然后我们将创建一个名为`base.test.js`的文件:

```
mkdir tests
touch tests/base.test.js
```

现在在`base.test.js`中添加以下代码，以创建一个测试 1 + 1 是否等于 2 的测试:

```
describe('Initial Test', () => {
  it('should test that 1 + 1 === 2', () => {
    expect(1+1).toBe(2)
  })
})
```

看一下这里的语法。我们传递字符串给测试一个标题，并描述它做什么。然后，如果传递给`expect()`的表达式评估为`.toBe()`内的值，测试将通过。让我们确保我们的设置工作正常:

```
npm test
```

好吧，那就放心了！Jest 帮我们查了一下，原来`1+1`T19 等于`2`。很快我们将开始把笑话付诸实践。

# 设置测试环境

首先，我们需要设置我们的测试脚本来使用测试数据库——让我们的测试操作我们的真实数据不是一个好主意。我们将通过使用一个名为[跨环境](https://www.npmjs.com/package/cross-env)的 npm 包来安排:

```
npm install cross-env --save-dev
```

Cross-env 允许我们在 npm 脚本中传递环境变量，在本例中，我们将使用这些变量来指定测试环境。让我们再次配置我们的`package.json`的`“scripts”`部分来做到这一点:

```
"test": "cross-env NODE_ENV=test jest --testTimeout=10000",
"pretest": "cross-env NODE_ENV=test npm run db:reset",
"db:create:test": "cross-env NODE_ENV=test npx sequelize-cli db:create",
```

试试看。

```
npm run db:create:test
npm test
```

在运行测试之前,`pretest`脚本会重新构建测试数据库——使用`NODE_ENV=test`可以让我们的脚本在不改变正常数据库的情况下完成所有工作。除此之外，您的终端还应该显示我们在 Jest 中的基本测试已经通过。

# 用 Jest 和 SuperTest 编写测试

是时候编写我们的第一个路由测试了。让我们为路由创建一个测试文件:

```
touch tests/routes.test.js
```

我们将使用 Jest 框架编写这个程序，并使用 SuperTest 调用 HTTP 方法。

让我们测试一下`/api/users`端点。当我们向该端点发出一个`GET`请求时，我们应该得到数据库中所有用户的列表，对吗？打开`tests/routes.test.js`并添加以下代码:

```
const request = require('supertest')
const app = require('../server.js')describe('User API', () => {
    it('should show all users', async () => {
        const res = await request(app).get('/api/users')
        expect(res.statusCode).toEqual(200)
        expect(res.body).toHaveProperty('users')
    }),
})
```

测试一下！

```
npm test
```

现在，您应该在两个测试套件中看到两个通过的测试。

接下来，我们将向同一个`User API`测试套件添加另一个测试，这个测试是针对`/api/users/3`端点的。在这个端点，`GET`请求应该从数据库返回一个特定的用户。在`tests/routes.test.js`中，将以下内容添加到先前测试的正下方:

```
 it('should show a user', async () => {
        const res = await request(app).get('/api/users/3')
        expect(res.statusCode).toEqual(200)
        expect(res.body).toHaveProperty('user')
    }),
```

测试一下！

```
npm test
```

我们可以为 API 的其他方面编写更多的测试。下面的代码包括在我们的应用中创建、更新和删除用户的测试:

```
const request = require('supertest')
const app = require('../server.js')describe('User API', () => {
    it('should show all users', async () => {
        const res = await request(app).get('/api/users')
        expect(res.statusCode).toEqual(200)
        expect(res.body).toHaveProperty('users')
    }), it('should show a user', async () => {
        const res = await request(app).get('/api/users/3')
        expect(res.statusCode).toEqual(200)
        expect(res.body).toHaveProperty('user')
    }), it('should create a new user', async () => {
        const res = await request(app)
            .post('/api/users')
            .send({
                firstName: 'Bob',
                lastName: 'Doe',
                email: 'bob@doe.com',
                password: '12345678'
            })
        expect(res.statusCode).toEqual(201)
        expect(res.body).toHaveProperty('user')
    }), it('should update a user', async () => {
        const res = await request(app)
            .put('/api/users/3')
            .send({
                firstName: 'Bob',
                lastName: 'Smith',
                email: 'bob@doe.com',
                password: 'abc123'
            })
        expect(res.statusCode).toEqual(200)
        expect(res.body).toHaveProperty('user')
    }), it('should delete a user', async () => {
        const res = await request(app)
            .del('/api/users/3')
        expect(res.statusCode).toEqual(204)
    })
})
```

您将看到新的测试使用了`.post()`、`.put()`和`.del()`方法，并且测试请求的主体可以在`.send()`中定义。

> 我们的每个示例测试都使用 Jest 的`.toEqual()`和/或`.toHaveProperty()`方法。要查看其他 Jest“匹配者”， [**查看 Jest 文档**](https://jestjs.io/docs/en/expect) 。

运行测试:

```
npm test
```

您应该在两个套件中看到六个通过的测试。我们现在有了 Express API 的测试覆盖率！

> 本文是与纽约市的软件工程师、编辑和作家杰里米·罗斯(Jeremy Rose)合著的。

# 有关 Sequelize CLI 和 Express 的更多信息:

*   【Sequelize CLI 入门
*   [使用顺序化 CLI 并查询](https://medium.com/@brunopgalvao/using-the-sequelize-cli-and-querying-4ba8d0ac4314)
*   [使用序列 CLI 创建序列关联](https://medium.com/@brunopgalvao/creating-sequelize-associations-with-the-sequelize-cli-tool-d83caa902233)
*   [使用 Faker 开始 Sequelize CLI](/getting-started-with-sequelize-cli-using-faker-824b3f4c4cfe)
*   [对 CLI 和 Express 进行排序](https://medium.com/@brunopgalvao/sequelize-cli-and-express-fb3ddefb9786)
*   [使用序列 CLI 和快速路由器构建快速 API](/build-an-express-api-with-sequelize-cli-and-express-router-963b6e274561)
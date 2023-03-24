# 使用序列 CLI 和快速路由器构建快速 API

> 原文：<https://levelup.gitconnected.com/build-an-express-api-with-sequelize-cli-and-express-router-963b6e274561>

![](img/021463944331716371fec71e4ce6afef.png)

使用用于对象关系映射的 Sequelize 和作为服务器框架的 Express，可以相当容易地构建一个成熟的 node . js API——Express 的 Router 类是一个以模块化、有组织的方式处理应用程序路线的有用工具。

在本演练中，我们将构建一个 Sequelize 项目，包括关联和种子数据，然后使用 Express Router 为我们的服务器定义路由。最后，我们将把我们的应用程序部署到 Heroku 来创建一个可公开访问的 API。

# 创建顺序化应用程序

我们将尽快完成我们的序列设置，以便开始使用路由器，但我们会为那些想了解更多有关使用序列 CLI 或定义序列关联的人提供一些注意事项。

让我们从安装 Postgres、Sequelize 和 [Sequelize CLI](https://github.com/sequelize/cli) 开始，在一个新的项目文件夹中，我们称之为`express-api-using-router`:

```
mkdir express-api-using-router
cd express-api-using-router
git init
npm init -y
npm install sequelize pg && npm install --save-dev sequelize-cli
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
    "database": "projects_api_development",
    "dialect": "postgres"
  },
  "test": {
    "database": "projects_api_test",
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

> 注:对于`production`，我们使用`use_env_variable`和`DATABASE_URL`。我们将把这个应用程序部署到 [Heroku](https://www.heroku.com/) 。Heroku 很聪明，他用生产数据库代替了`DATABASE_URL`，我们将在稍后的 *r.* 中看到这一点

现在我们可以告诉 Sequelize CLI 创建 Postgres 数据库:

```
npx sequelize-cli db:create
```

# 定义模型和添加种子数据

我们的演示应用程序将用户与某些项目相关联。让我们从使用 Sequelize CLI 创建一个`User`模型开始:

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
      firstName: 'John',
      lastName: 'Doe',
      email: 'john@doe.com',
      password: '123456789',
      createdAt: new Date(),
      updatedAt: new Date()
    },
    {
      firstName: 'John',
      lastName: 'Smith',
      email: 'john@smith.com',
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

你会注意到我们把三个用户都命名为“John”…只是因为。现在让我们添加一个`Project`模型，这样我们可以给这些约翰一些事情做:

```
npx sequelize-cli model:generate --name Project --attributes title:string,imageUrl:string,description:text,userId:integer
```

现在，我们将创建两个模型之间的关联。

> 要了解有关创建序列关联的更多信息，请参阅:
> [**使用序列 CLI 创建序列关联**](https://medium.com/@brunopgalvao/creating-sequelize-associations-with-the-sequelize-cli-tool-d83caa902233)

首先，在`/models`子目录中找到`user.js`，将代码替换为:

```
module.exports = (sequelize, DataTypes) => {
  const Project = sequelize.define('Project', {
    title: DataTypes.STRING,
    imageUrl: DataTypes.STRING,
    description: DataTypes.TEXT,
    userId: DataTypes.INTEGER
  }, {});
  Project.associate = function (models) {
    // associations can be defined here
    Project.belongsTo(models.User, {
      foreignKey: 'userId',
      onDelete: 'CASCADE'
    })
  };
  return Project;
};
```

现在在同一个目录中找到`project.js`,将代码替换为:

```
module.exports = (sequelize, DataTypes) => {
  const User = sequelize.define('User', {
    firstName: DataTypes.STRING,
    lastName: DataTypes.STRING,
    email: DataTypes.STRING,
    password: DataTypes.STRING
  }, {});
  User.associate = function (models) {
    // associations can be defined here
    User.hasMany(models.Project, {
      foreignKey: 'userId'
    })
  };
  return User;
};
```

最后，我们将把外键添加到最新的迁移文件中。您应该会在您的`/migrations`子目录中看到两个文件。查找以`create-project.js`结尾的文件名，并将里面的代码改为如下:

```
'use strict';
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('Projects', {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER
      },
      title: {
        type: Sequelize.STRING
      },
      imageUrl: {
        type: Sequelize.STRING
      },
      description: {
        type: Sequelize.TEXT
      },
      userId: {
        type: Sequelize.INTEGER,
        onDelete: 'CASCADE',
        references: {
          model: 'Users',
          key: 'id',
          as: 'userId',
        }
      },
      createdAt: {
        allowNull: false,
        type: Sequelize.DATE
      },
      updatedAt: {
        allowNull: false,
        type: Sequelize.DATE
      }
    });
  },
  down: (queryInterface, Sequelize) => {
    return queryInterface.dropTable('Projects');
  }
};
```

执行迁移以在 Postgres 数据库中创建项目表:

```
npx sequelize-cli db:migrate
```

让我们为项目创建一个种子文件:

```
npx sequelize-cli seed:generate --name projects
```

您将在您的`/seeders`子目录中看到一个以`projects.js`结尾的新文件。将该文件中的代码更改为以下内容:

```
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.bulkInsert('Projects', [{
      title: 'Project 1',
      imageUrl: 'https://upload.wikimedia.org/wikipedia/commons/6/6a/JavaScript-logo.png',
      description: 'This project was built using Vanilla JavaScript, HTML, and CSS',
      userId: 1,
      createdAt: new Date(),
      updatedAt: new Date()
    },
    {
      title: 'Project 2',
      imageUrl: 'https://www.stickpng.com/assets/images/584830f5cef1014c0b5e4aa1.png',
      description: 'This project was built using React & a 3rd-party API.',
      userId: 3,
      createdAt: new Date(),
      updatedAt: new Date()
    },
    {
      title: 'Project 3',
      imageUrl: 'https://expressjs.com/images/express-facebook-share.png',
      description: 'This project was built using Express & React.',
      userId: 2,
      createdAt: new Date(),
      updatedAt: new Date()
    },
    {
      title: 'Project 4',
      imageUrl: 'https://upload.wikimedia.org/wikipedia/commons/1/16/Ruby_on_Rails-logo.png',
      description: 'This project was built using Rails & React.',
      userId: 1,
      createdAt: new Date(),
      updatedAt: new Date()
    }], {});
  }, down: (queryInterface, Sequelize) => {
    return queryInterface.bulkDelete('Projects', null, {});
  }
};
```

现在，我们将运行两个种子文件，将我们的用户和项目添加到数据库中:

```
npx sequelize-cli db:seed:all
```

现在让我们进入`psql`来确保数据存在于数据库中:

```
psql projects_api_development
SELECT * FROM "Users" JOIN "Projects" ON "Users".id = "Projects"."userId";
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

现在我们将修改`package.json`文件以支持 nodemon。此外，我们可以通过创建一个新命令来促进开发:`npm db:reset`。我们将设置删除数据库、创建数据库、运行迁移，并在需要时重新播种！

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

> 要了解更多关于序列的基本 Express 设置，请参见:
> [**序列 CLI 和 Express**](/sequelize-cli-and-express-fb3ddefb9786)

# 使用带控制器的快速路由器

我们将从设置根路由开始。打开`./routes/index.js`文件，添加以下代码:

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

打开`./controllers/index.js`并添加以下内容:

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

这里，我们结合了 Sequelize 中定义的`User`模型，根据 API 请求中的信息创建一个新的数据库条目。为了实现这一点，我们将在服务器上创建一个路由来连接请求和控制器:

在`./routes/index.js`中，在您的“这是 root！”之后添加一个新行路线:

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

现在我们终于有一个不叫约翰的用户了！更重要的是，我们使用路由器和控制器将 Sequelize 中的数据传递给 API 用户。我们可以使用相同的策略将任何顺序查询连接到一个 Express 端点。

> 要了解有关自定义序列查询的更多信息，请参见: [**使用序列 CLI 和查询**](/using-the-sequelize-cli-and-querying-4ba8d0ac4314)

现在让我们创建另一个控制器方法，从数据库中获取所有用户及其相关项目。对`./controllers/index.js`进行以下更改:

```
const { User, Project } = require('../models');const createUser = async (req, res) => {
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
                    model: Project
                }
            ]
        });
        return res.status(200).json({ users });
    } catch (error) {
        return res.status(500).send(error.message);
    }
}module.exports = {
    createUser,
    getAllUsers
}
```

您会注意到我们已经在顶部的需求中添加了`Project`模型，新的`getAllUsers`函数指定应该包含用户的项目。我们也给`module.exports`增加了新的功能。现在，我们只需在`./routes/index.js`添加一条路线，将交通导向该功能:

```
router.get('/users', controllers.getAllUsers)
```

通过在浏览器中打开[http://localhost:3000/API/users](http://localhost:3000/api/users)或在 Postman 中执行`GET`请求来测试路由。

很好。现在让我们添加查找特定用户及其相关项目的功能。在`./controllers/index.js`中增加以下功能:

```
const getUserById = async (req, res) => {
    try {
        const { id } = req.params;
        const user = await User.findOne({
            where: { id: id },
            include: [
                {
                    model: Project
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
}
```

不要忘记在导出中添加`getUserById`:

```
module.exports = {
    createUser,
    getAllUsers,
    getUserById
}
```

现在我们将使用新功能在`./routes/index.js`中创建一条新路线:

```
router.get('/users/:id', controllers.getUserById)
```

这里我们添加了一个路由参数`:id`。我们的控制器函数从`req.params`中提取这些信息来找到一个特定的用户。我们在[http://localhost:3000/API/users/2](http://localhost:3000/api/users/2)测试一下吧！

因此，我们现在可以创建用户、显示所有用户以及显示特定用户。更新一个用户，删除一个用户怎么样？让我们在`./controllers/index.js`中为每个函数添加一个新函数:

```
const updateUser = async (req, res) => {
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
};
```

确保您的导出已更新:

```
module.exports = {
    createUser,
    getAllUsers,
    getUserById,
    updateUser,
    deleteUser
}
```

让我们在`./routes/index.js`中添加路线:

```
router.put('/users/:id', controllers.updateUser)
router.delete('/users/:id', controllers.deleteUser)
```

现在让我们通过在[http://localhost:3000/API/users/3](http://localhost:3000/api/users/3)的 Postman 中发出一个`PUT`请求来测试更新路由。请求正文应该如下所示:

```
{
    "firstName": "John",
    "lastName": "Smith",
    "email": "john.smith@smith.com",
    "password": "superPass1"
}
```

最后，在同一个 URL 上用一个`DEL`请求测试 delete。

现在我们只剩下*两名*约翰了！更重要的是，我们刚刚使用 Express Router 在 Express、Sequelize 和 Postgres 中构建了一个完整的 CRUD JSON API！

# 记录

这是集成更好的日志记录的好时机。现在，如果我们在点击[http://localhost:3000/API/users/2](http://localhost:3000/api/users/2)端点时检查我们的终端，我们将看到被执行的原始 SQL。出于调试目的和更好的日志记录，让我们安装一个名为 [morgan](https://www.npmjs.com/package/morgan) 的快速中间件:

```
npm install morgan
```

将以下内容添加到您的`server.js`文件中:

```
const logger = require('morgan');
app.use(logger('dev'))
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

# 部署到 Heroku

现在，让我们将我们的应用程序部署到 Heroku 来创建一个公共可访问的 API。

Heroku 将使用`package.json`从 Git 存储库构建我们的应用程序，因此我们需要如下更新该文件:

```
...
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node server.js",
    "dev": "nodemon server.js",
    "db:reset": "npx sequelize-cli db:drop && npx sequelize-cli db:create && npx sequelize-cli db:migrate && npx sequelize-cli db:seed:all"
  },
...
```

现在，当 Heroku 用`npm start`脚本启动您的远程服务器时，它将在标准节点中运行您的代码。(我们仍然可以通过运行`npm run dev`在本地使用 nodemon。)

> 您需要在您的终端中安装 Heroku CLI 来执行以下步骤。 [**在这里一探究竟。**](https://devcenter.heroku.com/articles/heroku-cli)

让我们使用 Heroku CLI 设置一个新的应用程序。用您喜欢的任何名称替换下面代码中的两个`your-heroku-app-name`实例，然后在您的终端中运行这三个命令:

1.  `heroku create *your-heroku-app-name*`
2.  `heroku buildpacks:set heroku/nodejs`
3.  `heroku addons:create heroku-postgresql:hobby-dev --app=*your-heroku-app-name*`

现在 Heroku 准备用 PostgreSQL 数据库构建 Node.js 应用。让我们将代码提交给 Git(确保您在`master`分支上！)并将存储库上传到 Heroku:

1.  `git status`
2.  `git commit -am "add any pending changes"`
3.  `git push heroku master`

Heroku 将为我们在本地环境中运行的应用开发一个产品版本。现在我们只需要告诉 Heroku 执行我们的迁移和种子文件:

1.  `heroku run npx sequelize-cli db:migrate`
2.  `heroku run npx sequelize-cli db:seed:all`

> 有问题吗？使用 Heroku 命令`heroku logs --tail`进行调试，看看 Heroku 服务器上发生了什么。

现在，您可以在远程服务器上测试端点，方法是将下面的“your-heroku-app-name”替换为您之前使用的任何名称:

*   https://your-heroku-app-name.herokuapp.com/api/users
*   https://your-heroku-app-name.herokuapp.com/api/users/1

又是那三个嫖客！尝试使用 Postman 来创建、更新和删除用户，就像我们上面做的一样。

太棒了。现在，您可以从地球上的任何地方访问新的 Express API。接下来，尝试[定义你自己的序列关联](https://medium.com/@brunopgalvao/creating-sequelize-associations-with-the-sequelize-cli-tool-d83caa902233)，进行[定制序列查询](https://medium.com/@brunopgalvao/using-the-sequelize-cli-and-querying-4ba8d0ac4314)，并添加[更健壮的种子数据](/getting-started-with-sequelize-cli-using-faker-824b3f4c4cfe)——或者通过 [**构建一个带有单元测试**](https://medium.com/@brunopgalvao/building-an-express-api-with-sequelize-cli-and-unit-testing-882c6875ed59) 的 Express API 来确保你的应用的可靠性！

> 本文是与纽约市的软件工程师、编辑和作家杰里米·罗斯(Jeremy Rose)合著的。

# 有关 Sequelize CLI 和 Express 的更多信息:

*   【Sequelize CLI 入门
*   [使用顺序 CLI 并查询](https://medium.com/@brunopgalvao/using-the-sequelize-cli-and-querying-4ba8d0ac4314)
*   [使用序列 CLI 创建序列关联](https://medium.com/@brunopgalvao/creating-sequelize-associations-with-the-sequelize-cli-tool-d83caa902233)
*   [使用 Faker 开始 Sequelize CLI](/getting-started-with-sequelize-cli-using-faker-824b3f4c4cfe)
*   将 CLI 和 Express 排序
*   使用序列 CLI 和单元测试构建 Express API！
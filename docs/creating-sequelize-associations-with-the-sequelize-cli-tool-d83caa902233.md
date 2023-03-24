# 使用序列 CLI 创建序列关联

> 原文：<https://levelup.gitconnected.com/creating-sequelize-associations-with-the-sequelize-cli-tool-d83caa902233>

![](img/021463944331716371fec71e4ce6afef.png)

Sequelize 是一个流行的、易于使用的 JavaScript 对象关系映射(ORM)工具，可用于 SQL 数据库。使用 Sequelize CLI 开始一个新项目相当简单，但是要真正利用 Sequelize 的功能，您需要定义模型之间的关系。

在本演练中，我们将建立一个 Sequelize 项目来为特定用户分配任务。我们将使用关联来定义这种关系，然后探索基于这些关联查询数据库的方法。

让我们从在一个新的项目文件夹中安装 Postgres、Sequelize 和 [Sequelize CLI](https://github.com/sequelize/cli) 开始:

```
mkdir sequelize-associations
cd sequelize-associations
npm init -y
npm install sequelize pg
npm install --save-dev sequelize-cli
```

接下来，让我们初始化一个 Sequelize 项目，然后在代码编辑器中打开整个目录:

```
npx sequelize-cli init
code .
```

> 要了解有关以下任何顺序化 CLI 命令的更多信息，请参见:
> [**顺序化 CLI 入门**](https://medium.com/@brunopgalvao/getting-started-with-sequelize-cli-c33c797f05c6)

让我们配置我们的 Sequelize 项目来使用 Postgres。在`/config`目录中找到`config.json`,并用以下代码替换那里的内容:

```
{
  "development": {
    "database": "sequelize_associations_development",
    "host": "127.0.0.1",
    "dialect": "postgres"
  },
  "test": {
    "database": "sequelize_associations_test",
    "host": "127.0.0.1",
    "dialect": "postgres"
  },
  "production": {
    "database": "sequelize_associations_production",
    "host": "127.0.0.1",
    "dialect": "postgres"
  }
}
```

酷，现在我们可以告诉 Sequelize 创建数据库:

```
npx sequelize-cli db:create
```

接下来我们将从命令行创建一个`User`模型:

```
npx sequelize-cli model:generate --name User --attributes firstName:string,lastName:string,email:string,password:string
```

运行`model:generate`会自动创建一个模型文件和一个带有我们指定属性的迁移。您可以在项目目录中找到这些文件，但是现在没有必要更改它们。(稍后，我们将编辑模型文件来定义我们的关联。)

现在我们将执行迁移，在数据库中创建`Users`表:

```
npx sequelize-cli db:migrate
```

现在让我们创建一个种子文件:

```
npx sequelize-cli seed:generate --name user
```

您将在`/seeders`中看到一个新文件。在该文件中，粘贴以下代码以创建一个“John Doe”演示用户:

```
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.bulkInsert('Users', [{
        firstName: 'John',
        lastName: 'Doe',
        email: 'demo@demo.com',
        password: '$321!pass!123$',
        createdAt: new Date(),
        updatedAt: new Date()
      }], {});
  },down: (queryInterface, Sequelize) => {
    return queryInterface.bulkDelete('Users', null, {});
  }
};
```

一旦我们保存了种子文件，让我们执行它:

```
npx sequelize-cli db:seed:all
```

进入`psql`并查询数据库，查看`Users`表:

```
psql sequelize_associations_development
SELECT * FROM "Users";
```

# 定义关联

太好了！我们有一个工作模型，但我们的无名氏似乎有点无聊。让我们通过创建一个`Task`模型来给 John 一些事情做:

```
npx sequelize-cli model:generate --name Task --attributes title:string,userId:integer
```

正如上面的`User`模型一样，这个 Sequelize CLI 命令将基于我们指定的属性创建一个模型文件和一个迁移。但是这一次，我们需要编辑两者，以便将我们的模型连接在一起。

首先，在项目目录的`/models`子目录中找到`task.js`。这是任务的顺序模型，你会看到`sequelize.define()`方法将`title`和`userId`设置为属性，就像我们上面指定的一样。

在那下面，你会看到`Task.associate`。它目前是空的，但这是我们将每个任务与一个`userId`绑定的地方。编辑您的文件，如下所示:

```
module.exports = (sequelize, DataTypes) => {
  const Task = sequelize.define('Task', {
    title: DataTypes.STRING,
    userId: DataTypes.INTEGER
  }, {});
  Task.associate = function(models) {
    // associations can be defined here
    Task.belongsTo(models.User, {
      foreignKey: 'userId',
      onDelete: 'CASCADE'
    })
  };
  return Task;
};
```

这些变化有什么作用？`Task.belongsTo()` [与`User`模型建立了一个“隶属”关系](https://sequelize.org/master/class/lib/model.js~Model.html#static-method-belongsTo)，这意味着每个任务将与一个特定的用户相关联。

我们通过将`userId`设置为“外键”，这意味着它引用了另一个模型中的一个键。在我们的模型中，任务必须属于一个用户，因此`userId`将对应于特定`User`条目中的`id`。(`onDelete: 'CASCADE'`配置我们的模型，以便如果用户被删除，用户的任务也将被删除。)

我们还需要改变我们的`User`模型，以反映这种关系的另一面。找到`user.js`并更改`User.associate`下的部分，使您的文件如下所示:

```
module.exports = (sequelize, DataTypes) => {
  const User = sequelize.define('User', {
    firstName: DataTypes.STRING,
    lastName: DataTypes.STRING,
    password: DataTypes.STRING,
    email: DataTypes.STRING
  }, {});
  User.associate = function(models) {
    // associations can be defined here
    User.hasMany(models.Task, {
      foreignKey: 'userId',
    })
  };
  return User;
};
```

对于这个模型，我们已经建立了一个“多”关系，这意味着一个用户可以有多个任务。在`.hasMany()`方法中，`foreignKey`选项被设置为 *other* 表上的键的名称。换句话说，当任务上的`userId`与用户的`id`相同时，我们就有了匹配。

我们还需要再做一个更改来在数据库中设置我们的关系。在您项目的`/migrations`文件夹中，您应该会看到一个文件名以`create-task.js`结尾的文件。更改标记为`userId`的对象，使您的文件看起来像下面的代码:

```
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('Tasks', {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER
      },
      title: {
        type: Sequelize.STRING
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
    return queryInterface.dropTable('Tasks');
  }
};
```

`references`部分将在我们的数据库中建立`Tasks`表，以反映我们上面描述的相同关系。现在我们可以运行我们的迁移了:

```
npx sequelize-cli db:migrate
```

现在我们的 John Doe 已经准备好接受任务，但是 John 仍然没有分配任何实际任务。让我们创建一个任务种子文件:

```
npx sequelize-cli seed:generate --name task
```

找到新生成的种子文件并粘贴到以下内容中以创建任务:

```
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.bulkInsert('Tasks', [{
      title: 'Build an app',
      userId: 1,
      createdAt: new Date(),
      updatedAt: new Date()
    }], {});
  }, down: (queryInterface, Sequelize) => {
    return queryInterface.bulkDelete('Tasks', null, {});
  }
};
```

我们将把`userId`设置为`1`,这样任务将属于我们之前创建的用户。现在我们可以填充数据库。

```
npx sequelize-cli db:seed:all
```

测试数据库:

```
psql sequelize_associations_development
SELECT * FROM "Users" JOIN "Tasks" ON "Tasks"."userId" = "Users".id;
```

# 通过序列查询

现在，我们可以基于这些关联在数据库中查询信息——通过 Sequelize，我们可以使用 JavaScript 来完成，这使得与 Node.js 应用程序的集成变得很容易。让我们创建一个文件来保存我们的查询:

```
touch query.js
```

将以下代码粘贴到新文件中:

```
const { User, Task } = require('./models')
const Sequelize = require('sequelize');
const Op = Sequelize.Op

// Find all users with their associated tasks
// Raw SQL: SELECT * FROM "Users" JOIN "Tasks" ON "Tasks"."userId" = "Users".id;

const findAllWithTasks = async () => {
    const users = await User.findAll({
        include: [{
            model: Task
        }]
    });
    console.log("All users with their associated tasks:", JSON.stringify(users, null, 4));
}

const run = async () => {
    await findAllWithTasks()
    await process.exit()
}

run()
```

上面的前三行导入了我们的`User`和`Task`型号，以及 Sequelize。之后，我们包含一个查询函数，返回每个`User`以及用户的相关任务。

Sequelize 的`.findAll()`方法接受选项作为 JavaScript 对象。上面，我们使用了`include`选项来[利用“急切加载”](https://sequelize.org/master/manual/eager-loading.html)——同时查询来自多个模型的数据。使用这个选项，Sequelize 将返回一个 JavaScript 对象，该对象包含每个`User`以及所有相关联的`Task`实例作为嵌套对象。

让我们运行我们的查询文件来看看实际情况:

```
node query.js
```

现在很明显我们的无名氏有项目要做了！当我们的查询找到一个`Task`时，我们可以使用相同的方法来包含`User`。将以下代码粘贴到`query.js`中:

```
// Find a task with its associated user
// Raw SQL: SELECT * FROM "Tasks" JOIN "Users" ON "Users"."id" = "Tasks"."userId";

const findTasksWithUser = async () => {
    const tasks = await Task.findAll({
        include: [{
            model: User
        }]
    });
    console.log("All tasks with their associated user:", JSON.stringify(tasks, null, 4));
}
```

通过添加一行调用`findTasksWithUser()`来修改`query.js`底部的`const run`。现在在 Node 中再次运行您的文件——每个`Task`应该包含它所属的`User`的信息。

> 本演练中的查询使用了`.findAll()`方法。要了解更多有关其他顺序化查询的信息，请参见: [**使用顺序化 CLI 和查询**](/using-the-sequelize-cli-and-querying-4ba8d0ac4314)

您还可以在`include`旁边包含其他选项，以进行更具体的查询。例如，下面我们将使用`where`选项仅查找名为 John 的用户，同时仍然返回每个用户的相关任务:

```
// Find all users named John with their associated tasks
// Raw SQL: SELECT * FROM "Users" WHERE firstName = "John" JOIN tasks ON "Tasks"."userId" = "Users".id;const findAllJohnsWithTasks = async () => {
    const users = await User.findAll({
        where: { firstName: "John" },
        include: [{
            model: Task
        }]
    });
    console.log("All users named John with their associated tasks:", JSON.stringify(users, null, 4));
}
```

将上述内容粘贴到您的`query.js`中，并将`const run`更改为调用`findAllJohnsWithTasks()`进行尝试。

现在您已经知道如何在 Sequelize 中使用模型关联，您可以设计您的应用程序来交付您需要的嵌套数据。对于您的下一步，您可能决定使用 Faker 或 [**将您的 Sequelize 应用程序与 Express**](https://medium.com/@brunopgalvao/sequelize-cli-and-express-fb3ddefb9786) 集成以创建 Node.js 服务器，从而 [**包含更健壮的种子数据！**](https://medium.com/@brunopgalvao/getting-started-with-sequelize-cli-using-faker-824b3f4c4cfe)

> 本文是与纽约市的软件工程师、编辑和作家杰里米·罗斯(Jeremy Rose)合著的。

# 有关 Sequelize CLI 的更多信息:

*   【Sequelize CLI 入门
*   [使用顺序 CLI 并查询](https://medium.com/@brunopgalvao/using-the-sequelize-cli-and-querying-4ba8d0ac4314)
*   [对 CLI 和 Express 进行排序](https://medium.com/@brunopgalvao/sequelize-cli-and-express-fb3ddefb9786)
*   [使用 Faker 开始使用 Sequelize CLI](https://medium.com/@brunopgalvao/getting-started-with-sequelize-cli-using-faker-824b3f4c4cfe)
*   [用序列命令行界面和快速路由器构建快速 API](https://medium.com/@brunopgalvao/build-an-express-api-with-sequelize-cli-and-express-router-963b6e274561)
*   使用序列 CLI 和单元测试构建 Express API！

# 资源

*   [https://sequelize.org/master/manual/associations.html](https://sequelize.org/master/manual/associations.html)
*   [https://sequelize.org/master/manual/querying.html](https://sequelize.org/master/manual/querying.html)
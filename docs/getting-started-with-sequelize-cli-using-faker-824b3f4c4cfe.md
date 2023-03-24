# 使用 Faker 开始 Sequelize CLI

> 原文：<https://levelup.gitconnected.com/getting-started-with-sequelize-cli-using-faker-824b3f4c4cfe>

![](img/021463944331716371fec71e4ce6afef.png)

如果您最近[开始使用 Sequelize 命令行界面(CLI)](https://medium.com/@brunopgalvao/getting-started-with-sequelize-cli-c33c797f05c6) 来使用 Sequelize，您可能已经注意到，如果您没有任何数据，您就无法对您的数据库做太多事情。

幸运的是，一个名为 [**Faker**](https://www.npmjs.com/package/faker) 的 npm 包可以帮助填补这个空白。在本演练中，我们将使用 [Sequelize CLI](https://github.com/sequelize/cli) 创建一个简单的项目，然后使用 Faker 为一组相当可信的虚拟用户生成合理可信的虚假信息。

让我们从安装 Postgres、Sequelize 和 [Sequelize CLI](https://github.com/sequelize/cli) 开始，在一个新的项目文件夹中，我们称之为`sequelize-practice`:

```
mkdir sequelize-practice
cd sequelize-practice
npm init -y
npm install sequelize pg
npm install --save-dev sequelize-cli
```

接下来，让我们初始化一个 Sequelize 项目，然后在代码编辑器中打开目录:

```
npx sequelize-cli init
code .
```

> 要了解有关以下任何顺序化 CLI 命令的更多信息，请参见:
> [**顺序化 CLI 入门**](https://medium.com/@brunopgalvao/getting-started-with-sequelize-cli-c33c797f05c6)

让我们配置我们的 Sequelize 项目来使用 Postgres。在`/config`目录中找到`config.json`，并用以下代码替换那里的内容:

```
{
  "development": {
    "database": "sequelize_practice_development",
    "host": "127.0.0.1",
    "dialect": "postgres"
  },
  "test": {
    "database": "sequelize_practice_test",
    "host": "127.0.0.1",
    "dialect": "postgres"
  },
  "production": {
    "database": "sequelize_practice_production",
    "host": "127.0.0.1",
    "dialect": "postgres"
  }
}
```

酷，现在我们可以告诉 Sequelize 创建数据库:

```
npx sequelize-cli db:create
```

接下来，我们将从命令行创建一个`User`模型:

```
npx sequelize-cli model:generate --name User --attributes firstName:string,lastName:string,email:string,userName:string,password:string,jobTitle:string
```

运行`model:generate`会自动创建一个模型文件和一个带有我们指定属性的迁移。现在，为了使用 Faker 生成种子数据，您不需要担心(或编辑)这些。

现在我们将执行迁移，在数据库中创建`Users`表:

```
npx sequelize-cli db:migrate
```

# 用 Faker 播种

既然我们的 Sequelize 项目已经建立，乐趣可以开始了！我们可以要求 Faker 创建各种看似真实的信息，从普通的 lorem ipsum 到虚构的电子邮件地址和电话号码。

这不仅有趣——它还使开发人员能够测试一旦输入真正的数据，他们的模型、关联和查询将如何表现。

让我们安装 Faker:

```
npm install faker
```

现在，我们将告诉 Sequelize CLI 生成一个种子文件:

```
npx sequelize-cli seed:generate --name users
```

这将在`/seeders`子目录中生成一个文件名类似于`20190904165805-users.js`的文件。(数字将基于当前日期和时间。)

用下面的代码替换该文件中的样板代码:

```
const faker = require('faker');const users = [...Array(100)].map((user) => (
  {
    firstName: faker.name.firstName(),
    lastName: faker.name.lastName(),
    email: faker.internet.email(),
    userName: faker.internet.userName(),
    password: faker.internet.password(8),
    jobTitle: faker.name.jobTitle(),
    createdAt: new Date(),
    updatedAt: new Date()
  }
))module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.bulkInsert('Users', users, {});
  }, down: (queryInterface, Sequelize) => {
    return queryInterface.bulkDelete('Users', null, {});
  }
};
```

这里我们生成一个 100 个对象的数组——对于每个对象，Faker 将使用类似`faker.name.lastName()`和`faker.internet.email()`的方法填充细节。然后，我们只需执行批量插入，将这些条目添加到数据库中。就这么简单！

保存文件后，执行它:

```
npx sequelize-cli db:seed:all
```

进入`psql`并查询数据库，找出 Faker 制造了什么:

```
psql sequelize_practice_development
SELECT * FROM "Users";
```

> 播种错误？您可以随时撤销:`*npx sequelize-cli db:seed:undo*`

现在你有一个 Sequelize 项目和一个有 100 个假用户的数据库！你最后和谁在一起了？莫尼克·麦克塔维什？诺姆·西波维茨？汉瑟·拉米雷斯？无名女尸？让·吕克·周？没关系！重要的是，现在你有了可以用来[在你的项目成形时试验你的顺序查询](/using-the-sequelize-cli-and-querying-4ba8d0ac4314)的数据。

[Faker 的可用虚假数据列表](http://marak.github.io/faker.js/#toc9__anchor)可以帮助您定制种子数据，以满足您的项目需求。在 [**创建一个顺序关联**](https://medium.com/@brunopgalvao/creating-sequelize-associations-with-the-sequelize-cli-tool-d83caa902233) 之后，尝试播种两个或更多不同的表！

> 本文是与纽约市的软件工程师、编辑和作家杰里米·罗斯(Jeremy Rose)合著的。

# 有关 Sequelize CLI 的更多信息:

*   【Sequelize CLI 入门
*   [使用顺序 CLI 并查询](https://medium.com/@brunopgalvao/using-the-sequelize-cli-and-querying-4ba8d0ac4314)
*   [使用序列 CLI 创建序列关联](https://medium.com/@brunopgalvao/creating-sequelize-associations-with-the-sequelize-cli-tool-d83caa902233)
*   将 CLI 和 Express 排序
*   [使用序列 CLI 和快速路由器构建快速 API](https://medium.com/@brunopgalvao/build-an-express-api-with-sequelize-cli-and-express-router-963b6e274561)
*   使用序列 CLI 和单元测试构建 Express API！
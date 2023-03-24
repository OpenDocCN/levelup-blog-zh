# 介绍全栈类型样板文件:一旦使用了 ORM，就再也回不去了！

> 原文：<https://levelup.gitconnected.com/introducing-the-full-stack-typing-boilerplate-once-you-orm-you-cant-go-back-e97b53a36f>

## 以 Typescript 和 Sequelize 为特色:在前端和后端之间共享类型，以获得最高的开发效率。

![](img/6b5cb704fa2773b59e06cc685c6a5f5e.png)

来自 [Pexels](https://www.pexels.com/photo/computer-screen-programming-software-225250/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的[马库斯·斯皮斯克](https://www.pexels.com/@markusspiske?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的照片

[*本帖镜像在我的博客上。*](https://chrisfrew.in/blog/sequelize-and-typescript-rest-once-you-orm-you-never-go-back/)

在软件世界中，99%的时候你都需要一个 API。

经常(也许总是🤔)API 需要能够在数据库级别执行一些 CRUD 操作(创建、读取、更新和删除)。

几年来，在 Node.js 中获得易于使用的数据库连接的“前沿”看起来是这样的:

*   您将拥有一个包含连接设置的`**db/index.js**`文件
*   您的连接器看起来像这样(在这个例子中，我使用 PostgreSQL 的包`**pg**`):

```
**const { Pool } = require("pg")****const pool = new Pool({
    user: process.env.YOUR_DB_USER,
    host: process.env.YOUR_DB_HOST,
    database: process.env.YOUR_DB_NAME,
    password: process.env.YOUR_DB_PASSWORD,
    port: process.env.YOUR_DB_PORT,
})****/**
 * DB Query
 * @param {object} req
 * @param {object} res
 * @returns {object} object
 */
function query(text, params) {
    return new Promise((resolve, reject) => {
        pool.query(text, params)
            .then(res => {
                resolve(res)
            })
            .catch(err => {
                reject(err)
            })
    })
}****exports.query = query**
```

然后使用`**db**`模块，像这样传入(几乎)原始的 SQL 语句:

```
**const db = require("../db")
const email = "test@test.com"
const userSelectPromise = db.query("SELECT * FROM users WHERE email = $1", [
    email,
])
Promise.resolve(userSelectPromise).then(userSelect => {
    if (userSelect && parseInt(userSelect[0].rowCount) > 0) {
        const username = userSelect.rows[0].username
        // etc...
    }
})**
```

现在，请随意复制并粘贴该代码，它将为您工作…

⚠:但是我不建议你使用它！⚠

这当然不再是最先进的做事方式了。从这种方法中可以清楚地看出一些事情:

*   为了最终得到您想要的值，需要进行大量的非空和大小检查
*   笨拙的承诺语法
*   也许最令人发指的是:原始 SQL 查询😱

在隔离/封锁/就地避难期间的某个时候，我问自己，

> 肯定有人已经抽象出了所有这些检查和承诺/异步的东西，对不对？

而且，

> 现在是 2020 年…一定有一种不用写原始 SQL 就能查询数据库的方法，对吗？

当然有！

# 介绍 Sequelize

随着时间的推移，在软件世界中，样板代码往往被仔细地组织和抽象，经常成为一个包。对于在 JavaScript 中连接和执行 SQL，这个包是 [Sequelize](https://github.com/sequelize/sequelize) 。开箱即用，Sequelize 可以为您节省大量时间，为您解决可能需要完成的任何与 SQL 相关的任务。干杯🍻尊重那个包的作者、维护者和贡献者！

当您将 TypeScript 的静态类型添加到这个组合中时，您就有了一个更加强大的工作流，能够将一个类——也就是一个模型——映射到一个表！事实上，这正是 ORM 的本质:对象关系映射。我记得当时我意识到所有的表都可以通过一个类 1:1 地表示出来:

作为一名软件工程师，我的生活永远改变了。

如题，一旦你形成了，你就不能回头了。一旦你设置好了，事情就简单多了。我也会证明这一点——在本指南的最后，我会给出一个额外的指南，说明您可以在*分钟内*添加 API 端点！您甚至可以确信您没有犯任何愚蠢的 SQL 语法错误或键入错误——它们都是由 Sequelize 或 Typescript 处理的！再也不用为 SQL 错误挠头了，再也不用去寻找查询返回的究竟是什么类型了！

从现在开始，在这篇文章中，当我提到“模型”这个词时，它可以和“桌子”这个词互换。

# 入门指南

在我为我的一个项目升级 REST API 的最初研究中，我发现了 Loren Stewart 的这篇文章。这篇文章采用了一种很好的组织模式，将众多的 API 模型组合成一个类。不过由于是 2016 年发的，现在有点过时了。(我知道，当我听到自己说一篇只有 4 年历史的文章已经“过时”时，我感到很不舒服，但那是软件🤷‍♂️).在 Sequelize 最新版本的 Typescript 文档的帮助下，我已经将他的组织模式从 JavaScript 转换成了 TypeScript。

🎺 👼听啊！我现在给你带来了一个面向 2020 年的现代 API 后端框架！

# 创建我们的第一个模型

让我们从为我们的`**users**`表创建一个模型开始，因为在我们旧的 JavaScript 做事方式中，我们甚至没有*这样的定义。下面是我们如何定义一个模型(*思考表*)的打字稿方式:*

```
**import { Model, DataTypes, Sequelize, BuildOptions } from "sequelize"
import IUser from "../../shared/interfaces/IUser"****type UsersStatic = typeof Model & {
    new (values?: object, options?: BuildOptions): IUser
}****const sequelize = new Sequelize(
    process.env.YOUR_DB_NAME,
    process.env.YOUR_DB_USER,
    process.env.YOUR_DB_PASSWORD,
    {
        host: "localhost",
        dialect: "postgres",
    }
)****const users = <UsersStatic>sequelize.define("users", {
    id: {
        type: DataTypes.INTEGER,
        autoIncrement: true,
        primaryKey: true,
    },
    email: {
        type: DataTypes.STRING(255),
        allowNull: false,
        unique: true,
    },
    username: {
        type: DataTypes.STRING(30),
        allowNull: false,
        unique: true,
    },
})**
```

您可能需要在`**<UserStatic>**`铸造线上方提供`**// eslint-disable-next-line @typescript-eslint/consistent-type-assertions**`，这取决于您的格式器和/或棉绒规则。这些复杂的打字遵循`**sequelize**`自己的[打字文档](https://sequelize.org/master/manual/typescript.html)的指导。`**IUser**`只是一个定义我们的`**user**`表中的列的接口。现在请注意它——虽然它是一个简单的界面，但它将非常*😉对我们以后有用:*

```
**export default interface IUser {
    id: number
    email: string
    username: string** **createdAt: Date
    updatedAt: Date
}**
```

注意`**createdAt**`和`**updatedAt**`默认带有 Sequelize `**define()**`函数，所以我们不必在模型定义中定义它们。让我们继续使用我们的用户选择示例，看看如何使用我们的新`**User**`模型创建 select 语句。在这种情况下，我们可以在声明`**users**`之后直接编写一个查询:

```
**import { Model, DataTypes, Sequelize, BuildOptions } from "sequelize"
import IUser from "../../shared/interfaces/IUser"****type UsersStatic = typeof Model & {
    new (values?: object, options?: BuildOptions): IUser
}****const sequelize = new Sequelize(
    process.env.YOUR_DB_NAME,
    process.env.YOUR_DB_USER,
    process.env.YOUR_DB_PASSWORD,
    {
        host: "localhost",
        dialect: "postgres",
    }
)****const users = <UsersStatic>sequelize.define("users", {
    id: {
        type: DataTypes.INTEGER,
        autoIncrement: true,
        primaryKey: true,
    },
    email: {
        type: DataTypes.STRING(255),
        allowNull: false,
        unique: true,
    },
    username: {
        type: DataTypes.STRING(30),
        allowNull: false,
        unique: true,
    },
})****// --> new code: query example
const user = users.findOne({
    where: {
        email: {
            [Op.eq]: email,
        },
    },
})****if (!user) {
    console.log("entry not found!")
}****if (user) {
    const username = user.username
}
// <-- end new code**
```

哇哦。我们刚刚在软件技术领域跨越了 4 年多！

注意这里的`**[Op.eq]:**`不是必须的。另一种方法是使用冒号。在`**sequelize**`中，这意味着`**=**`，也就是说，如果我们希望 email 列等于`**email**`变量，我们可以写:

```
**where: {
    email: email
}**
```

由于速记语法，我们甚至可以将其简化为:

```
**where: {
    email
}**
```

但这是一个滑坡。我经常在查询中使用各种操作，所以我通常已经导入了`**Op**`对象。

我发现`**[Op.eq]**`表示在准确描述正在发生的事情方面更加明确。

# 集中模型！

所以我们有了很好的类型化模型，但是很少有 API 只有一个表结构。很容易想象，每当我们需要连接到一个表时，总是声明`**sequelize**`对象*和*重复提供模型定义会有多痛苦。

如果我们一次性完成所有的初始化步骤，并且可以将所有的模型组织到一个集中的类中，那就太好了，对吗？那么我们将只访问所有其他 API 类中的*和*类。(为这个组织理念向 [Loren Stewart 的职位](https://lorenstewart.me/2016/09/12/sequelize-table-associations-joins)脱帽致敬)。

如果我们以后需要更复杂的`**JOIN**`查询，这个组织步骤也将非常有帮助——我们将立即获得所有的表以便于使用！

首先，我们需要稍微重构一下我们的`**user**`模型定义——我们想取出查询，并将声明包装在一个接受`**sequelize**`对象并返回`**users**`模型的函数中。`**users**`模型文件现在看起来是这样的:

```
**import { Model, DataTypes, Sequelize, BuildOptions } from "sequelize"
import IUser from "../../shared/interfaces/IUser"****type UsersStatic = typeof Model & {
    new (values?: object, options?: BuildOptions): IUser
}****export default function Users(sequelize: Sequelize) {
    const users = <UsersStatic>sequelize.define("users", {
        id: {
            type: DataTypes.INTEGER,
            autoIncrement: true,
            primaryKey: true,
        },
        email: {
            type: DataTypes.STRING(255),
            allowNull: false,
            unique: true,
        },
        username: {
            type: DataTypes.STRING(30),
            allowNull: false,
            unique: true,
        },
    })
    return users
}**
```

现在，让我们假设我们已经创建了一个额外的模型，叫做`**posts**`。我们将经历与对`**users**`相同的模型定义和接口声明过程——将`**posts**`模型包装在一个接受`**sequelize**`对象的函数中，并同样导出它。

现在，我们想将我们的两个模型`**users**`和`**posts**`导入到一个我们可以在任何地方使用的集中类中。我们只想初始化`**sequelize**`对象一次，并把它传递给我们所有的模型。我们可以创建一个这样的类，`**DB**`:

```
**import { Sequelize } from "sequelize"
import Users from "../models/Users"
import Posts from "../models/Posts"****class DB {
    // create sequelize connection
    public static readonly sequelize = new Sequelize(
        process.env.YOUR_DB_NAME,
        process.env.YOUR_DB_USER,
        process.env.YOUR_DB_PASSWORD,
        {
            host: "localhost",
            dialect: "postgres",
        }
    )** **// initialize models by passing sequelize into our model definition functions
    public static readonly users = Users(DB.sequelize)
    public static readonly posts = Posts(DB.sequelize)
}****export default DB**
```

现在，对于热衷于关系数据库的用户来说，您可能会注意到这里缺少了一条重要的信息。型号`**posts**`大概和型号`**users**`有关系吧？这可能就是我们所说的*一对多*或 *1:n* 关系。我们也应该想办法给这种关系下一次定义。为此，我们可以在定义`**DB**`类之前创建一个`**setRelations()**`函数来定义这些关系。现在完整的`**DB.ts**`文件变成了:

```
**class DB {
    // create sequelize connection
    public static readonly sequelize = new Sequelize(
        process.env.YOUR_DB_NAME,
        process.env.YOUR_DB_USER,
        process.env.YOUR_DB_PASSWORD,
        {
            host: "localhost",
            dialect: "postgres",
        }
    )** **// initialize models by passing sequelize into our model definition functions
    public static readonly users = Users(DB.sequelize)
    public static readonly posts = Posts(DB.sequelize)
}****// --> new code: setRelations() function
function setRelations(): void {
    /////////////////
    // One-To-Many //
    /////////////////** **// Users and Posts One-To-Many Relationship
    DB.users.hasMany(DB.posts, { foreignKey: "userId" })
    DB.posts.belongsTo(DB.users, { foreignKey: "userId" })
}****setRelations()
// <-- end new code****export default DB**
```

在这种情况下，表`**posts**`需要有列`**userId**`(如您在[示例代码库](https://github.com/princefishthrower/full-stack-typing-boilerplate)中所见)。现在 Sequelize 将知道表用户和帖子有一对多的关系，我们可以很容易地用这两者创建`**JOIN**`语句。

# 利用它！(后端)

很好，我们基本上完成了！使用我们的`**DB**`类的新方法如下(仍然坚持用户对`**users**`表的查询):

```
**import DB from "../DB"
import { Op } from "sequelize"****const email = "test@test.com"
const user = await DB.users.findOne({
    where: {
        email: {
            [Op.eq]: email,
        },
    },
})****if (!user) {
    // TODO: real error logging and handling
    console.log("error")
}****const username = user.username
// more logic...**
```

为了展示一个`**JOIN**`示例，让我们通过 Sequelize `**include**`指令获取给定用户的所有帖子。因为我们已经定义了一对多的关系，这个`**JOIN**`变成了一行代码(是的，技术上是一个 5 行代码——但是如果你愿意，你可以把它放在一行代码中😉 😂):

```
**import DB from "../DB"
import { Op } from "sequelize"****const email = "test@test.com"
const userWithPosts = await DB.users.findOne({
    where: {
        email: {
            [Op.eq]: email,
        },
    },
    include: [
        {
            model: DB.posts, // That's all it takes to JOIN on table posts! Sequelize knows the JOIN should be on column userId!
        },
    ],
})****if (!userWithPosts) {
    // TODO: real error logging and handling
    console.log("error")
}****const posts = userWithPosts.posts**
```

由此产生的`**userWithPosts**`对象将有一个嵌套的结构，完整的`**user**`对象在最高层，但是有一个额外的键`**posts**`，包含用户的文章，类型为`**Array<IPosts>**`。在这种情况下，用确切的类型定义一个新的接口是有用的(记住，我们假设已经写了一个`**IPosts**`):

```
**import IUser from "./IUser"
import IPost from "./IPost"****export default interface IUserWithPosts extends IUser {
    posts: Array<IPost>
}**
```

显式键入来自`**findOne()**`调用的响应是一个好习惯:

```
**...
import IUserWithPosts '../../shared/interfaces/IUserWithPosts';
...
const userWithPosts: IUserWithPosts = await DB.users.findOne({ ... });**
```

如果是一个`**findAll()**`调用，例如查找来自所有用户的所有帖子(一个玩具示例——谁知道你是否真的需要它，也许你会有所需要🤔)，返回类型成为该类型的数组版本:

```
**...
import IUserWithPosts '../../shared/interfaces/IUserWithPosts';
...
const usersWithPosts: Array<IUserWithPosts> = await DB.users.findAll({ ... });**
```

通过这种设置，您可以在定义端点时轻松地将其插入路由器。基本上，如果没有找到记录，您将返回 HTTP 404，如果找到了，则返回 HTTP 200 success！更多信息请参见[示例代码库](https://github.com/princefishthrower/full-stack-typing-boilerplate)。

# 利用它！(前端)

记得我说过要注意那个界面`**IUser**`吗？当我们进行前端开发时，我们可以可靠地输入所有的 API 请求！这意味着:

前端和后端共享他们输入的真实的单一来源！

当我第一次意识到使用 TypeScript 可以做到这一点时，我惊讶地发现这极大地减少了语法和运行时错误，从而提高了生产率。

当单个模型和界面定义您的数据时，跟踪数据流是如此容易——无论您是在后端还是前端。

我真的不在乎那些`**vim**`家伙说什么；如果您有这样的设置，并且正在使用具有 Intellisense 的现代编辑器，那么您只需点击几下鼠标就可以深入了解 API 的完整类型定义——同样，无论您是从后端还是前端开始。

作为一个前端例子，这里有一个`**fetch()**`调用，我们可以使用我们的接口键入它:

```
**...
import IUser '../../shared/interfaces/IUser';
...
// IUser typed result
try {
    const response = await fetch('https://your-api-url.com/get-user?email=test@test.com');
    if (response.status === 200) {
        const json = await response.json();
        const user: IUser = json.user;
        // user has all properties IUser has
    }
} catch (err) {
    console.log(err)
}****// IUserWithPosts typed result
...
import IUserWithPosts '../../shared/interfaces/IUserWithPosts';
...****try {
    const response = await fetch('https://your-api-url.com/get-user-with-posts?email=test@test.com');
    if (response.status === 200) {
        const json = await response.json();
        const userWithPosts: IUserWithPosts = json.userWithPosts;
        // userWithPosts has all properties IUserWithPosts has
    }
} catch (err) {
    console.log(err)
}**
```

当然，在响应对象中，你必须在第一个例子中传递一个键为`**user**`的对象，在第二个例子中传递一个键为`**userWithPosts**`的对象——参见[示例代码库](https://github.com/princefishthrower/full-stack-typing-boilerplate)。

# 全栈组织

您可能已经注意到，这种全堆栈类型的模式提出了三个一般领域，也许最好收集到文件夹中:

*   `**backend/**` -路由、数据库查询等。
*   `**shared/**` -接口、枚举、类型等。
*   `**frontend/**` -视图、组件、样式等。

根据许多因素，包括项目的规模和范围，您可能希望为每个“区域”建立一个存储库，或者将所有三个“区域”作为文件夹放在同一个存储库中。我将把它收集在一个库中，就像[示例代码库](https://github.com/princefishthrower/full-stack-typing-boilerplate)一样。

# 代码库

这里有一个[示例存储库](https://github.com/princefishthrower/full-stack-typing-boilerplate)，它包括前端、后端和共享区域。请在到达后阅读自述文件！

您可能还想看看[typescript-rest-boilerplate](https://github.com/vrudikov/typescript-rest-boilerplate)以了解更多的后端实现思想和组织——尽管注意，boilerplate 不包括前端部分。

你知道任何其他样板文件或框架使用了像我这样的模式吗？我很想知道。下面留言评论！

# 🥳BONUS！🥳

*本节是本帖的延伸，不再包含基本信息和概念。由于在后端引入了一个快速路由器，在前端引入了一个 react 应用程序，这需要一个跳跃。当然，非常欢迎感兴趣的读者继续阅读！*

我提到过，我将演练如何在这种类型的框架中添加 API 端点，我引用如下:

> 文字分钟

让我们看看我是否能履行我的承诺。我将使用[示例代码库的](https://github.com/princefishthrower/full-stack-typing-boilerplate)布局作为起点——随意使用并跟随，或者更好的是，克隆它并跟随编码！👩‍💻👨‍💻

准备好了吗？！

🕰，各就各位，准备🤨，去吧🚀！

# 第 1 分钟:场景/规格

让我们构建一个简单的用户搜索，可在`**/user-search**`访问，并期望查询参数`**username**`带有用户名(注意，我们将允许部分名称并利用`**LIKE**` SQL 操作符)。如果没有提供`**username**`参数，它应该返回一个 HTTP 400 代码，如果没有找到用户，返回 404，否则返回 200(当找到结果时)。

# 第 2 分钟:向路由器添加路由

首先，我们将我们的路线添加到`**Router/Router.ts**`，并将其传递给一个函数，比如说`**userSearch**`:

```
**Router.get("/user-search", GetRoutes.userSearch)**
```

# 第三分钟:编写用户搜索功能

然后我们实际编写函数`**userSearch**`。我们将它添加到`**Router/get/Users.ts**`文件中，因为我们需要访问的表是`**users**`:

```
**export async function userSearch(req: express.Request, res: express.Response) {
    // return 400 if username parameter not provided
    if (req.query.username === null) {
        return res.status(400).send("Username parameter not provided");
    }** **const users: Array<IUser> = await DB.users.findAll({
        where: {
                username: {
                    [Op.like]: '%' . req.query.username '%',
                },
            },
        }
    );** **if (users.length === 0) {
        return res.status(404).send("No users found! :(");
    }** **// send a 200 response with user data keyed as 'users'
    return res.status(200).send({ users });
}**
```

# 第 4 分钟:使用 Route 编写前端组件

在前端，在`**components/SearchUsers.tsx**`下，你可以用`**fetch**`构建这样一个 react 功能组件。注意`**fetch**`调用是硬编码的，因为这是最简单的例子:

```
**import React, { useState, useEffect } from "react"
import IUser from "../../../shared/interfaces/IUser"
import Constants from "../../../shared/Constants/Constants"****export default function GetUser() {
    const [users, setUsers] = useState<Array<IUser> | undefined>(undefined)** **useEffect(() => {
        if (!users) {
            searchUsers()
        }
    })** **const searchUsers = async () => {
        try {
            const response = await fetch(
                Constants.API_URL + "/search-users?username=tes"
            )
            if (response.status === 200) {
                const json = await response.json()
                setUsers(json.users)
            }
        } catch (err) {
            console.log(err)
        }
    }** **return (
        <>
            {users ? (
                <ul>
                    {users.map(user => {
                        return <li>{user.username}</li>
                    })}
                </ul>
            ) : (
                "Searching..."
            )}
        </>
    )
}**
```

咻，真快！不到 5 分钟！🥵，我在流汗！

我知道，我知道，你当然会构建一个交互式输入并呈现一个列表，但是我的*文字分钟*声明是关于构建一个 API 端点。这不包括前端组件😉。

那么，那是*字面上的分钟*？我也这么想😄

# 谢谢！

我希望这篇文章有助于让你的全栈应用程序开发更加愉快！

我知道我一直对这个装置很感兴趣！🚀 🚀 🚀

干杯！🍺

克里斯
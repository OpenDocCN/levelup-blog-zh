# Fastify 基础教程| Express 的更好选择

> 原文：<https://levelup.gitconnected.com/fastify-basics-tutorial-a-better-alternative-to-express-23170c2a5816>

![](img/914af5fd21e6d0b49bbb591bd6757374.png)

在这篇博客中，我们将学习你开始使用 Fastify 所需要知道的一切。

视频教程

# 什么是 Fastify？

Fastify 是 Node.js 的一个 web 框架。它是一个轻量级、快速、灵活的框架，用于构建现代服务器端 web 应用程序。

它与 Express 非常相似。但是它有一些特点使它与众不同。

*   模式验证
*   插件系统

# 设置

```
npm init -y
npm i fastify
```

# 创建一个基本节点服务器

```
const fastify = require('fastify')const app = fastify({ logger: true })const PORT = process.env.PORT || 8000app.listen(PORT).catch(error => {
    app.log.error(error)
    process.exit()
})
```

解释:

*   `app`是 Fastify 的实例。Fastify 有独立的登录系统。我们通过传递对象来启用记录器。
*   `PORT`是端口号。
*   `app.listen`是启动服务器的功能。如果出现任何错误，它将记录错误并退出该进程。

启动服务器

```
node <file>
```

或者，每当进行更改时，可以使用 nodemon 来重新启动服务器。

```
npm i -g nodemon
nodemon <file>
```

你可以看到它与 Express 非常相似。让我们创建一些简单的 api。

我将使用以下数据。

```
[
    {
        "id": 1,
        "name": "Innis Gladeche",
        "email": "igladeche0@yellowbook.com",
        "gender": "Male",
        "country": "North Korea"
    },
    {
        "id": 2,
        "name": "Woodman Haylands",
        "email": "whaylands1@addtoany.com",
        "gender": "Male",
        "country": "Russia"
    },
    {
        "id": 3,
        "name": "Caleb Galbraith",
        "email": "cgalbraith2@last.fm",
        "gender": "Male",
        "country": "Brazil"
    },
    {
        "id": 4,
        "name": "Earlie Beddie",
        "email": "ebeddie3@nsw.gov.au",
        "gender": "Genderqueer",
        "country": "Ukraine"
    },
    {
        "id": 5,
        "name": "Marcellus Cloake",
        "email": "mcloake4@opensource.org",
        "gender": "Male",
        "country": "Sweden"
    },
    {
        "id": 6,
        "name": "Mada Poll",
        "email": "mpoll5@washington.edu",
        "gender": "Female",
        "country": "Sweden"
    },
    {
        "id": 7,
        "name": "Ashly Goodrum",
        "email": "agoodrum6@photobucket.com",
        "gender": "Female",
        "country": "United States"
    },
    {
        "id": 8,
        "name": "Obed Mabbs",
        "email": "omabbs7@lycos.com",
        "gender": "Male",
        "country": "China"
    },
    {
        "id": 9,
        "name": "Margalo Weild",
        "email": "mweild8@freewebs.com",
        "gender": "Female",
        "country": "Sweden"
    },
    {
        "id": 10,
        "name": "Seth Jex",
        "email": "sjex9@deliciousdays.com",
        "gender": "Male",
        "country": "France"
    }
]
```

# 获取路线

```
const getUsers = (request, reply) => usersapp.get('/getUsers' getUsers)
```

*   要处理请求，请使用 app 中的 HTTP 方法。将`app.get`用于 GET 请求，将`app.post`用于 POST 请求等等。
*   该函数有两个参数。api 端点和回调函数。
*   处理函数有两个参数。请求和回复。
*   要返回响应，只需从函数返回数据。我们正在返回用户数组。

# 询问参数

您可以将 URL 作为查询参数发送附加信息。

示例:

```
[http://localhost:8000/getUsers?gender=female](http://localhost:8000/getUsers?gender=female)
```

在问号之后，我们有键值对。这就是我们所说的查询参数。让我们在`/getUsers`路线中使用它。我们将通过一个查询参数获得特定性别的用户。

```
const getUsers = (request, reply) => {
    const { gender } = request.query if (!gender) return users const filteredUsers = users.filter(
        (user) => user.gender.toLowerCase() === gender.toLowerCase()
    ) return filteredUsers
}app.get('/getUsers' getUsers)
```

解释:

*   我们从`request.query`对象中获取`gender`。
*   如果性别不存在，我们就发用户。
*   否则，我们将根据性别过滤用户，并将其作为响应返回。

# 邮寄路线

让我们创建一个新用户。

```
const addUser = request => {
    const id = users.length + 1 const newUser = { ...request.body, id } users.push(newUser) return newUser
}app.post('/addUser', addUser)
```

解释:

*   这次我们用`app.post`代替`app.get`。
*   我们从`request`对象获取请求体。
*   然后，我们使用请求主体的信息创建一个新用户，并将其添加到 users 数组中。

# 模式验证

模式是某种数据的结构化表示。在模式中，您可以指定数据将具有什么样的前缀以及将存储什么样的值。
Fastify 有一个内置的模式验证。您可以对请求体、查询参数、响应和头进行模式验证。
是我最喜欢的 Fastify 的功能。让我们在`/addUser`路线中使用它。

```
const addUserOptions = {
    schema: {
        body: {
            type: 'object',
            properties: {
                name: {
                    type: 'string',
                },
                age: {
                    type: ['number', 'string'],
                },
                gender: {
                    type: 'string',
                    enum: ['male', 'female', 'others'],
                },
            },
            required: ['name', 'gender'],
        },
    },
}const addUser = request => {
    const id = users.length + 1 const newUser = { ...request.body, id } users.push(newUser) return newUser
}app.post('/addUser', addUserOptions, addUser)
```

解释:

*   我们填充选项对象作为第二个参数。
*   我们正在为请求主体创建一个模式。
*   在 properties 对象中包含必要的属性及其类型。
*   在所需的数组中包含所需的属性。
*   要了解关于模式验证的更多信息，请查看视频教程。

这就是我想教你的。要了解 Fastify 的更多信息，请查看视频教程。我试图简单地解释事情。如果你卡住了，你可以问我问题。

顺便说一下，我正在一家公司寻找一个新的机会，在那里我可以用我的技能提供巨大的价值。如果你是一名招聘人员，正在寻找一个精通全栈网络开发并对改变世界充满热情的人，请随时联系我。此外，我愿意谈论任何自由职业者的项目。

从[这里](https://www.thatanjan.me/projects)看我的作品
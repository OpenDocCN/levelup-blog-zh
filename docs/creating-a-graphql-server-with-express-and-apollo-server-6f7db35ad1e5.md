# 使用 Express 和 Apollo 服务器创建 GraphQL 服务器

> 原文：<https://levelup.gitconnected.com/creating-a-graphql-server-with-express-and-apollo-server-6f7db35ad1e5>

![](img/bd5da3ab6947d6381f8f65155bc522d5.png)

照片由[费利克斯·索奇](https://unsplash.com/@felix_soage?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Apollo 服务器作为节点包提供。我们可以用它来创建一个接受 GraphQL 请求的服务器。

在本文中，我们将研究如何使用 Express 来创建我们自己的 GraphQL 服务器。

# Apollo 服务器入门

我们从安装`express-apollo-server`开始。

要使用 Express 安装它，我们运行:

```
npm install apollo-server-express express
```

然后创建一个`index.js`文件并添加:

```
const express = require('express');
const { ApolloServer, gql } = require('apollo-server-express');const books = [
  {
    title: 'JavaScript for Dummies',
    author: 'Jane Smith',
  },
  {
    title: 'JavaScript Book',
    author: 'Michael Smith',
  },
];const typeDefs = gql`
  type Book {
    title: String
    author: String
  } type Query {
    books: [Book]
  }
`;const resolvers = {
  Query: {
    books: () => books,
  },
};const app = express();
const server = new ApolloServer({ typeDefs, resolvers });
server.applyMiddleware({ app });app.listen(3000, () => console.log('server started'));
```

在上面的代码中，我们通过在`books`数组中创建数据来创建基本的 Apollo GraphQL 服务器。

然后我们使用带有模式定义字符串的`gql`标签来创建我们的模式，并将其分配给`typedefs`常量。

查询类型总是必需的，这样我们就可以从我们的服务器查询数据。没有它服务器就不能运行。

我们创建了一个带有字段`title`和`author`的`Book`类型。然后我们创建了一个`books`查询来返回一个`Book`的数组。

接下来，我们创建了我们的`resolvers`,这样我们就可以查询我们创建的数据。我们刚刚创建了一个`books`解析来返回`books`数组。

最后，我们有以下初始化代码来加载服务器:

```
const app = express();
const server = new ApolloServer({ typeDefs, resolvers });
server.applyMiddleware({ app });app.listen(3000, () => console.log('server started'));
```

然后，当我们在浏览器中转到`/graphql`时，我们会看到一个 UI 来测试我们的查询。

为了运行服务器，我们运行:

```
node index.js
```

我们可以通过运行以下命令来测试我们的服务器:

```
{
  books {
    title
    author
  }
}
```

然后，当我们单击中间的箭头按钮时，我们应该得到:

```
{
  "data": {
    "books": [
      {
        "title": "JavaScript for Dummies",
        "author": "Jane Smith"
      },
      {
        "title": "JavaScript Book",
        "author": "Michael Smith"
      }
    ]
  }
}
```

作为回应。

![](img/896ec1d7647efb5c45e55f7358e67c18.png)

照片由 [NASA](https://unsplash.com/@nasa?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

通过安装`express`和`express-apollo-server`包，我们用 Express 创建了一个简单的 Apollo GraphQL 服务器。

然后，我们通过将带有类型定义的字符串传递到`gql`标签中来创建类型定义。

一旦我们这样做了，我们就创建了一个解析器来返回从查询映射的响应。

然后我们运行服务器进行查询并返回数据。我们可以通过访问 Express Apollo 服务器附带的`/graphql`页面进行测试。
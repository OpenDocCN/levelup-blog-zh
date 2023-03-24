# 使用 Express Apollo 创建和使用模式指令

> 原文：<https://levelup.gitconnected.com/creating-and-using-schema-directives-with-express-apollo-36c6b77d751e>

![](img/c3d07d8ad3ec58728713a643337f97ca.png)

由[大卫·托里斯](https://unsplash.com/@djjabbua?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Apollo 服务器作为节点包提供。我们可以用它来创建一个接受 GraphQL 请求的服务器。

在本文中，我们将了解如何在我们的 Express Apollo Server GraphQL 应用程序中创建和使用模式指令。

# 使用模式指令

指令是一个标识符，前面有一个`@`字符。它后面可选地跟着一个命名参数列表。

下面是一个例子:

```
directive @deprecated(
  reason: String = "No longer supported"
) on FIELD_DEFINITION | ENUM_VALUE
```

在上面的代码中，我们有一个`@deprecated`指令，后面跟着一个它所属的字段。

`on`关键字表示不推荐使用的位置。`FIELD_DEFINITION`表示可以放在字段定义之后，`ENUM_VALUE`表示可以放在枚举值定义附近。

# 默认指令

GraphQL 提供了几个默认指令。它们是`@deprecated`、`@skip`和`@include`。

*   `@deprecated(reason: String)` -用消息将字段标记为已弃用
*   `@skip(if: Boolean!)` -如果`true`不调用解析器，GraphQL 执行会跳过该字段
*   `@include(if: Boolean!)` -如果`true`，则调用注释字段的解析器

# 使用自定义模式指令

我们可以通过定义一个扩展`SchemaDirecuiveVisitor`的类来定义我们自己的模式指令。

例如，我们可以如下定义自己的模式指令:

```
const express = require('express');
const { ApolloServer, gql, SchemaDirectiveVisitor } = require('apollo-server-express');
const { defaultFieldResolver } = require('graphql');class LowerCaseDirective extends SchemaDirectiveVisitor {
  visitFieldDefinition(field) {
    const { resolve = defaultFieldResolver } = field;
    field.resolve = async function (...args) {
      const result = await resolve.apply(this, args);
      if (typeof result === 'string') {
        return result.toLowerCase();
      }
      return result;
    };
  }
}const typeDefs = gql`
  directive @lower on FIELD_DEFINITION type Query {
    hello: String @lower
  }
`;const resolvers = {
  Query: {
    hello: () => {
      return 'Hello world!';
    },
  },
};const schemaDirectives = {
  lower: LowerCaseDirective,
}
const app = express();
const server = new ApolloServer({
  typeDefs, resolvers, schemaDirectives
});
server.applyMiddleware({ app });app.listen(3000, () => console.log('server started'));
```

在上面的代码中，我们有一个`LowerCaseDirective`类，我们定义如下:

```
class LowerCaseDirective extends SchemaDirectiveVisitor {
  visitFieldDefinition(field) {
    const { resolve = defaultFieldResolver } = field;
    field.resolve = async function (...args) {
      const result = await resolve.apply(this, args);
      if (typeof result === 'string') {
        return result.toLowerCase();
      }
      return result;
    };
  }
}
```

为了扩展`SchemaDirectiveVisitor`类，我们实现了`visitFieldDefintion`方法。它采用`field`参数。我们通过将`resolve`属性设置为一个异步函数来生成我们的结果，该函数解析到我们希望承诺解析到的`result`。

然后，我们将指令添加到类型定义中，如下所示:

```
const typeDefs = gql`
  directive @lower on FIELD_DEFINITION type Query {
    hello: String @lower
  }
`;
```

我们指出，我们希望在定义字段时应用该指令。

接下来，我们将模式指令名映射到`SchemaDirectiveVisitor`类，如下所示:

```
const schemaDirectives = {
  lower: LowerCaseDirective,
}const server = new ApolloServer({
  typeDefs, resolvers, schemaDirectives
});
```

在`resolver`中，我们以我们喜欢的任何方式返回字符串，然后通过指令将字符串转换成我们想要的值，即转换成小写字母。

然后，当我们进行如下的`hello`查询时:

```
{
  hello
}
```

我们得到:

```
{
  "data": {
    "hello": "hello world!"
  }
}
```

多亏了我们的`@lower`指令，而不是`‘Hello world!’`。

![](img/4feb99e8347606f9142239b0a2c56056.png)

照片由[卡格拉尔·阿拉兹](https://unsplash.com/@lelacag?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

指令是一个以`@`开头的标识符，后面是一列命名参数。

我们可以通过创建一个扩展了`SchemaDirectiveVisitor`类的类，然后实现`visitFieldDefinition`方法来定义一个。该方法接受一个`field`对象，然后将`field`的`resolve`方法设置为一个异步函数。

`resolve`方法解析出我们希望承诺解析出的结果。

然后，当我们创建 Apollo 服务器时，通过将`schemaDirectives`属性传递到对象中，我们将它映射到一个名称。

在我们的解析中，我们可以返回我们想要的任何东西，我们的指令将通过进行自己的更改来覆盖结果，然后将结果返回给用户。
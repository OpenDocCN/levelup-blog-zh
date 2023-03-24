# 使用 Express-GraphQL 创建 GraphQL 输入类型

> 原文：<https://levelup.gitconnected.com/creating-graphql-input-types-with-express-graphql-6d402351b326>

![](img/83485f6620c11ae6964da615507d4072.png)

由[伊森·麦克阿瑟](https://unsplash.com/@snowjam?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

使用 GraphQL，我们可以像创建对象类型和其他复合数据类型一样创建标量类型。

在本文中，我们将看看如何用`GraphQLInputObjectType`构造函数创建 GraphQL 输入类型。

# GraphQLInputObjectType 构造函数

我们可以使用`GraphQLInputObjectType`构造函数来创建一个新的输入类型。

为此，我们向带有`name`属性的`GraphQLInputObjectType`构造函数传递一个对象来设置输入类型的名称，然后传递一个带有字段名作为键和值的`fields`对象，这些对象带有`type`属性来指定字段的类型。

为了指定一个必填字段，我们使用了`GraphQLNonNull`构造函数。此外，我们可以通过为`defaultValue`属性指定一个值来指定一个默认值。

例如，我们可以如下指定一个突变:

```
const express = require('express');
const graphqlHTTP = require('express-graphql');
const graphql = require('graphql');class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }
}const PersonInputType = new graphql.GraphQLInputObjectType({
  name: 'PersonInput',
  fields: {
    firstName: { type: new graphql.GraphQLNonNull(graphql.GraphQLString) },
    lastName: { type: new graphql.GraphQLNonNull(graphql.GraphQLString) },
  }
});const PersonType = new graphql.GraphQLObjectType({
  name: 'Person',
  fields: {
    firstName: { type: graphql.GraphQLString },
    lastName: { type: graphql.GraphQLString },
  }
});let person = new Person('Jane', 'Smith');const mutationType = new graphql.GraphQLObjectType({
  name: 'Mutation',
  fields: {
    createPerson: {
      type: PersonType,
      args: {
        person: {
          type: new graphql.GraphQLNonNull(PersonInputType),
        },
      },
      resolve: (_, { person: { firstName, lastName } }) => {
        person = new Person(firstName, lastName);
        return person;
      }
    }
  }
});const queryType = new graphql.GraphQLObjectType({
  name: 'Query',
  fields: {
    person: {
      type: PersonType,
      resolve: () => {
        return person;
      }
    }
  }
});const schema = new graphql.GraphQLSchema({ query: queryType, mutation: mutationType });const app = express();
app.use('/graphql', graphqlHTTP({
  schema: schema,
  graphiql: true,
}));app.listen(3000, () => console.log('server started'));
```

在上面的代码中，我们创建了如下的`PersonInputType`输入类型:

```
const PersonInputType = new graphql.GraphQLInputObjectType({
  name: 'PersonInput',
  fields: {
    firstName: { type: new graphql.GraphQLNonNull(graphql.GraphQLString) },
    lastName: { type: new graphql.GraphQLNonNull(graphql.GraphQLString) },
  }
});
```

我们使用了`GraphQLInputObjectType`构造函数，并传入一个带有值为`'PersonInput'`的`name`字段的对象来指定类型的名称。

然后在`fields`属性中，我们指定该类型有一个`firstName`和`lastName`字段，这两个字段都是必需的，因为我们使用了传递了`GraphQLString`类型的`GraphQLNonNull`构造函数。

输入类型只能用于突变。

然后，我们创建一个采用如下`PersonInputType`参数的变异:

```
const mutationType = new graphql.GraphQLObjectType({
  name: 'Mutation',
  fields: {
    createPerson: {
      type: PersonType,
      args: {
        person: {
          type: new graphql.GraphQLNonNull(PersonInputType),
        },
      },
      resolve: (_, { person: { firstName, lastName } }) => {
        person = new Person(firstName, lastName);
        return person;
      }
    }
  }
});
```

在上面的代码中，我们用指定输出类型的`type`创建了一个`createPerson`变异，也就是我们在代码中指定的`PersonType`。

在`args`属性中，我们指定了`person`参数，并将`type`设置为`PersonInputType`。这指定 out 变异接受`PersonInuptType`输入类型对象。

然后在我们的`resolve`函数中，我们从第二个参数中的`args`参数获取`firstName`和`lastName`，并创建一个新的`Person`对象，将其设置为`person`并返回它。

所有 GraphQL 服务器应用程序都必须有一个根查询类型，所以我们还指定了一个`queryType`,如下所示:

```
const queryType = new graphql.GraphQLObjectType({
  name: 'Query',
  fields: {
    person: {
      type: PersonType,
      resolve: () => {
        return person;
      }
    }
  }
});
```

我们返回一个`Person`对象并返回`person`变量。

那么当我们进行如下突变时:

```
mutation {
  createPerson(person: {firstName: "John", lastName: "Doe"}){
    firstName
    lastName
  }
}
```

我们得到:

```
{
  "data": {
    "createPerson": {
      "firstName": "John",
      "lastName": "Doe"
    }
  }
}
```

作为回应。

当我们进行如下查询时:

```
{
  person {
    firstName
    lastName
  }
}
```

我们回来了:

```
{
  "data": {
    "person": {
      "firstName": "John",
      "lastName": "Doe"
    }
  }
}
```

![](img/20c122ae233323833a15937cd045e43d.png)

照片由[乔安娜·尼克斯](https://unsplash.com/@joanna_nix?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以用`GraphQLInputObjectType`构造函数创建一个 GraphQL 输入类型，其中`name`和`fields`属性在一个对象中一起传递给构造函数。

`name`字段具有名称，而`fields`对象具有字段和类型。

我们可以使用`GraphQLNonNull`来指定一个字段是必需的。

然后在我们的变异中，我们在`args`属性中指定输入数据类型。
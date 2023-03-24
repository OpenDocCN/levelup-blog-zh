# 用 Node 和 MongoDB 构建 GraphQL API

> 原文：<https://levelup.gitconnected.com/building-your-graphql-api-with-node-and-mongodb-799a2b9ae0b4>

## 我们将使用 Express framework for Node，以 MongoDB 作为我们的数据库，构建一个完整且完全可用的 GraphQL API。

![](img/883df854b0ec2f0bfef8748153f3a619.png)

在过去的十年里， [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) 已经成为设计 web APIs 的标准。随着 web 最佳实践的出现，REST 变得更加具体。然而，它已经变得太不灵活，无法满足复杂的客户端需求。因此，有更多的数据读取发生在后台，REST 在这种增长下表现不佳。为了克服僵化和低效，GraphQL 被创造出来。

在本文中，我们将构建一个跟踪厨师和菜肴的餐厅应用程序，可以使用 GraphQL 对其进行查询和更新

## GraphQL 是什么？

GraphQL 是一种由脸书创建的查询语言，旨在基于直观灵活的语法构建健壮的客户端应用程序。它充分描述了数据要求和与现有数据库的交互。GraphQL 由脸书内部开发，于 2015 年发布。

GraphQL 查询是发送到服务器进行解释和执行的字符串，响应将 JSON 返回给客户机。

## 我们为什么需要 GraphQL？

使用传统的 REST API 调用，我们无法让客户端请求一组定制的数据。相比之下，GraphQL 允许客户端定义所需数据的结构，从服务器返回相同的数据结构。这可以防止返回过多的数据。然而，它也增加了一层复杂性，这可能不适用于简单的 API。

此外，在 REST 架构中维护多个端点很困难。当应用程序增长时，端点的数量会增加，导致客户端需要从不同的端点请求数据。GraphQL APIs 通过在模式中提供结构化类型和字段，同时使用单个 API 端点来请求数据，从而更加有组织。

## 移动到代码

开始开发吧。首先，我们将创建一个新文件夹并初始化我们的`package.json`文件。然后使用下面列出的命令添加以下包:

```
yarn init
yarn add express graphql express-graphql mongoose
```

现在我们可以继续在根目录下创建我们的主文件`app.js` ，并从 Express GraphQL 包中请求`graphqlHTTP`。

```
const express = require('express');
const graphqlHTTP = require('express-graphql');

const mongo = require('mongoose');
const app = express();mongo.connect('mongodb://***yourusername***:***yourpassword***@ds053317.mlab.com:53317/gql-demo', {
    useNewUrlParser: true,
    useUnifiedTopology: true
})

mongo.connection.once('open', () => {
    console.log('connected to database');
})

app.use(‘/graphiql’, graphqlHTTP({ schema: require(‘./schema.js’), graphiql: true}));

app.listen(8080, () => {
    console.log('Server running succefully...')
})
```

在这里，我们已经从已安装的软件包中要求了`express`和`graphqlHTTP`。我们还使用`mlab`与我们的 MongoDB 数据库建立了连接。通过将`g[raphiql](https://graphql.org/swapi-graphql)`设为 true，我们可以发送和接收来自浏览器的请求，就像失眠症患者或邮递员一样。我们也可以在本地提供服务，并在[http://localhost:8080/graphi QL](http://localhost:8080/graphiql)测试以使用控制台。

我们的下一步是构建数据模型，以便将项目存储到数据库中。我们将新建一个文件夹`mongo-models`，我们将创建两个文件`chef.js` 和`dishes.js` ，如下图:

现在我们将创建一个文件夹，并将其命名为`Schema.js` ，在这里我们将向代码添加类型并定义我们的 GraphQL API:

```
const graphql = require('graphql');

const Dish = require('../mongo-models/dish');
const Chef = require('../mongo-models/chef');

const {
    GraphQLObjectType,
    GraphQLString,
    GraphQLBoolean,
    GraphQLSchema,
    GraphQLID,
    GraphQLFloat,
    GraphQLList,
    GraphQLNonNull
} = graphql;

const DishType = new GraphQLObjectType({
    name: 'Dish',
    fields: () => ({
        id: {
            type: GraphQLID
        },
        name: {
            type: GraphQLString
        },
        tasty: {
            type: GraphQLBoolean
        },
        country: {
            type: GraphQLString
        },
        chefs: {
            type: ChefType,
            resolve(parent, args) {
                return Chef.findById(parent.chefsId)
            }
        }
    })
});

const ChefType = new GraphQLObjectType({
    name: 'chefs',
    fields: () => ({
        id: {
            type: GraphQLID
        },
        name: {
            type: GraphQLString
        },
        rating: {
            type: GraphQLFloat
        },
        dish: {
            type: new GraphQLList(DishType),
            resolve(parent, args) {
                return Dish.find({
                    chefsId: parent.id
                })
            }
        }
    })
});
```

在上面的代码中，我们从文件夹中导入了`graphql`和我们的 Mongo 模型。我们还为 GraphQL 中的数据类型提供了类型定义，它包含在一个带粗箭头的字段函数中。在 GraphQL 中，我们有 resolve 函数，当我们编写一些根查询时，它会被触发，我们将在接下来的步骤中看到。

在`resolve`函数中，我们从数据库中获取数据。

```
const RootQuery = new GraphQLObjectType({
    name: 'RootQueryType',
    fields: {
        dish: {
            type: DishType,
            args: {
                id: {
                    type: GraphQLID
                }
            },
            resolve(parent, args) {
                return Dish.findById(args.id);
            }
        },
        chefs: {
            type: ChefType,
            args: {
                id: {
                    type: GraphQLID
                }
            },
            resolve(parent, args) {
                return Chef.findById(args.id);
            }
        },
        dishes: {
            type: new GraphQLList(DishType),
            resolve(parent, args) {
                return Dish.find({});
            }
        },
        chefs: {
            type: new GraphQLList(ChefType),
            resolve(parent, args) {
                return Chef.find({});
            }
        }
    }
});

const Mutation = new GraphQLObjectType({
    name: 'Mutation',
    fields: {
        addDish: {
            type: DishType,
            args: {
                name: {
                    type: new GraphQLNonNull(GraphQLString)
                },
                country: {
                    type: new GraphQLNonNull(GraphQLString)
                },
                tasty: {
                    type: new GraphQLNonNull(GraphQLBoolean)
                }
            },
            resolve(parent, args) {
                let dish = new Dish({
                    name: args.name,
                    country: args.country,
                    tasty: args.tasty,
                });
                return dish.save();
            }
        },
        addChef: {
            type: ChefType,
            args: {
                name: {
                    type: new GraphQLNonNull(GraphQLString)
                },
                rating: {
                    type: new GraphQLNonNull(GraphQLString)
                }
            },
            resolve(parent, args) {
                let chef = new Chef({
                    name: args.name,
                    rating: args.rating
                });
                return chef.save();
            }
        }
    }
})

module.exports = new GraphQLSchema({
    query: RootQuery,
    mutation: Mutation
});
```

我们已经在`schema.js`文件中添加了上面一行代码。这里我们添加了`rootquery`来获取带有`id`的菜肴类型，并返回带有`GraphQLList`导入的所有菜肴列表。`chefs`也是如此。

我们还向 GraphQL 中的对象添加了一个**突变**，我们可以通过强制字段`GraphQLnonNull` import 向数据库添加一道菜。在 resolve 函数中，我们返回了新对象，并将其保存到数据库中。厨师的突变也是如此。

最后，我们有我们的`rootquery`和`mutation` 的出口在最后*。*

就是这样！我们现在有了一个使用 GraphQL 和 Node 的工作的API。

我还在这里链接了我的 GitHub 链接，这样你们就可以派生并使用可用的代码了——[**链接这里**](http://bit.ly/2QLn7Ao\)

## **参考读物:**

[](https://graphql.org/) [## GraphQL:一种 API 查询语言。

### 学习准则社区规范行为准则基金会学习准则社区规范行为准则基金会类型项目…

graphql.org](https://graphql.org/) [](https://www.howtographql.com/) [## 如何使用 graph QL——graph QL 的完整教程

### 免费的开源教程，全面了解 GraphQL 从零到生产。第一场总共 42 分钟…

www.howtographql.com](https://www.howtographql.com/) [](https://scotch.io/tutorials/a-practical-graphql-getting-started-guide-with-nodejs) [## Node.js 实用 GraphQL 入门指南

### 我们大多数人可能对创建 REST APIs 非常熟悉。GraphQL 是一种查询语言，由脸书用…

scotch.io](https://scotch.io/tutorials/a-practical-graphql-getting-started-guide-with-nodejs) 

*********************** —快乐编码—* * * * * * * * * * * * * * * * * * * * * * *
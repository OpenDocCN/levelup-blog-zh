# Apollo 客户端+ Typescript 钩子模式

> 原文：<https://levelup.gitconnected.com/apollo-client-typescript-hook-patterns-33353b6315a1>

![](img/185a0ee19821594d0a5b8202f749e22e.png)

照片由[斯蒂夫·约翰森](https://unsplash.com/@steve_j?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在过去的 18 个月里，我们已经看到 React 和 Apollo 带来了许多伟大的新东西。可以说，钩子是最重要的变化之一。在我看来，它们对我们代码库的结构、外观和感觉产生了最大的影响。我花了一些时间和另一个开发人员 Ben Lacy 讨论我们希望我们的钩子结构是什么样子，这是我们发现最有用的。这是第 1/3 部分，包括文件夹结构和使用查询。寻找 useMutation 和 useLazyQuery 文章。

# 文件夹结构

## 以前

在之前一个基于 GraphQL 的项目中，我们使用了一个类似这样的文件夹结构。

```
app
 - graphql
    * query_name.gql
 - hoc
    * ApolloQueryHelper.js
 - pages
    * dashboard.js
 - components
    * ListItem.js
 - types
    * User.ts
```

这种结构有一个优点，你可以很容易地重用查询和片段。查询应该有唯一的名称。这种结构减少了具有相同签名但使用唯一名称的 gql 查询的重复。`RefetchQueries`使用查询名称，因此如果您的应用程序需要使用`RefetchQueries`函数，使用单个查询名称更容易跟踪。

## 更好的方法

如果我们开始更多地考虑模块，我们可以创建与应用程序其他部分耦合较少的组件。目标是只需要传入一个 id 来生成一个组件。这将创建具有本地化显示/逻辑的组件，并且更容易在整个项目中跟踪。这也将允许您更容易地将组件移植到新项目中。

```
app
 - components
   - UserName
     - graphql
       - __generated__.  <-- created by Apollo codegen 
         * generatedQueryType.ts  <-- created by Apollo codegen
       * useUserNameQuery.ts
       * useUserNameMutation.ts
     * UserName.tsx
```

注意到生成的文件了吗？我一般用打字稿工作，这些是由阿波罗 Codegen([https://github.com/apollographql/apollo-tooling](https://github.com/apollographql/apollo-tooling))创建的，我强烈推荐看看。这里的动机是将您的显示逻辑包含在`UserName.tsx`中，并将您的大部分业务/控制器逻辑移到`useUserNameQuery`或`useUserNameMutation`

# 使用查询

UseQuery 是一个用于控制 graphql 查询的钩子。在阿波罗客户端 3 中，它被放入了`@apollo/client`包中。查询挂钩通常非常简单。

```
import { useQuery } from "@apollo/client";
import gql from "graphql-tag";
import { ActivityTableQuery, ActivityTableQueryVariables } from "./__generated__/ActivityTableQuery";export default () =>
 useQuery<ActivityTableQuery, ActivityTableQueryVariables>(gql`
   query ActivityTableQuery {
    activity(order_by: { created_at: desc }) { 
     id
     title
   }
 }
`);
```

稍微剖析一下，我们的`useQuery`和`gql`的导入是标准的。下一行`import { ActivityTableQuery } from "./__generated__/ActivityTableQuery"`是导入生成的类型，我们将它传递给`useQuery`钩子。这个查询没有变量，但是如果有，我们会将它们作为第二个类型参数传入。之后，我们传递包装在`gql` 标签中的 gql 查询。需要注意的一点是，您的查询应该使用唯一的名称，匿名查询是禁止的。匿名查询是那些用花括号打开而不是使用`query MyQueryName.`的查询。查询名称将用于生成您的类型，我更喜欢大写类型名称，因此我的查询使用大写名称。

**消费**

```
import React from "react";import useActivityQuery from "./graphql/useActivityQuery";...interface ActivityTableProps {...}const ActivityTable = (props: ActivityTableProps) => {
 const { data, loading } = useActivityQuery();
 return (
   <ul>
    {data?.activity?.map(act=> <li key={act.id}>{act.title}</li>)
   </ul>
  )
}
```

GraphQL 的好处之一是您可以按照自己的方式设计数据。在我们的例子中，我们只需要活动的`title`和`id` ，所以这就是我们所要求的。

如果你能控制你的模式，我建议用 BFF(后端对前端)的风格来构建它。构建更能代表数据显示方式的类型，而不是看起来像 RDB 结构的类型，这将使每个人的生活更轻松，开发速度更快。

**如果我无法控制 API/图形怎么办？**

这是使用第三方 API 时的常见问题。我们如何修改钩子来使用数据，并以一种易于使用的方式将数据传递给显示组件？

假设我们有一个联合类型:`union Activity = SummerActivity | WinterActivity`使用不同的主键，`summerId`和`winterId.`我们的组件只想显示一个值`id`，所以这就是我们如何修改我们的钩子。

```
import { useQuery } from "@apollo/client";
import gql from "graphql-tag";
import { ActivityTableQuery, ActivityTableQueryVariables } from "./__generated__/ActivityTableQuery";export default () => {
const {data} =  useQuery<ActivityTableQuery, ActivityTableQueryVariables>(gql`
   query ActivityTableQuery {
    activity(order_by: { created_at: desc }) { 
     ... on WinterActivity{
       winterId
      }
     ... on SummerActivity{
       summerId
      }
   }
 }
`)const id = activity.__typename === 'SummerActivity' ? 
         activity.summerId
         : activity.__typename === 'WinterActivity' ?
         activity.winterId 
         : nullreturn { id }
};
```

现在，当我们使用数据时，我们将只看到一个 id，而不管它是什么类型。

这是第 1 部分，在我的下一篇文章中，我们将探索 useMutation 和用于包含变异数据的有趣模式。
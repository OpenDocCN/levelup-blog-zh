# GraphQL with。网

> 原文：<https://levelup.gitconnected.com/graphql-with-net-81a2e02ddfc9>

使用 DotNet Core 3 和 graphql-dotnet 创建 GraphQL API

![](img/e95437984fd6d6cfb00afd571eafcbcf.png)

所以前几天我偶然发现了[电影数据库](https://www.themoviedb.org/)，更有趣的是[电影数据库 API](https://developers.themoviedb.org/3) 。

它提供了一个 REST API 来检索和搜索电影、电视节目、演员等。以及添加评论等。我在用 VueJS twitter feed 制作的[上偶然发现了它](https://twitter.com/MadeWithVueJS)(不幸的是，我现在找不到确切的 tweet 了)，有人在 tweet 上展示了他们为它构建的 VueJS 前端。

“好主意！”我以为。我一直在寻找可以在 React 中构建的东西，这给了我一些实实在在的东西，在它背后有一个极其丰富的 API，给它一些脂肪。

所以我创建了基本的 React 应用程序，开始了我的旅程。

但后来我想“等等！休息死了！GraphQL FTW”(REST 并不是真的死了，只是 graph QL 酷多了)。幸运的是，我*也*一直在寻找一个使用 [graphql-dotnet](https://github.com/graphql-dotnet/graphql-dotnet) 创建的项目，所以我决定从那里开始。想法是创建一个 React 前端，最后用他们的新组合 API 创建一个 VueJS 前端。

因此，我现在承担了相当多的工作。但这一切都很有趣，所以我们开始吧！

你可以在 GitHub 上找到服务器[的所有源代码(不要因为缺少测试而太恨我。WIP)。](https://github.com/np-matt/gql-movies)

所以我从一个基本的 dotnet core 3 WebAPI 应用程序开始，去掉了天气预报的东西，添加了一个新的带有一个 HttpPost 端点的`GraphQlController`。

然后，我使用 [graphql-dotnet](https://github.com/graphql-dotnet/graphql-dotnet) 来创建模式并执行查询。

端点本身非常简单。整个控制器只有 40 条线:

需要调用`DeserializeObject`来启用 application/json 的 content-type 头的神奇设置。可能不是最有性能的做法，将一个字符串转换成对象，然后再转换成一个字符串进行传输；但是现在，它是有效的，并且是那种可以在以后改进的东西。

# 类型

类型需要一点样板。你首先必须创建一个基类来封装描述你的对象的数据，然后用它来扩展`ObjectGraphType`并描述你想要发布的字段。

比如电影课:

这然后被传递到一个`MovieType`:

`ObjectGraphType`接受其下对象的类型。这将在`Field`的回调函数中输入参数。您使用它来描述可以查询的 GraphQL 对象。它从您返回的属性类型派生字段类型。

您还可以做更复杂的事情来显式地键入属性，并添加底层对象上不存在的成员。

例如，`ResultsType`为`results`数组定义了一个字段。这样我就可以嵌套类型，得到一个带有`List<Movie>`的结果类，并从 GraphQL 查询返回一个`ListGraphType<MovieType>`。

# 问题

查询的定义方式与字段非常相似。只能有一个查询对象，但这并不意味着不能将查询对象分割开来。

为此，我创建了使用`MovieQuery`嵌套查询的`Query`类。

根据文档，这种嵌套的关键是在解析器中返回`new{ }`。

但是，这确实意味着我们最终会得到如下查询:

我不是 100%确定，但我想我喜欢搜索/电影嵌套:耸肩:

# 模式本身

Schema 类很简单:

这里最酷的是`IDependencyResolver`的注射。查看显示服务配置的`Startup.cs`类。我没有在模式中显式地使用它，但是注入它允许我进一步注入依赖关系，比如将`MovieService`注入到`MovieType`类中。

在模式中，我设置的只是一个`Query`对象。这也是我设置突变等的地方，但是为了这个例子的目的，我不会更新任何东西。

在这方面还有很多工作要做，它只涵盖了 GraphQL 和 Movie DB API 可能实现的功能的一个子集，但是它让我继续研究核心技术。这个项目的所有源代码可以在[https://github.com/mlawd/gql-movies](https://github.com/mlawd/gql-movies)找到。

*原载于*[*https://mattlaw . dev*](https://mattlaw.dev/blog/graph-ql-with-net/)*。*
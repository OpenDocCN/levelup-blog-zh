# 如何在中实现全文搜索？带弹性搜索的网络应用

> 原文：<https://levelup.gitconnected.com/how-to-implement-full-text-search-in-net-application-whit-elasticsearch-4a2b0e6c0b59>

## 了解如何使用 NEST 在 C#中使用自定义全文查询读写文档。

在这个简单的教程中，我将提供一个简单的演示来读写 Elasticsearch 的文档，并为 C#应用程序添加一个全文搜索功能。所有的源代码都可以在 [GitHub](https://github.com/zeppaman/csharp-elastisearch-tutorial) 上获得。

# 为什么选择 Elasticsearch？

Elasticsearch 是一个管理各种数据的分布式开源搜索引擎。但是为什么 Elasticsarch 是全文的最佳解决方案呢？

![](img/3bdce24fbf6a466b06d551083d176c23.png)

弹性搜索爱。网

我在 2009 年的论文中开始尝试全文搜索，当时我必须为一个数据推荐系统实现一个搜索算法。这次经历很有教育意义，但从头开始是一场血战。当我第一次有机会使用库时，我尝试使用 Apache Lucene 来实现全文搜索。随着应用程序架构变得复杂(例如，需要共享索引数据的多个服务器)，Solr 提供了一个可伸缩的解决方案。它是一个基于开源 API 的搜索引擎(基于 Lucene)。在使用了这些库之后，我使用了 Elastiserach，它现在是市场领导者。它有一个内部免费版本，可以在云上获得。它可以从简单的安装扩展到巨大的环境。所以问题是:为什么不呢？

# 示例应用程序

为了展示 Elasticsearch 如何工作，我创建了一个示例应用程序，其中实现了两个主要特性:

*   创建索引并向其中添加数据
*   使用全文查询读取数据

示例代码可从 [GitHub](https://github.com/zeppaman/csharp-elastisearch-tutorial) 获得。要运行和测试它，只需下载、编译并执行:

Github 项目的输出示例

控制台应用程序使用库`ConsoleLineParser`以人工方式解析输入。所以我只添加了两个动词属性的类，以及控制台动词和要运行的代码之间的映射。

控制台应用程序路由

基于用户输入的动词(search 或 create ),相应的过程被启动并传递参数。

# 如何写文档

写作部分相当容易。事实上，Elasticsearch 有两种选择。使用底层框架，您可以获得一个包装好的弹性 API 实现。这有助于避免手动绑定和组合 JSON 有效负载。但是，如果想玩数据，NEST 是一个不错的选择。NEST 是高级框架，如果您是 ORM 专家，这应该不会令人惊讶。

您只需为想要保存的文档创建类，并定义如何使用注释保存属性和调用 save API。

我觉得并不太复杂，听起来更像是一个普通的套路。下面是类定义的代码片段。在这个例子中，我在每个包含一行文本的文档中只使用了一个字段。

类别映射。按照惯例，Id 字段是 de 的唯一标识符

下一步是创建索引。在这一步中，我们将索引与类相关联。这可以手动完成，指定存储设置，或使用自动映射。在我们的例子中，“Id”字段被自动映射到文档的惟一标识符。

使用自动映射创建索引

最后，我们有代码中最愚蠢的部分:写数据。因为我们希望将这首诗的所有行保存到许多文档中，每行一行，所以我们只有一次迭代。

使用并行构造索引填充

请注意，在外部系统上使用 API，我们可以使用并行结构来提高性能。

# 如何查询文档

这一部分非常简单明了——至少我希望如此。访问文档的基本方法是使用常规流畅的 LINQ 语法。这是基本的用法，我更喜欢将这个演示集中在搜索全文数据这个不太好记录的用例上。

Elastic 允许使用原始查询来查找字段数据。为此，您可以使用正确的重载搜索方法，配置原始查询:

用于搜索的全文例程

# 带什么回家

Elasticsearch 是领先的搜索引擎解决方案。它为应用程序提供了丰富的功能，如全文搜索或文档索引。它可以用作服务或内部部署。无论哪种情况，为基本用途进行配置都非常简单。

NEST 框架允许我们通过 LINQ 存储和访问 Elasticsearch，就像它是一个简单的数据库一样，这使得一切都非常简单。

对于您希望让最终用户编写查询的复杂场景，您可以使用原始查询并将结果映射到类。

## 资源

*   [git-hub 演示项目](https://github.com/zeppaman/csharp-elastisearch-tutorial)
*   [官方弹性搜索网站](https://www.elastic.co/)

*喜欢这篇文章吗？成为* [*中等会员*](https://daniele-fontani.medium.com/membership) *继续无限制学习。如果你使用下面的链接，我会收到你的一部分会员费，不需要你额外付费。*

看看我下面最受欢迎的文章吧！

*   [从开源到 OpenMind](https://medium.com/better-programming/from-open-source-to-openmind-cb61ce05593a)
*   [如何使用 Kubernetes 部署 web 应用程序](https://medium.com/swlh/how-to-deploy-an-asp-net-application-with-kubernetes-3c00c5fa1c6e)
*   【Kubernetes 到底是什么？
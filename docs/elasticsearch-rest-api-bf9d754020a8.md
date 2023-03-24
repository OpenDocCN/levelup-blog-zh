# Elasticsearch Rest API

> 原文：<https://levelup.gitconnected.com/elasticsearch-rest-api-bf9d754020a8>

![](img/ac69b75b8e19acb7e797ef51ffc1ddf6.png)

Elasticsearch 提供了广泛的 REST APIs 来集成、查询和管理数据。在这篇博客中，我将从大量的 REST APIs 中讨论一些我们可以经常使用的主要 API。

我们可以使用 Elasticsearch REST APIs 做很多事情，比如:

*   检查我们的集群、节点和索引的健康、状态和统计数据等。
*   管理我们的集群、节点、索引数据和元数据。
*   对我们的索引执行 CRUD(创建、读取、更新和删除)和搜索操作。
*   执行高级搜索操作，如分页、排序、过滤、脚本编写、聚合等。

现在我将在这里解释其中的一些:

**_ 卡特彼勒 API**

我们可以通过使用以下 API 来获得集群的健康状况:

```
GET  /_cat/health?v
```

如果我们想获得 Elasticsearch 集群中的节点详细信息:

```
GET /_cat/nodes?v
```

如果我们想列出 Elasticsearch 集群中的指数:

```
GET /_cat/indices?v
```

**创建索引:**

如果我们想创建一个索引。例如，创建一个名为博客的索引

```
PUT /blogs?pretty
```

在上面的表达式中，我们提供了 pretty，它以 pretty 格式显示输出。

**删除指标** :
删除指标:

```
DELETE /customer?pretty
```

**创建文档** :
现在我们已经创建了索引，让我们在索引中创建一个文档。

```
PUT /blogs/technical/1?pretty
{
  "topic": "introduction to Elasticsearch"
}
```

**替换文件**:

我们可以替换文档中的数据:

```
PUT /blogs/technical/1?pretty
{
  "topic": "Elasticsearch Installation"
}
```

在上面的表达式中，我用不同的主题名替换了相同的文档 id。

**更新文件**:

要更新文档，我们需要运行以下表达式:

```
POST /blogs/technical/1/_update?pretty
{
  "doc": { "topic": "introduction to Elasticsearch", "category": "ELK" }
}
```

在上面的表达式中，我用不同的主题名和附加的类别键及其值更新了相同的文档 id。

**删除文档** :
我们可以从索引中删除文档:

```
DELETE /blogs/technical/1?pretty
```

在上面的表达式中，我删除了 id = 1 的文档

**加载数据** :
我们也可以从外部文件加载数据。例如，如果我们有一个 JSON 数据文件，我们可以直接将其推入 Elasticsearch:

```
curl -H "Content-Type: application/json" -XPOST 'localhost:9200/bank/account/_bulk?pretty&refresh' --data-binary "@blogs.json"
```

在上面的表达式中，我将 blogs.json 文件中的数据直接索引到 Elasticsearch 中。

在这篇博客中，我介绍了 Elasticsearch 的一些基本 REST APIs，用于创建索引、删除索引、创建文档、替换和更新文档、删除文档以及从外部文件加载数据。在我的下一篇博客中，我将解释 Elasticsearch 的搜索 API，以及我们如何应用不同类型的搜索。

你可以在推特上关注我:[https://twitter.com/anubioinfo](https://twitter.com/anubioinfo)

**关于 Elastic stack 的其他博客:** [Elastic Search 简介](https://bqstack.com/b/detail/31/Introduction-to-Elasticsearch)
[Elasticsearch 在 Ubuntu 14.04 上的安装和配置](http://bqstack.com/b/detail/52/Elasticsearch-Installation-and-Configuration-on-Ubuntu-14.04)
[使用 Elastic Stack 的日志分析](http://bqstack.com/b/detail/1/Log-analysis-with-Elastic-stack) [Elastic Search Rest API](https://bqstack.com/b/detail/83/Elasticsearch-Rest-API)
[Elastic Search 中数据搜索的基础知识](https://bqstack.com/b/detail/84/Basics-of-Data-Search-in-Elasticsearch)
[Elastic Search Rest API](https://bqstack.com/b/detail/83/Elasticsearch-Rest-API)
[Elastic Search](https://bqstack.com/b/detail/87/Wildcard-and-Boolean-Search-in-Elasticsearch)通配符和布尔搜索

*如果你觉得这篇文章很有意思，那么你可以探索“* [*【掌握基巴纳 6.0】*](https://www.amazon.com/Mastering-Kibana-6-x-Visualize-histograms/dp/1788831039/ref=olp_product_details?_encoding=UTF8&me=)*”、“* [*基巴纳 7 快速入门指南*](https://www.amazon.com/Kibana-Quick-Start-Guide-Elasticsearch/dp/1789804035) *”、“* [*学习基巴纳 7*](https://www.amazon.com/Learning-Kibana-dashboards-visualization-capabilities-ebook/dp/B07V4SQR6T) *”、“* [*Elasticsearch 7 快速入门指南*](https://www.amazon.com/gp/product/1789803322?pf_rd_p=2d1ab404-3b11-4c97-b3db-48081e145e35) *”等书籍，了解更多*

*原载于*[*https://bqstack.com*](https://bqstack.com/b/detail/84/Basics-of-Data-Search-in-Elasticsearch)*。*
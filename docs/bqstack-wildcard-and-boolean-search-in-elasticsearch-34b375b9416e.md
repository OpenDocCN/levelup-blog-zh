# Elasticsearch 中的通配符和布尔搜索

> 原文：<https://levelup.gitconnected.com/bqstack-wildcard-and-boolean-search-in-elasticsearch-34b375b9416e>

![](img/1af8f95c5bf9a01c16ef769e4b405fb2.png)

在我的上一篇博客[Elasticsearch 的数据搜索基础](https://bqstack.com/b/detail/84/Basics-of-Data-Search-in-Elasticsearch)中，我解释了基本的 elastic Search 查询。在这个博客中，我将解释高级搜索查询，我们可以构建更复杂的查询，如布尔查询，通配符查询等。让我们开始创建搜索查询:

**通配符查询:**

使用通配符查询，我们可以在不知道确切拼写的情况下搜索项目。这意味着如果某人不知道某个单词的确切拼写，他/她也可以搜索该单词。请参见下面的示例:

```
GET /blogs/technical/_search
{
  "query": { 
    "wildcard": { 
      "topic": "ki??na" 
    }
  }
}
```

在上面的查询中，我们正在寻找一个以' **ki** '开头并以' na '结尾的单词，这个单词有两个字符，标记为'？'。执行上述搜索后，我们将得到以下结果:

```
{
  "took": 5,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 1,
    "max_score": 1,
    "hits": [
      {
        "_index": "blogs",
        "_type": "technical",
        "_id": "2",
        "_score": 1,
        "_source": {
          "topic": "introduction to Kibana"
        }
      }
    ]
  }
}
```

由于通配符搜索“ki”，结果显示主题“Kibana 简介”与此主题匹配。在我以前关于 Elasticsearch 的博客中，我已经解释了索引文档的步骤，所以如果你想了解 Elasticsearch 的基础知识，请参考它们。如果我们不知道确切的字符长度，那么我们可以运行以下查询:

```
GET /blogs/technical/_search
{
  "query": { 
    "wildcard": { 
      "topic": "k*na" 
    }
  }
}
```

在上面的查询中，我们只知道单词以“k”开头，以“na”结尾，但不知道中间的字符数。即使我们不知道单词的结尾，我们也可以输入起始字符并传递一个' * '，它将获取以给定字符开头的所有单词，而不考虑匹配单词的长度。

布尔查询用于在用“or”、“and”、“not”条件连接结果的基础上搜索结果。比如用其中任何一个条件连接两个条件:

```
"name": "anurag" and "age": "30"
"name": "anurag" or "name": kapil"
```

上面的例子只是一个代表，并不是真正的 Elasticsearch 查询。现在让我们来了解一下，在 Elasticsearch 中，我们如何能够实现相同类型的条件。请参见下面的示例:

```
GET /blogs/technical/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "_type": "technical"
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "topic": "kibana"
          }
        }
      ]
    }
  }
}
```

在上面的查询中，我通过在“query”关键字后传递“bool”键来应用布尔查询，然后在“bool”块下，我提供了两个块“must”和“must_not”。这两个块具有完全不同的含义，因为“must”用于确保块内给定条件的存在，而“must_not”用于确保块内给定条件不存在。现在，在“must”块下，我添加了“match”块来匹配值为“technical”的“_type”键，在“must_not”下，我添加了“match”块来匹配“topic”和“kibana”。

那么会发生什么呢？在上面的查询中，Elasticsearch 将排除所有主题与“Kibana”匹配的文档，并将包括“_type”关键字与值“technical”匹配的文档。上述查询将返回以下结果:

```
{
  "took": 3,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "skipped": 0,
    "failed": 0
  },
  "hits": {
    "total": 1,
    "max_score": 1,
    "hits": [
      {
        "_index": "blogs",
        "_type": "technical",
        "_id": "1",
        "_score": 1,
        "_source": {
          "topic": "introduction to Elasticsearch",
          "category": "ELK"
        }
      }
    ]
  }
}
```

在上面的结果中，我们发现“_type”是“technical”，而“topic”是“Elasticsearch 简介”，没有“Kibana”。再举一个例子:

```
GET /blogs/technical/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "_type": "technical"
          }
        },
        {
          "match": {
            "topic": "kibana"
          }
        }
      ]
    }
  }
}
```

在上面的查询中，我们使用“必须”条件将“_type”匹配为“technical ”,将“topic”匹配为“kibana ”,因此它将返回这两个项目都匹配的文档。再举一个例子:

```
GET /blogs/technical/_search
{
  "query": {
    "bool": {
      "should": [
        {
          "match": {
            "_type": "technical"
          }
        },
        {
          "match": {
            "topic": "kibana"
          }
        }
      ]
    }
  }
}
```

在上面的查询中，我用“should”替换了“must ”,所以现在查询将列出所有“_type”与“technical”匹配或者“topic”与“kibana”匹配的文档。所以基本上在这里我们会得到主题为 Kibana 和 Elasticsearch 的两个文档。如果我用“must_not”替换“should”关键字，那么它将排除这两个条件，我们将不会得到单个文档。

在这篇博客中，我已经尝试解释了 Elasticsearch 的通配符查询和布尔查询。

你可以在推特上关注我:[https://twitter.com/anubioinfo](https://twitter.com/anubioinfo)

**关于 Elastic stack 的其他博客:** [Elastic Search 简介](https://bqstack.com/b/detail/31/Introduction-to-Elasticsearch)
[Elasticsearch 在 Ubuntu 14.04 上的安装和配置](http://bqstack.com/b/detail/52/Elasticsearch-Installation-and-Configuration-on-Ubuntu-14.04)
[使用 Elastic Stack 的日志分析](http://bqstack.com/b/detail/1/Log-analysis-with-Elastic-stack) [Elastic Search Rest API](https://bqstack.com/b/detail/83/Elasticsearch-Rest-API)
[Elastic Search 中数据搜索的基础知识](https://bqstack.com/b/detail/84/Basics-of-Data-Search-in-Elasticsearch)
[Elastic Search Rest API](https://bqstack.com/b/detail/83/Elasticsearch-Rest-API)
[Elastic Search](https://bqstack.com/b/detail/87/Wildcard-and-Boolean-Search-in-Elasticsearch)通配符和布尔搜索

*如果你觉得这篇文章很有意思，那么你可以探索“* [*【掌握基巴纳 6.0】*](https://www.amazon.com/Mastering-Kibana-6-x-Visualize-histograms/dp/1788831039/ref=olp_product_details?_encoding=UTF8&me=)*”、“* [*基巴纳 7 快速入门指南*](https://www.amazon.com/Kibana-Quick-Start-Guide-Elasticsearch/dp/1789804035) *”、“* [*学习基巴纳 7*](https://www.amazon.com/Learning-Kibana-dashboards-visualization-capabilities-ebook/dp/B07V4SQR6T) *”、“* [*Elasticsearch 7 快速入门指南*](https://www.amazon.com/gp/product/1789803322?pf_rd_p=2d1ab404-3b11-4c97-b3db-48081e145e35) *”等书籍，了解更多*

*原载于*[*https://bqstack.com*](https://bqstack.com/b/detail/87/Wildcard-and-Boolean-Search-in-Elasticsearch)*。*

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)
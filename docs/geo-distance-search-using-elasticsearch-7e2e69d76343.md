# 使用弹性搜索进行地理距离搜索

> 原文：<https://levelup.gitconnected.com/geo-distance-search-using-elasticsearch-7e2e69d76343>

![](img/28f81e7d797c5cb870e9a64d14ab65c5.png)

在这篇博客中，我将讲述如何使用 Elasticsearch 测量距离。地理距离测量是许多应用程序的一个非常重要的方面。现在让我们举一个非常简单的例子，当我们搬到任何一个新的地点时，谷歌开始向我们显示最近的景点，或者任何出租车聚合服务使用你的当前位置为你预订最近的出租车。基于地理距离的搜索也是一个非常重要的用例，我们试图找到最近的餐馆、自动取款机、医院或几乎任何东西。

到目前为止，我们已经讨论了基于地理距离的搜索非常重要的不同使用案例，对于一些企业来说，这一点非常重要。但是问题出现了:你实际上如何执行地理搜索？因此，让我们来看看如何在 Elasticsearch 中执行地理搜索。

Elasticsearch 还支持地理数据类型，这些数据可以是地理点和地理形状两种类型。在这个博客中，我们将重点关注地理点，它是一个点的表示，而点只不过是一个具有纬度和经度的地理坐标。尽管 Elasticsearch 接受没有为地理数据显式创建映射的数据，但我们需要显式创建映射，因为这是执行地理查询的第一步。

但是在创建映射之前，让我们了解我们在这个博客中要实现什么。因此，我们的想法是在 Elasticsearch 中创建一个索引，并创建一个 geo-point 类型的字段来保存纬度和经度。然后，我们将添加一些地理数据来表示一个假的位置，如一些停车位置。添加这些停车位置后，我们将传递一个任意的用户当前位置，并尝试找到最近的停车位置。

因此，让我们从索引创建以及地理点类型的位置字段映射开始。我们需要执行以下命令来创建索引和位置映射:

```
PUT parking_locations
{
  "mappings": {
    "properties": {
      "location": {
        "type": "geo_point"
      }
    }
  }
}
```

执行上述命令后，我们可以获得以下成功响应:

```
{
 "acknowledged" : true,
 "shards_acknowledged" : true,
 "index" : "parking_locations"
}
```

我们的停车位置索引与位置字段的映射一起创建。现在让我们添加一些任意位置，以便我们可以在以后应用地理搜索。我们将添加以下数据:

```
Parking 1 28.594817, 77.046608
Parking 2 28.593168, 77.041244
Parking 3 28.601345, 77.026352
Parking 4 28.590606, 77.062959
```

要添加这些数据，我们可以对每个文档执行以下查询:

```
POST parking_locations/_doc
{
 "name": "Parking 1",
 "location": "28.594817, 77.046608",
 "address": "street 1, sec abc"
}
```

执行上述命令后，我们可以得到以下响应:

```
{
  "_index" : "parking_locations",
  "_type" : "_doc",
  "_id" : "uDz1HnIBtlnNM0yOpqq6",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```

我们可以对其他三个文档进行同样的操作，也可以使用批量索引在单个查询中添加所有这些文档。现在我们有了一个包含四个不同停车位置的索引。要验证是否添加了所有文档，我们可以运行以下命令:

```
GET parking_locations/_search
```

上面的命令将列出 parking_locations 索引的所有文档，我们可以验证是否在索引中创建了所有四个文档。现在我们的索引已经为地理查询做好了准备。现在假设我是城市的新用户，当前位置为 **28.580116，77.051673** 。我正在寻找一个停车位置，如何找到最近的停车地址？

举个例子，我想搜索距离我当前位置 2 公里范围内的所有停车场。因此，要获得 2 公里范围内的停车位置，我们需要执行以下查询:

```
GET parking_locations/_search
{
    "query": {
        "bool" : {
            "must" : {
                "match_all" : {}
            },
            "filter" : {
                "geo_distance" : {
                    "distance" : "2km",
                    "location" : "28.580116, 77.051673"
                }
            }
        }
    }
}
```

执行以下命令后，我们可以看到在离我的位置 2 公里以内有三个停车场。请参考执行上述查询后我们可以收到的以下响应:

```
{
  "took" : 8,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 3,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      {
        "_index" : "parking_locations",
        "_type" : "_doc",
        "_id" : "uDz1HnIBtlnNM0yOpqq6",
        "_score" : 1.0,
        "_source" : {
          "name" : "Parking 1",
          "location" : "28.594817, 77.046608",
          "address" : "street 1, sec abc"
        }
      },
      {
        "_index" : "parking_locations",
        "_type" : "_doc",
        "_id" : "uTz3HnIBtlnNM0yOj6qg",
        "_score" : 1.0,
        "_source" : {
          "name" : "Parking 2",
          "location" : "28.593168, 77.041244",
          "address" : "street 3, sec wer"
        }
      },
      {
        "_index" : "parking_locations",
        "_type" : "_doc",
        "_id" : "uzz3HnIBtlnNM0yO_aou",
        "_score" : 1.0,
        "_source" : {
          "name" : "Parking 4",
          "location" : "28.590606, 77.062959",
          "address" : "street 4, sec qer"
        }
      }
    ]
  }
}
```

这样，通过提供不同的距离范围，我们可以从我们的当前位置获取具有地理位置的文档。我们可以将这个 2 KM 范围更改为任何其他范围，并且可以获得不同的文档集。

如有任何疑问，请留下您的意见。你也可以在推特上关注我:[https://twitter.com/anu4udilse](https://twitter.com/anu4udilse)

*如果你觉得这篇文章有趣，那么你可以探索一下“* [***【掌握基巴纳 6.0】***](https://www.amazon.com/Mastering-Kibana-6-x-Visualize-histograms/dp/1788831039/ref=olp_product_details?_encoding=UTF8&me=)*”、* [***基巴纳 7 快速入门指南***](https://www.amazon.com/Kibana-Quick-Start-Guide-Elasticsearch/dp/1789804035) *”、* [***学习基巴纳 7***](https://www.amazon.com/Learning-Kibana-dashboards-visualization-capabilities-ebook/dp/B07V4SQR6T) *”、* [***弹力***](https://www.amazon.com/gp/product/1789803322?pf_rd_p=2d1ab404-3b11-4c97-b3db-48081e145e35)

其他博客:

[Logstash 简介](https://faun.pub/introduction-to-logstash-afe109b886f0)

[基巴纳简介](/introduction-to-kibana-5ce16ae7244b)

[弹性叠加测井分析](https://faun.pub/log-analysis-with-elastic-stack-2a493c113084)

[open API 规范介绍](https://medium.com/swlh/introduction-to-openapi-specification-10c9d6fb0c8a)

[使用弹性搜索进行地理距离搜索](/geo-distance-search-using-elasticsearch-7e2e69d76343)

[将 CSV 数据加载到 Elasticsearch](/load-csv-data-into-elasticsearch-fdb562a7abd9)

[用 Jenkins 配置 SonarQube 扫描仪](https://faun.pub/configure-sonarqube-scanner-with-jenkins-27c23e7758fd)

[配置 Logstash 将 MongoDB 数据发送到 Elasticsearch](/configure-logstash-to-send-mongodb-data-into-elasticsearch-49cc0e9c2a8d)
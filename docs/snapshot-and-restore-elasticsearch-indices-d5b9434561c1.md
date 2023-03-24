# 快照和恢复弹性搜索索引

> 原文：<https://levelup.gitconnected.com/snapshot-and-restore-elasticsearch-indices-d5b9434561c1>

![](img/e9ba9c299553e38c2979d617286d37df.png)

在这篇博客中，我将解释如何为单个索引或多个索引拍摄快照以进行备份，以及如何恢复快照。我们不能通过复制所有节点的数据目录来对 Elasticsearch 进行备份，因为 Elasticsearch 不断改变其数据目录的内容。

Elasticsearch 提供了一个**快照**和**恢复** API。因此，要创建备份，我们需要执行以下操作:

*   我们首先需要确定要存储快照文件的目录位置。假设我想将它存储在“ ***/var/tmp/backups*** ”目录中。
*   我们需要向 Elasticsearch 用户提供目录访问，以便 Elasticsearch 可以写入快照文件。

```
chown -R elasticsearch. /var/tmp/backups
```

*   现在我们需要告诉 Elasticsearch 这是我们的快照目录位置。为此，我们需要在 elasticsearch.yml 文件中添加“ ***repo.path*** ”设置。

```
path.repo: ["/var/tmp/backups"]
```

*   在这里，我们使用本地文件系统目录来存储快照，但同样可以存储在云上。但是在这篇博客中，我们将只关注基于文件系统的快照。
*   我们首先需要创建用于拍摄快照和恢复的存储库。我们可以使用以下表达式创建存储库:

```
PUT _snapshot/anurag_backup
{
 "type": "fs",
 "settings": {
   "location": "/var/tmp/backups"
 }
}
```

*   创建存储库后，我们可以使用下面的表达式获取所有索引的快照:

```
PUT _snapshot/anurag_backup/snapshot_all_indices
```

*   如果我们只想拍摄一个或多个索引的快照，那么我们可以用逗号分隔的形式指定索引名称，请参考下面的表达式:

```
PUT _snapshot/anurag_backup/snapshot_some_indices
{
 "indices": "index1, index2"
}
```

*   如果我们想要查看快照详细信息，则需要运行以下表达式:

```
GET _snapshot/anurag_backup/snapshot_all_indices
```

上面的表达式为我们提供了快照细节，如版本、索引列表、开始时间、结束时间、持续时间等。

*   我们可以通过在快照名称后追加 _restore 端点来恢复快照。

```
POST _snapshot/anurag_backup/snapshot_all_indices/_restore
```

我们可以测试恢复过程，首先创建一些索引，拍摄它们的快照，然后删除这些索引。在此之后，我们可以恢复快照，以获得我们已经删除的索引。我希望你现在可以创建快照，并可以恢复它们，如果有任何疑问，请留下您的评论。

**Elastic Stack 上的其他博客:**

[如何创建 Elasticsearch 集群
Elastic Search 中的桶聚合](https://bqstack.com/b/detail/90/Bucket-Aggregation-in-Elasticsearch)
[Elastic Search 中的度量聚合](https://bqstack.com/b/detail/89/Metrics-Aggregation-in-Elasticsearch)
[配置 Logstash 将 MySQL 数据推入 Elastic Search](https://bqstack.com/b/detail/76/Configure-Logstash-to-push-MySQL-data-into-Elasticsearch)
[Elastic Search 中的通配符和布尔搜索](https://bqstack.com/b/detail/87/Wildcard-and-Boolean-Search-in-Elasticsearch)
[Elastic Search Rest API](https://bqstack.com/b/detail/83/Elasticsearch-Rest-API)
[Elastic Search 中数据搜索的基础知识](https://bqstack.com/b/detail/84/Basics-of-Data-Search-in-Elasticsearch)
[Elastic Search Rest API](https://bqstack.com/b/detail/83/Elasticsearch-Rest-API) [](http://bqstack.com/b/detail/1/Log-analysis-with-Elastic-stack) 

*如果你觉得这篇文章很有意思，那么你可以探索“* [*【掌握基巴纳 6.0】*](https://www.amazon.com/Mastering-Kibana-6-x-Visualize-histograms/dp/1788831039/ref=olp_product_details?_encoding=UTF8&me=)*”、“* [*基巴纳 7 快速入门指南*](https://www.amazon.com/Kibana-Quick-Start-Guide-Elasticsearch/dp/1789804035) *”、“* [*学习基巴纳 7*](https://www.amazon.com/Learning-Kibana-dashboards-visualization-capabilities-ebook/dp/B07V4SQR6T) *”、“* [*Elasticsearch 7 快速入门指南*](https://www.amazon.com/gp/product/1789803322?pf_rd_p=2d1ab404-3b11-4c97-b3db-48081e145e35) *”等书籍，了解更多*

你也可以在推特上关注我:[https://twitter.com/anubioinfo](https://twitter.com/anubioinfo)

*原载于*[*https://bqstack.com*](https://bqstack.com/b/detail/99/Snapshot-and-Restore-Elasticsearch-Indices)*。*
# 配置 Logstash 以将 MongoDB 数据发送到 Elasticsearch

> 原文：<https://levelup.gitconnected.com/configure-logstash-to-send-mongodb-data-into-elasticsearch-49cc0e9c2a8d>

![](img/06fa34a0f494ff698c0310596ac45d28.png)

在这篇博客中，我将解释如何将 MongoDB 数据导入 Elasticsearch。您可能会想，将 MongoDB 数据发送到 Elasticsearch 有什么好处，那么让我向您解释一下您可能希望将 MongoDB 数据推送到 Elasticsearch 的场景。

*   如果您想对数据进行可靠的搜索。
*   您希望在一个中央 Elasticsearch 集群上收集数据以进行分析和可视化。

现在，为了将数据推入 Elasticsearch，我们需要 logstash 的" *logstash-input-mongodb* "输入插件。因此，让我们看看如何安装这个输入插件并配置 Logstash，以便将 MongoDB 数据推入 Elasticsearch。

如果你想知道 Logstash 的基础知识，那么请参考*[*Logstash 简介*](https://bqstack.com/b/detail/102/Introduction-to-Logstash)*博客，在那里我解释了 Logstash 的基础知识。**

**首先，我们必须以 root 用户身份登录:**

```
**sudo su**
```

**然后我们可以转到 Logstash 安装目录(基于您的操作系统):**

```
**cd /usr/share/logstash**
```

**现在我们可以执行以下命令:**

```
**bin/logstash-plugin install logstash-input-mongodb**
```

**我们将得到以下回应:**

```
**Validating logstash-input-mongodb
Installing logstash-input-mongodb
Installation successful**
```

**现在我们需要创建一个配置文件，将 MongoDB 数据作为输入:**

```
**input {
        uri => 'mongodb://usernam:password@anurag-00-00-no6gn.mongodb.net:27017/anurag?ssl=true'
        placeholder_db_dir => '/opt/logstash-mongodb/'
        placeholder_db_name => 'logstash_sqlite.db'
        collection => 'users'
        batch_size => 5000
}
filter {}
output {
        stdout {
                codec => rubydebug
        }
        elasticsearch {
                action => "index"
                index => "mongo_log_data"
                hosts => ["localhost:9200"]
        }
}**
```

**创建配置文件后，我们可以执行以下命令来获取数据**

```
**bin/logstash -f /etc/logstash/conf.d/mongodata.conf**
```

**这个命令将开始从用户集合中获取数据，因为我们已经在配置文件中提到了集合的名称。这还将创建一个名为“mongo_log_data”的 Elasticsearch 索引，并将推送 MongoDB 数据。**

**我们还可以在终端上获得输出，因为我们已经给出了两个输出块，第一个用于使用 stdout 的终端输出，而另一个用于 Elasticsearch 输出。该命令将提取 MongoDB 数据供用户收集，并将数据推入 Elasticsearch 的“ *mongo_log_data* ”索引中。通过这种方式，我们可以从 MongoDB 的任何集合中提取数据，并通过在 Logstash 配置中定义索引名称将其推送到 Elasticsearch。**

***如果你觉得这篇文章很有意思，那么你可以探索“* [*【掌握基巴纳 6.0】*](https://www.amazon.com/Mastering-Kibana-6-x-Visualize-histograms/dp/1788831039/ref=olp_product_details?_encoding=UTF8&me=)*”、“* [*基巴纳 7 快速入门指南*](https://www.amazon.com/Kibana-Quick-Start-Guide-Elasticsearch/dp/1789804035) *”、“* [*学习基巴纳 7*](https://www.amazon.com/Learning-Kibana-dashboards-visualization-capabilities-ebook/dp/B07V4SQR6T) *”、“* [*Elasticsearch 7 快速入门指南*](https://www.amazon.com/gp/product/1789803322?pf_rd_p=2d1ab404-3b11-4c97-b3db-48081e145e35) *”等书籍，了解更多***

**如有任何疑问，请留下您的意见。你也可以在推特上关注我:[https://twitter.com/anu4udilse](https://twitter.com/anu4udilse)**

**其他博客:**

**[Logstash 简介](https://faun.pub/introduction-to-logstash-afe109b886f0)**

**[基巴纳简介](/introduction-to-kibana-5ce16ae7244b)**

**[弹性叠加测井分析](https://faun.pub/log-analysis-with-elastic-stack-2a493c113084)**

**[open API 规范介绍](https://medium.com/swlh/introduction-to-openapi-specification-10c9d6fb0c8a)**

**[使用弹性搜索进行地理距离搜索](/geo-distance-search-using-elasticsearch-7e2e69d76343)**

**[将 CSV 数据加载到 Elasticsearch](/load-csv-data-into-elasticsearch-fdb562a7abd9)**

**[用 Jenkins 配置 SonarQube 扫描仪](https://faun.pub/configure-sonarqube-scanner-with-jenkins-27c23e7758fd)**

**[配置 Logstash 将 MongoDB 数据发送到 Elasticsearch](/configure-logstash-to-send-mongodb-data-into-elasticsearch-49cc0e9c2a8d)**
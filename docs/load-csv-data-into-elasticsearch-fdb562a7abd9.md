# 将 CSV 数据加载到 Elasticsearch

> 原文：<https://levelup.gitconnected.com/load-csv-data-into-elasticsearch-fdb562a7abd9>

![](img/bc420cf1e1388a2113685adccb0f92f7.png)

在这篇博客中，我将解释如何将公开的 CSV 数据导入到 Elasticsearch 中。Elastic Stack 使我们能够轻松地分析任何数据，并帮助我们创建具有关键性能指标的仪表板。医疗保健、犯罪、农业等不同领域的 CSV 数据可在不同的政府网站上获得，我们可以轻松下载。下载 CSV 文件后，我们可以将它放入 Elasticsearch，在数据的基础上进行搜索和分析。我见过很多次，人们不知道如何将这些 CSV 数据导入到 Elasticsearch 中，这就是为什么我在这篇博客中一步一步地解释了这个过程。

数据导入后，您可以使用这些数据进行数据分析或创建不同的仪表板。这里我以 data.gov 网站([***https://catalog . data . gov/dataset/Crimes-2001-to-present-398 a4***](https://catalog.data.gov/dataset/crimes-2001-to-present-398a4))的‘犯罪——2001 年至今’为例。从这个网站上，你可以下载不同类型的 CSV 格式的数据。这个 CSV 文件的大小约为 1.6 GB。

现在，让我们开始将这些数据导入到 Elasticsearch 中。您需要执行以下操作:

*   从"https://catalog.data.gov/dataset 下载 CSV 文件(crimes_2001.csv)？res_format=CSV "网站。该文件包含以下字段:

```
ID | Case Number | Date | Block | IUCR | Primary Type | Description | Location | Description | Arrest | Domestic | Beat | District | Ward | Community Area | FBI Code | X Coordinate | Y Coordinate | Year | Updated On | Latitude | Longitude | Location
```

*   创建一个 Logstash 配置文件，用于读取 CSV 数据并将其写入 Elasticsearch。您需要在 Logstash 配置文件(crimes.conf)中编写以下表达式:

```
input {
    file {
        path => "/home/user/Downloads/crimes_2001.csv"
        start_position => beginning
    }
}
filter {
    csv {
        columns => [
                "ID",
                "Case Number",
                "Date",
                "Block",
                "IUCR",
                "Primary Type",
                "Description",
                "Location Description",
                "Arrest",
                "Domestic",
                "Beat",
                "District",
                "Ward",
                "Community Area",
                "FBI Code",
                "X Coordinate",
                "Y Coordinate",
                "Year",
                "Updated On",
                "Latitude",
                "Longitude",
                "Location"
        ]
        separator => ","
        }
}
output {
    stdout
    {
        codec => rubydebug
    }
     elasticsearch {
        action => "index"
        hosts => ["127.0.0.1:9200"]
        index => "crimes"
    }
}
```

*   创建 Logstash 配置文件后，使用以下命令执行配置:

```
/usr/share/logstash/bin/logstash -f /etc/logstash/conf.d/crimes.conf
```

该命令将创建管道来读取 CSV 数据并将其写入 Elasticsearch。

*   您可以通过在浏览器中列出索引来验证 Elasticsearch 中“犯罪”索引的创建:

```
[http://localhost:9200/_cat/indices?v](http://localhost:9200/_cat/indices?v)
```

*   如果您的索引“犯罪”被列出，您可以在 Elasticsearch 中查看数据:

```
[http://localhost:9200/crimes/_search?pretty](http://localhost:9200/crimes/_search?pretty)
```

在浏览器中或通过 Curl 打开上述 URL 后，您可以看到以下格式的数据:

```
{
        "_index" : "crimes",
        "_type" : "_doc",
        "_id" : "BTTXFWoB75utKkMR2zRC",
        "_score" : 1.0,
        "_source" : {
          "Case Number" : "HY190059",
          "Block" : "066XX S MARSHFIELD AVE",
          "FBI Code" : "26",
          "IUCR" : "4625",
          "X Coordinate" : "1166468",
          "Ward" : "15",
          "Y Coordinate" : "1860715",
          "Beat" : "0725",
          "Location Description" : "STREET",
          "Domestic" : "false",
          "Community Area" : "67",
          "Updated On" : "02/10/2018 03:50:01 PM",
          "Primary Type" : "OTHER OFFENSE",
          "Year of Crime" : "2015",
          "host" : "KELLGGNLPTP0305",
          "Date" : "03/18/2015 11:00:00 PM",
          "path" : "/home/user/Downloads/crimes.csv",
          "Arrest" : "true",
          "Longitude" : "-87.665319468",
          "id" : "10000094",
          "Description" : "PAROLE VIOLATION",
          "@timestamp" : "2019-04-13T08:37:05.351Z",
          "District" : "007",
          "Latitude" : "41.773371528",
          "@version" : "1"
        }
}
```

以上来自“犯罪”索引的 Elasticsearch 文档表示 CSV 文件中的单个记录。

通过这种方式，您可以将任何 CSV 数据推入 Elasticsearch，然后可以使用这些数据执行搜索、分析或创建仪表板。如果您有任何疑问，请评论。

你可以在推特上关注我:[https://twitter.com/anu4udilse](https://twitter.com/anu4udilse)

**Learning Kibana 7** 本书荣登最佳新弹性搜索书籍

![](img/e9e34d3e2f02455225f888e14a19a28c.png)

我很高兴地宣布，我的书《学习 Kibana 7:用 Kibana 的数据可视化功能构建强大的弹性仪表板，第二版》获得了 https://bookauthority.org/books/new-elasticsearch-books?[book authority 的最佳新弹性搜索书籍](https://bookauthority.org/books/new-elasticsearch-books?t=11vp86&s=award&book=1838550364) :
[t = 11vp 86&s = award&book = 1838550364](https://bookauthority.org/books/new-elasticsearch-books?t=11vp86&s=award&book=1838550364)
book authority 收集并排名世界上最好的书籍，能得到这种认可是莫大的荣幸。谢谢大家的支持！这本书在亚马逊上[有售。](https://www.amazon.com/dp/1838550364?tag=uuid10-20)

*如果你觉得这篇文章很有趣，那么你可以探索一下“* [*掌握基巴纳 6.0*](https://www.amazon.com/Mastering-Kibana-6-x-Visualize-histograms/dp/1788831039/ref=olp_product_details?_encoding=UTF8&me=) *”、“* [*基巴纳 7 快速入门指南*](https://www.amazon.com/Kibana-Quick-Start-Guide-Elasticsearch/dp/1789804035) *”、“* [*学习基巴纳 7*](https://www.amazon.com/Learning-Kibana-dashboards-visualization-capabilities-ebook/dp/B07V4SQR6T) *”和“* [*弹性搜索 7 快速入门指南*](https://www.amazon.com/gp/product/1789803322?pf_rd_p=2d1ab404-3b11-4c97-b3db-48081e145e35)

*其他博客:*

*[Logstash 简介](https://faun.pub/introduction-to-logstash-afe109b886f0)*

*[基巴纳简介](/introduction-to-kibana-5ce16ae7244b)*

*[弹性叠加测井分析](https://faun.pub/log-analysis-with-elastic-stack-2a493c113084)*

*[open API 规范介绍](https://medium.com/swlh/introduction-to-openapi-specification-10c9d6fb0c8a)*

*[使用弹性搜索进行地理距离搜索](/geo-distance-search-using-elasticsearch-7e2e69d76343)*

*[将 CSV 数据加载到 Elasticsearch](/load-csv-data-into-elasticsearch-fdb562a7abd9)*

*[用 Jenkins 配置 SonarQube 扫描仪](https://faun.pub/configure-sonarqube-scanner-with-jenkins-27c23e7758fd)*

*[配置 Logstash 将 MongoDB 数据发送到 Elasticsearch](/configure-logstash-to-send-mongodb-data-into-elasticsearch-49cc0e9c2a8d)*
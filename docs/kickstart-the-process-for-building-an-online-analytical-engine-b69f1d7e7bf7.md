# 启动构建在线分析引擎的过程

> 原文：<https://levelup.gitconnected.com/kickstart-the-process-for-building-an-online-analytical-engine-b69f1d7e7bf7>

## 了解它是如何工作的，以及我们有哪些选择是第一步

![](img/2eebd5b0d23593c988c4fc08cde7b9ce.png)

分析的使用不再局限于财大气粗的大公司。它现在已经普及，59%的企业在某种程度上使用了分析。公司正以多种方式利用这项技术。根据德勤(T2)的一项调查，49%的受访者表示，分析帮助他们做出更好的决策，16%的人表示，分析可以更好地实现关键的战略计划，10%的人表示，分析可以帮助他们改善与客户和业务合作伙伴的关系。

为了充分利用这些优势，您需要知道如何从您的数据中获取最大价值。这就是超快速分析引擎的用武之地。这些是在选择最佳方法时应该考虑的因素:

→ **摄取时间**:数据需要多长时间准备好以供查询

→ **查询延迟**:UI 应该多快显示结果

→ **存储开销**:将存储多少数据

→ **数据格式**:数据有预定义的模式还是没有模式

有多种开源产品就是为此而构建的。在大多数情况下，它们适用于正常的分析目的。我想到的一些是:

*   德鲁伊和比诺在工作方式上非常相似，因此我把他们归为一类。原始数据作为片段被接收、索引和存储。[德鲁伊](https://druid.apache.org/docs/latest/design/index.html)对其所有用例使用反转的[位图索引](/insights-into-indexing-using-bitmap-index-c28a3db1ad97)，而[皮诺](https://docs.pinot.apache.org/)拥有基于数据的多种索引技术。索引数据被写成在分配的时间量之后不可变的段。段类似于分区。
    查询引擎获取索引数据并检索结果。
*   **麒麟**
    [麒麟](http://kylin.apache.org/)更是典型的 OLAP 发动机。摄取的数据存储为聚合多维数据集。它使用一个[立方体规划器](https://tech.ebayinc.com/engineering/cube-planner-build-an-apache-kylin-olap-cube-efficiently-and-intelligently/)来高效智能地构建立方体。然后，这些立方体被写入纵列拼花文件格式(在旧版本中，数据被写入 HBASE)。对于在线查询，它仍然使用与 Druid 或 Pinot 类似的方法，将数据编入索引并保存在内存中。经过一段分配的时间后，立方体由此生成并转储到拼花文件中。

这两种系统各有利弊。这两种解决方案都非常快，尽管 Apache Druid 更成熟、更常用。在开始任何工作之前，通读他们的文档是一个好主意。

无论出于什么原因，如果您计划构建一个定制的分析引擎，这里有一个典型的分析引擎的三个主要支柱

# 事件流平台(消息系统)

最常见的消息系统是 Apache Kafka。如果你对 Kafka 不熟悉，它是一个可扩展的、容错的、发布-订阅消息系统，使你能够构建分布式应用程序，并为 LinkedIn、Twitter、Airbnb 等网络规模的互联网公司提供支持。

Kafka 是一个分布式系统，由**服务器**和**客户端**组成，它们通过高性能 [TCP 网络协议](https://kafka.apache.org/protocol.html)进行通信。它可以部署在本地和云环境中的裸机硬件、虚拟机和容器上。

**服务器** : Kafka 作为一个或多个服务器的集群运行，这些服务器可以跨越多个数据中心或云区域。其中一些服务器构成了存储层，称为代理。为了让您实现任务关键的用例，Kafka 集群具有高度的可伸缩性和容错性:如果它的任何一个服务器出现故障，其他服务器将接管它们的工作，以确保连续运行而不会丢失任何数据。

**客户端**:它们允许你编写分布式应用和微服务，并行、大规模、容错地读取、写入和处理事件流，即使在网络问题或机器故障的情况下。Kafka 附带了一些这样的客户端，由 Kafka 社区提供的[几十个客户端](https://cwiki.apache.org/confluence/display/KAFKA/Clients)增强:客户端可用于 Java 和 Scala，包括更高级的 [Kafka Streams](https://kafka.apache.org/documentation/streams/) 库，用于 Go、Python、C/C++和许多其他编程语言以及 REST APIs。

**其他类似系统**

有许多消息传递系统都有消息队列、分布式消息传递和高性能事件流系统的用例。基于来自 [confluent](https://www.confluent.io/kafka-vs-pulsar/) 的统计数据，以下是 Apache Kafka、Apache Pulsar 和 RabbitMQ 的对比

→ Kafka 提供了所有系统中最高的吞吐量，根据流行的 OpenMessaging 基准测试，其写入速度比 RabbitMQ 快 15 倍，比 Pulsar 快 2 倍*

→ Kafka 以更高的吞吐量提供了[最低延迟](https://www.confluent.io/blog/kafka-fastest-messaging-system/)(p99 时 5 毫秒)，同时还提供了强大的耐用性和高可用性*

→ RabbitMQ 可以实现比 Kafka 更低的端到端延迟，但吞吐量要低得多(Kafka 为 30K 消息/秒，而 Kafka 为 200K 消息/秒)，之后延迟会显著降低。

# 流式传输、聚合和数据分析引擎

Apache Spark 是大数据领域使用最多的分析引擎。Spark 的快速处理速度使其非常适合数据清理、数据争论和 ETL。它有一个先进的 DAG 执行引擎，支持非循环数据流和内存计算，这有助于运行程序的速度比 Hadoop MapReduce 快 100 倍。

随着[结构化流](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)(在 Spark 2.x 中)的增加，更高级别的 API 本质上允许开发人员创建无限的流数据帧和数据集。它还解决了用户在早期框架中遇到的一些非常实际的问题，特别是关于处理事件时间聚合和消息延迟交付的问题。对结构化流的所有查询都经过 Catalyst 查询优化器，甚至可以以交互方式运行，允许用户对实时流数据执行 SQL 查询。

Spark 提供了 3 个选项来聚集和操作数据。如何选择以及选择什么是基于您的用例的。

**RDD**

*   您希望对数据集进行低级转换、操作和控制
*   您的数据是非结构化的，例如媒体流或文本流
*   您希望使用函数式编程结构而不是特定于领域的表达式来操作数据

**数据集或数据帧**

*   如果您想要丰富的语义、高级抽象和特定于领域的 API，请使用 DataFrame 或 Dataset。
*   如果您的处理需要高级表达式、过滤器、映射、聚合、平均值、求和、SQL 查询、列访问以及在半结构化数据上使用 lambda 函数，请使用 DataFrame 或 Dataset。
*   如果您想在编译时获得更高程度的类型安全，想要类型化的 JVM 对象，利用 Catalyst 优化，并受益于钨的高效代码生成，请使用 Dataset。
*   如果您想要跨 Spark 库统一和简化 API，请使用 DataFrame 或 Dataset

应该根据您的使用情况和舒适度来决定使用哪一种。

# 数据存储

选择数据存储是一项棘手的工作，不是因为我们有众多的选项，而是因为这些选项是如此多样。对于一个用例来说完美的数据存储对于另一个用例来说可能会彻底失败。

通过与大量客户和用例打交道，我发现要考虑的最重要的标准是:

*   **卷—** 需要存储和查询多少数据
*   **查询模式** —需要运行的查询类型
*   **延迟** —检索结果需要多长时间

如果您的数据量在 GBs 级别，选择 Postgres 这样的关系数据库可能是一个非常好的选择。您拥有的数据越多，非关系数据库就越有用，因为它不会对传入的数据施加限制，使您可以更快地编写。

查询模式是在决定数据存储时起着非常关键作用的另一个因素。如果您希望查询运行大量的“连接”和“分组”操作，像 Cassandra 这样的 NoSQL 数据库可能不是一个很好的选择。然而，如果您的查询本质上是选择性的(大海捞针)，那么 Cassandra 可能就在其中。

另一个重要因素是查询的延迟。只有少数商店可以提供亚秒级延迟的结果。像 Redis 这样的键值存储或者像 Cassandra 这样的非 SQL 数据库可能是一个很好的选择。如果您可以对延迟宽容一些，基于 Hadoop 的解决方案也可以工作。

我见过的两个最常见的数据存储是:

1.  **HDFS 与 Hive:** 这是最常见的数据存储，许多团队都依赖它来满足他们的分析需求。使用的理想数据格式是[拼花地板](https://databricks.com/session_eu19/the-parquet-format-and-performance-optimization-opportunities)。最适合的查询引擎是[。Presto 是一个纯基于内存的架构，设计用于对任何大小的数据进行快速分析查询。这项工作可能需要使用 Spark 进行一些数据操作来优化查询检索。这种方法在数据模式和查询类型方面更灵活，但在延迟方面可能不是最有效的。](https://blog.treasuredata.com/blog/2015/03/12/presto-sql-on-hadoop/)
2.  Cassandra :另一个用于分析目的的流行数据存储是 Cassandra。它是一个高度可伸缩、高性能的分布式数据库。然而，采用 Cassandra 作为一个单一的，一刀切的数据库有几个缺点。分区/分布式数据存储模型使得在关系数据库中更直接的某些类型的查询或数据分析变得困难(通常效率很低)。此外，需要在数据聚合层做大量的准备工作，以获得低延迟的查询功能。

# 参考

[](https://medium.com/swlh/insights-into-parquet-storage-ac7e46b94ffe) [## 对拼花储物的洞察

### 从事大数据工作的大多数人都会听说过 parquet，以及它是如何针对存储等进行优化的。在这里我将…

medium.com](https://medium.com/swlh/insights-into-parquet-storage-ac7e46b94ffe) [](https://aws.amazon.com/blogs/startups/picking-the-right-data-store-for-your-workload/) [## 为您的工作负载选择合适的数据存储|亚马逊网络服务

### 选择的暴政为什么 AWS 有这么多的数据存储选项？哪个适合我？在这个博客系列中，我…

aws.amazon.com](https://aws.amazon.com/blogs/startups/picking-the-right-data-store-for-your-workload/)
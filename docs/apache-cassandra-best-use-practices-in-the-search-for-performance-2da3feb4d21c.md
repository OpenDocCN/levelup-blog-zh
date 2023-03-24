# Apache Cassandra:搜索性能的最佳实践

> 原文：<https://levelup.gitconnected.com/apache-cassandra-best-use-practices-in-the-search-for-performance-2da3feb4d21c>

![](img/eab9f7c14e3610b36c4f87dca11c7ab4.png)

由 [Perchek Industrie](https://unsplash.com/@perchek_industrie?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 提高 Apache Cassandra 的性能，最佳实践，等等！:)

Cassandra 是一个 NoSQL 数据库，开发它是为了确保数据的快速可伸缩性和高可用性，它是开源的，主要由 Apache 基金会及其社区维护。
其主要特点是:

*   “去中心化”:所有节点具有相同的功能。
*   “弹性”:多个节点复制数据；它还支持多个数据中心的复制。
*   “可伸缩性”:向集群添加新节点速度快，不影响系统性能；现在有很多系统使用 Cassandra 来处理成千上万的节点。

根据阿帕奇的说法，我们可以把卡珊德拉看作是:

> “当您需要可伸缩性和高可用性而又不牺牲性能时，Apache Cassandra 数据库是正确的选择。[线性可扩展性](http://techblog.netflix.com/2011/11/benchmarking-cassandra-scalability-on.html)和商用硬件或云基础设施上成熟的容错能力使其成为关键任务数据的完美平台。Cassandra 对跨多个数据中心复制的支持是同类最佳的，为您的用户提供了更低的延迟，并让您高枕无忧，因为您知道自己能够经受住区域性中断。”

 [## 阿帕奇卡桑德拉

### 当您需要可伸缩性和高可用性时，Apache Cassandra 数据库是正确的选择

cassandra.apache.org](https://cassandra.apache.org/) 

本出版物总结了一些提高 Cassandra 性能和表现的可能性和良好做法，以及 Cassandra 的替代方案。

**好习惯:**

像任何 NoSQL 数据库一样，我们必须考虑到，根据 www.howtouselinux.com 网站[的](http://www.howtouselinux.com)我们可以看看:

*   Cassandra 前面没有负载平衡器:
    Cassandra 已经在节点之间分发数据，并且大多数 Cassandra 驱动程序都有一个集成的算法来正确地路由请求。添加负载平衡器会引入额外的一层，可能会破坏驱动器使用的智能算法，并引入单点故障，而这在 Cassandra 世界中是不存在的。
*   尽可能避免二级索引。在 Cassandra 中，二级索引是本地的。因此，如果访问多个节点，使用辅助索引的查询可能会导致集群中的性能问题。在基数较低的列中偶尔使用二级索引(例如，有几百个唯一的州名的列)是正常的，但是一般情况下，要尽量避免使用二级索引。请不要将它用于高基数列。
*   避免全表检查。
    Cassandra 将分区分布在集群中的所有节点上。在大型集群中，它可以有数十亿条线路。因此，对包含所有记录的表进行完整扫描会导致网络瓶颈和极大的堆压力。相反，调整您的数据模型，这样您就不必完全扫描表。
*   将分区大小保持在 100 MB 以内
    最大实际限制是每个分区 20 亿个单元。但是，您应该尽量将分区的大小保持在 100 MB 以内。巨大的分区会对堆栈造成相当大的压力，并导致压缩缓慢。
*   不要使用批处理进行批量上传。
    当您想要保持一组非规范化的表同步时，应该使用批处理。不要使用 batch 进行批量装载(尤其是当涉及多个分区键时)，因为这会给协调节点带来很大的压力，并对性能造成不利影响。
*   尽可能利用准备好的语句
    在多次运行具有相同结构的查询时使用准备好的语句。Cassandra 将分析查询字符串并缓存结果。下次需要查询时，可以将变量与缓存中准备好的语句链接起来。这有助于提高性能，绕过每个查询的分析阶段。
*   避免对多个分区使用具有多个值的 IN 子句查询。
    对多个分区使用大量的‘in’子句查询给协调节点带来了巨大的压力。此外，如果协调节点由于负载过大而无法处理该查询，则一切都必须重复。相反，我更喜欢在这些情况下使用单独的查询，以避免单点故障和协调节点过热。
*   不要在多数据中心环境中使用 SimpleReplicationStrategy
    简单复制策略用于单数据中心环境。将复制副本顺时针放置在下一个节点上，不考虑机架或数据中心位置。因此，它不应用于具有多个数据中心的环境。
*   我更喜欢在有多个数据中心的环境中使用本地一致性级别。
    如果可能并且用例允许，在具有多个数据中心的环境中，始终优先使用本地一致性级别。借助本地一致性级别，本地副本将被咨询以准备响应，从而避免数据中心之间的通信延迟。
*   避免作为数据模型排队。这些类型的数据模型产生了许多墓碑。扫描多个墓碑来寻找匹配的切片查询并不理想。这会导致延迟问题，还会增加堆的压力，因为它会扫描大量的捕获数据以找到少量所需的数据。
*   避免在写模式之前阅读。
    读取非缓存数据可能需要多次磁盘访问。因此，这会大大降低写入的吞吐量，从而产生顺序 I/O。
*   尽量将集群中的表总数保持在合理的范围内。
    一个集群中的许多表会导致过多的堆和内存压力。因此，尽量将一个簇中的表数量保持在合理的范围内。由于与表格相关的几个因素，很难找到一个好的数字。尽管如此，从许多测试来看，已经确定应该尽量将表的数量保持在 200 以内(警告级别)，并且绝对不应该超过 500 个表(失败级别)。
*   如果有足够的 i /o 可用，则选择分级压缩策略来读取繁重的工作负载。
    假设几乎一致的线，分级压缩策略确保单个表满足所有读数的 90%。因此，它非常适合大量阅读和延迟敏感的用例。当然，它会导致更多的压缩，在压缩过程中需要更多的 i/o。
*   注意:在创建自己的表时，使用分级压缩策略总是一个好主意。一旦创建了表本身，以后要更改它就有点棘手了。尽管以后可以更改，但要小心，因为它会使节点过载大量 I/O。
*   将多个分区的批处理大小保持在 5 KB 以内
    大型批处理会导致显著的性能损失，使协调节点过载。因此，在使用多个分区批处理时，尽量将批处理大小保持在每批 5 KB。

以上是我们使用 Cassandra 要注意的几点。你知道另一个吗？你同意提出的观点吗？

**看性能:**

日常使用 Cassandra 的专业人士最常见的抱怨与他们的表现有关。为了解决这个问题，我们可以尽可能采用 Apache Spark 以瘫痪的方式进行查询，注意:

*   总是尽量减少通过网络发送的数据量。
    尽早过滤数据，不处理以后要丢弃的数据。
*   定义正确的分区数量。
*   避免数据失真。
*   在高成本或多重连接之前配置细分。
*   在 Cassandra 中录制之前，永远不要尝试在存储中录制之前进行分区，使用 Spark Cassandra 连接器，这将会以一种更具表演性的方式自动完成。
*   定义正确的执行器、内核和内存数量。
*   研究并定义 Spark 将使用的序列化。
*   无论何时使用 SparkSQL，都要使用 Spark Catalyst！
*   使用正确的文件格式。Avro 用于行级操作，Parquet 或 ORC 用于基于列的操作。

作为提示，请始终测试这些和其他优化，并尽可能使用一个平等的环境，一个生产的克隆，作为一个实验室！:)

如果这些都没有产生您期望或需要的差异，那么是时候考虑采用另一个数据库了，比如…

![](img/22a36ecbadad2cb04bab80fb73f440e0.png)

[https://www.scylladb.com/](https://www.scylladb.com/)

# Scylladb

“实时大数据数据库”

“实时大数据数据库”
Scylladb 使用 C ++语言实现，具有 Apache Cassandra 的 Cassandra 查询语言(CQL)接口，具有相同的横向扩展和容错特性，除了由算法组成以更好地利用计算资源，即它将始终使用 100%的可用资源以拥有其令人难以置信的性能，除了自动进行数据压缩和压缩。

有了这个数据库，我们有可能集成:

*   [Apache Spark](https://www.scylladb.com/product/technology/scylla-and-apache-spark/) ，数据处理框架；
*   [阿帕奇卡夫卡](https://www.scylladb.com/2020/02/18/introducing-the-kafka-scylla-connector/)，事件流平台；
*   [Datadog](https://www.datadoghq.com/) ，一个监控云解决方案的服务；
*   [Akka](https://akka.io/) ，极富弹性的 Scala 框架“消息驱动”；
*   [Presto](https://prestodb.io/) ，SQL 查询引擎，
*   [Apache Parquet](https://parquet.apache.org/) ，一种流行的“柱状存储”格式

此外，我们设法与 Scylladb 一起使用一些数据存储，例如:

*   [JanusGraph](https://tinkerpop.apache.org/gremlin.html)
*   [KairosDB](http://kairosdb.github.io/)
*   [蝾螈](http://opennms.github.io/newts/)
*   [IOTA 编年史](https://docs.iota.org/docs/chronicle/1.1/overview)
*   [估价员](https://www.scylladb.com/2019/02/20/valustor-a-memcached-alternative-built-on-scylla/)

比较 ScyllaDB 和 Cassandra，我们可以注意到:

*   它占据的空间比卡珊德拉少 10 倍。
*   更少的延迟峰值。
*   在 99%的情况下，延迟提高了 11 倍。
*   在相同环境下(AWS EC2 裸机)比 Cassandra 便宜 2.5
*   有可能在一个容器环境(Kubernetes、Openshift、Rancher、clouds……)中部署一个本地运营商。

你不相信，所以看看基准:

[](https://go.scylladb.com/scylla-vs-cassandra-i3.metal-benchmark-offer.html) [## AWS 裸机版的 Scylla 与 AWS 标准版的 Cassandra

### 了解 Scylla 如何从 AWS i3 裸机实例中获得最高性能，该基准测试比较了 4…

go.scylladb.com](https://go.scylladb.com/scylla-vs-cassandra-i3.metal-benchmark-offer.html) [](https://go.scylladb.com/scylla-cloud-vs-dynamoDB-benchmark-offer.html) [## Scylla vs DynamoDB -数据库基准测试

### 锡拉和 DynamoDB 关系密切。两者都是基于迪纳摩论文，定义了一个新的 NoSQL 类…

go.scylladb.com](https://go.scylladb.com/scylla-cloud-vs-dynamoDB-benchmark-offer.html) [](https://go.scylladb.com/scylla-cloud-vs-bigtable-benchmark-offer.html) [## Scylla vs BigTable -数据库基准测试

### Scylla 和 Bigtable 在同一个家谱里。了解相对性能特征很重要…

go.scylladb.com](https://go.scylladb.com/scylla-cloud-vs-bigtable-benchmark-offer.html) 

这是一篇关于 Apache Cassandra、良好的使用实践、一些提高性能的方法和一个替代方案 Scylladb 的文章，并对它们进行了比较。

**参考文献:**

[](https://pplware.sapo.pt/linux/apache-cassandra-tecnologia-nosql-alta-disponibilidade/) [## 阿帕奇·卡珊德拉:NoSQL 技术大学

### 阿帕切·卡珊德拉的 NoSQL 基地管理系统

pplware.sapo.pt](https://pplware.sapo.pt/linux/apache-cassandra-tecnologia-nosql-alta-disponibilidade/) [](https://www.casadocodigo.com.br/products/livro-apache-cassandra) [## 利弗罗-德阿帕奇-卡珊德拉-卡萨多科迪戈

### 内斯特·利弗罗，奥特·维奥·桑塔纳·阿布多或卡珊德拉，对 Java 的概念和应用非常熟悉。Após uma 简介 aos…

www.casadocodigo.com.br](https://www.casadocodigo.com.br/products/livro-apache-cassandra) [](https://www.scylladb.com/) [## 主页

### 实时 Big&nbspData 数据库的纵向扩展性能为每个节点 1，000，000 次操作，横向扩展至数百个节点…

www.scylladb.com](https://www.scylladb.com/)  [## 14 Apache Cassandra 针对开发人员和应用程序团队的最佳实践——howtouselinux

### 2020-11-06t 15:13:46.555 z-cassandra 前面没有负载平衡器 Cassandra 跨节点分发数据，并且…

www.howtouselinux.com](https://www.howtouselinux.com/post/14-cassandra-best-practices-for-developers-application-teams) [](https://dzone.com/articles/best-practices-for-cassandra-data-modeling) [## Cassandra 数据建模的最佳实践- DZone 数据库

### 不熟悉 NoSQL 数据库的人倾向于将 NoSql 与关系数据库联系起来，但是它们之间有很大的区别

dzone.com](https://dzone.com/articles/best-practices-for-cassandra-data-modeling) [](https://itnext.io/spark-cassandra-all-you-need-to-know-tips-and-optimizations-d3810cc0bd4e) [## Spark + Cassandra 所有你需要知道的:技巧和优化

### 本文的目标是理解 Spark 和 Cassandra 的内部机制，以编写高效的代码。

itnext.io](https://itnext.io/spark-cassandra-all-you-need-to-know-tips-and-optimizations-d3810cc0bd4e) [](https://luminousmen.com/post/spark-tips-partition-tuning) [## 火花提示。分区调整

### 世界上有许多不同的工具，每一种都可以解决一系列问题。他们中的许多人是如何判断…

luminousmen.com](https://luminousmen.com/post/spark-tips-partition-tuning) 

#感谢你的阅读！:)
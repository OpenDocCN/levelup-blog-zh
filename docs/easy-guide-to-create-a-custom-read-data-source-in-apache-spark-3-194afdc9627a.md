# 在 Apache Spark 3 中创建自定义读取数据源的简单指南

> 原文：<https://levelup.gitconnected.com/easy-guide-to-create-a-custom-read-data-source-in-apache-spark-3-194afdc9627a>

## 使用 Apache Spark 3.0.x 编写具有位置感知和多分区支持的自定义读取数据源的分步指南

![](img/014c56b839048dfcf892ef2286b5eda4.png)

安德里克·朗菲尔德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Apache Spark 是一个非常强大的分布式执行引擎。当我们通读它的文档和例子时，即使它有所有复杂的功能，我们发现它相对容易使用。当我们深入研究它并试图解决现实生活中的用例时，尽管它的特性打包了功能，我们还是需要编写一些定制。Apache Spark 真正突出的一点是它内在支持这些定制的能力。Spark 不仅提供了不同的接口来插入这种定制，而且还不断改进这些接口来克服限制。

# **我们会学到什么？**

*   什么是数据源 API 及其重要性？
*   我们将讨论在 Spark 3.0.x 中实现读取数据源所需的所有接口
*   这些接口的重要性及其在集群上的部署，驱动程序与执行器
*   然后，我们将实现这些接口，并创建一个简单的 CSV 读取数据源
*   最后，我们将学习如何编写一个稍微复杂的位置敏感的多分区读数据源。

因此，不浪费任何时间，让我们开始吧。

# 数据来源是什么？

数据源是 Apache Spark 中非常流行的功能。许多开发人员广泛使用它来连接第三方应用程序和 Apache Spark。从 2.4.x 版本开始，它有两个变体，V1 和 V2。

V1 在 v 2.3.x 之前可用。V2 API 在 2.3.0 中引入，并在 2.4.0 中修改。在 3.0.0 中，Spark 在 v2 APIs 中引入了重大变化，但是，为了向后兼容，v1 保持不变。

在之前的一篇[文章](https://medium.com/@aamargajbhiye/speed-up-apache-spark-job-execution-using-a-custom-data-source-fd791a0fa4b0)中，我讨论了如何使用 v2 数据源 API 创建定制数据源。在本文中，我们将了解如何为 Apache Spark 3.0.x 创建读数据源

# 数据源接口

*   表格提供者
*   桌子
*   扫描生成器
*   扫描
*   一批
*   输入分区
*   PartitionReaderFactory
*   分区阅读器

现在，让我们详细讨论其中的每一项及其用途。

## 表格提供者

在 Spark 2.4.x 中，数据源 API 中的主要接口是`DatasourceV2,`实现它或它的一个或其他专门化(如`ReadSupport`或`WriteSupport`)所需的所有定制数据源。这个接口在 3.0.x 中被移除了，取而代之的是引入了一个新的 [TableProvider](http://spark.apache.org/docs/3.0.0/api/java/org/apache/spark/sql/connector/catalog/TableProvider.html) 接口。它是所有不需要支持 DDL 的定制数据源的基本接口。此接口的实现应该有一个 0 参数的公共构造函数。让我们为我们的定制 CSV 数据源实现它，看看它看起来如何。

我们的数据源可以接受来自外部源的模式，比如通过`DataFrameReader.read()`的用户指定模式，我们从`supportsExternalMetadata`返回 true，不会实现`inferschema`。

## 桌子

该接口定义了表示结构化数据的单个逻辑实体。它可以是基于文件系统的数据源的文件/文件夹，Kafka 的主题，或者 JDBC 数据源的表。它可以与`SupportsRead`和`SupportsWrite`混合使用，以增加读写能力。在我们的例子中，CSV 文件是一个表格。`capabilities` API 返回表的所有功能。对于我们简单的 read 实现，让我们只返回 BATCH_READ。

## 扫描

对于我们简单的读取数据源，我们返回了 [**BATCH_READ**](http://spark.apache.org/docs/3.0.0/api/java/org/apache/spark/sql/connector/catalog/TableCapability.html#BATCH_READ) 。其他读取能力有`MICRO_BATCH_READ` 和`CONTINUOUS_READ` **。** SupportsRead 接口有返回 Scan 的 newScanBuilder() API。Scan 表示数据源扫描的逻辑计划，可以是批处理、微批处理流或连续流。对于这个例子，我们将实现`toBatch()` API，因为我们已经在表功能中添加了 BATCH_READ 作为功能。

## 一批

批处理代表一个物理计划。在运行时，逻辑表扫描被转换为物理扫描。这是计划数据源分区的界面。它定义了一个工厂，该工厂被发送给执行器，为每个`InputPartition`创建一个`PartitionReader`。

## 输入分区和分区阅读器

在使用所有已配置的选项后，应用运行时优化，如下推过滤器、列修剪等，创建物理规划，即`Batch`。这涉及到创建`InputPartition`和`PartitionReaderFactory` **。他们被部署在执行者身上。在每个执行器上，对于每个分区，使用 PartitionReaderFactory **创建一个 PartitionReader 实例。** `PartitionReader` 从数据存储器中读取数据，使用给定的 **s** chema **将其转换为`InternalRow`。****

所以，就这么定了。我们已经成功地定义了一个具有读取能力的简单定制数据源。这个读取数据源的完整源代码在这里分享[。](https://github.com/aamargajbhiye/big-data-projects/tree/master/Datasource%20spark3/src/main/java/com/bugdbug/customsource/csv)

现在，让我们利用所有这些知识，定义一个位置感知的多分区数据源。

# 具有首选位置的多分区数据源

我们已经定义了一个基本的 CSV 数据源，它支持外部元数据，并且只有一个分区。现在，让我们看看如何定义一个带有多个分区的定制 JDBC 数据源，而不需要外部模式和对分区的首选位置支持。

我们需要来自用户输入的三样东西。

1.  许多分区
2.  应该在其上对数据进行分区的表列。
3.  每个分区的位置信息

JDBC 可以推断出一个模式，因此`TableProvider`实现将从`inferSchema` API 实现返回模式。

`ReadSupport`、`ScanBuilder`和`Scan`接口的实现是相似的。`Batch`规划分区的实施会有所不同。`JdbcBatch`将使用给定的输入来规划输入分区，即多个分区和分区列。它将获取分区列数据，并将其划分为若干分区。因此，JdbcInputPartition 的每个实例都将获得一大块分区列数据。

对于`JdbcInputPartition`的每个实例，将创建`JdbcPartitionReader`。反过来，读取器的每个实例将只获取表的一部分数据，并将这些 id 作为过滤器传递。每个分区读取并行运行，因此数据读取会更快。

## 什么是位置感知？

假设，我们正在为像 HDFS 这样的分布式、多节点、分区存储编写自定义数据源。如果我们在更靠近 HDFS 分区的地方运行数据读取，它的执行速度会快得多。如果它运行在同一个节点上，它将在本地读取数据，而不是通过网络读取数据。考虑到数据存储的局部性，运行每个数据源分区读取数据的方式越接近越好。

分区位置信息可以通过`InputPartition`接口上的`preferredLocation` API 指定。它可以是主机地址或主机名。根据 API 文档，Apache spark 试图在给定的位置运行分区，但不能保证这一点。在集群中的不同节点上运行之前，它使用`**spark.locality.wait**` 配置。

我们可以使用`preferredLocation`在离数据源更近的地方运行数据获取并加快进程，但是不能保证局部性。因此，当分区在不同的节点上运行时，读取逻辑不应该是位置相关的，也不应该失败。

现在，让我们在集群上运行它。这里分享了在 windows 上设置 Apache Spark 独立集群的步骤

这个例子的完整源代码在这里分享[。](https://github.com/aamargajbhiye/big-data-projects/tree/master/Datasource%20spark3/src/main/java/com/bugdbug/customsource/jdbc)

# 到目前为止我们讨论了什么？

我们讨论了如何创建单分区读数据源，以及如何创建多分区的位置感知数据源。

通过使用`SupportsPushDownFilters`增加过滤能力和使用`SupportsPushDownRequiredColumns.`增加列修剪能力，可以进一步改进这个例子

感谢阅读。你喜欢这篇文章吗？请在评论中告诉我你的想法。

在下一篇文章中，我们将讨论如何在 Apache Spark 3 中创建一个写数据源。

# 参考

 [## org . Apache . Spark . SQL . connector . read(Spark 3 . 0 . 0 JavaDoc)

### 编辑描述

spark.apache.org](http://spark.apache.org/docs/3.0.0/api/java/org/apache/spark/sql/connector/read/package-summary.html) [](https://github.com/aamargajbhiye/big-data-projects/tree/master/Datasource%20spark3) [## aamargajbhiye/大数据项目

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/aamargajbhiye/big-data-projects/tree/master/Datasource%20spark3)
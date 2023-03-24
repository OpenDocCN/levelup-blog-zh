# 在 Apache Spark 3 中创建自定义写数据源的简单指南

> 原文：<https://levelup.gitconnected.com/easy-guide-to-create-a-write-data-source-in-apache-spark-3-f7d1e5a93bdb>

## 在 Apache Spark 3.0.x 中创建自定义事务性写数据源的分步指南

![](img/abae2e6a4d1a7524233fa92b7caaec73.png)

格伦·卡斯滕斯-彼得斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这是在 Apache Spark 3.0.x 中编写自定义数据源系列的第二篇文章。在第一篇文章[中，我们了解了 Apache Spark 3.0.x 中的数据源 API、它们的重要性以及 read APIs 的概述。首先，我们学习了创建一个简单的定制读数据源，然后创建了一个稍微复杂的位置感知的多分区读数据源。在本文中，我们将学习实现一个自定义的写数据源。](/easy-guide-to-create-a-custom-read-data-source-in-apache-spark-3-194afdc9627a)

# 我们会学到什么？

*   在本文中，我们将了解数据源编写 API。
*   对于分布式执行，每个接口及其在集群上的部署的重要性，驱动程序与执行器节点。
*   我们将通过实现所有必要的接口来创建一个简单的写数据源。
*   之后，我们将对其进行更新，并为其添加交易支持。
*   我们将了解任务失败、重试机制、推测任务以及如何实现最多一次 行为 ***。***

# 编写数据源 API

Apache Spark 数据源具有读写能力。当我们调用`dataset.write().format(**“**output_format**”**).save()`时，使用 write APIs 以指定的输出格式保存数据帧。开箱即用，火花支持 CSV，拼花，JDBC，兽人等。作为书写格式。

# **编写接口**

*   表格提供者
*   桌子

这两个在`read` 和`write` API 中很常见。以下是只写接口

*   支持写入
*   编写器
*   BatchWrite
*   LogicalWriteInfo
*   PhysicalWriteInfo
*   数据写入工厂
*   数据写入器
*   WriterCommitMessage

现在，让我们详细了解每个接口的重要性，并在此过程中创建一个写数据源。

# 表格提供者

正如我们在上一篇[文章](/easy-guide-to-create-a-custom-read-data-source-in-apache-spark-3-194afdc9627a)中看到的，主数据源接口`DatasourceV2,`被移除，新接口 [TableProvider](http://spark.apache.org/docs/3.0.0/api/java/org/apache/spark/sql/connector/catalog/TableProvider.html) 被引入。它是所有不需要支持 DDL 的定制数据源的基本接口。这个接口的实现应该有一个 0 参数的公共构造函数，对于 Java，它是默认的构造函数。让我们看看它看起来怎么样。

# 支持写入

一个`Table`接口定义了一个代表结构化数据的逻辑实体。它可以是基于文件系统的数据源的文件/文件夹，Kafka 的主题，或者 JDBC 数据源的表。它可以与`SupportsRead`和`SupportsWrite`混合使用，分别增加读写能力。`capabilities`方法返回表的所有功能。对于我们简单的 write 实现，让我们返回 BATCH_WRITE。一个`SupportsWrite`接口有一个`newWriteBuilder()`方法，它返回定义数据源写行为的`WriteBuilder`。

# WriteBuider

现在，让我们实现这个接口，并从`JdbcTable` 实现中返回它的实例。WriteBuilder 用于创建用于批量写入的`BatchWrite`或用于将数据流写入流数据源的`StreamingWrite` 的实例。我们已经从表功能中返回了 BATCH_WRITE，所以我们将实现`buildForBatch().`

该接口提供的默认行为是使用`BatchWrite`实现向给定表追加数据的能力。它可以与其他接口一起使用，以提供额外的功能，如覆盖(`SupportsOverwrite`)、截断现有数据(`SupportsTruncate`)。

我们将在另一篇文章中学习如何创建流写数据源。

# BatchWrite

顾名思义，这个接口用于将数据批量写入给定的数据源。它处理为每个分区创建的写作业和任务。这里处理作业级提交和中止。它返回`**DataWriterFactory.**` **的实例，这三个接口的实例运行在驱动程序节点上，在调用** `**dataset.write.save()**` **时会创建一个写作业。**

# 数据写入工厂

顾名思义，它是一个创建`DataWriter` 实例的工厂。BatchWrite 实例在驱动程序节点上运行。`BatchWrite` 创建一个`DataWriterFactory` 实现的实例，并将其发送给执行者节点。这里要注意的一件重要事情是使它可序列化。

# 数据写入器

这是主接口，它实际上完成了将数据写入目标系统的工作。对于每个数据帧分区，使用 DataWriterFactory 在 executor 节点上创建一个`DataWriter` 实例。为每个内部行调用一个`write(record)`。当一个分区中的所有记录都被成功写入时，就会调用`commit()`。如果在写入这些记录的过程中出现任何异常，就会调用`abort()`。我们可以使用这些 API 来支持分区级事务。我们稍后会看到更多相关内容。

Spark 并行写入每个分区。因此，`DataWriter`实现不需要线程安全。为每个分区创建一个单独的`DataWriter` 实例。

# 其他接口

*   **LogicalWriteInfo** :携带源数据帧模式、执行写操作所需的选项和分配给作业的查询 id。
*   **PhysicalWriteInfo** :携带源数据帧的分区计数信息。
*   **WriterCommitMessage:** 这是一个标记接口。它可以用于将分区 id、任务 id 等信息从执行器节点上的数据写入器传输到驱动程序节点。

就是这样。我们已经成功创建了一个自定义写入数据源。现在，让我们看看如何将现有的数据帧写入 JDBC 表。

# 添加事务支持

有了这些知识，让我们使这个实现更加成熟，并看看如何将事务支持添加到这个实现中。

正如我们前面看到的，`DataWriter`接口有`commit()`和`abort()`方法。这些可用于在分区级别实现事务支持。对于 Jdbc 类型的数据源，可以做以下事情

*   关闭连接的自动提交。
*   在一个连接上继续写。
*   成功写入整个分区数据后，将调用 datawriter.commit()。我们将从此方法调用 connection.commit()。
*   如果`write(record)`抛出任何异常，则调用`abort()`。我们可以调用`connection.rollback()`来还原不完整的数据。

对于像文件数据源这样的非 JDBC 数据源，可以将数据写入像内存缓冲区这样的临时存储中，然后在 commit()中，可以一次写入所有数据，也可以在 abort()中完全清除数据。

## 作业级别交易

一个`BatchWrite`可用于添加一个作业级事务支持。这可以使用临时存储来完成。在我们的例子中，我们将传递一个临时表名，而不是传递用户指定的表名。分区的所有数据写入器都将写入该临时表。成功写入所有分区后，将调用`BatchWrite.commit( WriterCommitMessage[] writerCommitMessages)`。在这个方法中，我们将把表重命名为原来的表。在任何异常情况下，我们将删除`abort(WriterCommitMessage[] writerCommitMessages)`中的临时表。

# 任务失败

在分布式系统中，失败是很常见的。这可能是由于网络中断、节点故障等原因造成的。像 Apache Spark 这样健壮可靠的系统肯定知道如何处理这样的故障。它有一个任务失败的重试机制。对于每个 spark 作业，多个任务在执行器节点上运行。在我们的写作业中，为写每个数据源分区创建了一个单独的任务。单个任务的失败不应该完全是写作工作的失败。出于同样的原因，Spark 有一个重试机制来在不同的节点上执行失败的任务。重试次数可通过`spark.task.maxFailures.`进行配置

执行写操作时，数据一致性应该是主要目标。重试机制给数据源实现者带来了挑战。如果数据源没有正确实现，有些数据可能会被多次写入。

为了增加分区级事务支持，我们在关闭自动提交的情况下在连接上写入数据。我们仅在所有内容都成功写入时提交，否则使用回滚丢弃所有内容。这可以确保即使重试任务因失败而运行，也不会多次写入记录。

# 投机任务

Apache Spark 还有另一个惊人的特性，叫做投机。当一个正在运行的任务花费太多时间来完成时，另一个推测性任务将在任何其他节点上为同一任务运行。这意味着，现在有**多个任务同时运行来写入相同的数据**。对于只读任务，这无关紧要。较早完成阅读的任务将被考虑在结果中，其他的将被忽略。

但是，写作业中的情况有所不同。如果多个任务并行运行，试图将相同的数据写入目标系统，一致性就会受到影响。为了避免这种情况，Spark 使用 commit co-ordinator 来确保只有一个正在运行的任务应该提交数据。可以使用下面的方法通过`BatchWrite`实现来打开提交协调程序

```
public class JdbcBatchWrite implements BatchWrite {

    public boolean useCommitCoordinator() {
        return true;
    }
}
```

但是，只有当我们实现了分区级别的事务时，这才会起作用。否则，重复的条目将被写入目标系统。可以使用`spark.speculation`配置推测功能。

# 总结一下

可以实现定制的写数据源以写入目标系统。与 read 相比，它稍微复杂一些，因为需要添加事务支持，错误的处理可能会导致数据不一致。我们需要了解重试机制和推测性任务机制，以及分区级事务如何处理可能因此产生的数据不一致问题。

这个例子的完整源代码在这里分享[。](https://github.com/aamargajbhiye/big-data-projects/tree/master/Datasource%20spark3/src/main/java/com/bugdbug/customsource/jdbc)

通过实现`SupportOverwrite` 和`SupportsTruncate` 接口来添加覆盖和截断功能，可以进一步增强这个示例。

感谢阅读。你喜欢这篇文章吗？请在评论中告诉我你的想法。

# **参考文献**

 [## /docs/latest/API/Java/org/Apache/spark/SQL/connector/write 的索引

### 编辑描述

spark.apache.org](http://spark.apache.org/docs/latest/api/java/org/apache/spark/sql/connector/write/) [](https://github.com/aamargajbhiye/big-data-projects/tree/master/Datasource%20spark3) [## aamargajbhiye/大数据项目

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/aamargajbhiye/big-data-projects/tree/master/Datasource%20spark3) 

# 进一步阅读

[](/easy-guide-to-create-a-custom-read-data-source-in-apache-spark-3-194afdc9627a) [## 在 Apache Spark 3 中创建自定义读取数据源的简单指南

### 在 Apache Spark 3.0.x 中使用位置感知和多分区编写自定义读取数据源的分步指南…

levelup.gitconnected.com](/easy-guide-to-create-a-custom-read-data-source-in-apache-spark-3-194afdc9627a) [](https://medium.com/@aamargajbhiye/apache-spark-and-in-memory-hadoop-file-system-igfs-8b405997930) [## Apache Spark 和内存中 Hadoop 文件系统(IGFS)

### Apache Spark 和内存中 Hadoop 文件系统(IGFS)

Apache Spark 和内存中 Hadoop 文件系统(IGFS)medium.com](https://medium.com/@aamargajbhiye/apache-spark-and-in-memory-hadoop-file-system-igfs-8b405997930) [](https://medium.com/@aamargajbhiye/apache-spark-setup-a-multi-node-standalone-cluster-on-windows-63d413296971) [## Windows 上的 Apache Spark 独立集群

### 在 Windows 上设置 Apache Spark 多节点独立集群

medium.com](https://medium.com/@aamargajbhiye/apache-spark-setup-a-multi-node-standalone-cluster-on-windows-63d413296971)
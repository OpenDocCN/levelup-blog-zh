# 雪花数据库不是正确选择的 7 个理由

> 原文：<https://levelup.gitconnected.com/7-reasons-snowflake-might-not-be-the-right-option-5eaec1409ae5>

每个人都告诉你只要切换到雪花，但它是你的用例的正确选择吗？

![](img/0dbe013b9b023d1bfd66183d63a096e7.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[zdenk macha ek](https://unsplash.com/@zmachacek?utm_source=medium&utm_medium=referral)拍摄的照片

数据仓库是任何组织的 IT 战略的关键组成部分，充当所有数据的中央存储库。它通常被认为是部署和维护成本高昂的组件。组织需要知道这些成本会给他们带来什么。

用他们自己的话来说，创始人“设计了雪花来推动数据云，在数据云中，成千上万的组织可以无缝地探索、共享和释放他们数据的真正价值。”

在什么情况下切换到雪花不是正确的选择？这就是我们在这里讨论的问题。

1.  **雪花的数据仓库贵**

根据你所需要的特性，雪花的价格从 2 美元到 4 美元不等。雪花提供物化视图和 HIPAA、PCI DSS、SOC 1 和 SOC 2 Type 2 兼容数据库，这些数据库需要 4 美元的支付层。大多数数据库，包括 Azure SQL 和 Amazon Redshift，在其最低层提供这些相同的功能。

除此之外，我们还必须为按需存储每月支付每 TB 40 美元，如果我们想预付，则每月支付每 TB 23 美元。

**2。没有版本控制**

没有简单的方法来跟踪 SQL 文件中的变化。您需要手动跟踪您对这些文件所做的更改，并且容易地比较这两个版本也是一件麻烦的事情。

3.**专有雪花格式增加了延迟**

雪花选择使用微分区文件格式，这可能有利于提高性能。不幸的是，这意味着雪花“引擎”不能直接与 Apache Parquet 和其他开放格式一起工作。数据可以很容易地导入到雪花文件格式，但检索数据会增加延迟。

**4。数据流**

雪花的最新创新 Snowpipe 是一项持续的数据摄取服务，可以在几分钟内将新鲜数据加载到微批处理或流表中。因此，如果您寻求的延迟少于一分钟，这将不是您的选择。然而，如果几分钟没问题，这可能是它。

**5。存储过程是用 JavaScript、雪花脚本和 Scala 编写的。**

虽然这不一定对每个人都有坏处，但重要的是要记住，管理雪花的绝大多数人主要是数据工程师或数据库管理员。大多数人缺乏使用这些语言的经验或者经验有限。

**6。没有开源社区**

考虑到雪花不是基于开源格式，它没有开源社区也就不足为奇了。然而，我确实想花点时间强调一下，相比之下，替代方案确实有一个非常强大的开源社区。

**7。缺少所需的数据**

如果您拥有的数据数量很少，或者它的增长率仍然很低，那么实现像雪花这样的东西可能仍然是多余的。另一种选择是 SQL Server，它的使用和初始化要便宜得多。

我只是列出了一些对雪花来说不可行的要点。如果你遇到过其他人，请告诉我。

```
Resources1\. [https://www.snowflake.com/company/](https://www.snowflake.com/company/)
2\. [https://www.snowflake.com/pricing/](https://www.snowflake.com/pricing/)
3\. [https://docs.snowflake.com/en/sql-reference/stored-procedures-overview.html](https://docs.snowflake.com/en/sql-reference/stored-procedures-overview.html)
```
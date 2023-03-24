# 雪花与其他数据仓库相比有什么独特之处？

> 原文：<https://levelup.gitconnected.com/what-is-unique-about-snowflake-vs-other-data-warehouses-2e5a017fc7f6>

## 雪花已经成为一个受欢迎的数据仓库，因为它具有用于数据分析的高性能 SQL 查询引擎、基于云的架构、易于使用的 web 界面、透明的定价模型以及根据需求自动扩展。以下是雪花与其他数据仓库的比较。

![](img/862f55b01ab285ce35def24e096c7497.png)

由[凯伦·瑞金](https://unsplash.com/@kalaniparker?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/snowflake?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的封面照片

与其他数据仓库解决方案相比，雪花数据平台提供了一些独特的优势，至少在性能和易用性方面。在我们了解雪花有何不同之前，让我们先来看看一个简化的分析工作流程:1)清理，2)仓库，3)分析。

**第一步:清洁。**假设您已经有了数据，比如 HubSpot 营销数据和客户销售数据，您的第一步将是“清理”这些数据，无论是手动还是使用自动化工具，比如 [dbt](https://www.getdbt.com/) 。清理的目的是协调来自多个原始来源的不同数据，删除无关和不正确的记录，并为分析准备数据。

**第二步:仓库。现在您已经有了干净的数据，您将希望在某种类型的存储库中存储或“储存”您的数据。在使用雪花的时候，你使用它的[虚拟仓库](https://medium.com/propel-data-analytics-blog/what-are-warehouses-in-snowflake-data-analytics-platform-9e07ee8e1fc1)来上传和操作云中的数据。雪花将其数据存储(数据库和模式)与处理数据进行分析所需的计算能力([仓库](https://medium.com/snowflake/how-many-virtual-warehouses-can-snowflake-hold-db22f75b4a2c))分离开来。**

**第三步:分析。**使用基于云的数据仓库意味着您不需要对常规数据库运行查询分析，这可能会干扰正常操作，例如处理客户销售。使用雪花时，可以直接运行 SQL 查询，也可以使用分析 API。

# 雪花相对于其他仓库有什么独特之处？

那么，与其他数据仓库解决方案相比，雪花的独特之处在哪里呢？答案在于它的性能和易用性。雪花有一个微分区存储架构，以列的形式组织数据，这意味着分析查询在雪花中的运行速度要比在传统的 SQL(结构化查询语言)数据库中快得多。SQL 数据库通常将数据存储在单一的、面向行的表中，没有自动分区，这种特性对于事务性数据很好，但对于分析性查询来说效率很低。尽管其内部架构很复杂，但雪花很容易使用，因为它的查询引擎使用基于 SQL 的语法。此外，它是基于云的，因此开发人员不必担心高昂的前期成本。

当然，雪花不是唯一可用的云仓库，但云服务总体上有明显的优势。将数据托管在云中所需的管理开销比其他方案要少，因为组织不需要管理硬件。此外，云数据仓库还提供了可伸缩性，即轻松提高性能(和成本)以响应需求增长的能力，以及灵活性，即在需求减少时缩减规模的能力。

雪花提供了弹性和可扩展性，而没有直接管理服务器的开销，您只需为您实际使用的存储成本和计算时间付费。 [Snowflake 的数据仓库](https://betterprogramming.pub/is-snowflake-a-data-warehouse-for-analytics-and-insights-baa4e0cf9318)的弹性意味着，如果你想更快地加载数据，或者运行更复杂或更频繁的查询，你可以扩大数据仓库以获得额外的计算资源(更大的数据仓库)，然后在查询结束时缩小数据仓库。与私有服务器相比，无论是内部管理还是通过亚马逊弹性云计算(EC2)等云服务管理，雪花都能提供更大的灵活性，同时提供一致的性能。

同时，雪花的虚拟仓库允许同时(或并发)运行多个同步作业。单个仓库将根据可用的处理能力自动确定查询是需要顺序运行还是并发(并行)运行。需要大量并发用户的数据应用可以使用雪花的[多集群虚拟仓库](/what-is-a-multi-cluster-virtual-warehouse-in-the-snowflake-data-platform-4fd944f5184d)，根据实时分析需求自动扩展可用节点的数量。这些特性在其他一些数据仓库中是不存在的。

雪花也比其他解决方案更加用户友好——你不必猜测“由第三代 AMD EPYC 处理器支持的亚马逊 EC2 M6a 实例，运行频率高达 3.6 GHz”是否提供了价格、性能和维护的正确权衡。相反，雪花的定价很简单(通过雪花积分)，仓库有简单的尺寸(从 X-Small 到 6X-Large)，每个尺寸增量都提供两倍的性能。不习惯通过命令行配置定制服务器的员工仍然可以通过他们的 web 用户界面 Snowsight 轻松管理雪花。

也就是说，雪花并不完美。例如，雪花在 2021 年才开始支持 pdf、音频文件和大型文本 blob[等非结构化数据。(以前，像图像这样的数据必须转换成字符串或二进制文件，而且文件大小也有限制。)首先将数据加载到 Snowflake 可能很棘手，而](https://www.snowflake.com/blog/snowflake-launches-unstructured-data-support-in-public-preview/?_ga=2.257768392.259827065.1658159587-569441304.1655143019) [Snowpipe](https://docs.snowflake.com/en/user-guide/data-load-snowpipe.html) 是事件(流)数据的唯一选项之一。最后，你很容易在你的雪花账户中花费超过预期的金额，因为你必须通过管理员设置的“资源监视器”来手动限制支出。

不过，总的来说，雪花已经成为最受欢迎的云数据仓库解决方案之一，因为它易于使用，性能卓越，价格透明。当我们考虑通过“清理、存储和分析”的工作流获取业务数据时，许多组织使用雪花进行存储和分析是有意义的。

在许多用例中，雪花是分析的最佳选择，但我将在未来的文章中探索类似 Google BigQuery 和 Amazon RedShift 的替代方案。总的来说，像雪花这样的数据仓库在运行一次性分析查询(如手动报告)方面比构建仪表板和其他数据应用程序好得多。为面向客户的分析实施经济高效的并发是今天[数据工程师、数据科学家和数据分析师](https://towardsdatascience.com/what-is-the-difference-between-a-data-engineer-a-data-scientist-and-a-data-analyst-8d3431ce505e)面临的最具挑战性的问题之一。

**编码快乐！** ❄👩‍💻❆🧑‍💻❅‍

德里克·奥斯汀博士是《职业编程:如何在 6 个月内成为一名成功的 6 位数程序员 》一书的作者，该书现已在亚马逊上架。
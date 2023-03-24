# OCI 得分超过 AWS 的领域很少

> 原文：<https://levelup.gitconnected.com/few-areas-where-oci-scores-above-aws-481e959140b9>

## OCI vs AWS

## 还有其他一些地方没有

![](img/8cf5703b68e4d8db7e20989dde910287.png)

图片由来自 [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5238022) 的[斯蒂芬·凯勒](https://pixabay.com/users/kellepics-4893063/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5238022)拍摄

我的云基础设施之旅始于 AWS，我一直对这方面的创新速度感到惊讶。市场上有如此多的大玩家(谷歌、微软、甲骨文、IBM 等)，他们都试图在创新方面超越彼此。甲骨文的 OCI 可能是这个领域的最新进入者之一，但它已经凭借其现有的一系列市场领先的产品成为某些领域的领导者。我最近开始探索 OCI，但由于我过去在 AWS 的经历，我的脑海中一直在比较 OCI 和 AWS。我开始记下我最初的想法，OCI 似乎比 AWS 做得更好。我绝不是 OCI 方面的专家，因为这只是一个开始，还有很多东西需要学习。我可能错过了某些关键的东西，所以请有所保留。随着对 OCI 了解的加深，我会更新我的笔记。

**以下是我对 OCI 服务的初步了解，以及它们与 AWS 服务的对比:**

## [专用区域云](https://www.oracle.com/cloud/cloud-at-customer/dedicated-region/)

专用区域云是来自 OCI 的一个独立的云区域，它在客户选择的物理位置提供所有内部公共云服务。通常，当您谈论云迁移时，您会将应用程序移动到公共云，但这是将云带给您，或者实际上带给您的数据中心。这是目前 OCI 相对于 AWS 的最大优势之一。这可以为在将数据和/或应用程序迁移到公共云方面受到约束或限制的客户(例如政府客户、监管机构等)实现云迁移。此外，OCI 为专用云区域提供了与公共云区域相同的 SLA。

AWS Outposts 提供了一些类似的东西，但是它们仅限于有限的服务(在撰写本文时有 17 个)，所以它并不是一个真正孤立的环境。另一方面，OCI 在专用区域作为独立的环境提供其所有服务。

## [故障域](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm#fault)

故障域是可用性域内的一组硬件和基础设施。可用性域(AD)类似于 AWS 可用性区域(AZ ),高可用性的推荐策略是跨 AZ 或 AD 分布节点。作为附加的抽象，OCI 在每个 AD 中提供了三个容错域，您可以选择在 AD 中的不同容错域中创建实例。这为应用程序提供了另一层弹性，增加了针对单个硬件故障的更多保护措施。在 AWS 中，对于具有多层架构的复杂解决方案，AZ 中的多个节点可能会出现在同一个硬件堆栈上，并且 AZ 中的单个硬件故障可能会表现为 AZ 故障。OCI 通过提供跨不同容错域分布实例的选项，使避免这种情况变得更加容易。

您可以在 AWS 中为 EC2 创建放置组，以获得类似的硬件隔离，但是只需点击几下就可以获得容错域，并且可以为每个实例选择容错域。

## [厢](https://docs.oracle.com/en-us/iaas/Content/Identity/Tasks/managingcompartments.htm)

隔间是 OCI 中用于安全隔离和访问控制的强大功能。隔离专区是相关云资源的集合。隔离专区类似于 AWS 资源组，但有一些优势。虽然 AWS 资源组仅限于一个区域，但是隔离专区可以跨区域用于您的租赁(= AWS 帐户)。此外，隔离专区最多可以嵌套六层，从而为更好的组织提供了灵活性。对于真正全球化的产品，这种级别的组织是有帮助的。例如，您可以为不同的域(如生产、暂存和沙箱)创建隔离专区，然后进一步为较小的应用程序或模块创建嵌套隔离专区。除了细粒度的访问控制，区间还允许您通过为每个区间设置配额来跟踪预算和费用。

## [自治数据库](https://docs.oracle.com/en-us/iaas/Content/Database/Concepts/adboverview.htm)

Oracle 无疑是市场上最好的 RDBMS 之一，而 OCI 的自主数据库服务将它推向了一个新的高度。它是一种完全自动化的数据库服务，建立在自动驾驶的自主技术之上，能够在没有任何停机时间或人工干预的情况下提供自动化的修补、升级、调整和所有维护任务。AWS RDS 也提供 Oracle DB 作为托管服务，但它远不能与 Oracle 自治数据库提供的功能相提并论。对于生产环境中的关键数据库来说，升级和维护仍然是一个令人头痛的问题，但 OCI 自治数据库使这一问题变得很容易。就基础设施和性能而言，OCI 有许多不同的选择。

另一方面，AWS 为 MS SQL Server、Postgres、MongoDB 等各种供应商提供了更广泛的数据库产品作为托管服务。它还为特定用例提供了更多选项，如图形数据库(Neptune)、文档(DocumentDb)、物联网时间序列(Amazon Timestream)、分类帐(QLDB)等。

## [资源经理](https://www.oracle.com/devops/resource-manager/)

资源管理器是 OCI 提供的基础设施即代码(IaC)产品，可自动部署和配置基础设施资源。这里的优点是资源管理器基于流行的开源技术 Terraform。这优于其 AWS 对应的云形成，因为 terraform 是云不可知的技术，可以重用现有的内部代码，具有更少的启动时间，并且相对更容易从外部找到专业知识和资源。

另一方面，AWS 似乎有更多的整体 DevOps 服务来满足各种各样的用例(如代码管道、代码构建、代码部署等)。OCI DevOps 服务涵盖了其中几项 AWS 服务，但目前我无法对其具体的限制或优势发表评论。

## 简化名称

从更轻松的角度来说，OCI 用一个简单的命名惯例来保持它，你不会弄错的。每个服务都如其名，所以不需要记忆或猜测，只需搜索你想做的，这就是服务的名称。如果你将 OCI 的名字如 Audit、logging、DDOS protection、Streaming 等与它们对应的 AWS 名字如 CloudWatch、CloudTrail、Shield、Kinesis 等进行比较，你会立即感觉到对这项服务很熟悉。

这是一件小事，因为您已经习惯了名称，但是对于任何开始云迁移之旅的人来说，如果名称不是描述性的，就需要花费一些精力来了解每个服务的作用。就我个人而言，我喜欢有趣的名字，但从长远来看，描述性的名字肯定更容易，特别是对于有很多人不经常使用这些服务的大型组织。

## 成本比较

在类似服务的成本比较方面，OCI 似乎确实比 AWS 有很多优势。最显著的成本节约是在通用服务方面，如 OCI 数据块存储比 AWS EBS 便宜 95%(截至 2020 年 2 月)。同样，与 AWS 相比，OCI 的数据出口成本和英特尔虚拟机也非常便宜。

另一个突出的因素是，OCI 的服务跨地区全球定价，但全球不同地区的 AWS 定价不同。如果您打算在多个地区运行您的应用程序，这使得预算或管理费用有点困难。

## **托管缓存服务**

分布式缓存是大多数现代架构中的关键技术之一，而 OCI 似乎没有为缓存提供任何托管服务。AWS ElastiCache 为两个流行的缓存引擎 Redis 和 Memcached 提供了选项。它还可以从 AWS marketplace 获得 Hazelcast 服务。甚至 AWS DynamoDb DAX 也为其 NoSQL 数据库提供了缓存选项。看来，提供托管分布式缓存或缓存服务是 OCI 需要尽快填补的空白之一。Oracle Coherence Cloud 是 OCI 市场上的一款应用。人们总是可以在 OCI 建立一个 Redis 或 Hazelcast 集群，但是让云供应商来管理它会更好。

# 最后的想法

毫无疑问，AWS 拥有比 OCI (100 多个)更多的全面管理服务(200 多个),但其中大多数服务都是为了满足特定的使用案例或一些利基领域。OCI 目前提供的服务较少，但试图在 AWS 等其他供应商提供的功能基础上构建更多功能。公共云的市场是巨大的，尽管 OCI 在这场游戏中起步有点晚，但 OCI 的前景似乎很有希望。正如我前面提到的，这只是我最初学习中的一些观察。当我接触到更多的 OCI 服务时，我会添加更多的细节并给出更多的注释。如果你对这个话题有任何想法，请随时在评论中提供你的反馈。

感谢阅读！

[](https://medium.com/management-in-simple-english/leadership-bytes-my-top-articles-on-medium-e6c455078b97) [## 领导力字节——我在媒体上的热门文章

### 这里有一个帮助你选择下一本书的摘要

medium.com](https://medium.com/management-in-simple-english/leadership-bytes-my-top-articles-on-medium-e6c455078b97) [](https://praveshbhargav.medium.com/membership) [## 用我的推荐链接加入媒体

### 阅读 P . r . a . v . e . s . h 的每一个故事(以及媒体上成千上万的其他作者)。你的会员费直接支持 P…

praveshbhargav.medium.com](https://praveshbhargav.medium.com/membership)
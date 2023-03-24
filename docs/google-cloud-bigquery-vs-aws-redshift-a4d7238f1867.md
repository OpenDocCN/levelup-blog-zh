# 谷歌云 BigQuery 与 AWS 红移

> 原文：<https://levelup.gitconnected.com/google-cloud-bigquery-vs-aws-redshift-a4d7238f1867>

## 哪个云数据库适合你？

![](img/6052c080b962c72dbd8620f30f1d2f3f.png)

唐纳德·詹纳蒂在 [Unsplash](https://unsplash.com/s/photos/technology?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

对于我们这些必须管理跨具有不同需求的大型组织的大型数据集的人来说，管理自己的服务器和应用程序集已被证明是非常困难的。

因此，一窝蜂地涌向云。

谷歌和亚马逊是一些最大的云服务提供商！许多公司依赖于他们的各种产品，经常混合搭配他们提供的不同产品。

在本文中，我们想要讨论云数据仓库的概念。

具体参考分别由 Amazon 和 Google 构建的 RedShift 和 BigQuery，并讨论哪个应用程序最适合您的组织。让我们开始吧:

# 什么是数据仓库？

在我们深入探讨细节之前，什么是数据仓库？

简而言之，数据仓库是任何商业智能或分析团队的生命线。它允许这些团队存储和分析来自整个组织的领域(如财务、运营和人力资源)的有用数据。

由于对原始源系统和数据仓库进行了设计上的修改，这也使得数据的交互变得更加简单。

总的来说，数据仓库的主要目的是在复杂的业务流程和分析师之间架起一座桥梁。

# 云数据仓库

直到最近，数据仓库都是在内部开发的。这意味着系统受限于它们所在的服务器，通常必须垂直扩展。

这可能会花费很多时间和额外的人力。

现在有了云，许多数据仓库提供商允许您作为一家公司根据需要进行扩展和缩减。

此外，许多云提供商已经开发了特定于数据仓库的数据系统。这使得它们比标准的关系数据库更快更有效。

团队如何使用云数据仓库有很多选择。今天我们将重点讨论 BigQuery 和红移。

基本上，亚马逊对谷歌。

所以我们来看看。

# BigQuery

BigQuery 是 Google 使用 BigTable 构建的无服务器企业级数据仓库。

该应用程序可以在几秒钟内对过去难以管理的大量数据执行复杂的查询。

BigQuery 支持 SQL 格式，并通过命令行工具和 web 用户界面提供可访问性。这是一种可扩展的服务，允许用户专注于分析，而不是处理基础架构。

就我个人而言，我非常喜欢 BigQuery 的在线用户界面。无需设置任何连接器或下载任何第三方工具来与数据交互。

# 红移

Redshift 是亚马逊打造的面向列的基于云的数据仓库系统。一些人说，在甲骨文首席执行官吹嘘亚马逊需要甲骨文继续经营后，他们可以停止依赖甲骨文。

红移集群由多台存储一小部分数据的机器组成。

这些机器并行工作，保存数据，因此我们可以有效地处理它。这里，Redshift 有一些计算节点，它们由领导者节点管理，以管理计算节点之间的数据分布和查询执行。还有其他设计优势，如大规模并行处理(MPP)。

总的来说，BigQuery 和 Redshift 在设计时都考虑到了分析。因此，像 MPP 和列存储这样的概念都是设计决策，以确保运行分析查询是高效的。

# BigQuery 和红移的区别

## **定价**

虽然两者都有按需和统一费率选项，但 BigQuery 和 Redshift 在定价方面有很大不同。BigQuery 对存储、查询和流插入收费，而 Redshift 对集群中的每个节点收费。

[RedShift 的存储成本约为每月每 TB 306 美元](https://www.xplenty.com/blog/redshift-vs-bigquery-comprehensive-guide/)，并提供无限的处理能力，而 BigQuery 的成本仅为每月每 TB 20 美元[，每 TB 5 美元](https://www.xplenty.com/blog/redshift-vs-bigquery-comprehensive-guide/)。

## ***安全***

RedShift 使用 Amazon IAM 进行身份认证，而 BigQuery 使用 Google Cloud IAM。BigQuery 带有默认的数据加密选项，而在 BigQuery 中，您必须手动启用该选项。

[从角色的角度来看，这两种 iam 有些相似，但您可以在此处阅读更多关于不同之处的信息](https://www.stratoscale.com/blog/compute/comparing-google-iam-aws-iam/)。

## ***简单性***

BigQuery 抽象出底层数据库、配置和硬件的细节。红移需要你对红移有非常深入的了解。这包括像分发密钥和 MPP 这样的概念。

## ***数据加载***

Amazon RedShifts 允许你从任何地方加载数据。然而，它最自然地与亚马逊 S3 联系在一起，而类似的事情也可以说是关于谷歌大查询和谷歌云存储。

这两个系统都支持流方式的数据插入，并且都支持 JSON、CSV 和 Avro 等格式的数据序列化。

此外，还有许多其他方法可以用来加载这些系统。例如，Airflow 和 Luigi 都可以用于加载，但 AWS Glue 也可以。

# 更新表格

许多传统的数据仓库和 BI 专业人员可能会用来合并、更新和插入一组 DMLs(数据操作语言)语句，这些语句经常出现在 Oracle、SQL Server、MySQL 和几乎所有其他标准数据库中。

然而，Redshift 和 BigQuery 不一定以相同的方式支持所有这些子句。

例如，对于 merge Redshift 有 upserts，但它并不完全是 merge 的直接版本。

引用 AWS:

> Amazon Redshift 不支持单个 *merge* 语句(update 或 insert，也称为 *upsert* )来插入和更新来自单个数据源的数据。
> 
> 但是，您可以有效地执行合并操作。为此，将数据加载到临时表中，然后将临时表与更新语句和插入语句的目标表连接起来。有关说明，请参见[更新和插入新数据](https://docs.aws.amazon.com/redshift/latest/dg/t_updating-inserting-using-staging-tables-.html)。

所以这真的是一个变通的办法。说实话，这还是让我有点难过。

BigQuery 实际上支持 merge 子句。

事实上，直到 2017 年左右，它实际上是作为一个仅附加系统而设计的。但是 BigQuery 已经从[开始更新、插入](https://cloud.google.com/bigquery/docs/reference/standard-sql/dml-syntax)和合并子句。

这为工程师在 Bigquery 上开发数据仓库提供了一种更加直接的方法。

继续 BigQuery 更容易使用的想法。

## 结论

Redshift 和 BigQuery 都是优秀的数据仓库，可以帮助企业获得有用的见解。在做出选择之前，考虑您的表列长度、业务需求和技术能力是很重要的。

Redshift 在管理资源方面提供了更大的灵活性。然而，要操作一个星团，你需要了解红移背后的许多细微差别。这可能会迫使您的工程师花费大量时间来微调您的数据仓库。

另一方面，BigQuery 不期望您管理资源，因此它抽象出所有底层配置、硬件和数据库细节。由于其类似 SQL 的基础设施，它非常用户友好，易于学习。

虽然有一些不同，但两者都有各自的优点。因此，通读以上文章，然后与您组织的要求进行比较，以做出最佳选择。
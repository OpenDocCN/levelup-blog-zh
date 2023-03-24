# 如何使用 Kafka Connect 连接 Heroku 上的两个数据源

> 原文：<https://levelup.gitconnected.com/how-to-use-kafka-connect-to-connect-two-data-sources-on-heroku-49104d693e4b>

![](img/0befd982dd9c7d0122cedce4da294515.png)

约书亚·内斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 介绍

随着其他业务部门的需求不断增加，IT 部门不得不不断寻找服务改进和成本节约的机会。这篇文章展示了正在研究或已经在使用 Kafka 的公司的几个具体用例，特别是 Kafka Connect。

Kafka Connect 是一个企业级解决方案，用于集成大量应用程序，从传统数据库到 Salesforce 和 SAP 等业务应用程序。可能的集成场景包括从应用程序之间的连续流事件和数据到可用于替代手动数据传输的大规模可配置批处理作业。

# 为什么是卡夫卡和赫罗库

[Apache Kafka](https://kafka.apache.org/) 最初是 LinkedIn 的一个内部项目，旨在取代多年来发展的各种排队和消息传递系统。目标是创建一个可横向扩展的系统，能够可靠地存储和处理每秒数百万条消息。今天，Kafka 社区提供了一个完整的工具和功能生态系统，从简单的日志消息传递到执行基本的动态 ETL 任务，再到复杂的数据管道处理。Kafka 的多功能性和健壮性以及可扩展性使其成为企业的绝佳选择。

Kafka 的强大是有代价的:虽然从客户的角度来看使用 Kafka 很容易，但 Kafka 的设置和操作却是一项艰巨的任务。建立一个可靠的 Kafka 集群是一个需要经验的挑战。如果您的公司没有这方面的经验，可能需要几周甚至几个月的时间来设置和调整生产就绪型集群。这就是 Heroku 上 Kafka 的亮点:[建立一个私有 Kafka 集群只需几分钟](https://devcenter.heroku.com/articles/kafka-on-heroku#provisioning-the-add-on)，这使得 it 部门可以专注于他们想要提供的服务，而不是运营 Kafka 集群。

一旦你在使用 Kafka，添加 Kafka Connect 是合乎逻辑的下一步。但是 Heroku 上的 Kafka Connect 可以节省大量的时间，即使你不打算在你的应用程序中直接使用 Kafka。Kafka Connect 的运营开销很小，可以为任意数量的应用程序提供一个集成点。Kafka Connect 为这些问题提供了一个统一的解决方案，而不是运行和维护用于重复数据传输、按需数据传输和系统间持续更新的不同解决方案。

在下一节中，您将了解一些典型的用例，并了解 Kafka Connect 能够集成哪些应用程序。

# Kafka Connect 的典型业务用例

IT 部门不断努力满足业务部门的需求，这些业务部门必须连接和集成各种定制和现成的应用程序。这些集成通常使用不太可靠的机制，比如循环批处理作业，而不是跨系统连续“流式”更新。Kafka Connect 提供了一个可扩展的、可靠的解决方案，通常可以通过“流式”更新来取代缓慢的批处理作业。常见场景包括:

1.  您的销售部门经常需要使用来自 Salesforce 的数据更新内部 SQL 数据库。使用 Kafka Connect 将更改从 Salesforce 连续传输到您的内部数据库。
2.  您的合规部门需要对某些数据库或应用程序的更新进行审计跟踪。使用 Kafka Connect 将相关更改传输到本地文件服务器，传输到 S3 进行异地存储，甚至传输到 Elasticsearch 进行快速检索。
3.  用强大的解决方案取代不可靠、运行缓慢的 ETL 批处理作业。使用 Kafka 连接到

*   跨多个应用程序更新数据，同时使它们更快、更可靠
*   不断地从结构化文件(CSV、JSON、日志等)中传输变更。)到数据库和应用程序中
*   在异构应用程序和数据库之间可靠、连续地传输更新

IT 部门甚至可以创造新的自助服务机会，用自助服务流程取代手动或劳动密集型的数据传输或提取请求，从而减少 IT 部门的工作量，同时缩短业务部门的响应时间。假设有一个系统，贵公司的营销部门在您的服务台软件中创建一个票证，该票证会自动创建并触发数据传输，否则 IT 服务台将不得不手动处理。

# Kafka Connect 可以集成的系统的(不完整)列表

Kafka Connect 可以[读取和写入](https://docs.confluent.io/current/connect/managing/connectors.html)越来越多的应用和服务，包括企业应用，如 SAP 和 Salesforce:

*   任何 SQL 数据库:Oracle、Microsoft SQL Server、IBM Db2、MySQL、PostgreSQL、MariaDB 等等
*   NoSQL 数据库，如 MongoDB、Redis、Cassandra、InfluxDB 等
*   消息队列，如 ActiveMQ、RabbitMQ、IBM MQ、亚马逊 SQS、Azure Event Hub、Google Pub/Sub 等
*   IT 服务台应用程序，如吉拉、ServiceNow 和 Zendesk
*   日志和监控服务、应用程序和协议，如 SNMP、Syslog、Elasticsearch、Amazon CloudWatch、Appdynamics、Metrics、Splunk 等
*   通用的“低级”数据源和协议，包括 HTTP、SFTP、MQTT、JMS、文件系统等等
*   可以使用 Java 轻松编写健壮的定制连接器，充分利用可靠的 Kafka Connect 框架和底层基础设施
*   由于 Kafka Connect 使用 Kafka 作为底层基础设施，任何使用 Kafka 作为消息总线的应用程序都可以自动集成

# 设置 Kafka Connect 集成既快速又简单

我们来举一个具体的例子。一个常见的集成场景是这样的:您有两个 SQL 数据库，您需要用另一个数据库中的信息更新其中一个数据库。例如，当订单的状态在您的履行系统中发生变化时，您可能需要更新内部 CRM 数据库来反映这一变化。下面是具体的场景:

*   每当完成数据库中的状态表改变时
*   将更改写入 CRM 数据库中的 ORDER_STATUS 表

为简单起见，我们假设两者都是 MySQL 数据库。其他连接器的步骤是相同的，只是配置不同。

假设您有一个带有 Kafka Connect 的工作 Kafka 集群，设置这种集成需要两个步骤:

*   创建“源-连接”以从履行数据库的状态表中读取数据
*   创建“接收器连接”以将数据写入 CRM 数据库的 ORDER_STATUS 表

下面是相应的 cURL 调用，以及对它们各自作用的解释。他们使用 [Kafka Connect REST API](https://docs.confluent.io/current/connect/references/restapi.html) 来创建源和接收器。请注意，这些调用不是特定于 Heroku 的。它们可以与任何 Kafka Connect 安装一起使用:

1.  创建源连接。

```
curl -X POST http://localhost:8083/connectors -H "Content-Type: application/json" -d '{    
  	"name": "jdbc_source_mysql_01",     
  	"config": {       
  		"connector.class": "io.confluent.connect.jdbc.JdbcSourceConnector",       
  		"connection.url": "jdbc:mysql://fulfillmentdbhost:3306/fulfillmentdb",       
  		"connection.user": "fullfilment_user",       
  		"connection.password": "<password>",       
  		"topic.prefix": "order-status-update-",       
  		"mode":"timestamp",       
  		"table.whitelist" : "fulfullmentdb.status",       
  		"timestamp.column.name": "LAST_UPDATED",       
  		"validate.non.null": false     
  	}   
}'
```

2.创建接收器连接。

```
curl -X POST http://localhost:8083/connectors -H "Content-Type: application/json" -d '{    
	"name": "jdbc_sink_mysql_01",     
	"config": {       
		"connector.class": "io.confluent.connect.jdbc.JdbcSinkConnector",       
		"connection.url": "jdbc:mysql://crmdbhost:3306/crmdb",       
		"connection.user": "crm_user",       
		"connection.password": "<password>",       
		"topics": "order-status-update-status",       
		"table.name.format" : "crmdb.order_status"    
	}   
}'
```

你完了！从这一点开始，履行数据库的状态表中的任何新条目都将自动写入 CRM 数据库的 ORDER_STATUS 表中。让我们稍微深入一下每个调用都在做什么。

## cURL 呼叫 1:连接信号源

第一个 cURL 命令告诉 Kafka Connect 使用特定类型的源连接器，即 JdbcSourceConnector，使用提供的凭证连接到位于 fulfillmentdbhost:3306/fulfillmentdb 的 MySQL 数据库。它还配置这个连接器，根据“LAST_UPDATED”列中的时间戳监视数据库中名为“status”的表中的新条目因此，每当向表中写入一个新行时，Kafka Connect 都会为每个新行自动向 Kafka 主题“order-status-update-status”中写入一条新消息。

接下来，我们需要一个 sink 连接器来监控 Kafka 主题的变化。这就是第二个 cURL 调用的作用。

## cURL 呼叫 2:连接水槽

第二个 cURL 命令创建一个接收器连接器(JdbcSinkConnector ),它连接到另一个数据库，即 crmdbhost:3306/crmdb。该连接器将监视主题“order-status-update-status”中的新消息，并将任何新消息写入 crmdb-database 中的表“order_status”。

此示例使用 JdbcSourceConnector 和 JdbcSinkConnector 连接两个关系数据库。可以安装其他类型的连接器，然后以相同的方式使用。每个连接器都有自己的文档和一组选项。在[汇合网站](https://www.confluent.io/hub/)上可以找到可用连接器的非正式列表及其文档链接。

# 更上一层楼

您已经看到了如何只用两个命令在两个 SQL 数据库之间建立连续的流集成。以类似的方式，您可以集成 Kafka Connect 为其提供连接器的任何应用程序，甚至可以编写自己的应用程序。只需设置一个源连接器和一个接收器连接器，就可以开始更新了。

但这还不是全部。一旦设置了源连接器，就可以使用同一个源来更新多个目标(“接收器”)。在我们的示例中，您可以运行第三个 cURL 命令，将所有订单状态更新写入另一个数据库。

# 结论

如果您的团队面临本文中描述的任何问题，您应该尝试 Kafka Connect。Kafka Connect 为现有的 Kafka 集群添加了一套全新的功能，从长远来看，这将使您的团队生活更加轻松。如果您目前没有运行 Kafka 集群，本文将向您展示如何在几分钟内完成设置。一旦 Kafka Connect 启动并运行，您的团队就可以专注于解决实际的业务问题。

一旦您的团队掌握了本文中描述的基本工作流，您可能还想了解 Kafka Connect 动态转换[数据的能力，这可用于解决一系列全新的问题。](https://docs.confluent.io/current/connect/transforms/index.html)
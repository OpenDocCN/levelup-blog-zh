# 如何使用 Python 和 R 访问和查询您的 Google BigQuery 数据

> 原文：<https://levelup.gitconnected.com/how-to-access-and-query-your-google-bigquery-data-using-python-and-r-e5a3f65042c7>

![](img/3aa4a09ff39cc61cf288ba4b08b0454f.png)

# 概观

在本文中，我们将看到如何使用 Python 和 R 加载 Google BigQuery 数据，然后查询数据以获得有用的见解。我们利用 [Google Cloud BigQuery](https://rudderstack.com/integration/gcp-storage/) 库来连接 BigQuery Python，而[big query](https://github.com/r-dbi/bigrquery)库用于对 r 做同样的事情

我们还研究了使用 Python/R 操作 BigQuery 数据的两个步骤:

*   连接到 Google BigQuery 并访问数据
*   使用 Python/R 查询数据

> *在本帖中，我们假设您将所有客户数据都存储在 Google BigQuery 中。*
> 
> *如果您有兴趣了解如何将数据源中的数据实时导入到 Google BigQuery 等工具和其他数据仓库解决方案中，您应该探索一下* [*客户数据基础设施工具，如 RudderStack*](https://rudderstack.com) *。*

# 计算机编程语言

Python 是最广泛使用的通用编程语言之一。由于其易用性和灵活性，它获得了很多关注和欢迎。

许多工程师和数据科学团队也更喜欢 Python，因为他们可以使用大量的库和工具来连接其他第三方系统以操作数据。

# 用 Python 连接 Google BigQuery

为了使用 Python 查询您的 Google BigQuery 数据，我们需要将 Python 客户端连接到我们的 BigQuery 实例。我们使用 Google BigQuery API 的云客户端库来实现这一点。您也可以选择使用任何其他第三方选项来连接 BigQuery 和 Python 由 *tylertreat* 开发的 [BigQuery-Python 库](https://github.com/tylertreat/BigQuery-Python)也是一个不错的选择。

我们使用 Google Cloud BigQuery 库，因为它是稳定的，并且得到了 Google 的官方支持。

> 对于这篇文章，我们假设你已经建立了一个 Python 开发环境。如果没有，我们强烈推荐你参考 [*Python 开发环境设置指南*](https://cloud.google.com/python/setup) *。*

要安装库，请从终端运行以下命令:

`pip install --upgrade google-cloud-bigquery`

接下来，我们将客户端连接到数据库。为此，您需要下载一个包含 BigQuery 服务帐户凭证的 JSON 文件。如果您没有服务帐户，[按照这个指南创建一个](https://cloud.google.com/iam/docs/creating-managing-service-accounts)，然后继续下载 JSON 文件到您的本地机器。

现在我们已经设置好了一切，我们继续初始化连接。以下 Python 代码用于实现这一目的:

```
rom google.cloud import bigquery
from google.oauth2 import service_account
credentials = service_account.Credentials.from_service_account_file(
'path/to/file.json')

project_id = 'my-bq'
client = bigquery.Client(credentials= credentials,project=project_id)
```

在上面的代码片段中，您需要指定 JSON 密钥文件的和位置，方法是将`**'path/to/file.json'**`替换为本地存储的 JSON 文件的实际路径。

> *在 Google BigQuery 中，该项目是一个顶级容器，并提供跨所有数据集的默认访问控制。*

# 使用 Python 对 BigQuery 数据执行查询

既然我们已经设置了 BigQuery 客户机并准备好使用，我们就可以对 BigQuery 数据集执行查询了。

为此，我们使用 query 方法，它将一个查询作业插入到 BigQuery 队列中。然后异步执行这些查询——也就是说，我们不指定任何超时，客户机等待作业完成。作业一完成，该方法就返回一个包含结果的`Query_Job`实例。

关于这种方法如何工作的更多细节，请参考官方文档[这里](https://googlecloudplatform.github.io/google-cloud-python/latest/bigquery/reference.html#google.cloud.bigquery.job.QueryJob)。

所需的 Python 代码如下:

```
query_job = client.query("""
   SELECT *
   FROM dataset.my_table
   LIMIT 1000 """)

results = query_job.result() # Wait for the job to complete.
```

请注意，上面的查询默认使用标准的 SQL 语法。如果您希望使用遗留 SQL，请使用下面的代码:

```
job_config.use_legacy_sql = True
query_job = client.query("""
   SELECT *
   FROM dataset.my_table
   LIMIT 1000""", job_config = job_config)

results = query_job.result() # Wait for the job to complete.
```

# 稀有

r 是许多数据科学家和工程师使用的 Python 的流行替代品。如果详细的、有条理的统计数据分析是你的目标，那么很少有语言像 r 一样好。

当使用 Google BigQuery 时，R too 提供了一个健壮且易于使用的库来进行数据操作和查询。在这篇文章中，我们使用了由首席科学家 Hadley Wickham 创建和维护的库。

对于本节，我们假设您已经建立了 R 开发环境。如果没有，您可以随时[按照本](https://rstudio-education.github.io/hopr/starting.html) [指南](https://rstudio-education.github.io/hopr/starting.html)设置 RStudio。

# 用 R 连接到 Google BigQuery

为了安装 bigrquery，我们在 R 控制台中运行以下命令:

`install.packages("bigrquery")`

就是这样！我们准备好了。

与 Python 一样，我们需要授权我们的 R 客户机访问 Google 云服务。根据 bigrquery 的[文档](https://github.com/r-dbi/bigrquery#authentication-and-authorization)，我们将按照 R 控制台中的提示打开授权 URL 并将代码复制到控制台中。

> *注意，这个授权只需要做一次。后续请求将自动刷新访问凭证。*

# 用 R 对 BigQuery 数据执行查询

要使用 R 对 BigQuery 数据执行查询，我们将遵循以下步骤:

*   从 Google Cloud 控制台指定项目 ID，就像我们使用 Python 一样。
*   形成查询字符串来查询数据。
*   用您的项目 ID 和查询字符串调用`query_exec`。

实现这一点的代码如下:

```
#import library
library(bigrquery)

#Your project ID here
project_id <- "your-project-id" # Your project ID goes here

#Sample query
sql_string <- "SELECT * FROM dataset.my_table LIMIT 1000"

#Execute the query and storing the result
query_results <- query_exec(sql_string, project = project_id, useLegacySql = FALSE)
```

与 Python 一样，如果您希望使用遗留 SQL 执行查询，可以在您的`query_exec`函数中将`useLegacySql`改为`TRUE`。

# 结论

在这篇文章中，我们看到了使用 Python 和 r 来访问和操作存储在 Google BigQuery 中的数据是多么简单和直接。

这两种语言使得在这些数据的基础上建立一个统计模型变得非常容易，这可以用于各种目的——理解客户的应用内行为，预测流失率等。只是一些使用案例。

与使用其他介质(如 CSV 文件)相比，使用数据库存储数据有很大的优势。除了可以灵活地存储各种数据类型的大量数据之外，您还可以利用 SQL 的强大功能来生成复杂的查询，从而获得有意义的见解。

虽然这篇文章关注的是 Google BigQuery，但是使用 R 和 Python 来使用任何其他数据库工具都同样容易。唯一的区别是用于连接数据库的 Python/R 库的选择。

# 今天试试方向舵堆栈

开始构建更智能的客户数据管道。使用你所有的客户数据。回答更难的问题。向您的整个客户数据堆栈发送见解。今天就报名参加[舵栈云免费](https://app.rudderlabs.com/signup?type=freetrial)。

加入我们的 [Slack](https://resources.rudderstack.com/join-rudderstack-slack) 与我们的团队聊天，查看我们在 [GitHub](https://github.com/rudderlabs) 上的开源报告，订阅[我们的博客](https://rudderstack.com/blog/)，在社交上关注我们: [Twitter](https://twitter.com/RudderStack) 、 [LinkedIn](https://www.linkedin.com/company/rudderlabs/) 、 [dev.to](https://dev.to/rudderstack) 、 [Medium](https://rudderstack.medium.com/) 、 [YouTube](https://www.youtube.com/channel/UCgV-B77bV_-LOmKYHw8jvBw) 。不要错过任何更新。[立即订阅](https://rudderstack.com/blog/)我们的博客！

*原载于 https://rudderstack.com*[](https://rudderstack.com/blog/how-to-access-and-query-your-bigquery-data-using-python-and-r)**。**
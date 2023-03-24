# ETL 与 ELT |用示例解释

> 原文：<https://levelup.gitconnected.com/etl-versus-elt-explained-with-examples-part-1-b33c83d89969>

![](img/3cb842e8a1c7645d4c485014d5dff16a.png)

克里斯托夫·迪翁在 [Unsplash](https://unsplash.com/s/photos/pipeline?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

谈到数据工程，有两种常见的方法来转换和加载数据。第一种方法是 ETL(提取转换负载)。另一种称为 ELT(提取、加载、转换)。人们很容易注意到缩写 TL 和 LT 中的转换和加载步骤被交换了。然而，这种措辞上的小变化在数据处理中有更大的影响。为了解释这一点，我们先来快速解释一下 ETL。

ETL 代表提取、转换和加载。ETL 是一种高级数据处理和加载架构。ETL 的第一步是 Extract，它指的是从源中提取数据的过程。这可以通过点击 API 来接收数据，从 SFTP 或 S3 获取数据，或者从 URI 下载数据。下一步是转换，即对数据进行数据转换，以使其符合、清理并聚合到最终阶段。这可能涉及修改数据格式、将 JSON 转换为 CSV，或者其他修改，比如将数据集连接到其他数据源。最后，最后一步是加载，这是指将完全转换的数据加载到数据库中。

# 简化示例——冰淇淋店的数据 ETL

在我们的简化示例中，我们将构建一个 ETL 流程，为一家冰淇淋店提取和收集数据。我们需要点击一个 API 来提取关于冰淇淋店的数据。我们需要将数据汇总到存储级别，并将最终结果存储在我们的数据库中。

## 来自 API 的源数据

```
[
 {
  transaction_id:"1"
  store:"10",
  date:"05/01/2020 10:05:01"
  price:100.5
 },
 {
  transaction_id:"2"
  store:"10",
  date:"05/01/2020 10:06:02"
  price:120.5
 },
]
```

## **提取步骤-**

我们点击 API 来获得 JSON 响应。然后，我们使用 pandas 展平 JSON 响应，并将结果写入 CSV。这是一个简化的示例，在现实生活中，还会应用模式验证、数据质量和数据类型检查。

```
import csv
import pysftp
import store_api
import pandas as pddef get_data_from_api(): username = os.getenv('username')
    password = os.get('password') 
    response=  requests.get('[https://api.github.com/user'](https://api.github.com/user'), auth=(username, password)) json_result = request.json()
    return json_result def flatten_and_write_data():
    current_datetime = get_current_datetime()
    flatten_data = pd.json_normalize(data)
    flatten_data.to_csv("source_file_{}".format(current_datetime))
```

## **变换步骤-**

在我们的最终表格中，我们需要每个商店的总销售额。为此，我们需要执行一个聚合步骤。我们通过创建一个 python 脚本并使用 Pandas 库来实现这一点。我们的脚本将执行以下操作-

1.将日期时间转换为日期

2.按日期和商店计算总价

3.最后，我们希望将这个结果保存为扁平格式，在本例中是一个 CSV 文件。

```
import pandas as pddef agg_data()
   latest_file = get_latest_file()
   data['date'] = data['datetime'].dt.date
   select_col=['store','date','price']
   new_df = data[select_col].groupby([data["store"],data["date"]]).sum()
   new_df.rename(columns={"price":"revenue"},inplace=True)
   agg_df.to_csv("stage_file_{}".format(current_date))
```

## 中间 CSV 数据

```
+----------------+-------+---------------------+-------+
| transaction_id | store |      datetime       | price |
+----------------+-------+---------------------+-------+
|              1 |    10 | 05/01/2020 10:05:01 | 100.5 |
|              2 |    10 | 05/01/2020 10:06:02 | 120.5 |
+----------------+-------+---------------------+-------+
```

## **加载步骤-**

最后一步是将转换后的数据加载到 datbasae。我们希望将数据加载到表名为 **ice_cream_store_rev** 的模式 f**final**中

```
from sqlalchemy import create_engine
def load_data():
   username = os.getenv('username')
   password = os.get('password')
   database = "target_database"
   engine = create_engine('postgresql://{0}:{1}[@localhost](http://twitter.com/localhost):5432/{2}'.format(username,password,database))
   df = pd.read_csv("source_file_{}")
   df.to_sql('ice_cream_store_rev',schema="final", engine)
```

## 最终数据

```
+-------+------------+---------+
| store |    date    | revenue |
+-------+------------+---------+
|    10 | 05/10/2020 |  220.10 |
+-------+------------+---------+
```

## ETL 步骤-

```
## Pull Data from API
data = get_data_from_api()## Output Data to CSV
write_data(data)## Transformation to Aggregate Data
run_transform()## Load Data to DB
load_data()
```

最后，我们将把结果加载到目标表中。在模式 final 中，目标表的名称是- **ice_cream_store_rev**

# 提取负载转移

在 ELT(提取、加载、转换)中，转换和加载组件是翻转的。ELT vs ETL 背后的主要思想是，ELT 中的转换发生在目标数据库上，而不是通过脚本或工具进行批处理。

在我们的例子中，我们需要修改转换代码来对数据库执行转换。为此，我们在 SQL 中转换了熊猫转换语句。然后，我们在数据库上发出这个查询来创建最终的表。

我们将需要修改我们的加载代码来加载源文件而不是最终文件。

```
from sqlalchemy import create_engine
def load_data():
   username = os.getenv('username')
   password = os.get('password')
   database = "target_database"
   engine = create_engine('postgresql://{0}:  {1}[@localhost](http://twitter.com/localhost):5432/{2}'.format(username,password,database))
   df = pd.read_csv("source_file_{}")
   df.to_sql('ice_cream_store',schema="stage", engine)def run_transform()
   query_stmt = """
     CREATE TABLE final.ice_cream_store_rev AS
     SELECT
     store_name,EXTRACT(month from datetime) as date,sum(revenue) AS     revenue
     FROM stage.ice_cream_store;
   """def execute_query(connection, query_stmt):
     cursor = connection.cursor()
     try:
         cursor.execute(query_stmt)
         return cursor
     except Exception as e:
         print("Following Query Failed: {}".format(query_stmt))
         raise Exception(str(e))conn = psycopg2.connect(dbname="test", user="postgres",     password="secret")execute_query(conn, query_stmt)
```

## ELT 步骤-

```
### Original Extract 
get_data_from_api()## Output Data as CSV
write_data()## New Load Step
load_data()## New Transform Step issuing SQL statement in database. 
run_transform()
```

通常，转换步骤不像用 group by 汇总数据那样简单。这是有意保持简单的，以使人们了解这两个过程之间的主要区别。还有许多数据清理步骤和数据丰富，如填充空白或执行与其他数据集的连接。

这些清洁步骤会使直接从提取到装载的过程变得困难。如果数据的结构不正确，许多数据库会拒绝加载。根据数据的非结构化和/或不干净程度，可能会有一个中间步骤来执行多次转换以清理数据。这些文件问题的例子包括 CSV 中的引号字符、日期格式不正确、数据类型不兼容等..

幸运的是，许多数据库工具，如 Redshift 和 Snowflake，都内置了处理这些清理步骤的选项，可以在加载时自动清理数据集。

在我们的第二篇文章中，我们将评估 ETL 和 ELT 方法之间的权衡。我们将讨论哪些场景会受益于 ETL 或 ELT，以及如何在现实世界中实现这些架构的建议。

(第 2 部分我们深入探究原因)
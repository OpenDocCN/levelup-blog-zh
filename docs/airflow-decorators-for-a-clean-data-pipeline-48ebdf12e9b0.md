# 气流:干净数据管道的装饰者

> 原文：<https://levelup.gitconnected.com/airflow-decorators-for-a-clean-data-pipeline-48ebdf12e9b0>

## 如何使用气流装饰器抽象出数据管道中的复杂性

想象一下这样一个场景，您必须运行多个日常作业来从数据湖中提取数据，对它们进行预处理，并将清理后的数据集存储到专用数据库中。如果我们必须每天运行流水线，不断检查可能的错误，那将是非常乏味的。这就是 Airflow 派上用场的地方:它为您提供了自动构建和监控多个数据管道的所有工具。

![](img/7b52c64e6847c461e4fa4550fa27bc89.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Pietro Jeng](https://unsplash.com/@pietrozj?utm_source=medium&utm_medium=referral) 拍摄

这篇文章是我最近开始的与气流相关的系列文章的一部分:

```
1\. [Airflow for Data Pipeline 101](/airflow-for-data-pipeline-101-7a42cc28cf3a)
2\. **Airflow: Decorators for a Clean Data Pipeline**
3\. [Unit Testing Your Airflow Data Pipeline](/airflow-unit-testing-for-bug-free-data-pipeline-d96f87a3cc8f)
4\. *TBD...*
```

在这里，我将讨论装饰者的主题，以及如何在 Airflow 中使用它们来抽象出构建数据管道时的许多 Airflow 复杂性。

```
**Table of Content:** 1\. Airflow 101
2\. Defining an Extract-Transform-Load (ETL) data pipeline
3\. Airflow before decorators
4\. Airflow after decorators
```

# 气流 101

在我们开始之前，请确保您的机器中的气流设置正确，并且您对气流的工作原理有一个基本的了解。下面的帖子将指导你完成安装和设置过程。

[](/airflow-for-data-pipeline-101-7a42cc28cf3a) [## 数据管道 101 的气流

### 如何使用 Apache Airflow 设置自动化数据管道

levelup.gitconnected.com](/airflow-for-data-pipeline-101-7a42cc28cf3a) 

正确设置后，您将能够查看 Airflow web GUI，如下图所示。该页面将突出显示您的所有管道、它们的所有者、时间表以及用于监控系统健康状况的相关诊断。

![](img/53dddb9a7468b32eaebf5b32b17eec57.png)

气流网络用户界面(图片由作者提供)

# 定义 ETL 数据管道

提取-转换-加载(ETL)是一个数据摄取管道，它组合来自多个来源的数据，应用转换，并将它们加载到一致的数据仓库或数据库中。

在本教程中，我们将定义一个简单的 ETL 管道，如下所示:

1.  **提取**一个 JSON 文件，
2.  **转换**数据并返回总行数，
3.  **加载**转换后的数据并打印出来(而不是存储到数据库/仓库中)

# **装修工前的气流**

在传统的气流管道中，上述过程看起来会像这样:

**第一步:导入库**

```
import datetime as dt
import jsonfrom airflow import DAG
from airflow.operators.python_operator import PythonOperator
```

**第二步:建立默认设置**

```
default_args = {
    'owner': 'me'
}
```

**第三步:定义一个有向无环图(DAG)任务**

DAG 到底是什么？

*   **定向**是指按预定顺序发生的有序任务
*   **非循环的**意味着一个终止的任务，没有进入永久循环的可能性
*   **图**表示可以建立多对多任务关系的结构

```
with DAG('airflow_tutorial_v01',
         default_args=default_args,
         schedule_interval='0 * * * *',
         ) as dag:

    extract = PythonOperator(task_id='extract',
                             python_callable=extract)

    transform = PythonOperator(task_id='transform',
                               python_callable=transform)

    load = PythonOperator(task_id='load',
                          python_callable=load)
```

**第四步:定义 ETL 函数**

for extract()函数

```
def extract():
    order_data_dict = json.loads(data_string)

    return order_data_dict
```

对于 transform()函数，我们有

```
def transform(order_data_dict: dict):
    return {"total_count": len(order_data_dict)}
```

对于 load()函数，我们有

```
def load(total_count: int):
    print(f"Total order value is: {total_count}")
```

**第五步:连接管道**

在 DAG 函数之外，我们可以如下连接管道:

```
extract >> transform >> load
```

嗯，这些步骤看起来不错，但如果我们的管道变得复杂，它们可能会很乏味。气流提供的关键抽象技术之一是**装饰者**。所以现在让我们看看如何使用 decorators 来简化上面的步骤。

# **装修工后的气流**

现在让我们使用 decorators 重构上面的管道。

Python 中的 decorators 是什么？简单地说，decorators 使我们能够包装一个函数并扩展它的功能。您可以在此[链接](https://www.geeksforgeeks.org/decorators-in-python/)中找到更多详细信息。

**第一步:导入库**

```
import json

from airflow.decorators import dag, task
```

**第二步:建立默认设置**

```
default_args = {
    'owner': 'me'
}
```

**第三步:定义 DAG 和函数**

```
@dag(default_args=default_args, tags=['etl'])
def etl_pipeline():

    @task()
    def extract():
        return json.loads(data_string) @task(multiple_outputs=True)
    def transform(order_data_dict: dict):
        return {"total_count": len(order_data_dict)} @task()
    def load(total_order_value: float):
        print(f"Total order value is: {total_count}") extracted = extract()
    transformed = transform(extracted)
    load(transformed["total_count"])
```

**第四步:连接管道**

```
etl_dag = etl_pipeline()
```

因此，我们没有为 DAG 和不同的 ETL 步骤定义一个单独的函数，而是使用**decorator**将所有东西放在一个完整的序列中。不仅如此，对于工程师来说，代码看起来更实用、更自然。

# 结论

我们已经成功地抽象出使用 decorators 在 Airflow 中构建数据管道的复杂性。Decorators 允许你以一种有序且易处理的方式编写数据管道函数。不仅如此，decorators 还允许您为相同数量的功能编写更少的代码！和往常一样，如果你喜欢这篇文章，我会写更多的文章详细介绍气流中更先进的概念。

*请订阅我的电子邮件简讯:*[*https://tinyurl.com/2npw2fnz*](https://tinyurl.com/2npw2fnz)*在那里，我会定期用通俗易懂的英语和漂亮的可视化方式总结编程技巧和 AI 研究论文。*
# 如何在 Jupyter 实验室中运行 SQL 查询

> 原文：<https://levelup.gitconnected.com/how-to-run-sql-queries-in-jupyter-lab-1cbb1e3b1c04>

## 在这篇小文章中，我将说明如何在 jupyter lab 中编写 SQL 查询，执行数据操作以及可视化。

![](img/a687eb35a56e996f35752b7e2c951c47.png)

约翰·巴克利普在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

最近，我的大部分工作都集中在在 MySQL work bench 中编写 MySQL analytics 查询，所以当我完成我的查询时，如果我想执行一些可视化，我会下载 csv 文件中的查询结果，将其加载到 pandas 中并执行可视化。所以我想有没有更好的方法？的确有。输入`ipython-sql.`

> *在这种情况下，我将演示如何连接 MySQL，但连接其他 SQL 引擎也非常相似*

*点击此链接找到 jupyter 笔记本，此外，这篇文章最好用笔记本来理解。*

链接为: [*本文为*](https://gist.github.com/kshirsagarsiddharth/c03722c2da91a025987ae02d84504016)Jupyter 笔记本

1.  ***步骤一:安装***

要安装 ipython-sql，只需打开命令行并键入

`pip install ipython-sql`

`pip install mysqlclient`

**2*2。定义连接字符串:*** 为了连接到数据库，请确保您的数据库服务器正在运行，如果您是本地主机，请从命令行启动您的 sql server。下面是建立连接的示例连接字符串。

`<engine>://username:passsword@hostname/dbname`

在我们的例子中，我们连接到 MySQL，因此我们的连接字符串如下所示

`mysql://username:passsword@hostname/dbname`

## 执行简单的多行选择查询

```
**%%sql 
select * from orders
limit 10;*** mysql://root:***@localhost/mavenfuzzyfactory
10 rows affected.
```

![](img/2fbf91e910282dda3663dd480fcb6211.png)

## 将结果存储在变量中

```
**results = %sql select * from orders limit 4;
results*** mysql://root:***@localhost/mavenfuzzyfactory
4 rows affected.
```

![](img/0efc95b16f8a3fc8a5f46198f9be0ed7.png)

## 转换为数据帧

```
**results_df = results.DataFrame()
results_df.head()**
```

![](img/050f69ab300ebd435d4ebe4220fec5bf.png)

## 转换成 python 字典

```
**# Returns a single dict built from the result set**
**results.dict()**{'order_id': (1, 2, 3, 4),
 'created_at': (datetime.datetime(2012, 3, 19, 21, 12, 46),
  datetime.datetime(2012, 3, 20, 5, 57, 37),
  datetime.datetime(2012, 3, 20, 17, 14, 45),
  datetime.datetime(2012, 3, 20, 20, 11, 45)),
 'website_session_id': (20, 104, 147, 160),
 'user_id': (20, 104, 147, 160),
 'primary_product_id': (1, 1, 1, 1),
 'items_purchased': (1, 1, 1, 1),
 'price_usd': (Decimal('49.99'),
  Decimal('49.99'),
  Decimal('49.99'),
  Decimal('49.99')),
 'cogs_usd': (Decimal('19.49'),
  Decimal('19.49'),
  Decimal('19.49'),
  Decimal('19.49'))}**# Iterator yielding a dict for each row
for elem in results.dicts():
    print(elem)**{'order_id': 1, 'created_at': datetime.datetime(2012, 3, 19, 21, 12, 46), 'website_session_id': 20, 'user_id': 20, 'primary_product_id': 1, 'items_purchased': 1, 'price_usd': Decimal('49.99'), 'cogs_usd': Decimal('19.49')}
{'order_id': 2, 'created_at': datetime.datetime(2012, 3, 20, 5, 57, 37), 'website_session_id': 104, 'user_id': 104, 'primary_product_id': 1, 'items_purchased': 1, 'price_usd': Decimal('49.99'), 'cogs_usd': Decimal('19.49')}
{'order_id': 3, 'created_at': datetime.datetime(2012, 3, 20, 17, 14, 45), 'website_session_id': 147, 'user_id': 147, 'primary_product_id': 1, 'items_purchased': 1, 'price_usd': Decimal('49.99'), 'cogs_usd': Decimal('19.49')}
{'order_id': 4, 'created_at': datetime.datetime(2012, 3, 20, 20, 11, 45), 'website_session_id': 160, 'user_id': 160, 'primary_product_id': 1, 'items_purchased': 1, 'price_usd': Decimal('49.99'), 'cogs_usd': Decimal('19.49')}
```

## 获取列名

```
**results.field_names**['order_id',
 'created_at',
 'website_session_id',
 'user_id',
 'primary_product_id',
 'items_purchased',
 'price_usd',
 'cogs_usd']
```

## 获取结果索引

```
**results.index**<function ResultSet.index(value, start=0, stop=9223372036854775807, /)>
```

## 使用`<

```
**%%sql 
result << 
SELECT 
    * 
FROM orders 
limit 5*** mysql://root:***@localhost/mavenfuzzyfactory
5 rows affected.
#Returning data to local variable resultresult
```

![](img/bf4394aa8034036a9d4faef85c9c9684.png)

## 变量替换(在 where 子句中使用变量)

```
**item_value = 2****%%sql 
result << 
SELECT user_id 
FROM orders 
WHERE items_purchased = :item_value
LIMIT 4*** mysql://root:***@localhost/mavenfuzzyfactory
4 rows affected.
Returning data to local variable resultresult
```

![](img/0ea6a1c472638180b64dbd42598f2bfb.png)

user _ id 116186 116266 116378 103416

## 形象化

1.找出数据集中 1 件商品和 2 件商品的价格

```
 **%%sql
result << 
SELECT items_purchased,
       AVG(price_usd) as price
FROM orders
GROUP BY items_purchased;****result.DataFrame().style.background_gradient()**
```

![](img/0b52a632263855b696e510afafb5b56f.png)

查找网站会话在上午、下午、晚上和夜间的分布

2.查找网站会话在上午、下午、晚上和夜间的分布

![](img/2b363d265b37a4d3664307c7212dad05.png)

```
result
```

![](img/b7340f2154e6106d1fa1cc03e1730ae6.png)

配这个的笔记本。
# 比熊猫还快:极地(下)

> 原文：<https://levelup.gitconnected.com/deep-dive-polars-part-2-alternatives-to-pandas-cc9efa881f72>

![](img/8a5168853dc236173038606c61cd0e65.png)

在上一篇文章[比熊猫还快:极地者(第一部分)](https://medium.com/@thinkbot/deeper-dive-alternatives-to-our-beloved-pandas-polars-part-1-e34d31398006)中我们注意到:

*   Polars 确实大大减少了将数据读入 Polars 数据帧的时间。
*   Polars 允许惰性操作，因此代码只有在被明确要求执行时才会被执行

在今天的实验中，我们将快速检查 polars 对象对内存消耗的影响。在文章的最后，我们将总结什么样的 Polars 数据帧方法消耗最少的内存，并且在功能上比默认方法更好或相当。

所以，让我们开始陈述吧！

我们的设置和以前一样

```
**!** python **--**version
Python 3.9.4
```

# 安装 polars

```
**!** pip install polars
```

导入库

```
**import** polars **as** pl
```

# 获取数据

像往常一样，我们从使用熊猫读取 2021 年 1 月的纽约出租车数据开始。首先，我们从亚马逊 S3 链接下载数据到本地存储。

```
**!** wget "https://s3.amazonaws.com/nyc-tlc/trip+data/yellow_tripdata_2021-01.csv"**!** wc **-**l yellow_tripdata_2021**-**01.csv--2022-03-11 07:32:13--  [https://s3.amazonaws.com/nyc-tlc/trip+data/yellow_tripdata_2021-01.csv](https://s3.amazonaws.com/nyc-tlc/trip+data/yellow_tripdata_2021-01.csv)
Resolving s3.amazonaws.com (s3.amazonaws.com)... 3.5.0.130
Connecting to s3.amazonaws.com (s3.amazonaws.com)|3.5.0.130|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 125981363 (120M) [text/csv]
Saving to: ‘yellow_tripdata_2021-01.csv.3’

yellow_tripdata_202 100%[===================>] 120.14M  27.5MB/s    in 7.6s    

2022-03-11 07:32:21 (15.9 MB/s) - ‘yellow_tripdata_2021-01.csv.3’ saved [125981363/125981363]

 1369766 yellow_tripdata_2021-01.csv
```

![](img/dbbf95eec90d5587ff9e76ce4570053f.png)

鸣谢:锡德·巴拉钱德朗(unsplash)

# 让我们先按熊猫的方式做，然后得到基准

```
**import** pandas **as** pd
**import** datetime
```

%% time 是 jupyter notebook 中的一个神奇动作，可以计算单元格内所有执行的时间。

```
**%%**time
data **=** pd.read_csv("yellow_tripdata_2021-01.csv", low_memory**=False**)CPU times: user 1.63 s, sys: 195 ms, total: 1.83 s
Wall time: 1.83 s
```

让我们编写一个函数来获取本地或全局环境中任何对象的大小(可读格式)

```
**from** sys **import** getsizeof
**def** whats_my_footprint(x):
    **if** 'x' **in** locals() **or** 'x' **in** globals():
        size **=** round(getsizeof(data) **/** 1024 **/** 1024,2)
        print(f'{size} MB in the memory')
    **else**:
        print('are you sure the object exists?')
```

Pandas dataframe 最终在内存中占用了将近 430MB 的数据，如下所示。

```
whats_my_footprint(data)
428.64 MB in the memory
```

让我们来看看数据类型。

```
display(data.dtypes)VendorID                 float64
tpep_pickup_datetime      object
tpep_dropoff_datetime     object
passenger_count          float64
trip_distance            float64
RatecodeID               float64
store_and_fwd_flag        object
PULocationID               int64
DOLocationID               int64
payment_type             float64
fare_amount              float64
extra                    float64
mta_tax                  float64
tip_amount               float64
tolls_amount             float64
improvement_surcharge    float64
total_amount             float64
congestion_surcharge     float64
dtype: object
```

我们可以看到，数据中的日期不是被推断为日期，而是对象。对象是数据帧中最低效的内存。对象是“任何东西”,在这种情况下是字符串。我们需要将它们编码成底层的数字形式，以占用更少的空间，并允许将来更有效的计算。

```
**%%**time
*# creating a date parser to reformat the dates in this data*mydateparser **=** **lambda** x: datetime.datetime.strptime(x, '%Y-%m-%d %H:%M:%S')# applying the date parser to the columns 
data **=** pd.read_csv("yellow_tripdata_2021-01.csv", parse_dates**=**['tpep_pickup_datetime','tpep_dropoff_datetime'], date_parser**=**mydateparser, low_memory**=False**)display(data.dtypes)VendorID                        float64
tpep_pickup_datetime     datetime64[ns]
tpep_dropoff_datetime    datetime64[ns]
passenger_count                 float64
trip_distance                   float64
RatecodeID                      float64
store_and_fwd_flag               object
PULocationID                      int64
DOLocationID                      int64
payment_type                    float64
fare_amount                     float64
extra                           float64
mta_tax                         float64
tip_amount                      float64
tolls_amount                    float64
improvement_surcharge           float64
total_amount                    float64
congestion_surcharge            float64
dtype: objectCPU times: user 15.7 s, sys: 287 ms, total: 16 s
Wall time: 16 swhats_my_footprint(data)
250.99 MB in the memory
```

输入文件中的行数是 1，369，766。从使用默认值读取 csv 到读取文件和解析日期的操作时间猛增了 **10x** 。文件大小从**的 430MB 减少到了**的 251MB。通过分配正确的数据类型，内存占用减少了 41%。

我们仍然将**存储和转发标志**作为对象。我们可以修复它并进一步优化它。让我们看看我们能做什么。

```
**%%**time
display(data.store_and_fwd_flag.value_counts())N    1252433
Y      18980
Name: store_and_fwd_flag, dtype: int64 #Change the data type of the store flag to categorical
data.store_and_fwd_flag **=** data.store_and_fwd_flag.astype('category')display(data.dtypes)
display(whats_my_footprint(data))VendorID                        float64
tpep_pickup_datetime     datetime64[ns]
tpep_dropoff_datetime    datetime64[ns]
passenger_count                 float64
trip_distance                   float64
RatecodeID                      float64
store_and_fwd_flag             category
PULocationID                      int64
DOLocationID                      int64
payment_type                    float64
fare_amount                     float64
extra                           float64
mta_tax                         float64
tip_amount                      float64
tolls_amount                    float64
improvement_surcharge           float64
total_amount                    float64
congestion_surcharge            float64
dtype: object178.96 MB in the memory=CPU times: user 75.3 ms, sys: 3.92 ms, total: 79.3 ms
Wall time: 78.4 ms
```

因此，我们又花了几秒钟来转换一个熊猫无法正确读取的明显的分类特征，将其转换为分类形式，并将内存占用从 **251 MB 减少到 179 MB** 。使我们的总占地面积**比默认值**减少 58.3%！这是一个很大的内存浪费。

让我们看看 Polars 是怎么做的

![](img/00bf40b5ac82c3edfd2feb43216dc5e7.png)

鸣谢:汉斯-于尔根·马格尔(unsplash)

# 使用 Polar 读取数据

有两种主要方法可以将数据读入 Polar。

1.  Polars.scan_
2.  Polars.read_

Polars 可以处理 csv、ipc、parquet、sql、json 和 avro，因此我们覆盖了 99%的基础。因此，没有进一步的 adue，让我们读入 2021 年 1 月纽约出租车数据的 csv 文件。

```
df **=** pl.read_csv("yellow_tripdata_2021-01.csv")display(df.head())*# selecting all columns* data **=** df[[pl.col("*"),]]
display(type(data))
display(whats_my_footprint(data))polars.internals.frame.DataFrame
0.0 MB in the memory *# selecting all columns and converting it to arrow format* data **=** df[[pl.col("*"),]].to_arrow()
display(whats_my_footprint(data))
display(type(data))239.78 MB in the memory
pyarrow.lib.Tablepyarrow.Table
VendorID: int64
tpep_pickup_datetime: **large_string**
tpep_dropoff_datetime: **large_string**
passenger_count: int64
trip_distance: double
RatecodeID: int64
store_and_fwd_flag: **large_string**
PULocationID: int64
DOLocationID: int64
payment_type: int64
fare_amount: double
extra: double
mta_tax: double
tip_amount: double
tolls_amount: double
improvement_surcharge: double
total_amount: double
congestion_surcharge: double
```

这里需要注意的是 Polars 对象数据帧没有任何内存占用。这只是在原始框架上要完成的操作的映射。甚至使用“read_csv”(与产生 lazyframe 的“scan_csv”相比)；我们最终得到了一个大小为 0 MB 的 polars . internals . frame . data frame。

我们仍然有日期列，并且 sotLets 使用 pyarrow.compute 将 store_and_forward_flag 列转换为大字符串格式。让我们将它们转换成日期格式和分类格式

```
**from** pyarrow **import** compute **as** pc
**from** pyarrow **import** Table **as** pt**def** fix_datetime(x, column_name):
    replacement **=** pc.strptime(x.column(column_name), format**=**'%Y-%m-%d %H:%M:%S', unit**=**'s')
    position **=** x.column_names.index(column_name)
    x **=** x.set_column(position, column_name, replacement)
    **return** xdata **=** fix_datetime(data, 'tpep_pickup_datetime')
data **=** fix_datetime(data, 'tpep_dropoff_datetime')whats_my_footprint(data)
184.03 MB in the memorydata.schema
VendorID: int64
tpep_pickup_datetime: timestamp[s]
tpep_dropoff_datetime: timestamp[s]
passenger_count: int64
trip_distance: double
RatecodeID: int64
store_and_fwd_flag: **large_string** PULocationID: int64
DOLocationID: int64
payment_type: int64
fare_amount: double
extra: double
mta_tax: double
tip_amount: double
tolls_amount: double
improvement_surcharge: double
total_amount: double
congestion_surcharge: double
```

新的 pyarrow 模式看起来像是我们已经将 large_String 对象固定到了时间戳中。大小是 184mb！伟大的

现在让我们将 large_String 转换为分类特征。太好了！但它是否改变了内存占用。

```
**def** fix_categories(x, column_name):
    replacement **=** x.column(column_name).dictionary_encode()
    position **=** x.column_names.index(column_name)
    x **=** x.set_column(position, column_name, replacement)
    **return** xdata **=** fix_datetime(data, 'store_and_fwd_flag')whats_my_footprint(data)
184.03 MB in the memorydata.schemaVendorID: int64
tpep_pickup_datetime: timestamp[s]
tpep_dropoff_datetime: timestamp[s]
passenger_count: int64
trip_distance: double
RatecodeID: int64
store_and_fwd_flag: dictionary<values=large_string, indices=int32, ordered=0>
PULocationID: int64
DOLocationID: int64
payment_type: int64
fare_amount: double
extra: double
mta_tax: double
tip_amount: double
tolls_amount: double
improvement_surcharge: double
total_amount: double
congestion_surcharge: double
```

让我们改变我们减少了多少内存。等待..它没有减少内存占用。在将大字符串列特性转换为分类特性时，Pandas 从 **251 MB 缩减到了 179 MB** 。但是 Polars/pyarrow 没有。这意味着 arrow 中的 large_string 已经被索引了(不像 Pandas，它存储为未索引的对象！！).

好了，现在我们可以检查最后一次尝试，在读取 Polars 中的数据时指定数据类型是否会改变什么。(直到现在，我们都在看箭头数据帧)。

# 具有明确数据类型说明的极坐标

```
**%%**timedf **=** pl.scan_csv("yellow_tripdata_2021-01.csv")
df **=** df.with_columns(
[
pl.col("tpep_pickup_datetime").str.strptime(pl.Datetime, '%Y-%m-%d %H:%M:%S'),
pl.col("tpep_dropoff_datetime").str.strptime(pl.Datetime, '%Y-%m-%d %H:%M:%S')
]).collect()
display(df.schema){'VendorID': polars.datatypes.Int64,
 'tpep_pickup_datetime': polars.datatypes.Datetime,
 'tpep_dropoff_datetime': polars.datatypes.Datetime,
 'passenger_count': polars.datatypes.Int64,
 'trip_distance': polars.datatypes.Float64,
 'RatecodeID': polars.datatypes.Int64,
 'store_and_fwd_flag': polars.datatypes.Utf8,
 'PULocationID': polars.datatypes.Int64,
 'DOLocationID': polars.datatypes.Int64,
 'payment_type': polars.datatypes.Int64,
 'fare_amount': polars.datatypes.Float64,
 'extra': polars.datatypes.Float64,
 'mta_tax': polars.datatypes.Float64,
 'tip_amount': polars.datatypes.Float64,
 'tolls_amount': polars.datatypes.Float64,
 'improvement_surcharge': polars.datatypes.Float64,
 'total_amount': polars.datatypes.Float64,
 'congestion_surcharge': polars.datatypes.Float64}CPU times: user 1.01 s, sys: 262 ms, total: 1.27 s
Wall time: 536 ms whats_my_footprint(df)
**184.03 MB in the memory**
```

现在你一定已经注意到北极星不占任何空间。然而，一旦转换为熊猫或箭头或任何其他实现的格式，它们将占用与任何其他工具相同的预期空间。主要的好处是速度和灵活性。

# 结论

Polars 仅仅是一个编排层，它做了许多智能的事情(查看文档，网址为[https://pola-RS . github . io/polars-book/user-guide/optimizations/lazy/intro . html](https://pola-rs.github.io/polars-book/user-guide/optimizations/lazy/intro.html)，了解谓词、projunction 下推 API 和其他优化)。Polars 对象就像实际执行的地图。除非有必要，否则它不会在内存中创建一个多余的对象，这最终会节省大量的时间。

Polars 的核心是一个基于 Apache arrow 的柱状框架。它智能地执行功能，以节省时间和内存。最终，您需要将 Polars.dataframe 转换为 Pandas。Dataframe 或任何您想要保存数据的对象，并“实现”Polars 智能排序和编排的更改。

快速注意:to_pandas() broken (fix) Polars 安装不会将 pyarrow 安装为依赖项。我强烈建议使用 pip install pyarrow 将 pyarrow 作为一个单独的安装来安装。pyarrow 之后，您需要重新安装/升级 polars。这样，它将识别预先存在的 pyarrow 安装，并允许您做许多事情，如调用函数 *pl.dataframe.to_pandas()* 。这种差异是因为 polars 内部使用 pyarrow 任务批处理(性能的一个原因)。

下一次，我们将更深入地研究在做一些我们通常最终会做的事情时，极地动物与熊猫相比效率如何。我们将计时并检查每一步的足迹(在处理过程中在内存中),以给出完整的图片。

在那之前，继续编码吧！

如果这篇文章对你有帮助

请通过按下喜欢按钮来分享一些爱。

请 [***成为会员***](https://ithinkbot.com/membership) 和[***订阅***](https://ithinkbot.com/subscribe) 获取更简洁的教程。

![](img/6a3a73d6912e28e6fd2e9c505ea88e2f.png)

鸣谢:布雷特·乔丹(unsplash)
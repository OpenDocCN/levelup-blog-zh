# 创建需求销售预测模型并最小化误差

> 原文：<https://levelup.gitconnected.com/create-a-demand-sales-forecast-model-and-crush-the-error-9fe1653d9abf>

Python 的循序渐进教程。

![](img/e6b17b75b910d121999fdde7ddc766ab.png)

最小化成本函数

**销售或需求预测是大量公司(从初创公司到跨国公司)数据科学/分析部门的优先事项**。退一步说，这个领域的专家在 T4 供应不足。即使减少很小的误差，也能在收入或节约上产生巨大的差异。

在本文中，我们将用真实数据做一个简单的销售**预测模型**，然后通过使用 Python 寻找相关特性来改进它。

# 我们要做什么

*   第一步:定义并理解**数据**和**目标**
*   第二步:制作一个**简单预测模型**
*   第三步:增加**新的财务指标**进行改进
*   步骤 4:分析**结果**

# 第一步。定义并理解数据和目标

在本文中，我们将使用沃尔玛提供的真实的每周销售数据。

沃尔玛发布了包含每家**实体店**99 个**部门**(服装、电子产品、食品……)的**周销售额**以及一些其他附加功能的数据。

![](img/984c0441075d862ed7f599aab970a0a7.png)

沃尔玛数据集截图

为此，我们将**创建一个以“ *Weekly_Sales”为目标的 ML 模型***，，用前 70%的观察值进行训练，并对后 30%进行测试。

目标是**最小化未来周销售额的预测误差**。

![](img/c962c38f7315ff0309c25be9cfd9d167.png)

我们将**添加影响销售或与销售有关系的外部变量**，如**美元**指数、**石油**价格和关于沃尔玛的**新闻**。

# 第二步。制作一个简单的预测模型

首先，您需要安装 Python 3 和以下库:

```
$ pip install pandas OpenBlender scikit-learn
```

然后，打开一个 Python 脚本(最好是 Jupyter notebook)，让我们导入所需的库。

```
from sklearn.ensemble import RandomForestRegressor
import pandas as pd
import OpenBlender
import json
```

现在，让我们定义用于所有实验的方法和模型。

**首先是**，数据的*日期范围*是从 2010 年 1 月到 2012 年 12 月**。**让我们定义用于**训练**的数据的第一个 **70%** 和用于**测试**的后一个 **30%** (因为我们不希望我们的预测出现数据泄漏)。

![](img/70fa1a7ec20e4df32c14413a563fa4cd.png)

**接下来**，让我们将**标准模型**定义为具有 50 个估计量的 *RandomForestRegressor* ，这是一个相当好的选择。

最后，为了使事情尽可能简单，让我们将误差定义为误差的绝对和。

![](img/dceef16d4b0360c8d97a8871baa4d472.png)

现在，让我们把它放到一个 Python 类中。

```
class **StandardModel**:

    model = RandomForestRegressor(n_estimators=50, criterion='mse')

    def **train**(self, df, target):        
        # Drop non numerics
        df = df.dropna(axis=1).select_dtypes(['number'])   

        # Create train/test sets
        X = df.loc[:, df.columns != target].values
        y = df.loc[:,[target]].values        

        # We take the first bit of the data as test and the 
        # last as train because the data is ordered desc.
        div = int(round(len(X) * 0.29))        
        X_train = X[div:]
        y_train = y[div:]        
        print('Train Shape:')
        print(X_train.shape)
        print(y_train.shape)        
        #Random forest model specification
        self.model = RandomForestRegressor(n_estimators=50)        
        # Train on data
        self.model.fit(X_train, y_train.ravel())

    def **getMetrics**(self, df, target):

        # Function to get the error sum from the trained model        
        # Drop non numerics
        df = df.dropna(axis=1).select_dtypes(['number'])      

        # Create train/test sets
        X = df.loc[:, df.columns != target].values
        y = df.loc[:,[target]].values        
        div = int(round(len(X) * 0.29))        
        X_test = X[:div]
        y_test = y[:div]        
        print('Test Shape:')
        print(X_test.shape)
        print(y_test.shape)

        # Predict on test
        y_pred_random = self.model.predict(X_test)  

        # Gather absolute error
        error_sum = sum(abs(y_test.ravel() - y_pred_random))        
        return error_sum
```

上面我们有一个包含 3 个元素的对象:

*   **模型** (RandomForestRegressor)
*   **训练:**用数据帧和目标训练模型功能
*   **getMetrics:** 用测试数据对训练好的模型进行测试并检索错误的函数

我们将在所有实验中使用这种配置，尽管您可以根据需要修改它来测试不同的模型、优化参数、特征工程或其他任何东西。附加值将保持不变，并有可能提高。

现在，让我们得到沃尔玛的数据。你可以在这里下载‘walmartdata . CSV’[。](https://github.com/federico2001/walmart_data)

```
df_walmart = pd.read_csv('walmartData.csv')
print(df_walmart.shape)
df_walmart.head()
```

![](img/4977f3459bce2768489d64b5813d5d9c.png)

有 421，570 个观察值。如前所述，观察值是每个部门每个商店的每周销售额的记录。

让我们将数据插入到模型中，而不要篡改它。

```
our_model = StandardModel()
our_model.train(df_walmart, 'Weekly_Sales')
total_error_sum = our_model.getMetrics(df_walmart, 'Weekly_Sales')
print("Error sum: " + str(total_error_sum))***> Error sum: 967705992.5034052***
```

整个模型的所有误差总和为 **$ 967，705，992.5 美元**。

这本身没有太大的意义，唯一的参考就是那段时间所有销售额的总和**6，737，218，987.11 美元**。

由于有大量的数据，在本教程中，我们将**仅关注商店#1** ，但该方法绝对可用于所有商店。

再来看看**存储 1** 单独产生的**错误**。

```
# Select store 1
df_walmart_st1 = df_walmart[df_walmart['Store'] == 1]# Error of store 1
error_sum_st1 = our_model.getMetrics(df_walmart_st1, 'Weekly_Sales')
print("Error sum: " + str(error_sum_st1))**# > Error sum: 24009404.060399983**
```

因此，商店 1 应对**24，009，404.06 美元**误差负责，而**这个** **将是我们进行比较的阈值。**

现在，让我们按部门分解错误，以便稍后有更多的可见性。

```
error_summary = []
for i in range(1,100):
    try:
        df_dept = df_walmart_st1[df_walmart_st1['Dept'] == i]
        error_sum = our_model.getMetrics(df_dept, 'Weekly_Sales')
        print("Error dept : " + str(i) + ' is: ' + str(error_sum))
        error_summary.append({'dept' : i, 'error_sum_normal_model' : error_sum})
    except: 
        error_sum = 0
        print('No obs for Dept: ' + str(i))error_summary_df = pd.DataFrame(error_summary)
error_summary_df.head()
```

![](img/1787e75c976a4d247350acac492cb6c1.png)

现在，我们有了一个数据框架，其中包含了与阈值模型中商店 1 的每个部门相对应的错误。

让我们**提高**这些数字。

# 第三步。通过添加新闻和财务指标进行改进

让我们选择部门 1 作为一个简单的例子。

```
df_st1dept1 = df_walmart_st1[df_walmart_st1['Dept'] == 1]
```

现在，让我们搜索相交的数据集。

```
# First we need to add the **UNIX timestamp** which is the number 
# of seconds since 1970 on UTC, it is a very convenient 
# format because it is the same in every time zone in the world!df_st1dept1['timestamp'] = OpenBlender.dateToUnix(df_st1dept1['Date'], 
                       date_format = '%Y-%m-%d', 
                       timezone = 'GMT')df_st1dept1 = df_st1dept1.sort_values('timestamp').reset_index(drop = True)
```

现在，让我们在 OpenBlender 中搜索关于“商业”或“沃尔玛”的时间交叉(重叠)数据集。

**注意:**要获得您*需要的令牌*必须在 [openblender.io](https://www.openblender.io/#/welcome/or/42) 上创建一个帐户(免费)，您可以在您个人资料图标的“帐户”选项卡中找到它。

![](img/5ae067841f53ddbba55e20393640c115.png)

```
token = '**YOUR_TOKEN_HERE**'print('From : ' + OpenBlender.unixToDate(min(df_st1dept1.timestamp)))
print('Until: ' + OpenBlender.unixToDate(max(df_st1dept1.timestamp)))# Now, let's search on OpenBlender
search_keyword = 'business walmart'# We need to pass our timestamp column and 
# search keywords as parameters.
OpenBlender.searchTimeBlends(token,
                             df_st1dept1.timestamp,
                             search_keyword)
```

![](img/a089ae8d48c0f0e1636fbf7c09c9b38f.png)

搜索找到了几个数据集。我们可以看到名称、描述、url、特征，最重要的是，我们的时间交互，因此我们可以将它们混合到我们的数据集。

让我们从混合这个[沃尔玛推文](https://www.openblender.io/#/dataset/explore/5e1deeda9516290a00c5f8f6/or/42)数据集开始，寻找宣传片。

![](img/3918f090e09dfdedb91b040f7f0d260c.png)

*   *注:*我挑了这个，因为它有道理，但是你可以搜索几百个其他的。

我们可以**通过搜索按时间聚合的文本或新闻术语，将新列混合到我们的数据集**。例如，我们可以创建一个“推广”功能，其提及次数将与我们自制的 ngrams 相匹配:

```
**text_filter** = {'name' : 'promo', 
               'match_ngrams': ['promo', 'dicount', 'cut', 'markdown','deduction']}# **blend_source** needs the id_dataset and the name of the feature.blend_source = {
                'id_dataset':'**5e1deeda9516290a00c5f8f6**',
                'feature' : '**text**',
                'filter_text' : **text_filter**
            }df_blend = OpenBlender.timeBlend( token = token,
                                  anchor_ts = df_st1dept1.timestamp,
                                  blend_source = blend_source,
                                  blend_type = 'agg_in_intervals',
                                  interval_size = 60 * 60 * 24 * 7,
                                  direction = 'time_prior',
                                  interval_output = 'list')df_anchor = pd.concat([df_st1dept1, df_blend.loc[:, df_blend.columns != 'timestamp']], axis = 1)
```

timeBlend 功能的参数(你可以在这里找到文档[):](https://www.openblender.io/#/api_documentation)

*   **anchor_ts** :我们只需要发送我们的时间戳列，这样它就可以作为一个锚来混合外部数据。
*   **blend_source** :关于我们想要的特性的信息。
*   **blend _ type**:‘agg _ in _ intervals’因为我们希望对我们的每个观察进行 1 周时间间隔的聚合。
*   **inverval_size** :间隔的大小，以秒为单位(本例中为 24 * 7 小时)。
*   **方向**:‘time _ prior’因为我们希望间隔收集前 7 天的观察值，而不是转发以避免数据泄漏。

我们现在有了原始数据集，但增加了两个新列，即“推广”功能的“计数”和一个实际文本列表，以防有人想要遍历每个文本。

```
df_anchor.tail()
```

![](img/3a6c8911256497d006de5e6c55b791c6.png)

现在我们有了一个数字特征，关于我们的 ngrams 被提及的次数。如果我们知道哪个商店或部门对应于“1”，我们可能会做得更好。

让我们应用标准模型，并将误差与原始值进行比较。

```
our_model.train(df_anchor, 'Weekly_Sales')
error_sum = our_model.getMetrics(df_anchor, 'Weekly_Sales')
error_sum**#> 253875.30**
```

目前的模型有 253，975 美元的误差，而以前的模型有 290，037 美元的误差。这是一个 12%的进步。

但是这个**并不能证明什么**，可能是随机森林运气好。毕竟，原始模型是用超过 299K 的观测值训练的。目前一只**训练用 102！！**

我们也可以混合数字特征。让我们试着混合[美元指数](https://www.openblender.io/#/dataset/explore/5e91029d9516297827b8f08c)、[油价](http://5e91045a9516297827b8f5b1)和[月度消费者情绪](https://www.openblender.io/#/dataset/explore/5e979cf195162963e9c9853f)

```
**# OIL PRICE**blend_source = {
                'id_dataset':'5e91045a9516297827b8f5b1',
                'feature' : 'price'
            }df_blend = OpenBlender.timeBlend( token = token,
                                  anchor_ts = df_anchor.timestamp,
                                  blend_source = blend_source,
                                  blend_type = 'agg_in_intervals',
                                  interval_size = 60 * 60 * 24 * 7,
                                  direction = 'time_prior',
                                  interval_output = 'avg')df_anchor = pd.concat([df_anchor, df_blend.loc[:, df_blend.columns != 'timestamp']], axis = 1)**# DOLLAR INDEX**blend_source = {
                'id_dataset':'5e91029d9516297827b8f08c',
                'feature' : 'price'
            }df_blend = OpenBlender.timeBlend( token = token,
                                  anchor_ts = df_anchor.timestamp,
                                  blend_source = blend_source,
                                  blend_type = 'agg_in_intervals',
                                  interval_size = 60 * 60 * 24 * 7,
                                  direction = 'time_prior',
                                  interval_output = 'avg')df_anchor = pd.concat([df_anchor, df_blend.loc[:, df_blend.columns != 'timestamp']], axis = 1)**# CONSUMER SENTIMENT**blend_source = {
                'id_dataset':'5e979cf195162963e9c9853f',
                'feature' : 'umcsent'
            }df_blend = OpenBlender.timeBlend( token = token,
                                  anchor_ts = df_anchor.timestamp,
                                  blend_source = blend_source,
                                  blend_type = 'agg_in_intervals',
                                  interval_size = 60 * 60 * 24 * 7,
                                  direction = 'time_prior',
                                  interval_output = 'avg')df_anchor = pd.concat([df_anchor, df_blend.loc[:, df_blend.columns != 'timestamp']], axis = 1)df_anchor
```

![](img/cd9066837fc949fa487309ac6c97c8f6.png)

现在我们又有了 **6 个特征**，石油指数、美元指数和消费者情绪在 7 天间隔内的平均值，以及它们各自的计数(这在本例中是不相关的)。

让我们再运行一次那个模型。

```
our_model.train(df_anchor, 'Weekly_Sales')
error_sum = our_model.getMetrics(df_anchor, 'Weekly_Sales')
error_sum**>223831.9414**
```

现在，我们减少到 223，831 美元的错误。相对于最初的 290，037 美元，提高了 24.1%！！

# 第四步。分析结果

为了查看结果是否一致，对每个商店的前 10 个部门运行了上面的测试(我没有添加代码，因为它是一个太大的脚本)。但实际上，这是在每个商店的每个部门(前 10 个)进行的。

我根据每个添加的特性来分离实验，以测量每个特性的误差减少量:

0:原始错误

1:沃尔玛促销

2:石油价格

3:美元指数

4:消费者情绪

![](img/35d1e5f7c33eea683e74d6fd3be5b287.png)

第 4 和第 6 部门比其他部门更高级。让我们把它们拿掉，仔细看看剩下的部分。

![](img/3caebcaa5dda0410ce840edef90d6bd1.png)

我们可以看到，随着我们**增加新功能**，几乎所有部门的错误都降低了。我们还可以看到，石油指数(第三个特征)对某些部门不仅没有帮助，甚至是有害的。

在销售预测中，减少百分之几的误差就意味着巨大的收入。需要改进的 3 个主要驱动因素是:**特征工程**，模型和**参数优化**和**融合有用特征**。
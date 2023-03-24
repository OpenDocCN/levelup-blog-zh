# 使用通用语句编码器的聊天机器人诊断工具

> 原文：<https://levelup.gitconnected.com/chatbot-diagnostic-tool-using-universal-sentence-encoder-adee9a87486d>

## **话语和意图的困境**

![](img/946cd0c86bc96c27f0975aef12afe984.png)

T 意图和话语的困境 **c** 创造良好的[对话 AI](https://servisbot.com/how-it-works/) 体验在很大程度上依赖于构建意图和话语形式的对话设计。聊天机器人是通过将话语组织成意图来设计的，其中意图充当一组话语的类别标签。但是，可能会出现一些与意图和话语有关的问题，例如:

> 通常，相似的话语可能与两种不同的意图相关联。例如，“我能为我的新车申请保险索赔吗”，“我能为我的新车申请保险报价吗？”
> 
> 意图中的一些话语可能是嘈杂的或异常的。人们通常不会用“你的出生年份”来表达结婚的意图。通常的口头禅是“你多大了”、“你多大了”、“告诉我你的年龄”。
> 
> 在一个意图中，一些话语可能有许多变化，或者一些话语可能没有任何变化。上面的例子显示了对于 get_age 意图，离群值有一个变化，而普通话语有许多变化。

本文解释了使用无监督机器学习技术识别和分析意图中的这种话语以及理解重叠意图的几种方法。

## 语义话语

语义话语是指使用各种单词嵌入在矢量中表示话语。单词嵌入是表示单词向量的方式，以便从它们的向量距离或相似性中隐含地捕捉它们的语义相关性。

例如，两个单词“Bus”和“Train”属于运输，因此它们的语义相关度很接近，并且可以从这两个单词嵌入/向量的相似度/距离来计算。可以计算句子嵌入向量，对其中的所有单词向量进行平均。对于这种语义话语嵌入，可以使用各种预训练的单词向量。

然而，通用语句编码器使用深度平均神经网络对预训练的单词嵌入执行话语嵌入任务。这是代表话语向量的常用方法。话语的通用编码器表示如下所示:

![](img/79cb0f93bb28207e1c9b5f429f52cf8d.png)

**句子/话语的通用句子编码器表示**(参考文献 1)

![](img/76aaf92a123a05ed881d5dbba1020217.png)

**话语相似度图**

下面给出了这种话语的向量相似度。这两句话“你多大了？”以及“你多大了？”语义相似，因此具有高度相似性(参考文献 1)。

在下一节中，我将解释如何使用局部密度来理解使用通用编码器的意图中的内部和外部话语

# 局部异常因素(LOF):分析意图中的内部和外部话语

局部异常值因子算法是一种异常检测技术，用于估计局部密度以识别数据中的异常值(参考文献 2 和 3)。这是一种无监督的基于密度的方法，它估计给定话语或数据点的局部邻居的密度，并判断它是否低于其邻居的局部密度。在这种情况下，它被认为是一个离群值，否则它就是一个内联值。

这种方法的优点是，在估计局部异常值因子时，您只需要一些邻域，而不需要对导出数据的分布做任何假设。由于 O(n)算法(参考文献 2 和 3)，当数据集很大时，计算成本很高。然而，我们将这种算法应用于那些有意图的话语，并且通常数量不多。

它还估计每个给定话语的局部异常因子值(LOF 值),以对它们进行排序或分级，从而了解哪些是强内倾话语，哪些是强外倾话语。离群值往往具有较大的 LOF 值，而内联值往往具有较小的 LOF 值。

让我们看一个例子来理解这个概念。假设我们有如下几个句子:

```
utterances=[“how old are you”,”what is your age”,”your age please”,”my phone is good”,”your cellphone is great”]
```

通用编码器将在 512 个向量维度中表示每个话语。

```
import tensorflow_hub as hub
import ssl
from scipy.spatial import distance
from sklearn.neighbors import LocalOutlierFactor
import pandas as pd 
import operator
ssl._create_default_https_context = ssl._create_unverified_contextdef loadUniversalSentenceEncoder():
    embed = hub.load("https://tfhub.dev/google/universal-sentence-encoder/4")
    return embedembed=loadUniversalSentenceEncoder()
utterance_embeddings=embed(utterances)
utterance_embeddings=utterance_embeddings.numpy()
utterance_embeddings=utterance_embeddings.tolist()
num_utterances=len(utterance_embeddings)
```

然后，创建数据帧来计算任意两个话语之间的余弦距离，其代码如下所示

```
dataframe_dist=pd.DataFrame(index=range(num_utterances),columns=range(num_utterances))
dataframe_dist[:]=0.0
for i in range(num_utterances):
    for j in range(i+1, num_utterances):
        dist=distance.cosine(utterance_embeddings[i], utterance_embeddings[j])
        dataframe_dist[i][j]=dist
        dataframe_dist[j][i]=dist
print(dataframe_dist)
```

距离数据帧将是

```
+---+----------+----------+----------+----------+----------+
|   |    0     |    1     |    2     |    3     |    4     |
+---+----------+----------+----------+----------+----------+
| 0 |        0 | 0.198413 | 0.307763 | 0.971798 | 0.679368 |
| 1 | 0.198413 |        0 | 0.187032 | 0.897253 | 0.637271 |
| 2 | 0.307763 | 0.187032 |        0 | 0.887289 | 0.714970 |
| 3 | 0.971798 | 0.897253 | 0.887289 |        0 |  1.02424 |
| 4 | 0.679368 | 0.637271 | 0.714970 |  1.02424 |        0 |
+---+----------+----------+----------+----------+----------+
```

接下来的步骤是用预定义的距离数据帧计算每个话语的 LOF 得分。代码将是这样的

```
clf = LocalOutlierFactor(n_neighbors=2, metric='precomputed')
y_pred = clf.fit_predict(dataframe_dist)
negative_outlier=clf.negative_outlier_factor_
lof=[-x for x in negative_outlier]
index=0
for ut in utterances:
    print("{}: LOF score is {}".format(ut,lof[index]))
    index=index+1
```

上述代码的输出将是

```
how old are you: LOF score is 0.9111736723327293
what is your age: LOF score is 1.2160311655250895
your age please: LOF score is 0.9111736723327293
my phone is good: LOF score is 3.2123746351649505
where do you stay: LOF score is 2.370095692078931
```

下一节将解释如何在一个意图中选择原型或引导话语，以理解每个意图中存在的话语的不同变体。

# 原型/领导话语

原型或引导话语是代表话语，其潜在地代表每个主题，其中意图中的话语被多个主题覆盖。

这种方法是一种增量单扫描聚类方法，通常取决于话语的扫描顺序。然而，我们按照话语的 LOF 分数的升序来固定我们的扫描顺序。这意味着话语的顺序是从强内侧到强外侧排列的。

这种聚类方法需要用户定义的阈值θ，该阈值是领导者聚类方法的距离阈值。增量聚类方法最初以空集作为原型或前导话语集开始，并将第一次扫描的话语添加到其中(参考文献 4、5 和 6)。

下一个被扫描的话语将检查它是否在集合中的任何原型或引导话语的θ距离内，如果是，则它成为该原型话语的跟随者。如果不为真，则被扫描的话语成为新的原型话语或添加到现有原型/引导者话语集合的引导者。

重复这个过程，直到按照 l of 分数的顺序扫描完所有的话语。聚类过程将数据集划分成半径为θ的半球。下图说明了这种聚类方法如何输出领导者。

![](img/986d7113a69de9bcf91179cf629ef157.png)

**半球领导聚类法，点是领导/原型话语**

如果一个意图中的话语被多个话题所覆盖，那么每个引导语/原型话语覆盖一个意图中的话题。主题的微调取决于用户选择的阈值θ。如果θ值很小，那么微小的话题就是典型话语的代表(参考文献 4、5 和 6)。

这里用一个例子来解释 leaders 聚类。让我们按照下面给出的 LOF 分数的升序排列原型聚类算法的话语。

```
utterance_embeddings=list(zip(utterance_embeddings,lof)
utterance_embeddings.sort(key=operator.itemgetter(1))
utterance_embeddings=[x[0] for x in utterance_embeddings]
```

现在，我们可以将 leaders 聚类方法定义如下。该因子的值是 leaders 聚类算法的阈值

```
def PrototypicalUtterances(utterance_embeddings, factor):
    leaders_index=[]
    index=0
    for index in range(len(utterance_embeddings)):
        if(len(leaders_index)==0):
            leaders_index.append(index)
        else:
            leader_flag=True
            for index1 in leaders_index:
                data1=utterance_embeddings[index1]
                data2=utterance_embeddings[index]   
                dist=distance.cosine(data1, data2)
                if(dist<=factor):
                    leader_flag=False
                    break
        if(leader_flag):
            leaders_index.append(index)
    return leaders_index
```

这个方法可以被调用，原型话语可以被打印出来

```
leaders_index=PrototypicalUtterances(utterance_embeddings, 0.4)
print("The prototypical utterances are")
for index in leaders_index:
    print(utterances[index])
```

输出如下所示。您可以看到，在前三个话语中，有一个被代表为原型话语或领导者话语

```
The prototypical utterances are
how old are you
my phone is good
where do you stay
```

# 意图重叠分析:

意图重叠分析是为了理解两个意图之间的相似性重叠。这种方法使用两个意图的原型话语来计算意图重叠，以获得计算优势。原型话语是每个意图中主题的代表。因此，我们通过获取两个意图的原型话语之间的相似性来计算意图重叠分析。

假设我们有 Intent1=[p11，p12]和 intent2=[p21，p22]，那么我们已经通过两个 Intent 的原型的所有最小距离的平均值定义了 Intent overlap 分析。原型(p11)的最小距离计算如下

```
minDist(p11)=min[dist(p11, p21),dist(p11,p22)]
minDist(p12)=min[dist(p12, p21),dist(p12,p22)]
minDist(p21)=min[dist(p21, p11),dist(p21,p12)]
minDist(p22)=min[dist(p22, p11),dist(p22,p12)]IntentOverlap=minDist(p11)+minDist(p12)+minDist(p21)+minDist(p22)/4
```

本文解释了用于 Bot 诊断的各种机器学习方法。大部分工作都是从以下论文的一些概念中实现的。

```
**References:**1\. [https://tfhub.dev/google/universal-sentence-encoder/4](https://tfhub.dev/google/universal-sentence-encoder/4)2.[https://medium.com/@pramodch/understanding-lof-local-outlier-factor-for-implementation-1f6d4ff13ab9#](https://medium.com/@pramodch/understanding-lof-local-outlier-factor-for-implementation-1f6d4ff13ab9#).3\. Breunig, M. M., Kriegel, H. P., Ng, R. T., & Sander, J. (2000, May). LOF: identifying density-based local outliers. In ACM sigmod record.4\. P Viswanath, and ​V Suresh Babu, ​Rough DBSCAN: A Fast Hybrid Density Based Clustering Method for Large Data Sets​, ​Pattern Recognition Letters​(Elsevier), 30(16):1477-­1488, 2009.5\. V Suresh Babu​, and P Viswanath, ​Rough Fuzzy weighted k nearest leader classifier for large data sets, Pattern Recognition​(Elsevier), 42(9): 1719­-1731, 2009.​6\. V Suresh Babu, ​and P Viswanath, ​Weighted k Nearest Leader Classifier for Large Data Sets​, ​2nd​ International Conference on Pattern Recognition and Machine Intelligence (PReMI’07) ​, Lecture Notes in Computer Science, Springer, 4815:17­-24, India, 2007.
```
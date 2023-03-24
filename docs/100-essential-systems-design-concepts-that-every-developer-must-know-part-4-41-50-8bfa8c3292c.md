# 每个开发人员都必须知道的 100 个基本系统设计概念(第 5 部分:41–50)

> 原文：<https://levelup.gitconnected.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-4-41-50-8bfa8c3292c>

这些是作为开发人员必须知道的 100 个基本系统设计概念。

这些将帮助您设计高效、容错和可伸缩的系统。

为了保证可读性，我将这些分成多篇博文。

![](img/91df954d961f069f46caba83bfc9e0a5.png)

Firmbee.com 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[的照片](https://unsplash.com/@firmbee?utm_source=medium&utm_medium=referral)

可以在下面找到以前部分的链接:

[](/100-essential-systems-design-concepts-that-every-developer-must-know-part-1-1318c2c402ca) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 1 部分)

### 设计高效、容错和可扩展系统的首选清单

levelup.gitconnected.com](/100-essential-systems-design-concepts-that-every-developer-must-know-part-1-1318c2c402ca) [](/100-essential-systems-design-concepts-that-every-developer-must-know-part-2-b6c4c6239af8) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 2 部分)

### 设计高效、容错和可扩展系统的首选清单

levelup.gitconnected.com](/100-essential-systems-design-concepts-that-every-developer-must-know-part-2-b6c4c6239af8) [](/100-essential-systems-design-concepts-that-every-developer-must-know-part-3-256762dbed0e) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 3 部分)

### 设计高效、容错和可扩展系统的首选清单

levelup.gitconnected.com](/100-essential-systems-design-concepts-that-every-developer-must-know-part-3-256762dbed0e) [](https://bamania-ashish.medium.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-4-31-40-733d19958c37) [## 每个开发人员必须知道的 100 个基本系统设计概念(第 4 部分:31–40)

### 设计高效、容错和可扩展系统的首选清单

bamania-ashish.medium.com](https://bamania-ashish.medium.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-4-31-40-733d19958c37) 

## 41.键值数据库

使用唯一标识记录的*键*存储和检索数据的数据库(*值*)。

比如 Redis 和 etcd。

## 42.内存数据库

依靠 RAM 存储和访问数据的数据库。这使得操作比使用磁盘数据存储的传统数据库快得多。

比如 Redis 和 SQLite。

![](img/553a25e143c4bfdb29d26bc92428496f.png)

[活动创建者](https://unsplash.com/@campaign_creators?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## 43.图形数据库

一种数据库，使用图形数据结构，将数据及其相关属性存储在节点中，将数据关系存储在边中。

一个流行的图形数据库是 **Neo4j** 。

它使用了 ***密码****，这是一种针对数据库的图形优化查询语言。*

*![](img/a3c29c979ae0b5ffaa4726329e372794.png)*

*照片由 [Alina Grubnyak](https://unsplash.com/@alinnnaaaa?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄*

## *44.时间序列数据库*

*为存储和分析时序数据而优化的数据库。*

*流行的时序数据库有**普罗米修斯** & **InfluxDB** 。*

*![](img/06d358f42e4c86aef88312081b4106bd.png)*

*照片由[唐纳德吴](https://unsplash.com/@donaldwuid?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)*

## *45.二进制大对象*

*它是单个实体形式的二进制数据的大型浓缩集合(例如，图像、音频文件、Word 文档、PDF 文档和视频)。*

## *46.**斑点存储***

*这是一个为存储二进制大型对象而优化的数据库。*

*这方面的例子有**亚马逊 S3、谷歌云存储** & **Azure Blob 存储。***

## *47.空间数据库*

*它是一个数据库，经过优化可存储几何空间中定义的空间数据/对象(例如地图)。*

*这方面的例子有 **Esri 地理数据库** & **雪花。***

*![](img/c02f94e3443321cedffc409cbee891b5.png)*

*[安德鲁·尼尔](https://unsplash.com/@andrewtneel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片*

## *48.网络分区*

*它是网络系统中不同节点之间的通信故障。*

## *49.CAP 定理*

*它指出分布式数据库只能确保三个中的两个**:***

*   *[**一致性**](/100-essential-systems-design-concepts-that-every-developer-must-know-part-3-256762dbed0e)*
*   *[**可用性**](/100-essential-systems-design-concepts-that-every-developer-must-know-part-3-256762dbed0e)*
*   ***分区容忍度:**是数据库集群即使在网络分区的情况下也能继续工作的能力。*

## *50.空间定理*

*它是 CAP 定理的推广。*

*它指出:*

*   *在网络分区(P)的情况下，一个系统只能保证[可用性](/100-essential-systems-design-concepts-that-every-developer-must-know-part-3-256762dbed0e) (A)或[一致性](/100-essential-systems-design-concepts-that-every-developer-must-know-part-3-256762dbed0e) (C ) (CAP 定理)*
*   *否则(E)，在没有网络划分的情况下，系统只能保证[延迟](/100-essential-systems-design-concepts-that-every-developer-must-know-part-3-256762dbed0e) (L)或[一致性](/100-essential-systems-design-concepts-that-every-developer-must-know-part-3-256762dbed0e) (C)*

*![](img/1159241eafa35a27ac6321ba1ebd7d5a.png)*

*照片由[格伦·卡斯滕斯-彼得斯](https://unsplash.com/@glenncarstenspeters?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄*

**感谢阅读！下一部分再见！**

*[](/100-essential-systems-design-concepts-that-every-developer-must-know-part-6-51-60-978801728a4b) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 6 部分:51–60)

### 设计高效、容错和可扩展系统的首选清单

levelup.gitconnected.com](/100-essential-systems-design-concepts-that-every-developer-must-know-part-6-51-60-978801728a4b)* 

**如果你是 Python 或编程的新手，可以看看我的新书，书名为“* [**”《没有公牛**t 学习 Python 指南**](https://bamaniaashish.gumroad.com/l/python-book)**”***下面:**

*[](https://bamaniaashish.gumroad.com/l/python-book) [## 学习 Python 的无牛指南

### 你是一个正在考虑学习编程却不知道从哪里开始的人吗？我有适合你的解决方案…

bamaniaashish.gumroad.com](https://bamaniaashish.gumroad.com/l/python-book) [](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium——Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership)*
# 每个开发人员必须知道的 100 个基本系统设计概念(第 4 部分:31–40)

> 原文：<https://levelup.gitconnected.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-4-31-40-733d19958c37>

这些是作为开发人员必须知道的 100 个基本系统设计概念。

这些将帮助您设计高效、容错和可伸缩的系统。

为了保证可读性，我将这些分成多篇博文。

![](img/729815d7f1347673cfa0955e015db1ec.png)

[克里斯汀·塔博瑞](https://unsplash.com/@ktabori?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

可以在下面找到以前部分的链接:

[](/100-essential-systems-design-concepts-that-every-developer-must-know-part-1-1318c2c402ca) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 1 部分)

### 设计高效、容错和可扩展系统的首选清单

levelup.gitconnected.com](/100-essential-systems-design-concepts-that-every-developer-must-know-part-1-1318c2c402ca) [](/100-essential-systems-design-concepts-that-every-developer-must-know-part-2-b6c4c6239af8) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 2 部分)

### 设计高效、容错和可扩展系统的首选清单

levelup.gitconnected.com](/100-essential-systems-design-concepts-that-every-developer-must-know-part-2-b6c4c6239af8) [](/100-essential-systems-design-concepts-that-every-developer-must-know-part-3-256762dbed0e) [## 每个开发人员都必须知道的 100 个基本系统设计概念(第 3 部分)

### 设计高效、容错和可扩展系统的首选清单

levelup.gitconnected.com](/100-essential-systems-design-concepts-that-every-developer-must-know-part-3-256762dbed0e) 

## 31.可量测性

它是系统在不影响其功能的情况下处理不断增加的工作量的能力。

![](img/5a6e457d1f52bc025d7c133ff04ac6a4.png)

照片由[艾萨克·史密斯](https://unsplash.com/@isaacmsmith?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## **32。水平缩放**

这包括添加更多节点来处理增加的工作负载。

例如，添加更多的服务器来处理越来越多的用户对我们应用程序的请求。

## **33。垂直缩放**

这包括增加单个节点的计算能力，以处理增加的工作负载。

例如，增加我们的服务器节点的 RAM 来处理我们的应用程序不断增加的用户请求。

![](img/472e9c3cbb1d0b2be13585edd1b271d0.png)

克拉克·范·德·贝肯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 34.关系数据库

它是组织到一个或多个由行和列组成的表中的数据集合。

在关系数据库中，不同的表之间可以有逻辑连接/关系。

这些数据库通常支持使用**结构化查询语言(SQL)** 。

比如 PostgreSQL 和 MySQL。

## 35.非关系数据库

它是组织成非表格形式的数据集合。

存储模型的结构非常灵活，并且针对要存储的数据类型进行了优化。

这些通常不支持使用 SQL，也称为**非 SQL 数据库**。

比如 Firebase 和 MongoDB。

![](img/70dab362ff0fd04cc3ebe37d67c011ce.png)

由[本杰明·雷曼](https://unsplash.com/es/@benjaminlehman?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 36.数据库管理系统

它是使用户能够定义、创建、维护和控制对数据库的访问的软件系统。

## 37.数据库事务

它是数据库管理系统中执行的逻辑或工作的单一单元。

## 38.酸性交易

这些是 DBMS (ACID 兼容)中的事务，可确保以下属性:

*   **原子性**:每个事务要么成功并在数据库中执行它想要做的任务，要么完全失败，让数据库保持原始状态。
*   **一致性**:在事务完成时，数据库应该处于有效/结构合理的状态，以确保一致性
*   **隔离:**事务的并发执行应该在数据库中引起与顺序执行事务相同的变化
*   **持久性:**即使在网络分区或电源故障的情况下，已完成的事务也应该保存在数据库中

SQL 数据库通常是 ACID 兼容的。

## 39.基础交易记录

这些是 DBMS(基本兼容)中的事务，可确保以下属性:

*   **基本可用性:**通过在不同的数据库集群节点之间传播和复制数据库来确保数据库可用性
*   **软状态:**数据库不必提供即时一致性
*   **最终一致性:**数据库在稍后的时间点提供一致性

没有像 Cassandra、MongoDB 和 Redis 这样的 SQL 数据库通常是 base 兼容的。

> ACID 事务模型提供了**一致性**。
> 
> 基本事务模型提供**高可用性**。

## 40.create, read, update, and delete

它是**创建、读取、更新**和**删除**的缩写。

它描述了数据库中发生的四个基本操作。

![](img/f37f4ef01502ef2b1109b2e6e6e6caf3.png)

达米安·扎列斯基在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

*感谢阅读！下一部分再见！*

[](https://bamania-ashish.medium.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-4-41-50-8bfa8c3292c) [## 每个开发人员必须知道的 100 个基本系统设计概念(第 4 部分:41–50)

### 设计高效、容错和可扩展系统的首选清单

bamania-ashish.medium.com](https://bamania-ashish.medium.com/100-essential-systems-design-concepts-that-every-developer-must-know-part-4-41-50-8bfa8c3292c) 

*如果你是 Python 或编程的新手，可以看看我的新书，书名是'* [**《没有公牛**t 学习 Python 指南》**](https://bamaniaashish.gumroad.com/l/python-book) **'** *下面:*

[](https://bamaniaashish.gumroad.com/l/python-book) [## 学习 Python 的无牛指南

### 你是一个正在考虑学习编程却不知道从哪里开始的人吗？我有适合你的解决方案…

bamaniaashish.gumroad.com](https://bamaniaashish.gumroad.com/l/python-book) [](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium——Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership)
# 使用 Elasticsearch 对不同的全文搜索方法进行基准测试

> 原文：<https://levelup.gitconnected.com/benchmarking-different-methods-for-full-text-search-using-elasticsearch-80d9cf3939a4>

如何在不同的分析器和查询之间进行选择，以获得最佳的搜索性能？当然是标杆啦！

![](img/d002014be7a45cddb687c6e790912789.png)

Arie Wubben 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

部署大规模全文搜索引擎可能非常困难。Elasticsearch 让这项工作变得更加容易，但它并不是放之四海而皆准的——恰恰相反。

Elasticsearch 有许多配置和功能，但拥有许多功能也意味着有许多方法来实现相同的目标，并且知道什么是你正在构建的产品的最佳方法并不总是简单的。

让我们从找出通过用户名/名字找到用户的主要方法开始，测量他们的性能、优点和缺点。

# **实验统计**

# 匹配查询

这将使用模糊参数匹配术语。

**优点**

*   简单易用
*   不占用太多空间
*   允许模糊搜索

**缺点**

*   如果索引词的大小大于搜索项+fuzzy _ size，则不匹配
*   模糊搜索会减慢速度

# **前缀查询**

**优点**

*   简单易用
*   可能非常快(特别是如果您使用 [index_prefixes](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-prefixes.html) 选项)

**缺点**

*   只有当索引项以搜索项开始时，它才会匹配
*   如果您使用 index_prefixes 选项，它将使用更多的空间
*   没有模糊搜索

# **通配符查询**

使用关系数据库 SELECT 时，其工作方式与“LIKE %term%”有点类似。

**赞成者**

*   易于实施和调试

**缺点**

*   通常是最慢的选项，尤其是当通配符放在开头或者使用的字符很少时

# **匹配查询+ ngram 分析器**

**优点**

*   即使搜索词在单词中间，也会匹配
*   良好的搜索性能
*   允许进行“模糊”搜索，因为它将匹配每个单词的片段

**缺点**

*   专业分析仪
*   使用更多磁盘空间
*   仅当搜索词的大小至少为最小“克”时才匹配

# 映射

**标准**

**Ngram**

# 问题

**匹配查询**

**前缀查询**

**通配符查询**

**匹配查询+ Ngram 分析器**

# 查询基准

为了进行基准测试，我创建了一个小的 python 脚本，它使用 4 个并行进程，每个进程运行 1000 个连续的查询。

它对每一种查询都运行这个函数。

主要目的不是知道每个查询需要多长时间，而是**比较**它们在相同条件下的执行时间。

*   以秒为单位的时间是将 1000 次运行的时间相加，然后计算 4 个并行过程的平均值

# 结论

*   **不惜一切代价避免通配符查询:**我看到到处都在推荐通配符查询，但正如我们所见，它是最慢的选项，使用其他选项可以获得更好的结果。
*   **如果您可以忍受只匹配单词的开头:**前缀查询可以完成这项工作，而且速度非常快。如果你的用例符合这一点，这是一个很好的选择。也有可能使用 index_prefix 选项以磁盘空间为代价来加快速度。
*   **如果你想节省磁盘空间**:使用带有匹配+模糊参数的标准分析器应该可以做到。
*   **如果你希望即使搜索词在一个词的中间也能够匹配，并且确实需要它很快** : ngram 似乎是这种情况下的选择。尽管有时使用它会很“危险”。

**使用 ngram 分析器**时，您应避免最小和最大 gram 大小之间的距离过大，也应避免使用非常小的 ngram 大小，如 1，以便在仅使用 1 个字母时显示结果。

如果你有一个大范围的内存大小，它将会变得非常昂贵，并有可能降低你的性能。

相反，例如，您可以使用使用标准分析器的字段，并在 search_term < min_ngram_size 时执行简单的匹配或前缀查询。

# 进入 Elasticsearch？看看这些:

[](https://luis-sena.medium.com/the-complete-guide-to-increase-your-elasticsearch-write-throughput-e3da4c1f9e92) [## 提高弹性搜索写吞吐量的完整指南

### 您可以使用多种策略来增加批处理作业和/或在线作业的弹性搜索写入容量…

luis-sena.medium.com](https://luis-sena.medium.com/the-complete-guide-to-increase-your-elasticsearch-write-throughput-e3da4c1f9e92) [](https://luis-sena.medium.com/using-event-sourcing-to-increase-elasticsearch-performance-3fc0ba1232c8) [## 使用事件源提高弹性搜索性能

### 持续不断的文档更新可以让 Elasticsearch 集群屈服。幸运的是，有办法做到…

luis-sena.medium.com](https://luis-sena.medium.com/using-event-sourcing-to-increase-elasticsearch-performance-3fc0ba1232c8) 

这一切听起来如何？你有什么想让我详述的吗？请在下面的评论区告诉我你的想法(如果有用，请鼓掌)！

敬请关注下一篇帖子。跟着走就不会错过了！
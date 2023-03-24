# 雪花的时间旅行真的能为你做些什么？

> 原文：<https://levelup.gitconnected.com/what-can-time-travel-really-do-for-you-e24815ff5c0f>

## 利用雪花的时间旅行功能优化您的工作流程

![](img/0eac6feda12aa43f9749e5df0ab88654.png)

图片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[德罗林出租](https://unsplash.com/@deloreanrental?utm_source=medium&utm_medium=referral)拍摄

雪花的一个很棒的功能是“时间旅行”[(这里有文档)](https://docs.snowflake.com/en/user-guide/data-time-travel.html)，它允许你设置你的表格，这样你就可以回到过去 90 天。这可能看起来只是另一个“很酷”的功能，但它远不止于此...这是改善你工作流程的好方法，可以节省你很多时间和麻烦。让我们直接开始吧。

# 调试您的 ETL 失败(使用“当时看起来”的数据)

这是周五晚上，你在家看爱情岛*(你知道你做…)* ，与此同时，财务月末对账脚本正在制作中失败。周末过去了。周一早上，你来到办公室，看到的是:一个红色的大盒子，一堆提醒，还有你最喜欢的财务人员向你询问最新进展。您重新运行脚本，它成功了！等等，怎么会这样？什么变了？？

![](img/9a3753d397e2c43ac8f1abc97e4626bb.png)

照片由[布鲁斯·马尔斯](https://unsplash.com/@brucemars?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这是典型的*什么？成功了？我错过了什么？？*“场景:不知道它为什么从失败到成功意味着你可能有一个漏洞在雷达下:如果你没有发现它，它可能会再次发生！不好…

现在是时候从你的魔术包里拿出时间旅行了！

您只需转到 Snowflake 控制台的 history，检查失败的 query_id，然后回到当时的上下文:

```
(...)
SELECT this, and, that, sum(other)
FROM super_important_table AT (STATEMENT=> "....")
INNER JOIN more_of_that AT (STATEMENT=> "....")
    ON this=this
GROUP BY this, and, that;
(...)
```

公园散步！

# 回归测试

你正在调整一个通宵脚本的性能:删除那个，移动那个，删除这个，改进那里的连接。在它的结尾，剧本看起来是惊人的和最佳的。你测试了它，它看起来很好，但是你怎么能确定它在生产中 100%地工作呢？这个问题很容易回答，甚至比找出这些图片中的 7 个不同点还要简单:

![](img/80234261d82cc825f2c017eb09e31540.png)

找出 7 个不同点！(感谢[https://games.kidzsearch.com/](https://games.kidzsearch.com/))

您将变更投入生产，并引发新一轮运行。然后你将它的样子与它之前的样子进行比较(使用时间旅行):

```
select 'before' when, hash_agg(*) 
from table AT (TIMESTAMP=> date_add('hour',-1,current_timestamp))union allselect 'after' when, hash_agg(*) 
from table
```

如果整个表的哈希值匹配，就可以开始了。

如果不匹配呢？好吧，那么你总是可以做好旧的“减/除”，或任何其他你喜欢的策略来找到哪些行改变了，为什么:

```
select * 
from table AT (TIMESTAMP => date_add('hour', -1, current_timestamp) )minusselect * from table
```

# 维度版本控制被扭曲了，有重复的！现在怎么办？？

我们都见过这种情况发生…有一天，由于一个错误或缺乏更好的了解，发生了一些事情，搞砸了所有的客户端维度有效性字段。我见过这样的错误花费一整天来正确修复，因为拥有重复版本或从来不存在的版本的所有代理键下游影响，突然你有几十个事实和 dim 需要修复。

![](img/279709ae37404edb00712973337d2e1d.png)

由[约什·金斯利](https://unsplash.com/@yoshginsu?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

在过去，你可能会被一整天的选择和更新所困扰，或者恢复一个备份(*让我们面对它，这是一个巨大的痛苦，需要很长时间…* )，但现在不是了！现在你有了…你猜对了，时间旅行！只要及时回到你需要的桌子。如果你知道哪些实体被损坏了，你可以有选择地这样做:

```
CREATE TEMP TABLE please_save_me_from_myself AS 
SELECT * 
FROM table AT (TIMESTAMP => "before_i_screwed_up"::timestamp)
WHERE some_col in ('damaged', 'records');DELETE FROM table WHERE some_col in ('damaged', 'records');
INSERT INTO table 
SELECT * 
FROM please_save_me_from_myself;
```

但是如果真的是审判日，你的大部分桌子都完蛋了呢？不要担心，只要在您喜欢的时候克隆 DWH 模式，重新运行 ETL，然后继续您的下一个任务！

```
CREATE SCHEMA PRE_DOOM 
CLONE DWH 
AT (TIMESTAMP=>'before_doom'::timestamp);ALTER SCHEMA PRE_DOOM 
SWAP WITH DWH;
```

用一个简单的 DDL 就可以将几行数据恢复到一个完整的数据库，这有很多种组合方式…拜托，这很酷，对吧？

# 额外提示:使用 HASH_AGG 进行数据集比较

这有点偏离了本文的主题，但是您可能已经注意到我在其中一个脚本中使用了一个名为 HASH_AGG 的函数。好吧，如果这是你第一次看到它，你必须仔细阅读它，并把它加入你的军火库。

我第一次使用它是在 Greenplum，这是一种非常棒的回归测试/匹配 2 个表的方法。因为它计算一个表的完整散列*(高度可并行化的操作)*，所以这是匹配两个数据集的最快和最有效的方法。此外，因为它只是一个类似于 *sum()* 或 *avg()* 的聚合，所以您可以聚合您喜欢的任何列(如果您觉得懒，甚至可以聚合 *all ** 列)，您可以根据您需要的任何列进行分组，也可以过滤数据集。

```
select hash_agg(col1::int, col2) 
from table_A 
where date=current_date;select hash_agg(col1, col2::varchar) 
from table_B;
```

它可以无缝地处理空值和复杂数据类型，但是请注意，任何微小的偏差都会产生差异*(即。1.01 != 1.011)* ，数据类型确实很重要*(即。1 != '1')* 。

# 结论

这些都不是真正的突破性或火箭科学，但我认为我们有时会太沉迷于鼠标滚轮，以至于忘记利用我们周围的一些可用功能。在过去的几年里，我用了很多次，它们已经成了我的面包和黄油，但是有一天有人说"*哦，哇，你可以在雪花里做这个？*“那时我意识到这真的不是面包和黄油。所以我决定分享，希望它可以帮助别人。

感谢你阅读这篇文章，干得好！下次见！

# 雪花系列

*这篇文章是我的(不断成长的)雪花系列的一部分。如果你很好奇想了解更多关于雪花的知识，这些文章可能也会让你感兴趣:*

*   [是什么让雪花比其他的好那么多](https://jmarquesdatabeyond.medium.com/what-makes-snowflake-so-much-better-than-others-58e839e29e80)
*   [迁移到雪花？下面是你需要知道的](https://jmarquesdatabeyond.medium.com/migrating-to-snowflake-here-is-what-you-need-to-know-8310b84d2741)
*   [你可能不知道存在的雪花结构](https://jmarquesdatabeyond.medium.com/snowflake-configurations-you-probably-didnt-know-existed-ccc0c56c9d21)
*   [使用雪花？不要犯这些代价高昂的错误](/using-snowflake-dont-make-these-expensive-mistakes-66c1eaa7d1ee)
*   [*雪花中的近实时数据摄取(以及如何实现热/冷/冻结数据存储策略)*](/near-realtime-data-ingestion-in-snowflake-7033d45ce860)
*   [*使用外部表时的性能考虑*](https://jmarquesdatabeyond.medium.com/using-snowflake-external-tables-you-must-read-this-aeb66ae8e0e6)
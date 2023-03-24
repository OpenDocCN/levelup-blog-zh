# 解决 Leetcode 问题，获得你梦想中的公司的报价

> 原文：<https://levelup.gitconnected.com/solve-leetcode-and-get-offers-from-your-dream-companies-problem-695-max-area-of-island-b65477167931>

在这个系列中，我将和我的朋友一起解决 Leetcode 媒体问题，你可以在我们的 youtube 频道上看到，今天我们将解决问题 695。**岛屿最大面积**。

稍微介绍一下我，过去有来自**优步**印度和**亚马逊**印度的 offers，目前在阿姆斯特丹**Booking.com**工作。

![](img/65418a67f4073b5d5cfed811ba1e4f9c.png)

由[克里斯托弗·高尔](https://unsplash.com/@cgower?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

> [如果你还不是 medium 的会员，你可以使用这个链接成为会员，只需每月 5 美元，支持我创造伟大的内容](https://nilmadhab.medium.com/membership)

**学习数据结构和算法的动机**

多年来的工资增长

> 这篇文章最初发表在[简单编码](https://www.simplecoding.dev/articles/solve-leetcode-problems-and-get-offers-from-your-dream-companies-max-area-of-island-n80)

我在印度做了 4 年的软件开发员。我从大学三年级开始学习算法和数据结构，因为我是电子专业出身。这是我这些年来的工资增长情况(全部以每年 10 万卢比为单位)

2016 年:大学毕业后在 **Flipkart** 中的职位，IIT·KGP(180 万基数+20 万奖金=**200 万**)。但由于 Flipkart 遇到了一些麻烦，我的提议推迟了 6 个月，所以我加入了三星。

2016: **三星诺伊达**(校外)(140 万基数+50 万加盟奖金=**190 万**)。他们付给印度人 190 万卢比，但其他大学为同样的工作支付 90-140 万卢比，这是假的。

2017:**Oyorooms**(**17**lakh 固定，无奖金，无股票)。我接受了减薪，因为我在三星没有学到任何东西，所以加入了 Oyo。

2019: **Sharechat** (26 万固定+2.6 万奖金+股票期权)我在班加罗尔加入 Sharechat，身份是 SDE1

2020 年:SDE2 角色中来自**亚马逊**的报价(265 万基数+185 万加盟奖金= **43** 万)。他们提供股票，但第一年只授予 5%，所以我忽略了它。

SDE2 职位中来自**优步**(每年 330 万卢比+150 万卢比股票期权(4 年 96000 美元)+50 万卢比加入奖金= **每年 55 万卢比)。**

印度的许多初创公司和老牌公司每年支付超过 350 万卢比给顶尖的编程人才，仅仅是 4 年多的经验，像 Dunzo，Dream11，Rubric 等，看看

[](https://leetcode.com/discuss/compensation) [## 补偿

### 提高你的编码技能，迅速找到工作。这是扩展你的知识和做好准备的最好地方…

leetcode.com](https://leetcode.com/discuss/compensation) 

我拒绝了两个邀请，最终加入了 Booking.com，因为我想探索欧洲。我不能透露我目前的工资。

我得到了这么多 offers，因为我练习了很多数据结构和算法。我用 Leetcode 解决了超过 410 个问题。

> [在 Twitter 上关注我](https://twitter.com/Nilmadhabmondal),了解最新的博客帖子、有趣的算法和 web 开发内容

[](https://leetcode.com/nilmadhab/) [## 无 MADHAB — LeetCode 配置文件

### 提高你的编码技能，迅速找到工作。这是扩展你的知识和做好准备的最好地方…

leetcode.com](https://leetcode.com/nilmadhab/) 

先说岛问题的最大面积(LC 695)。

youtube 讨论

[](https://medium.com/javarevisited/how-to-use-breath-fast-search-pattern-for-craking-coding-interviews-50ca7e827199) [## 如何使用快速搜索模式进行疯狂编码面试

### 如何识别哪些问题可以由 BFS 解决，并获得顶级科技公司的聘用

medium.com](https://medium.com/javarevisited/how-to-use-breath-fast-search-pattern-for-craking-coding-interviews-50ca7e827199) 

# **问题陈述**

给定 0 和 1 的非空 2D 数组`grid`，一个**岛**是一组 4 个方向(水平或垂直)连接的`1`(代表陆地)。)你可以假设网格的四个边都被水包围着。

在给定的 2D 数组中找出一个岛的最大面积。(如果没有岛，最大面积为 0。)

**例 1:**

```
[[0,0,1,0,0,0,0,1,0,0,0,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,1,1,0,1,0,0,0,0,0,0,0,0],
 [0,1,0,0,1,1,0,0,**1**,0,**1**,0,0],
 [0,1,0,0,1,1,0,0,**1**,**1**,**1**,0,0],
 [0,0,0,0,0,0,0,0,0,0,**1**,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,0,0,0,0,0,0,1,1,0,0,0,0]]
```

给定上面的网格，返回`6`。注意答案不是 11，因为岛必须是 4 向连接的。

**例 2:**

```
[[0,0,0,0,0,0,0,0]]
```

给定以上网格，返回`0`。

**注:**给定的`grid`中每个维度的长度不超过 50。

# 解决办法

为了解决这个问题，我们必须把 2D 矩阵想象成一个图形。这个问题我们可以套用`**DFS**`或者`**BFS**`。在本教程中，我们将使用 BFS 算法。

广度优先搜索是一种图形遍历算法，它从根节点开始遍历图形，并探索所有相邻节点。然后，它选择最近的节点并探索所有未探索的节点。该算法对每个最近的节点遵循相同的过程，直到它找到目标。([来源](https://www.javatpoint.com/breadth-first-search-algorithm))

焊盘应垂直和水平连接。(非对角线)这将我们的可能路径减少到 4 个；向上(0，1)，向下(0，-1)，向左(1，0)，向右(0，-1)。因此，当我们找到一个点是陆地时，我们将在该点上做 BFS，我们将累积陆地和连接到该特定点的总点数。

我们将保持一个访问过的矩阵的大小等于给定的网格，这样我们就可以跟踪所有访问过的点(这样我们就不会陷入无限循环)。

我们将保留另一个名为 max_area 的变量，它找到了答案。将 max_area 与可能的岛的面积进行比较，从而找到最大面积。

以下是针对该问题的详细记录的 python 代码。

下面给出了这个问题的 Java 代码。

一个类似的问题也可以用 BFS 来解决。

[](https://medium.com/javarevisited/solve-leetcode-problems-and-get-offers-from-your-dream-companies-da534d1414ad) [## 解决 Leetcode 问题，获得你梦想中的公司的报价

### Leetcode 1020。飞地数量

medium.com](https://medium.com/javarevisited/solve-leetcode-problems-and-get-offers-from-your-dream-companies-da534d1414ad) [](https://medium.com/javarevisited/solve-leetcode-problems-and-get-offers-from-your-dream-companies-404b6c288d09) [## 解决 Leetcode 问题，获得你梦想中的公司的报价

### 问题编号 752。打开锁(BFS/图)

medium.com](https://medium.com/javarevisited/solve-leetcode-problems-and-get-offers-from-your-dream-companies-404b6c288d09) 

这个问题也可以用 DFS 来解决。所有代码可以在下面的 GitHub repo 中找到。

[](https://github.com/webtutsplus/LeetCode) [## webtutsplus/LeetCode

### 在 GitHub 上创建一个帐户，为 webtutsplus/LeetCode 的开发做出贡献。

github.com](https://github.com/webtutsplus/LeetCode) 

## 感谢您阅读并关注本出版物，了解更多 Leetcode 解决方案！😃

[](https://medium.com/leetcode-simplified) [## LeetCode 简体

### 我们将现场解决 Leetcode 问题，您可以在我们的 youtube 频道上观看

medium.com](https://medium.com/leetcode-simplified)
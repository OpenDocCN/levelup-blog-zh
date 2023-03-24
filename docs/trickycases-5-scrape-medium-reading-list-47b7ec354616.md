# 棘手案例五。刮中等阅读列表

> 原文：<https://levelup.gitconnected.com/trickycases-5-scrape-medium-reading-list-47b7ec354616>

***免责声明:****【tricky cases】是一系列包含相当短的代码片段的帖子，在日常的 ML 实践中非常有用。在这里你可以找到一些你想在 StackOverflow 中搜索的东西。*

![](img/a33203fecc12804f150d7a3d48214380.png)

照片由 [Christa Dodoo](https://unsplash.com/@krystagrusseck?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

最近 Medium 增加了一个新功能，允许你将你收藏的文章分发给几个阅读列表，而以前你只有两个:主列表和存档。我很有条理，我喜欢把东西放在它们该在的地方，但我也是害怕错过的一个好例子(FOMO)。现在，我的主列表中有 612 个被书签标记的帖子，而且几乎不知道如何手动分割它们，所以我决定结合我的编码技能和一点机器学习。

我将在这一篇以及 TrickyCases 系列的后续几篇文章中向您展示如何做到这一点。

> 你可以在这里找到我关于[网页抓取的代码](https://bit.ly/3omBlaG)，格式简单。

**高级算法**如下所示:

1.  手动获取网站主页上的登录链接。为此，你必须在匿名模式下打开 medium.com，并用你的电子邮件签名。然后，将链接保存在从 Medium 收到的消息中，目前为止不要使用它。
2.  设置 Selenium hub。我不打算详细介绍这个，因为有很多关于它的在线教程。我更喜欢通过 docker 来做，因为我只需要拉一个 docker 镜像，比如 [standalone-chrome](https://hub.docker.com/r/selenium/standalone-chrome) ，启动它并在我的代码中连接到它。
3.  **算法的编码部分从这里开始。**首先，我们将使用登录链接获得一个会话。然后，我们将会话存储到一个 cookies 文件中，以便以后可以重用。请记住，cookies 有一个截止日期，所以你不能无休止地使用它们。
4.  下一步是移动到阅读列表页面，向下滚动以获得想要的文章数量。Medium 以 20 个帖子为单位显示你的列表，所以你可以自己计算你想要抓取多少个帖子，然后将这个数字除以 20，得到需要滚动的数量。
5.  最后一部分是刮贴标题。在这一点上，我相信将文章分成不同的主题就足够了，但是我会和你一起学习。

**潜在警告:**

*   似乎标题都存储在”。kb”类，但我不确定这是否会随着时间的推移而持续下去。也许，在下一次媒体更新期间，这个脚本将不得不被改变。
*   我不确定 Medium 对网站的这一部分有多友好。我会和网站所有者谈谈，并更新你的信息。

**代码:**

“助手”文件列表:

**我的成绩:**

```
Title
0         35 Actionable Tips to Grow Your Medium Blog
1                         Most hyped stocks on Reddit
2                   How to Build an EDA App in Python
3   How to Scrape Tweets Without Twitter’s API Usi...
4   A Gentle Intro to Time Series Forecasting for ...
..                                                ...
95  How to deploy your Neural Network Model using ...
96               BERT: Multilabel Text Classification
97         Fine-tuning a BERT model with transformers
98                                                   
99          How to implement EWMA plots using Python?
```

我希望你能在你的个人资料中复制这段代码。如有任何相关问题，请随时提问，如有需要，请寻求帮助。

在接下来的 TrickyCases 部分，我将向您展示如何将这个标题列表分成几个部分，然后我们将尝试自动将每个帖子分配到其对应的阅读列表。
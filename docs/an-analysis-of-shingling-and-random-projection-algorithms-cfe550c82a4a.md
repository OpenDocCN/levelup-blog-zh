# 收缩和随机投影算法分析

> 原文：<https://levelup.gitconnected.com/an-analysis-of-shingling-and-random-projection-algorithms-cfe550c82a4a>

![](img/de28b15feed9f511138f39b584dc7da6.png)

鲁本·特奥在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在[之前的一篇文章](https://kyleziegler.medium.com/dynamic-programming-for-sequence-similarity-ccc26472912e)中，我们通过 Levenshtein 距离讨论了序列相似性，并在 O(m*n)处对其进行了评估。当我们希望将这种算法应用于更大规模的数据集时，它变得不切实际。这里要考虑的用例是搜索引擎网页索引和剽窃。对于搜索引擎来说，重要的是检测相近的重复项，并评估它们的页面更新、镜像、垃圾邮件、钓鱼网站，并减少抓取错误。

假设我们想要检测严格的重复——在计算机科学中有一种我们已经熟悉的非常快速的方法，散列法。但是，如果我想检测近似重复呢？改变一个字符将完全改变散列算法的输出。

# Shingling (Broder 等人，1997 年)

Shingling，也称为 w-shingling，是一种比较无序集中作为重叠 n 元文法的两个序列之间的相似性的技术。例如，作为重叠三元组的序列“我们将评估的用例”将是{“我们的用例”，“我们将评估的用例”，…}。然后，我们使用 Jaccard 系数来描述这两个集合之间的差异，即由并集归一化的两个集合的重叠。您的输出将以[0，1]为界

请注意，收缩的一个限制是局部顺序被保留，而全局顺序不被保留，当您想要显示数学差异时，可能会出现文档中的项目被四处移动并且您的相似性是相同的情况。收缩的运行时间是 O(m+n ),而 Levenshtein 距离的运行时间是 O(n*m)。

# 步伐

1.  标记两个输入序列
2.  从序列中创建 n-grams
3.  返回两个序列之间的 Jaccard 相似度

# Python 实现

我还包含了 Jaccard 相似性的代码。注意，你可以给它传递一个你想要创建的 n 克的长度值。对于您传入的序列，我会在 2 到 5 之间进行实验。

# 最优化:随机投影算法

MinHash 和局部敏感性散列是进一步改进分析两个序列之间差异的两种方法。例如，使用 shingling 和 Jaccard 来比较文件夹中的文档具有 O(N /2)的上限，因为我们不必比较 doc[0] <> doc[1]和 doc[1] <> doc[0]，所以这是相同的计算。MinHash 是一种比较两个文档的近似算法，比单独使用 shingling 和 Jaccard 相似性要快得多。

## 算法

1.  创建一个最小散列签名，不管集合的长度是多少，该签名的长度总是相同的
2.  随机洗牌 2 组中的物品
3.  比较每个集合中的第一个值:hashed_a[0] == hashed_b[0]

这里的关键是 MinHash 签名比文档中的瓦片数要短得多。更直观的，我们可以把哈希后第一项的概率想成和 shingling 一样，得到两个集合的 Jaccard 相似度。

例子

> vec_a[3，10，7]，vec_b[9，3，11]

我们有一个共同点，洗牌后“3”成为向量中第一个元素的几率有多大？

这里的集合相似度是 1/3，对于 MinHash，它是组件的数量乘以匹配的概率，这与 Jaccard 相似度相同。我们期望 MinHash 相似性的输出接近 Jaccard 相似性。

# 参考

*   [随机投影和哈希法，2015 年](https://www.cs.utah.edu/~jeffp/teaching/cs7931-S15/cs7931/3-rp.pdf)
*   (Broder，Glassman，Manasse，和 Zweig 1997) *网页的句法聚类*。SRC 技术说明。
*   [识别和过滤近似重复的文档](http://cs.brown.edu/courses/cs253/papers/nearduplicate.pdf) (Broder 等人，2000 年)
*   [关于高维空间中距离度量的惊人行为](https://bib.dbvis.de/uploadedFiles/155.pdf) (Aggarwal，Hinneburg 等人，2001 年)
*   [Python 代码的 MinHash 教程](https://mccormickml.com/2015/06/12/minhash-tutorial-with-python-code/) ( [克里斯·麦考密克](https://mccormickml.com/)，2015)

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)
# 序列化并保存 ML 模型，以备将来在 Python 中使用

> 原文：<https://levelup.gitconnected.com/serialize-and-save-ml-models-for-future-use-in-python-c0953e4193f9>

## 增加毅力和避免长时间再培训的实用指南

![](img/e61e895f05def7965b568b98ce00321e.png)

[斯蒂夫·约翰森](https://unsplash.com/@steve_j?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

作为数据科学家，**我们每天都和模型打交道**。有时，模型在训练阶段需要不连续的时间，保存一个(完成的)模型可能会有所帮助。

保存模型的过程被称为**序列化**，它有几个优点。比如**将来重用模型**，**创建一个演示 app** 来展示它的特性，或者干脆**避免再培训**。

存在不同的**序列化(和反序列化)**模型的方式，但是在本文中，**我将介绍以下两种**:

1.  ***序列化库(Joblib，Pickle，…)***
2.  ***机器学习库提供的专用序列化方法***

让我们探索这些替代方案:)

## 1.序列化库

在这个类别中，**两个比较知名的库是** [**泡菜**](https://docs.python.org/3/library/pickle.html) **和** [**Joblib**](https://joblib.readthedocs.io/en/latest/) 。

这些库的主要任务是**实现可再现性，避免多次计算相同的东西**。有保证的持久性允许**恢复计算任务**。这些解决方案通常比定制的写入磁盘脚本效果更好。[1, 2]

你可以两者都用，但是 Joblib 在管理大型 NumPy 数组时更有效。

记住:你应该**永远不要对来自不可信来源**的**对象使用这些库**。

这是因为**加载序列化对象时**可以执行任意(恶意)代码。**只反序列化你可以信任的对象**【3】。

现在让我们看看这些库是如何工作的。

*   ***泡菜:***

在这个例子中，我们序列化了一个名为`model`的模型。这个操作的输出是磁盘上的一个文件，名为`output.pkl`。这个模型可以照常加载和使用。[2]

用 pickle 进行序列化和反序列化。作者代码

*   ***Joblib:***

在这个例子中，我们序列化了同一个叫做`model`的模型。输出是磁盘上的一个文件，这次命名为`output.joblib`。与 Pickle 一样，我们可以加载模型并使用它。Joblib 的`dump`和`load`函数基于 Python Pickle 序列化。[1]

用 joblib 序列化和反序列化。作者代码

如你所见**这两种方法工作方式相似，**保存你的模型**非常简单**。

![](img/bc41fcd6f9a6e69c6067ee0ad4ec9108.png)

由 [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 2.ML 库提供的专用序列化方法

在这个选项中包含了那些为保存和加载特定模型提供定制方法的**库。**

不可能提到所有提供专用方法的库。总是**检查你使用的库的官方文档**以了解是否有可用的方法以及哪些方法可用。一般来说，**如果一个库提供了专用的方法**，那么使用那些总是**最好。**

**举个例子**，不得不介绍一下 Gensim。 [**Gensim**](https://radimrehurek.com/gensim/) **是一个开源的、生产就绪的库**，它提供了**自然语言处理(NLP)和主题建模特性**【4】。

特别是我使用了 **Doc2Vec** 模型，其中 [**将**](https://en.wikipedia.org/wiki/Sentence_embedding) **一组单词嵌入到一个向量**中，考虑到语义(文本的语义表示)。

Doc2Vec 模型可能很难训练，而 **Gensim 提供了保存和加载已训练模型**的具体方法，避免了重新训练。下面是 Gensim `save`和`load`函数的工作方式[4]:

用 Gensim 保存并加载 Doc2Vec 模型。作者代码

**Gensim 为其他模型**提供了类似的方法，比如 Word2Vec。

## 结论

**如果你有合适的工具，序列化一个模型既简单又聪明**。 **Pickle 和 Joblib 提供了一个简单通用的方法**来保存和加载对象和模型，但是**与不可信数据**有一个共同的安全问题。

**其他 ML 库**，如 Gensim，**提供了工作良好且易于使用的定制方法**。

如果你需要**为你的推荐系统序列化一个 Scikit-learn 模型或者一个相似矩阵**，你可以使用 **Joblib** 。如果您使用的是**不同的库，** **查看文档**查看**是否有** **具体方法使用**。并利用它们。

*感谢阅读，快乐模型连载:)*

您可能对以下内容感兴趣:

[](https://pub.towardsai.net/improve-your-classification-models-with-threshold-tuning-bb69fca15114) [## 通过阈值调整改进您的分类模型

### 实用而重要的指南

pub.towardsai.net](https://pub.towardsai.net/improve-your-classification-models-with-threshold-tuning-bb69fca15114) [](https://pub.towardsai.net/sql-vs-nosql-choose-the-most-convenient-technology-4506d831b6e4) [## SQL 与 NoSQL:选择最方便的技术

### 数据库的酸碱属性

pub.towardsai.net](https://pub.towardsai.net/sql-vs-nosql-choose-the-most-convenient-technology-4506d831b6e4) 

## 参考

[1] [JobLib 文档](https://joblib.readthedocs.io/en/latest/persistence.html)，readthedocs.io

[2] [咸菜文献](https://docs.python.org/3/library/pickle.html)，docs.python.org

[3] [安全公告:WML CE sci kit-learn vulnerable to responsible usage](https://www.ibm.com/support/pages/security-bulletin-wml-ce-scikit-learn-vulnerable-irresponsible-usage-0)(2020)，IBM 支持

[4] [Gensim 文件](https://radimrehurek.com/gensim/auto_examples/index.html#documentation)，radimrehurek.com

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**将像你这样的开发人员安置在顶级创业公司和科技公司**](https://jobs.levelup.dev/talent/welcome?referral=true)
# 新的人工智能框架 LightGBM 如何超越 XGBoost

> 原文：<https://levelup.gitconnected.com/how-lightgbm-a-new-ai-framework-outperforms-xgboost-6044ea7b8281>

以速度和准确性为导向:LightGBM 已经获得了大量的研究和有希望的实证结果。

![](img/e3c595484638d678f273285b3b681a09.png)

来自 Unsplash 的 Pavel Danilyuk

在搜索一个比 XGBoost 更快更准确的机器学习优化器时，我偶然发现了 LightGBM，Light Gradient Boosting Machine 的缩写。我自己用它实现了成功，我想撰写这篇文章来分解我对 LightGBM 的理解(它是开源的，免费的，最初是由微软开发的(2016 年，在 MIT 许可下[5])。

LightGBM [1]是一个使用基于树的学习算法的梯度推进框架。它的设计比传统的梯度推进决策树(GBDT)更有效，可以有效地处理大规模数据集。同样，它支持在多个 GPU/CPU 上进行并行训练[2]。此外，它还使用了一些高级功能，如 GPU 加速[3]、自动并行化[4]、大规模树提升支持以及针对其优化过程的高效内存使用[4]。适用于 LightGBM 环境的理想实现包括预测建模、特性选择或工程任务。

![](img/5e6a006eb7354ecc7f8aedb4f0868e20.png)

来自 Unsplash 的马里乌斯·哈克斯塔德(T3)

## 与其他机器学习模型相比，使用 light GBM 的一个优势是它可以训练数据集的速度——在特定的用例中，与 XGBoost 和 AdaBoost 相当，甚至可能更快。

更重要的是，它能比 XGBoost [6]更准确地实现结果。

# **light GBM 和 XGBoost 的 4 个主要区别**

1.LightGBM 使用基于梯度的单侧采样算法[1]来过滤掉不重要的样本，而 XGBoost 使用预先排序的算法技巧[7]来选择正确的分割点，这可能会导致与 LightGBM 相比可能更慢的速度。

2.LightGBM 垂直生长树[8]，而 XGBoost 水平生长树[9]。

![](img/7b96d0d107b433f2028b9f8740868b7a.png)

由[张秀坤大镰刀](https://unsplash.com/@drscythe)从 Unsplash

3.LightGBM 使用贪婪算法预测特定的实现管道[1]，而 XGboost 使用分数函数[10](例如，借助基于牛顿的方法或牛顿推进[11])。

4.学习率度量:LightGBM 具有更高的学习率[12]和良好的泛化能力，而 XGBoost 的学习任务更加保守[13]。

# **light GBM 的实际工作原理**

LightGBM 是一种机器学习算法，通常用于分类和回归任务。它属于被称为梯度推进机器(GBMs)的算法家族。GBM 是强大的机器学习模型，已被证明在各种任务中优于许多其他类型的模型，包括深度神经网络。

LightGBM 使用一种称为基于直方图的宁滨[1]的新技术，使其能够比传统的 GBM 更有效地从数据中学习。此外，LightGBM 可以处理高维的大型数据集，具有相对的可扩展性。

![](img/c0869ecb2a4879f36fb451736dd6d621.png)

来自 Unsplash 的 Taiki Ishikawa

要理解 LightGBM 的工作原理，首先理解 GBM 的一般工作原理是有帮助的。GBM 是组合多个弱学习者模型的预测以产生强预测的集成模型。GBM 通常由一系列决策树组成，其中每棵树都根据数据子集进行训练，并做出预测，然后将这些预测与集成中其他树的预测相结合。

要进一步打开上面斜体部分的内容:

## Bagging (Bootstrap Aggregation)和 Boosting 的概念可以在本文中进一步阐述。也就是说，当我们谈论 Boosting 时，有必要澄清(如 [### 人工智能和产品:帖子和聚光灯，截至 2022 年 7 月 24 日](https://medium.com/u/912391a02ce3#1</h2><div class=) 

[pventures.substack.com](https://medium.com/u/912391a02ce3#1</h2><div class=)

我写了以下与这篇文章相关的内容:他们可能与你有相似的兴趣:

# **XGBoost:它现在的能力和机器学习的用例**

[](https://pub.towardsai.net/xgboost-its-present-day-powers-and-use-cases-b4cac3d6e1d5) [## XGBoost:它在机器学习方面的当前能力和用例

### XGBoost 基于当前实现的深入研究

pub.towardsai.net](https://pub.towardsai.net/xgboost-its-present-day-powers-and-use-cases-b4cac3d6e1d5) 

*参考文献。*

***1。*刘天元(未标明)。LightGBM:一种高效的梯度推进决策树。神经信息处理系统进展，30。[*https://proceedings . neur IPS . cc/paper/2017/file/6449 f 44 a 102 FDE 848669 BDD 9 EB 6b 76 fa-paper . pdf*](https://proceedings.neurips.cc/paper/2017/file/6449f44a102fde848669bdd9eb6b76fa-Paper.pdf)**

**②*。*** *柯、、王泰丰、、祁伟业、、。一种通信高效的决策树并行算法。《神经信息处理系统进展》，第 1271-1279 页，2016 年。*

***3。*** *张、h、斯、&# 38；谢长廷(2017 年 6 月 26 日)。大规模树提升的 GPU 加速。ArXiv.Org。*[*https://arxiv.org/abs/1706.08359*](https://arxiv.org/abs/1706.08359)

***4。*** *(未注明)。ACM 数字图书馆。检索 2022 年 7 月 24 日，转自*[*https://dl.acm.org/doi/abs/10.1145/3357254.3357290*](https://dl.acm.org/doi/abs/10.1145/3357254.3357290)

***5。*** *欢迎 tLightGBM 的 stLightGBM 的文档！— LightGBM 3.3.2 文档。(未注明)。检索 2022 年 7 月 24 日，转自*[*https://lightgbm.readthedocs.io/en/v3.3.2/*](https://lightgbm.readthedocs.io/en/v3.3.2/)

**6*。*** *Daoud，E. A .(未注明)。使用家庭信用数据集比较 XGBoost、LightGBM 和 CatBoost。国际计算机与信息工程杂志，13(1)，6–10。*

**7。** *(未注明)。ACM 数字图书馆。检索到 2022 年 7 月 24 日，来自*[*https://dl.acm.org/doi/abs/10.1145/3436286.3436293*](https://dl.acm.org/doi/abs/10.1145/3436286.3436293)

***8。*** *【内梅特】，博金，&# 38；米哈尔诺克。(2019 年 1 月 1 日)。预测能源发展的机器学习方法 xgboost 和 lightgbm 的比较。斯普林格国际出版公司。*[*https://link . springer . com/chapter/10.1007/978-3-030-31362-3 _ 21*](https://link.springer.com/chapter/10.1007/978-3-030-31362-3_21)

***9。*** *【孙，】刘，&# 38；司马，Z. (2020)。基于 LightGBM 的加密货币价格趋势预测模型。金融研究快报，32，101084。*[*https://doi.org/10.1016/j.frl.2018.12.032*](https://doi.org/10.1016/j.frl.2018.12.032)

***10。*** *不平衡-XGBoost:利用加权和焦点损失进行二元标签-不平衡分类。(未注明)。模式识别字母，136，190–197。*[*https://doi.org/10.1016/j.patrec.2020.05.035*](https://doi.org/10.1016/j.patrec.2020.05.035)

***11。*** *尼尔森博士(n.d .)。用 xgboost 提升树。*[*https://ntnuopen . ntnu . no/ntnu-XM lui/bitstream/handle/11250/2433761/16128 _ full text . pdf*](https://ntnuopen.ntnu.no/ntnu-xmlui/bitstream/handle/11250/2433761/16128_FULLTEXT.pdf)

**12*。*** *LightGBM:金融行业预测客户忠诚度的有效决策树梯度推进方法。(未注明)。IEEE Xplore。检索 2022 年 7 月 24 日，转自*[*https://ieeexplore.ieee.org/abstract/document/8845529*](https://ieeexplore.ieee.org/abstract/document/8845529)

**13*。*** *伊斯兰教，S. F. N .，Sholahuddin，a .，&# 38；阿卜杜拉(2021)。极端梯度推进法在美元兑印尼盾汇率预测中的应用与分析。物理学杂志:会议系列，1722(1)。*[*https://doi.org/10.1088/1742-6596/1722/1/012016*](https://doi.org/10.1088/1742-6596/1722/1/012016)

***14。***【科克尔】【h】【奥多姆】【p】【杨】【s】【38 号】；纳塔拉詹(2020 年)。知识密集型梯度推进的统一框架:利用人类专家处理噪声稀疏领域。AAAI 人工智能会议论文集，34(04)，4460–4468。[*https://doi.org/10.1609/aaai.v34i04.5873*](https://doi.org/10.1609/aaai.v34i04.5873)

***15。*** *SecureGBM:安全多方梯度提升。(未注明)。IEEE Xplore。检索 2022 年 7 月 24 日，来自*[*https://ieeexplore.ieee.org/abstract/document/9006000*](https://ieeexplore.ieee.org/abstract/document/9006000)

***16。*** *k-Means 聚类— Michael Fuchs Python。*[*https://Michael-fuchs-python . netlify . app/2020/05/19/k-means-clustering/*](https://michael-fuchs-python.netlify.app/2020/05/19/k-means-clustering/)

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级达人集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)
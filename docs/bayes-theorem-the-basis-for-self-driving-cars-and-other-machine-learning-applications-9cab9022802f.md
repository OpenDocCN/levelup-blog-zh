# 贝叶斯定理:自动驾驶汽车和其他机器学习应用的基础

> 原文：<https://levelup.gitconnected.com/bayes-theorem-the-basis-for-self-driving-cars-and-other-machine-learning-applications-9cab9022802f>

## 贝叶斯定理简介

![](img/89299b9b96f088b5e72c499efa3fb524.png)

照片由[托马里克](https://www.pexels.com/@tomas-malik-793526?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从[佩克斯](https://www.pexels.com/photo/iceland-bird-s-eye-view-cars-road-1670457/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

[贝叶斯定理](https://blogs.scientificamerican.com/cross-check/bayes-s-theorem-what-s-the-big-deal/)由托马斯·贝叶斯在 18 世纪发明，描述了一种简单而强大的方法，用于计算给定一条新证据/观察时信念/假设发生的概率。

纵观历史，贝叶斯定理已经被应用于[寻找核弹](https://www.redalyc.org/pdf/5117/511751360022.pdf)并且是机器学习算法(分类器)的基础。它被用于垃圾邮件过滤、无人驾驶汽车、评估金融风险等等。

这些算法可以准确识别事件发生的概率，从而做出正确的决策。

**我们来过一个经典的例子。**

想象你感觉不舒服，怀疑可能是很严重的事情。然后你去做一个测试，不幸的是结果是阳性。考虑到测试有 95%的准确率——假设测试呈阳性，患这种疾病的概率是多少？**这是关键问题。**

面对这样的事实，你会感到绝望并匆忙下结论，这是很常见的。认为有 95%的可能性或接近 95%的可能性是合理的，因为这毕竟是测试的准确性。

但是故事还没完。

在我继续之前，让我具体说明一下这项测试 95%的准确性——这意味着 100 个患病者中有 95 个测试为阳性，100 个未患病者中有 95 个测试为阴性。假阳性率是 5%(我们也需要这个来计算)

根据贝叶斯定理，为了更精确地计算概率，您必须考虑三个组成部分，然后将它们插入该公式:`**P(B|E) = P(E|B)*P(B)/P(E)**`

p =概率；b =信念；e =证据。让我们来看看每一个。

*   P(乙)或 P(人有此病)。**这个人得这种病的概率有多大(信念)？**这叫做“先验概率”。假设 100 个人中有 2 个人有这种情况。那么概率是 0.02 或者 2%。
*   P(E|B)或(人测试呈阳性|鉴于疾病)。如果这个人患有这种疾病，检测呈阳性的概率有多大？由于测试有 95%的准确度，则该值为 0.95。
*   P(E)或 P(个人测试呈阳性)。**不管是否患病，检测呈阳性的概率有多大？**该要素是患有疾病且检测呈阳性(“真阳性”/公式的分子部分)和未患有疾病但检测呈阳性(假阳性)的组合。对于后者，你将“人群中没有患病的人”乘以“假阳性”，即 0.98，再乘以“假阳性”，即 0.05。

```
**P(B|E)** = 0.95 * 0.02 / (0.95 * 0.02) + (0.98 * 0.05)
       = 0.019 / 0.019 + 0.049
       = 0.019 / 0.068
       = 0.27941 **(27%)**
```

当你迭代地应用这个公式时，贝叶斯定理的力量会更加明显。当你计算每一个新证据的概率时，结果会越来越准确。如果你做第二次测试，患病的概率会从 27%上升到 **86%** ！那是因为，第一次测试后，P(B) /“先验”变成了 0.27。已经不是 0.02 了。

贝叶斯定理是被称为朴素贝叶斯的监督机器学习算法/分类器的基础。在下一篇文章中，我将介绍用 Python 从头开始实现它。

感谢阅读。

***供进一步阅读:***

[](https://towardsdatascience.com/deep-learning-understand-the-principle-ad146d9f54dd) [## 深度学习:理解原理

### 温和的介绍。它解决了什么问题，为什么是分层结构

towardsdatascience.com](https://towardsdatascience.com/deep-learning-understand-the-principle-ad146d9f54dd) [](https://medium.com/programming-for-beginners/how-to-use-python-generators-to-efficiently-process-large-data-sets-38e18bd8ae29) [## 如何使用 Python 生成器高效地处理大型数据集

### python 生成器函数介绍。

medium.com](https://medium.com/programming-for-beginners/how-to-use-python-generators-to-efficiently-process-large-data-sets-38e18bd8ae29)
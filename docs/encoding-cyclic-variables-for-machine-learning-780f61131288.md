# 为机器学习编码循环变量

> 原文：<https://levelup.gitconnected.com/encoding-cyclic-variables-for-machine-learning-780f61131288>

特征工程是建立精确的机器学习模型的关键活动。通过转换信息的表示，我们使机器学习模型更容易将我们的输入变量映射到我们的目标值。突然出现的一类常见特征是循环变量。

当表示时间特征时，例如一周中的某一天或一天中的某个小时，经常会出现循环变量。以星期几为例。一周中的某一天的常见表示法分配整数，例如星期日是 0，星期一是 1，星期二是 2，依此类推。对于某些类别的模型来说，这种表示是毫无用处的。例如，[逻辑回归](https://en.wikipedia.org/wiki/Logistic_regression)模型将很难从这种表示中提取很多信息，因为目标很可能与这种表示不线性相关。一个常见的解决方案是使用 [one-hot-encoding](https://en.wikipedia.org/wiki/One-hot) 将 7 天转换成 7 个二进制变量。但是这种方法会给我们带来什么损失呢？

![](img/d2c511c88c777f5e317dc09ea35fae49.png)

纯粹的分类特征彼此之间没有距离。用一个热编码对这样的变量进行编码不会丢失信息，因为没有天生的邻近性。例如，对红色、黄色和橙色进行一次热编码意味着红色并不比黄色更接近橙色。以类似的方式对一周中的日子进行编码会抛出这样的信息，即星期三比星期五更接近星期四。如果值的基数足够低，并且训练数据中存在足够多的示例，则丢失相似性信息可能不会有太大影响，但是这样做会使模型更难学习可概括的模式，因为一周中每一天的影响都是独立学习的。

在设计特征编码时，考虑您正在使用的算法是一个很好的练习。

![](img/b54cb9083a7b9fb0830d66a77a1ad909.png)

照片由[哈利·汉密尔顿](https://unsplash.com/@haleywayphotography?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

对于逻辑回归模型，我们学习每个输入特征的系数。如果我们假设当我们学习星期二的影响时，我们获得了一些关于星期三的影响的信息，我们就能进化出我们的一热编码。我们可以将星期二编码为[0，0，1，0，0，0，0]而不是编码为[0，0.5，1，0.5，0，0]或[0.25，0.5，1，0.5，0.25，0.25，0.0]，随着距离的增加，强度逐渐减弱。这种效果平滑类似于图像处理中使用的[卷积滤波器](https://adeshpande3.github.io/A-Beginner%27s-Guide-To-Understanding-Convolutional-Neural-Networks/)，其中核权重通常是自由学习的。在空间分析中，核通常是封闭形式的，例如 [Matérn](https://en.wikipedia.org/wiki/Mat%C3%A9rn_covariance_function) 或[高斯](https://en.wikipedia.org/wiki/Gaussian_process)协方差函数，其中平滑程度通过核带宽值来控制。在我们的例子中，传播距离和形式可以控制为通过交叉验证优化的超参数。

![](img/d381f5dd0c7aeaccfbf01eebe0a2df47.png)

照片由 [Fabrice Villard](https://unsplash.com/@fabulu75?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果我们把我们的考虑转向基于决策树的模型，比如 [XGBoost](https://xgboost.readthedocs.io/en/latest/) 或者随机森林，我们会得到不同的编码。决策树在特定的切割点递归分割变量。考虑一下我们的星期编码，它从 0 开始给星期天编号。如果我们的数据包括工作日对周末的影响，那么将周日(0)和周六(6)从工作日中分离出来需要两次分离。相反，如果我们以星期一为 0 开始星期编码，那么只需要一次分割。

与我们第一次检查的回归模型不同，除了从 6 到 0 的不连续性之外，决策树在我们的 0 到 6 编码中自然地保留了工作日的邻近性。我们如何解决这个问题？

一种方法是简单地提供围绕一周中的几天旋转的多个 0 到 6 编码；也许一种编码是星期一为 0，另一种编码是星期五为 0。这种冗余为模型提供了更多的选项。

不过，要挑出周二和周三，需要两个切入点。相反，如果我们认为几天可能会有影响，我们可以改变编码。XGBoost 经常使用一种热编码来表示类别。在星期几的情况下，这将向模型添加 7 个特征。使用相同数量的要素，我们可以生成表示每天偏移量的要素。例如，周日特性在周日为 0，在周一或周六为 1，在周二或周五为 2，依此类推。对于周日的训练记录，这 7 个特征将具有值[0，1，2，3，3，2，1]。使用这种表示法，该模型只需一次切割就可以提取出以星期日为中心对称的任意天数。

诸如神经网络的其他模型可能受益于其他编码，例如[空间符号变换](https://medium.com/life-at-hopper/ai-in-travel-part-2-representing-cyclic-and-geographic-features-4ada33dd0b22)。

关于什么是最好的，没有硬性的规则，因为它将取决于您的用例中效果的动态，但是理解您的模型在决定首先尝试什么时如何学习是一个好的起点。
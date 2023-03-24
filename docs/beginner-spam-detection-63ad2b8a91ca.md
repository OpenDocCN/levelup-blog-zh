# 初级垃圾邮件检测

> 原文：<https://levelup.gitconnected.com/beginner-spam-detection-63ad2b8a91ca>

![](img/5ffccbe17eba9063a9c3f5494fcd9635.png)

[**垃圾邮件 Vs 火腿**](https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.mentalfloss.com%2Farticle%2F20997%2Forigin-spam-food-spam-email&psig=AOvVaw25Vdg1kAAzZMwFOl4vrgFW&ust=1598505365606000&source=images&cd=vfe&ved=0CA0QjhxqFwoTCJjrtO2OuOsCFQAAAAAdAAAAABAK)

这是 python 中的一个小项目，使用机器学习来检测给定的文本是垃圾邮件还是 ham(不是垃圾邮件)。我在完成了密歇根大学在 Coursera 上的“Python 中的应用文本挖掘”课程后做了这个项目，课程的链接在博客的末尾。在这里，我将尝试解释我的代码中的每一步，如果你想要完整的代码，GitHub 链接也将在最后提供。

我用我的本地机器做这个项目，规范如下。虽然这是一个非常小的项目，所以你不需要太多的计算能力。

# 规格

名称:宏碁 Predator Helios 300 (2019)

显卡:英伟达 GeForce GTX 1660 Ti

处理器名称:英特尔酷睿 i7–9750h

内存:16 GB

# 导入库

我使用 Pandas 和 NumPy 进行数据操作，使用 matplotlib 进行图形绘制，使用 sklearn 进行预处理、模型创建和模型评估。我将解释当我在代码中使用它们时，每个库在做什么。

P.S. — %matplotlib 笔记本，是一款 jupyter 笔记本神奇功能。

# 分析数据

使用 df.head()函数来查看数据，df 是 Pandas DataFrame 对象，我已经从 CSV 函数中加载了数据，这里我们看到只有两列。文本，包含用于检测和目标的文本，作为标签来区分该文本是否是垃圾邮件。

现在，我已经绘制了数据集中有多少数据点是垃圾邮件或垃圾邮件。在这里，我们可以看到这是一个不平衡的数据集，因为垃圾邮件文本的数量远远小于火腿文本。

虽然我没有对数据做任何预处理，比如处理数据的不平衡，或者处理停用词，像‘the’，‘a’等词，以及像“.”这样的符号。, ';'等。你可以做这些事情来获得更好的结果。

# 特征工程

我把数据按 80% — 20%的比例拆分，分别是训练和测试。然后，我用以下参数初始化了一个计数矢量器。尽管你可以改变这些来获得更好的结果，但是记住不要过度拟合你的模型。

初始化矢量器后，我拟合并转换了训练和测试数据，并给它们添加了一些新的特性。增加的特性是文本的总长度、文本中数字字符的总数和文本中的字数。

add_feature()函数是我从课程中复制的，它是一个方便的函数，正如它的名字一样，向现有数据添加要素(矢量化)。

# **模特创作**

在创建我自己的模型之前，我使用了一个虚拟分类器来获得基线性能。用训练数据对其进行拟合，并使用测试数据进行预测。

现在，我选择逻辑回归作为我的模型，超参数 C=100，max_iter=1000，用训练数据拟合它，并对测试数据进行预测。您可以尝试 SVC 或任何其他分类器，看看结果如何变化。

# **模型评估**

使用混淆矩阵评估模型、虚拟回归和逻辑回归。我们可以看到，逻辑回归比我们的虚拟模型做得好得多。但它仍然不完美，因为据我所知，检测垃圾邮件是一个更加精确的问题，所以仍然有改进的空间。

# **结果**

这是它的项目，是的，这是一个没有什么太复杂的小项目。现在，它更像是一个初级项目，但有可能变得更加复杂和高级。因为检测垃圾邮件是一个非常常见的问题，这只是一个简单的问题，但是您可以使用 nltk 这样的库添加更多的预处理，从而获得更好的结果。

# **链接**

GitHub:[https://github.com/Conero007/Spam-Detection](https://github.com/Conero007/Spam-Detection/tree/master)

领英:[https://www.linkedin.com/in/jayesh-rohan-singh-40a122197/](https://www.linkedin.com/in/jayesh-rohan-singh-40a122197/)

课程:[https://www . coursera . org/learn/python-text-mining/home/welcome](https://www.coursera.org/learn/python-text-mining/home/welcome)
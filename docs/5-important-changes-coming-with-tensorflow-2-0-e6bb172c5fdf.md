# TensorFlow 2.0 带来的 5 项重要变化

> 原文：<https://levelup.gitconnected.com/5-important-changes-coming-with-tensorflow-2-0-e6bb172c5fdf>

## 什么是新的，什么是好的！

![](img/20253621247da76149ac07a8af4933e8.png)

> 想获得灵感？快来加入我的 [**超级行情快讯**](https://www.superquotes.co/?utm_source=mediumtech&utm_medium=web&utm_campaign=sharing) 。😎

TensorFlow 2.0 是针对 [TensorFlow 开源库](https://www.tensorflow.org/)的下一个主要版本。自 2015 年首次发布以来，TensorFlow 经历了许多重大变化，主要集中在扩展库的功能，以便能够做机器学习从业者希望可能做的一切事情！

TensorFlow 2.0 将代表图书馆发展的一个重要里程碑。在过去的几年里，ML 从业者对 TensorFlow 的主要抱怨是它复杂的黑箱风格。

2.0 版本的设计直接旨在解决这个问题，使 TensorFlow 更加用户友好和易于使用。在这里，我们将看看 TensorFlow 新版本中的 5 个重要变化，它们是这一努力的一部分。

# (1)默认急切执行

为了在 TF 1.x 中构建一个[神经网络](https://en.wikipedia.org/wiki/Artificial_neural_network)，我们需要定义一个张量流图。这是一个非常抽象的黑盒数据结构。运行时我们看不到里面有什么。

如果我们试图在运行时使用一个简单的 Python `print()`函数打印一个 TF 图变量，我们将看不到变量的值。相反，我们会看到对图形节点的引用——而不是我们要找的信息！

热切的执行改变了这一切。TensorFlow 代码现在可以像普通 Python 代码一样急切地运行。它开始看起来和工作起来很像 basic Numpy！

不再需要创建一个`tf.Session()`并且不能看到图形节点的值。使用一个简单的`print()`，所有变量立刻可见。

## TensorFlow 1.x 中的培训:

## TensorFlow 2.0 中的培训:

# (2) Keras 作为高级 API

对于许多 ML 从业者来说， [Keras](https://keras.io/) 是构建深度学习模型的首选 API。它只是*所以*比其他 ML 框架如 TensorFlow 或 MXNet 更容易使用。网络可以以非常直观的方式构建，将输入和输出从一个函数传递到下一个函数，其中每个函数代表一个网络层，如卷积或池。

最后，Keras 在 2.0 版本中成为了 TensorFlow 的官方高级 API。当您安装 TensorFlow 2.0 时，它将与 Keras 一起提供。它与 TensorFlow 无缝集成，不需要任何类型的桥代码。

这一点的好处之一是能够将 [Keras *与* TensorFlow](https://towardsdatascience.com/4-awesome-things-you-can-do-with-keras-and-the-code-you-need-to-make-it-happen-858f022eec85) 结合使用——它不必再是完全脱节的替代品。当 Keras 足以完成工作时，您可以使用 100%的 Keras 代码。如果您需要做一些奇特的事情，如自定义层或全新的训练方案，那么您可以简单地用 TensorFlow 代码添加这些额外的代码片段和函数。这是一个完美的平衡！

# (3) API 清理

随着 TensorFlow 1.x 的发展，许多定制和贡献 API 涌现出来，试图扩展该库的功能。

在 TensorFlow 1.x 中构建神经网络时，你有很多选项可以选择:`tf.slim` *、* `tf.layers` *、* `tf.contrib.layers` *、*和`tf.keras`。除此之外，还提供了更多用于调试、数学和特定 ML 函数的定制代码。不用说，已经变得相当乱了！

2.0 版本进行了大量的 API 清理，以简化和统一 TensorFlow API。许多 API，如 tf.app、tf.flags 和 tf.logging，在 2.0 中不是消失了就是被移动了。拥有 Keras 等价物的 API 已经完全被更简单的 Keras 版本所取代。

总而言之，这是一个受欢迎的变化。该库的一般用法现在简单多了。文档更清晰。一个重要的原因是:代码现在可以更容易地在用户之间共享，因为每个人最终都将使用相同的 API！

一个额外的好处是:如果你想用现在移动的 API 将你的 TF 1.x 代码升级到 TF 2.0，你可以使用 [v2 升级脚本](https://www.tensorflow.org/beta/guide/upgrade)！

# (4) TF 数据集

不再需要那些丑陋的[队列管理器](https://www.tensorflow.org/api_docs/python/tf/train/queue_runner/QueueRunner)，它们是大型数据集优化训练所需要的。对于 TensorFlow 2.0，队列管理器已经被完全替换为 [tf.data](https://www.tensorflow.org/guide/datasets) 。

有了 tf.data，使用输入管道以更清晰的方式读取定型数据。API 本身得到了简化，更易于使用，处理方式与 Keras 中的 [*fit_generator* 和相关 *flow* 函数](https://keras.io/models/sequential/)类似。还支持从内存数据(如 Numpy 数组)中方便地输入数据。

关于如何在 TensorFlow 2.0 中使用 tf.data 的教程，请看这个[链接](https://www.tensorflow.org/guide/datasets)！

# (5)2.0 版本仍然可以运行 TensorFlow 1.x 代码

最后，一个最大的欣慰来自于听说运行 TensorFlow 2.0 时还能使用 TensorFlow 1.x 代码。这一点非常重要，因为它使得从 1.x 版本到 2.0 版本的过渡更加平稳，不会出现任何重大问题。

当然，这并不能让您利用 TensorFlow 2.0 中的许多改进。但是它允许您进行稳定的转换，根据需要用 2.0 的改进替换部分代码。

# 喜欢学习？

在[推特](https://twitter.com/GeorgeSeif94)上关注我，我会在这里发布所有最新最棒的人工智能、技术和科学！也在 [LinkedIn](https://www.linkedin.com/in/georgeseif/) 上和我联系吧！
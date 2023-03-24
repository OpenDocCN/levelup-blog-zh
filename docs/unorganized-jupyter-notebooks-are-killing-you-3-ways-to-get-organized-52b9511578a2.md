# 杂乱无章的 Jupyter 笔记本会要了你的命:3 种变得有条理的方法

> 原文：<https://levelup.gitconnected.com/unorganized-jupyter-notebooks-are-killing-you-3-ways-to-get-organized-52b9511578a2>

## Kedro、Azure 机器学习和 Scikit-Learn 管道的简要概述

![](img/affc80f8769572526407d64f8832bfe3.png)

照片由[在](https://unsplash.com/@theblowup?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) [Unsplash](https://unsplash.com/s/photos/pipeline?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上放大

不要误会我；Jupyter 笔记本(以及 Databricks 笔记本)非常适合执行初始探索性数据分析、探索模型性能和测试初始 ETL 作业。然而，仅仅依靠笔记本进行训练和部署机器学习模型限制了可重复性和标准化。很难抢到别人的 jupyter 笔记本立马滚！

## 那么，我们该如何将 Jupyter 笔记本“探索”转化为可重复的标准化管道呢？

对于将最初在笔记本中开发的代码转换成一组标准化的管道，我们有很多选择。这些可以包括特定于机器学习库的选项(例如 scikit-learn、Keras 等)。)，特定于云提供商(例如 Azure Machine learning 或 AWS Sagemaker)，或者跨大多数库和云(例如 Kedro)

## 在我们开始浏览一些管道示例和后续步骤之前，让我们进一步讨论一下为什么管道很重要

简而言之，管道使您的工作能够超越个人项目，进入可以快速共享、协作构建和轻松部署的领域。管道的一些具体特征:

*   **更简单的源代码控制:**将你的 jupyter 笔记本拆分成 python 文件、类和对象的可消化子集，使你能够执行更好的源代码控制(Github、Azure DevOps、AWS CodeCommit 等。)并跟踪更改
*   **模块化代码:**将您的单块 jupyter 笔记本拆分成离散的 python 文件和类，也使您能够轻松地重用以前管道中的组件，而不是从头开始重新工作另一个机器学习项目
*   **更容易部署:**机器学习并不以高性能而告终。它以部署到 web 应用程序的推理结束，以重要性洞察推动更好的业务决策，并以可伸缩性决策支持生产数据的洞察生成。所有这些都可以通过管道来简化。

# 精选机器学习管道解决方案概述

## [**凯德罗**](https://kedro.readthedocs.io/en/stable/)

*   **要点:**开源 python 框架，提供了执行管道、组织数据和简化参数选择的结构。额外收获:使用 Kedro-Viz 可以轻松地可视化管道流
*   **优点:**可用于所有机器学习库(使用 Python)和云平台
*   **缺点:**对于高级用例来说，可能需要一点学习曲线
*   **如何开始:**查看[文档](https://kedro.readthedocs.io/en/stable/get_started/install.html)和下面的分类管道示例

 [## GitHub-van-William/kedro-分类-蘑菇:ked ro 机器学习分类

### 这个项目一开始是对 Kedro 的介绍性探索。更直白地说，它开始是寻找更好的…

github.com](https://github.com/van-william/kedro-classification-mushrooms) 

## [**Azure 机器学习**](https://azure.microsoft.com/en-us/services/machine-learning/) **(或其他云 PaaS 解决方案)**

*   **要点:** Azure 提供不同级别的机器学习服务，从无代码到低代码，再到托管计算实例；[*Azure Machine Learning Designer*](https://docs.microsoft.com/en-us/azure/machine-learning/concept-designer)*提供了低/无代码模块与定制 python 或 SQL 代码块的平衡*
*   ***优点:**这提供了广泛的工具(超越管道),并支持与其他云资源的简化集成*
*   ***缺点:**这将你的机器学习解决方案与特定的云提供商结合起来，如果扩展不当，可能会变得昂贵*
*   ***如何入门:** [在这里注册一个免费的 Azure 账号](https://azure.microsoft.com/en-us/free/)即可入门。查看下面我的 Azure 机器学习指南:*

*[](/from-no-code-to-code-in-azure-machine-learning-38ee6b556de2) [## Azure 机器学习从无代码到有代码

### 使用骨科多类数据的 Azure ML 演练

levelup.gitconnected.com](/from-no-code-to-code-in-azure-machine-learning-38ee6b556de2) 

## [**Scikit-Learn**](https://scikit-learn.org/stable/modules/generated/sklearn.pipeline.Pipeline.html)**(或其他框架特有的管道)**

*   要点: Scikit-Learn 在其开源库中提供了管道。这允许用户在一个管道流中简单地将预处理、训练和评分串在一起。([例此处](https://towardsdatascience.com/how-to-use-sklearn-pipelines-for-ridiculously-neat-code-a61ab66ca90d))
*   **优点:** Scikit-Learn 内置管道易学易用。Scikit-learn 被广泛使用，所以用 scikit-learn 共享和部署不会是一个大问题
*   **缺点:** Scikit-Learn 管道也相当依赖于在 Scikit-Learn 中构建模型。这可以通过一些变通方法解决([链接到示例](https://gist.github.com/MaxHalford/9bfaa8daf8b4bc17a7fb7ba58c880675))
*   **如何开始:**媒体上的这个例子是一个很好的起点:

[](https://towardsdatascience.com/how-to-use-sklearn-pipelines-for-ridiculously-neat-code-a61ab66ca90d) [## 如何将 Sklearn 管道用于极其简洁的代码

### 我喜欢的关于 Scikit-Learn 的一切，都在一个地方

towardsdatascience.com](https://towardsdatascience.com/how-to-use-sklearn-pipelines-for-ridiculously-neat-code-a61ab66ca90d) 

# Kedro 的其他注意事项

## 节点和管道

Kedro 很好地利用了节点和管道来分解离散的机器学习功能，并将它们串成一个管道( [Kedro 有一个很好的 hello world 教程和一个关于 docs 的更详细的教程](https://kedro.readthedocs.io/en/stable/get_started/hello_kedro.html)

**节点:**节点是由功能、输入和输出组成的管道的离散部分。下面是一个简单的例子:

这允许您在管道中创建特定的操作(如拆分数据)，这些操作可以很容易地复制到其他机器学习项目中。示例中的输入是用于分割数据的 pandas 数据帧和分割选项的参数字典(稍后将详细介绍)。总的来说，这最大限度地减少了在 jupyter 笔记本上更改参数和功能的人工操作。

**管道:**管道将节点串在一起，方便数据通过多个函数传递。下面是一个例子。

在这里，我们看到一个标准的数据科学管道分割预处理数据，训练模型，并对其进行评估。所有这些都是通过将每个节点的输入和输出串联起来实现的。

## 数据目录和参数

但是数据输出和参数是如何表示的呢？

所有数据都在数据目录 yaml 文件中表示，所有参数都在其代表性的管道配置 yaml 文件中捕获

**数据目录:**该 yaml 文件用于表示管道文件中使用的输入名称。下面是一个例子:

在这里，您可以看到数据集(蘑菇分类)以. csv 文件的形式存储在管道的中间部分。通过运行管道。csv 文件保存在目录路径中(所有 Kedro 型号使用的标准目录路径)。这使得任何人都可以克隆存储库并运行管道，而无需查找文件和更改目录名。

**参数:**Kedro 没有将参数隐藏在 jupyter 笔记本的函数或参数字典中，而是为每个管道提供了一个 yaml 文件来存储管道参数。例如，见下文:

所有这些参数都可以很容易地从管道中引用。这简化了使用机器学习管道的特定参数。

Kedro 还有很多，包括 Kedro-Viz。[一定要检查文件](https://kedro.readthedocs.io/en/stable/index.html)。

**总的来说，管道可以将特定的个人项目转化为可扩展的机器学习项目，可以很容易地部署到现实世界的解决方案中**

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看更多内容请参见[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)*
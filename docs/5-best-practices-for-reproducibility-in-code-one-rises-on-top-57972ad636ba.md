# 代码可再现性的 5 个最佳实践——一个上升到顶端

> 原文：<https://levelup.gitconnected.com/5-best-practices-for-reproducibility-in-code-one-rises-on-top-57972ad636ba>

## 任何开发代码的过程都应该以可再现性为基础:这是 5 个最佳实践和建议。

![](img/bc87f30861432fc1d7a8644ed31f25b4.png)

来自 Unsplash 的 Florian Schmetz

再现性是可重复执行的定义特征，尤其是在编码中。将原始数据和代码转换成发布的结果是复杂的，通常涉及许多步骤和不同的软件包。尽管编码在这个过程中起着至关重要的作用，但对于如何格式化或共享代码，还没有被广泛接受的标准。因此，其他人通常很难理解或复制用于生成已发布结果的方法。在这篇文章中，我提出编码实践会显著影响工作的可重复性。此外，我还将讨论实施这些实践所涉及的一些挑战，尤其是与专有软件和数据相关的挑战。

## 任何开发代码的过程都应该建立在可再现性的基础上。结果必须能够实现可重复性，由其他开发者或用户进行验证。

# 最佳实践的 5 种方法

我推荐一组编码的最佳实践，我将其分为五个主题:

(1)跟踪变化；

(2)使用标准格式；

(3)记录代码；

(4)公开共享代码；和

(5)独立于平台的可移植代码。

对于每个主题，我描述了最佳实践建议以及实现建议。

![](img/ef75d9648f736e42c58e26ca134cb59a.png)

由来自 Unsplash 的 [Matteo Vistocco](https://unsplash.com/@mrsunflower94)

# **1。** **轨迹变化**

跟踪项目期间对代码的所有更改是至关重要的。这不仅包括对代码本身的更改，还包括对输入数据文件和配置设置的更改。跟踪变更允许团队在必要时返回并重新创建代码的先前版本。这也使其他人更容易理解代码是如何随着时间的推移而演变的，并识别出可能在开发过程中引入的潜在错误。有几种不同的工具可用于跟踪代码库中的变更:

1.git([https://git-scm.com/](https://git-scm.com/))；

2.汞([https://www.mercurial-scm.org/](https://www.mercurial-scm.org/))；

3.颠覆([https://subversion.apache.org/](https://subversion.apache.org/))；和

4.比特桶([https://bitbucket.org](https://bitbucket.org/))。

一些 ide(集成开发环境)也有跟踪代码变更的内置选项(例如..g .、RStudio 和 py charm[https://www . jetbrains . com/py charm/)。](https://www.jetbrains.com/pycharm/).)

**推荐:**

使用版本控制系统跟踪代码更改、输入数据文件和配置设置。

选择适合项目规模和复杂性以及合作者偏好的工具。

在如何使用工具上保持一致(例如，经常提交，写清楚的提交信息)，以使其他人容易理解您项目的历史。

![](img/f240b7b88ad798542ee00e62a0e824f4.png)

由来自 Unsplash 的[陶黎黄](https://unsplash.com/@h4x0r3)

# **2。** **使用标准格式**

当共享代码时，使用一种广大用户可读的标准格式是很重要的。共享代码最常见的格式是纯文本，任何文本编辑器或文字处理器都可以阅读。其他常见格式包括:

1.降价([https://daringfireball.net/projects/markdown/](https://daringfireball.net/projects/markdown/))；和

2.超文本标记语言。

对于较大的项目，使用支持协作特性的 IDE 也可能有所帮助，例如版本控制和差异(比较版本之间的变化)。

**建议:**

共享代码时使用纯文本、Markdown 或 HTML，以便广大用户可以轻松阅读。

对于较大的项目，考虑使用具有内置协作功能的 IDE。

在整个项目中使用一致的编码风格，使其他人更容易阅读和理解您的代码。

![](img/5124a4b973802a7a1118bc229a878e0a.png)

来自 Unsplash 的 Jon Del Rivero

# **3。** **代码文档**

你的代码是做什么的，它是如何工作的？文档应包括有关代码用途、如何运行代码以及所需的任何依赖项的信息(例如，需要安装的库或工具)。好的文档使其他人更容易复制您的成果并在您的工作基础上更进一步。有几种不同的方法来记录代码，包括:

1.内嵌注释；

2.文档字符串([http://www.pythonforbeginners.com/basics/python-docstrings](http://www.pythonforbeginners.com/basics/python-docstrings))；和

3.自述文件([https://help.github.com/articles/about-readmes/](https://help.github.com/articles/about-readmes/))。

当使用注释时，目标是清晰而不是简洁——记住，阅读您代码的人可能不熟悉问题域或所使用的特定编码语言。在适当的地方添加外部文档的链接也很有帮助(主要包括函数或类参考手册)。

**推荐:**

使用注释、文档字符串和自述文件来记录代码，以便其他人能够理解它的功能和工作原理。

在你的文档中保持清晰和简洁，以便不熟悉问题领域或使用的编码语言的人也能容易地理解。

适当时包括外部文档的链接。

![](img/4b4c6810e96e05d019c74421e8d05846.png)

由 Pexels 的[罗德尼制作](https://www.pexels.com/@rodnae-prod/)

# **4。** **公开分享代码**

公开共享代码是确保他人可以访问和使用您的作品的最佳方式之一。共享代码有几种不同的方式，包括:

1.在 GitHub 上托管存储库([https://github.com](https://github.com/))；

2.bit bucket([https://bitbucket.org](https://bitbucket.org/))；或者

3.git lab([https://about.gitlab.com](https://about.gitlab.com/))。

除了代码之外，共享运行代码所需的数据和其他资源也很重要。例如，如果您的代码使用自定义数据集，则它应该与代码一起提供。如果您的代码作为期刊文章或会议论文的一部分发布，您也应该将代码(以及任何其他必需的文件)作为补充材料共享。

**建议:**

公开共享代码，以便他人可以访问和使用您的作品。

GitHub、Bitbucket 或 GitLab 上的主机存储库。

共享运行代码所需的数据集和其他资源。

# **5。** **创建可移植且独立于平台的代码**

创建可移植且独立于平台的代码可以确保其他人可以在他们的系统上运行您的代码。在制作可移植代码时，有几个因素需要考虑，包括使用标准库、指定绝对路径而不是相对路径，以及检查特定于系统的依赖性(例如，操作系统、硬件架构)。在多个平台上测试代码以确保可移植性也很重要。

**推荐:**

使用标准库最大限度地提高可移植性。

指定绝对路径，而不是相对路径。

检查特定于系统的依赖性(例如，操作系统、硬件架构)。

在多个平台上测试代码。

很多次有人问我，在这五个问题中，我个人认为哪一个最重要；我发现是第五种:创建可移植的和平台无关的代码。我的许多用例涉及到创建可重复的实现，我需要这些实现供我的最终用户编译。尽管“即服务”解决方案推动了过度简化，使得许多文档需求可以被编码或构建到他们的产品中，但正是开源选项为工程师和开发人员(像我一样)提供了创建与用户共享的端到端解决方案以便立即实现的机会(在我的例子中，预先打包我为用户部署的内容，以便开箱即用地进行编译)。)当然，我的选择并没有降低其他四个的重要性。

如果您有任何编辑/修改建议或关于进一步扩展此主题的建议，请考虑与我分享您的想法。

# 另外，请考虑订阅我的每周简讯:

[](https://pventures.substack.com/) [## 周日报告#1

### 设计思维与 AI 的共生关系设计思维能向 AI 揭示什么，AI 又能如何拥抱…

pventures.substack.com](https://pventures.substack.com/) 

# **我写了以下与这篇文章相关的内容；他们可能与你有相似的兴趣:**

# 1.非结构化数据与结构化数据

[](https://medium.com/@AnilTilbe/unstructured-vs-structured-data-the-5-most-important-differences-8d86078a163b) [## 非结构化数据与结构化数据:5 个最重要的区别

### 结构化数据、非结构化数据的分类、各自的优势，以及如何将它们部署在一起…

medium.com](https://medium.com/@AnilTilbe/unstructured-vs-structured-data-the-5-most-important-differences-8d86078a163b) 

# 2.我用 Python 为 ML 和 NLP 编码，但是你应该在 2023 年之前学会 R

[](https://pub.towardsai.net/i-code-in-python-for-ml-and-nlp-but-you-should-learn-r-before-2023-747474293c23) [## 我用 Python 为 ML 和 NLP 编码，但是你应该在 2023 年之前学会 R

### 你应该学习 R 的 6 个理由，什么使 R 有效，R 的闪光点在哪里，以及 R 的最佳实践。

pub.towardsai.net](https://pub.towardsai.net/i-code-in-python-for-ml-and-nlp-but-you-should-learn-r-before-2023-747474293c23) 

*参考文献:*

*1。弗吉尼亚州斯托登。实践中的科学方法:计算科学中的再现性。检索 2022 年 7 月 19 日，来自*[](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=1550193)

**2。高层介绍。EBSCOhost。(未注明)。再现性危机、科学方法和发表研究的质量:解开心结。检索到 2022 年 7 月 19 日，来自*[*https://tinyurl.com/2cj3366t*](https://tinyurl.com/2cj3366t)*。**

# *分级编码*

*感谢您成为我们社区的一员！在你离开之前:*

*   *👏为故事鼓掌，跟着作者走👉*
*   *📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容*
*   *🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)*

*🚀👉 [**加入升级达人集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)*
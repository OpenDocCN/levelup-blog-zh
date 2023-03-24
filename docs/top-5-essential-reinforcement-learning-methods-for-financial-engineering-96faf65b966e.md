# 金融工程五大基本强化学习方法

> 原文：<https://levelup.gitconnected.com/top-5-essential-reinforcement-learning-methods-for-financial-engineering-96faf65b966e>

金融工程的用例及特定算法/模型

![](img/e2e2d3a8667142e164540e67e1b97438.png)

由来自 Pexels 的 [Pixabay](https://www.pexels.com/@pixabay/)

我以前写过关于无监督学习、深度学习和 NLP 模型的文章，以放大金融工程用例。通过这些帖子中的一个，你们中的一些人伸出手来，问我是否也可以隔离强化学习，并在金融工程的背景下分解它。

我很乐意，因为金融工程交叉领域的强化学习这个话题非常合适。至于你，tradecraft 专业人员，不要成为强化学习的陌生人。

# **但是为什么现在强化学习呢？**

金融工程是一个复杂的领域，需要使用复杂的数学模型和工具。强化学习方法可以通过提供自动优化这些模型中的关键参数的方法和途径来帮助简化金融工程的过程。

在许多情况下，金融工程师处理的是不断变化的时间敏感型数据集。可以部署强化学习算法来构建动态模型，以适应这些动态变化，从而实现潜在的更准确的预测。许多金融工程应用涉及在不确定性下优化投资组合或策略；强化学习以基于方法的方式为解决此类问题提供了一条途径，尤其是通过其处理随机环境的能力[16]。

金融工程中的另一个常见任务是风险管理，其动机包括量化和最小化各种类型的风险(例如，市场、信用或流动性风险)。强化学习技术可以提供一个解决方案，因为它们可以应用于设计管理高维空间的计算方案[1]。

底线在前面(BLUF):让我们开门见山。

![](img/e50570d1c0900856ab5c9f087620982f.png)

来自 Pexels 的 Janko Ferlic

# **1。****Q-学习**

它是一种强化学习算法，应用无模型教学程序[14]来优化给定决策规则的性能。可以部署它来寻找交易策略，协助投资组合分配设计(基于风险和回报概况)，以及跨做市策略的自动化[3](考虑跨高频方法进行交易)。我对 Q-learning 做了更深入的探究(见本文底部的链接)；请随时访问以了解更多信息。

![](img/607541face4ff2d598bb633585419c66.png)

由 Pexels 的 [Pixabay](https://www.pexels.com/@pixabay/)

# **2。** **【深 Q 网(DQN)】**

DQN 可以帮助识别数据中的模式和关系:例如，通过训练 DQN 扫描市场数据，它可能能够发现隐藏的相关性，或者对市场如何运作形成新的见解。由此产生的用例可能导致开发交易策略或优化风险管理技术。

dqn 可以从经验中学习[7]，当他们收到更多的数据点时提高他们的性能。相比之下，传统的财务分析方法通常需要更多的(相比较而言)人工输入。也就是说，dqn 可以用于提取大量数据，这意味着它们可以用于处理和分析大数据集(与通过更传统的方法人工输入等量数据所需的时间相比)。

除了结构化或非结构化数据之外，DQN 方法还允许 it 从不同类型的数据(例如，文本、图像、视频)中学习，这可能使它们非常适合在需要同时考虑多个输入源的金融工程问题中实施。进一步说，使用基于 DQN 的强化学习技术可能有助于金融工程过程的关键方面的自动化，减少对人工输入的依赖，并影响决策所需的时间。

![](img/7d7f99e4b1bfb3ac849fe03a8b4ad442.png)

来自 Pexels 的 Pixabay

# **3。** **双 DQN**

将它们整合起来，潜在地训练一个代理人[10]通过使用以前行动的反馈来分析如何考虑财务决策。例如，它们可以通过预测市场对某些事件的反应如何(朝什么方向)[11]来帮助识别交易。我将这一部分保持简短，以便在金融工程的交叉点上做一个单独的深入探讨(因为它的潜力和一系列用例)。

![](img/f4af66c508850cd33603d103ad31bb7c.png)

由来自 Pexels 的[皮克斯贝](https://www.pexels.com/@pixabay/)

# **4。** **优先体验回放**

优先体验重放(PER)是一种强化学习技术，有可能提高交易策略的效率和优化[25]。PER 的工作原理是根据经验重放记忆[26]对训练代理人的有用性对其进行优先排序，从而产生可能的结果来通知计算资源和训练时间的有效使用。

在金融工程中有几种使用 PER 的机会:

—效率:通过对经验回放记忆进行优先排序，代理可能会更快、更有效地从中学习，这可能会缩短训练时间并提高在不可见数据集上的性能。

—准确性:学习的效率可能会导致样本外测试集的泛化能力和准确性的优化。换句话说，当应用于真实世界的数据时，使用 PER 训练的模型可能比没有使用 PER 训练的模型表现出更好的性能。

—使用有限的计算资源:在训练深度学习模型时，由于有限的计算资源，使用所有可用的数据通常是不可行的。PER 可能允许通过优先化对培训代理最有影响(特别)的经验重放记忆来使用这些资源。

—模拟环境中的真实性:通过使用 PER，可以在更接近真实世界条件的环境中训练代理，这是可能的，因为 PER 允许探索状态空间[26]，这可能导致整体覆盖(等待您对特定金融工程用例的经验评估)和对代理在不同情况下如何表现的理解。

![](img/34bf1ba3029e2f55dbd6afc65433d4ce.png)

来自 Pexels 的 cottonbro

# **5。** **莎莎**

状态动作奖励状态动作(SARSA) [27]一种基于策略的强化学习算法:代理通过遵循当前策略进行学习，并在探索环境时更新其策略[28]。SARSA 可用于解决涉及连续或离散状态空间[29]和动作的最优控制问题。

为什么 SARSA 可能是一个追求:

—该算法可应用于连续和离散状态/行动空间问题设置。

—由于它是一种基于策略的方法，SARSA 可能比 Q-learning 等基于策略的方法收敛得更快[30]。

![](img/6597a87fe8be43b0c66ee0011d949296.png)

来自 Pexels 的鲁道夫·基罗斯

# **离别的思念**

如果你对这篇文章的编辑有任何建议，或者对进一步扩展这个主题领域有什么建议，请和我分享你的想法。另外，请考虑[订阅我的每周简讯](https://pventures.substack.com):

# 我撰写了以下帖子(与本文相关)；也许你会对它们感兴趣:

## 金融工程的学习方法:你需要知道的一切

[](https://medium.datadriveninvestor.com/q-learning-methods-for-financial-engineering-all-you-need-to-know-ae730ae8b0b) [## 金融工程的学习方法:你需要知道的一切

### 如何开发和部署 Q-learning 方法，包括强化学习中的应用程序、用例、最佳实践…

medium.datadriveninvestor.com](https://medium.datadriveninvestor.com/q-learning-methods-for-financial-engineering-all-you-need-to-know-ae730ae8b0b) 

## 金融工程的三大基本无监督学习方法

[](https://medium.datadriveninvestor.com/top-3-essential-unsupervised-learning-methods-for-financial-engineering-fb5356a7a401) [## 金融工程的三大基本无监督学习方法

### 具体和最重要的算法方法以及如何实现它们，简单解释

medium.datadriveninvestor.com](https://medium.datadriveninvestor.com/top-3-essential-unsupervised-learning-methods-for-financial-engineering-fb5356a7a401) 

## 金融工程十大基本深度学习模型

[](https://medium.datadriveninvestor.com/top-10-essential-deep-learning-models-for-financial-engineering-ca550fcff91) [## 金融工程十大基本深度学习模型

### 金融工程中这些方法的 10 个基本深度学习(DL)模型和用例。

medium.datadriveninvestor.com](https://medium.datadriveninvestor.com/top-10-essential-deep-learning-models-for-financial-engineering-ca550fcff91) 

## 金融工程的 5 大基本机器学习库

[](https://pub.towardsai.net/top-5-essential-machine-learning-libraries-for-financial-engineering-d1ad6d7a8195) [## 金融工程的 5 大基本机器学习库

### 五个机器学习库，其中一个是最重要的库，用于金融工程用例

pub.towardsai.net](https://pub.towardsai.net/top-5-essential-machine-learning-libraries-for-financial-engineering-d1ad6d7a8195) 

## 金融工程的 10 大基本 NLP 模型

[](https://medium.com/mlearning-ai/top-10-essential-nlp-models-for-financial-engineering-f78f2536a2a9) [## 金融工程的 10 大基本 NLP 模型

### 金融工程中这些方法的 10 个基本 NLP 模型和用例。

medium.com](https://medium.com/mlearning-ai/top-10-essential-nlp-models-for-financial-engineering-f78f2536a2a9) 

*参考文献:*

*1。* *强化学习的不端行为。(未注明)。IEEE Xplore。检索到 2022 年 8 月 4 日，来自*[*https://ieeexplore.ieee.org/abstract/document/6767062*](https://ieeexplore.ieee.org/abstract/document/6767062)

*2。* *佐藤 H. (2008)。高维动作空间的强化学习——基于主成分分析的动作空间压缩。IEICE 技术报告；IEICE 理工大学。众议员，108(276)，37–42。*[*https://doi . org/https://ken . ieice . org/ken/download/2008 11 06 xahi/eng/*](https://doi.org/https:/ken.ieice.org/ken/download/20081106XaHi/eng/)

*3。* *老林。(2018 年 4 月 27 日)。高频做市的强化学习。【https://discovery.ucl.ac.uk/id/eprint/10116730/】[](https://discovery.ucl.ac.uk/id/eprint/10116730/)*

**4。* *蒂尔贝，阿尼尔。(2022 年 7 月 25 日)。自然语言处理的三个重要数学概念。照明。*[*https://medium . com/illumination/top-3-math-concepts-essential-for-NLP-81 F3 AC 73 ab 08*](https://medium.com/illumination/top-3-math-concepts-essential-for-nlp-81f3ac73ab08)*

**5。*、*、【38】、&、【王、郑、谢；杨振宁(2020 年 7 月 31 日)。深度 q 学习的理论分析。PMLR。*[*https://proceedings.mlr.press/v120/yang20a.html*](https://proceedings.mlr.press/v120/yang20a.html)*

**6。* *蒂尔贝，阿尼尔。(2022 年 7 月 31 日)。5 个必须知道的数学概念，深度学习。升级编码。*[*https://level up . git connected . com/top-5-must-know-math-concepts-for-deep-learning-95fd 09 ef 038 b*](/top-5-must-know-math-concepts-for-deep-learning-95fd09ef038b)*

*7 .*。**【m .】罗德里克，j .&# 38；Tellex，S. (2017 年 11 月 20 日)。实施深度 q 网络。ArXiv.Org。*[](https://arxiv.org/abs/1711.07478)*

***8。* *蒂尔贝，阿尼尔。(2022 年 7 月 27 日)。深度学习的线性代数，简单讲解。走向 AI。*[*https://pub . toward sai . net/linear-algebra-for-deep-learning-simple-explained-e 279998 cfad 1*](https://pub.towardsai.net/linear-algebra-for-deep-learning-simply-explained-e279998cfad1)**

***9。* *深度强化学习对用双深度 q-网络进行交易。(未注明)。IEEE Xplore。检索到 2022 年 8 月 4 日，来自*[【https://ieeexplore.ieee.org/abstract/document/9031159】T21](https://ieeexplore.ieee.org/abstract/document/9031159)**

**10。 *Sewak。(2019 年 1 月 1 日)。深度 Q 网络(DQN)，双 DQN，决斗 DQN。新加坡斯普林格。*[*https://link . springer . com/chapter/10.1007/978-981-13-8285-7 _ 8*](https://link.springer.com/chapter/10.1007/978-981-13-8285-7_8)**

***11。*、*、&、【李、魏、朱、郭、*；坎布里亚，2021 年。基于双深度 Q 网络的股票交易规则发现。应用软计算，107，107320。[](https://doi.org/10.1016/j.asoc.2021.107320)**

****12。* *蒂尔贝，阿尼尔。(2022 年 7 月 24 日)。人工智能的线性代数:NLP 和 ML 用例。走向 AI。*[*https://pub . toward sai . net/linear-algebra-for-ai-NLP-and-ml-use-cases-simplely-explained-c 0 BAC 7159 a 7 f*](https://pub.towardsai.net/linear-algebra-for-ai-nlp-and-ml-use-cases-simply-explained-c0bac7159a7f)***

***13。* *蒂尔贝，阿尼尔。(2022 年 8 月 2 日)。20 大机器学习算法。升级编码。*[*https://level up . git connected . com/top-20-machine-learning-algorithms-explained-in-less-10-seconds-each-8fd 728 f 70 b 19*](/top-20-machine-learning-algorithms-explained-in-less-than-10-seconds-each-8fd728f70b19)**

***14。* *蒂尔贝，安尼尔。(2022 年 8 月 3 日)。金融工程顶级 q-learning 深度学习。数据驱动投资者。*[*https://medium . datadriveninvestor . com/q-learning-methods-for-financial-engineering-all-you-need-to-know-AE 730 AE 8 b 0b*](https://medium.datadriveninvestor.com/q-learning-methods-for-financial-engineering-all-you-need-to-know-ae730ae8b0b)**

***15。* *蒂尔贝，阿尼尔。(2022 年 8 月 3 日)。金融三大基本无监督学习。数据驱动投资者。*[*https://medium . datadriveninvestor . com/top-3-essential-unsupervised-learning-methods-for-financial-engineering-FB 5356 a7a 401*](https://medium.datadriveninvestor.com/top-3-essential-unsupervised-learning-methods-for-financial-engineering-fb5356a7a401)**

***16。* *蒂尔贝，阿尼尔。(2022 年 7 月 28 日)。金融工程中 10 个必不可少的深度学习。数据驱动投资者。*[*https://medium . datadriveninvestor . com/top-10-essential-deep-learning-models-for-financial-engineering-ca 550 fcff 91*](https://medium.datadriveninvestor.com/top-10-essential-deep-learning-models-for-financial-engineering-ca550fcff91)**

**17。 *蒂尔贝，阿尼尔。(2022 年 7 月 26 日)。金融工程 5 大 AI ML 库。走向 AI。*[*https://pub . toward sai . net/top-5-essential-machine-learning-libraries-for-financial-engineering-d1ad 6d 7a 8195*](https://pub.towardsai.net/top-5-essential-machine-learning-libraries-for-financial-engineering-d1ad6d7a8195)**

**18 岁。 *蒂尔贝，阿尼尔。(2022 年 7 月 27 日)。金融工程的 10 个基本 NLP 模型。mlearning . ai .*[*https://medium . com/mlearning-ai/top-10-essential-NLP-models-for-financial-engineering-f78f 2536 a 9*](https://medium.com/mlearning-ai/top-10-essential-nlp-models-for-financial-engineering-f78f2536a2a9)**

***19。* *【法特汉姆】，&# 38；Delage，E. (2021 年 5 月 19 日)。最优停止的深度强化学习及其在金融工程中的应用。ArXiv.Org。*[*https://arxiv.org/abs/2105.08877*](https://arxiv.org/abs/2105.08877)**

***20。* *菲舍尔，T. G .(未注明)。金融市场中的强化学习。一项调查。检索到 2022 年 8 月 4 日，来自*[*https://www.econstor.eu/handle/10419/183139*](https://www.econstor.eu/handle/10419/183139)**

***21。* *马林格，&# 38；拉姆托胡尔。(2011).投资决策中的状态转换递归强化学习。计算管理科学，9(1)，89–107。*[](https://doi.org/10.1007/s10287-011-0131-1)**

****22。* *采用自适应强化学习的自动化外汇交易系统。(未注明)。专家系统与应用，30(3)，543–552。*[*https://doi.org/10.1016/j.eswa.2005.10.012*](https://doi.org/10.1016/j.eswa.2005.10.012)***

***23。* *【江、张志军、徐婷】&# 38；梁，J. (2017 年 6 月 30 日)。金融投资组合管理问题的深度强化学习框架。ArXiv.Org。*[【https://arxiv.org/abs/1706.10059】T21](https://arxiv.org/abs/1706.10059)**

***24。* *Carin 等《部分可观测随机环境下的多任务强化学习》。*[*https://www.jmlr.org/papers/volume10/li09b/li09b.pdf*](https://www.jmlr.org/papers/volume10/li09b/li09b.pdf)**

**25。 *本地能源市场中的交易策略，一种深度强化学习方法。(未注明)。IEEE Xplore。检索到 2022 年 8 月 4 日，转自*[*https://ieeexplore.ieee.org/abstract/document/9621459*](https://ieeexplore.ieee.org/abstract/document/9621459)**

***26。*、【李】、【陆】、&# 38；苗，C. (2021 年 2 月 5 日)。重温优先体验重放:价值视角。ArXiv.Org。[*https://arxiv.org/abs/2102.03261*](https://arxiv.org/abs/2102.03261)**

***27。* *科萨纳，v .，桑托什，m .，提帕西，k .，&# 38；库马尔，S. (2022 年)。使用策略 SARSA 算法进行精确风速预测的新动态选择方法。电力系统研究，108174。*[*https://doi.org/10.1016/j.epsr.2022.108174*](https://doi.org/10.1016/j.epsr.2022.108174)**

***28。* *罗、唐、傅、&# 38；埃伯哈德。(2018 年 1 月 1 日)。动态环境下基于 Deep-Sarsa 的多无人机路径规划和避障。斯普林格国际出版公司。*[*https://link . springer . com/chapter/10.1007/978-3-319-93818-9 _ 10*](https://link.springer.com/chapter/10.1007/978-3-319-93818-9_10)**

***29。* *利用连续动作空间解决离散问题。(未注明)。IEEE Xplore。检索到 2022 年 8 月 4 日，来自*[*https://ieeexplore.ieee.org/abstract/document/5178745*](https://ieeexplore.ieee.org/abstract/document/5178745)**

**30 岁。 *后向 Q 学习:Sarsa 算法和 Q 学习的结合。(未注明)。人工智能的工程应用，26(9)，2184–2193。*[【https://doi.org/10.1016/j.engappai.2013.06.016】T21](https://doi.org/10.1016/j.engappai.2013.06.016)**
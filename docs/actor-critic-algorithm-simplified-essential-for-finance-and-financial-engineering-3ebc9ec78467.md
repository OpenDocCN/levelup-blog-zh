# 演员-评论家算法，简化:金融和金融工程的基本

> 原文：<https://levelup.gitconnected.com/actor-critic-algorithm-simplified-essential-for-finance-and-financial-engineering-3ebc9ec78467>

对算法及其应用和缺点的简单介绍

![](img/705244af197b0b18369c3aa4547391d5.png)

由来自 Pexels 的 [Pixabay](https://www.pexels.com/@pixabay/)

行动者-批评家算法是一种强化学习方法，它将价值函数估计与策略搜索结合起来[3][4]。行动者-批评家算法的目标是学习什么行动是最优的，以及如何最优地执行这些行动[6][7]。

强化学习中的行动者-评论家算法可以应用于金融，因为它们能够学习和改进政策，这是因为行动者-评论家方法可以逼近价值函数，将它们设置为估计回报的用例，并改进这些政策。此外，这些算法可能比其他方法收敛得更快(例如，与 Q-learning 相比)[8][9]。

![](img/5a72e9dbfc8d80bfb3a56cd533994517.png)

来自 Pexels 的 [Rodolfo Clix](https://www.pexels.com/@rodolfoclix/)

**算法背后的目标**

演员-评论家算法背后的关键思想是，代理人可以通过使用类似于这种两步方法的过程来改善其行为[10]:首先，它估计每个动作(“演员”)的预期回报。随后，它根据该评估执行最佳行动(“批评家”)。

在许多情况下，如在线学习或当存在高维连续状态空间时，这一两步过程可以通过为每项任务使用单独的神经网络来完成:一个网络学习预测回报(“参与者”)[16]。相比之下，另一个网络通过选择给定估计值的动作来学习(“批评家”)[10]。

![](img/b64439c81c854f64a8ab2ad0e4af92d3.png)

来自 Pexels 的照片

**潜力**

后者中概述的这种分离有几个优点。价值函数可能比政策收敛得更慢，因为它们致力于预期的未来回报[17]；平均而言，额外的时间步长[18]影响时间范围内 *n* 增加的每个因素。这意味着，如果我们希望我们的代理人有长远的眼光[19]，我们可能需要识别非常慢的学习者或多个抽象层次[19]，这两者都会产生影响(如计算费用)[3]。除了这种影响之外，由于维度复杂性增加，收敛速度可能会呈指数级增长。

结合价值函数估计和策略搜索可能有助于克服演员-评论家算法的一些缺点。例如，虽然纯粹的策略搜索方法可能会陷入局部最优[20]，但添加价值函数[21]可以提供指导，帮助代理逃离这些次优[22]区域。

![](img/c8958cab6588df0f2a0fa0acb9d1bacd.png)

来自 Pexels 的照片

**小心接近**

演员-评论家算法并非没有缺点。特别是，它们往往比其他强化学习方法更复杂——无论是在概念上还是在计算上——并且可能需要更多的努力才能正确实现。尽管有一些缺点，这些算法在一些环境中还是很有前途的——包括投资组合管理、风险规避和定价。

![](img/edd32770621f578849702f2bdd4a6630.png)

由来自 Pexels 的[皮克斯贝](https://www.pexels.com/@pixabay/)

**还有:优势演员评论家(A2C)和异步优势演员评论家(A3C)**

一般了解强化学习算法的类型:基于值的和基于策略的。Advantage Actor Critic)是一种基于策略的算法，已成功用于许多具有挑战性的任务，如下围棋和国际象棋[25]。异步优势行动者批评家(A3C)是 A2C 的一个扩展，它使用多个工作者来并行化训练过程[26]。

![](img/cf59b373a523bdacf2e73695ba7c60b3.png)

来自 Pexels 的玛格达·埃勒斯

**金融领域的用例**

演员-评论家算法可以应用于金融应用，如投资组合管理和定价(如衍生品)。在投资组合管理的情况下，参与者可以是预测股票价格的神经网络，而评论家将根据实际价格数据评估预测的价格以训练网络。类似地，在衍生产品定价中，参与者可以根据过去的价格数据预测未来的股票价格，而批评家可以将这些预测与市场价格进行比较，以评估准确性。一般来说，用例包括预测价格和优化投资组合。

金融业的实施仍在继续，因为该算法可以帮助识别价值——是被高估的投资吗？传统的分析方法可以用于这种算法整合:估值过高的投资是对研究资产当前价格与其估计公允价值之间差异的评估。总的来说，潜力在于从以前的数据中学习(描述性分析),并将这些知识应用于未来的定价决策。

![](img/430d59bcf8a29a272f0460b0f3a32dd5.png)

来自 Pexels 的 cottonbro

**离别的思念**

如果你对这篇文章的编辑有任何建议，或者对进一步扩展这个主题领域有什么建议，请和我分享你的想法。

另外，请考虑订阅我的每周简讯

**我创建了这个列表，把我所有的金融工程文章放在一起:**

[](https://medium.com/@AnilTilbe/list/1bc857a2c2d4) [## 金融工程:我的帖子

### 我关于金融工程的帖子。

medium.com](https://medium.com/@AnilTilbe/list/1bc857a2c2d4) 

在我看来，这里有一些你应该考虑阅读的

## 金融工程的学习方法:你需要知道的一切

[](https://medium.datadriveninvestor.com/q-learning-methods-for-financial-engineering-all-you-need-to-know-ae730ae8b0b) [## 金融工程的学习方法:你需要知道的一切

### 如何开发和部署 Q-learning 方法，包括强化学习中的应用程序、用例、最佳实践…

medium.datadriveninvestor.com](https://medium.datadriveninvestor.com/q-learning-methods-for-financial-engineering-all-you-need-to-know-ae730ae8b0b) 

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

*1。* *强化学习的不当行为。(未注明)。IEEE Xplore。检索到 2022 年 8 月 4 日，来自*[*https://ieeexplore.ieee.org/abstract/document/6767062*](https://ieeexplore.ieee.org/abstract/document/6767062)

*2。* *佐藤 H. (2008)。高维动作空间的强化学习——基于主成分分析的动作空间压缩。IEICE 技术报告；IEICE 理工大学。众议员，108(276)，37–42。*[*https://doi . org/https://ken . ieice . org/ken/download/2008 11 06 xahi/eng/*](https://doi.org/https:/ken.ieice.org/ken/download/20081106XaHi/eng/)

*3。**&# 38；萨顿，R. S .(未注明)。渐进式自然演员-评论家算法。神经信息处理系统进展，20。*

*4。* *Kobayashi 等人利用资格痕迹的演员/评论家算法分析:价值函数不完美的强化学习。*[*http://users . umi ACS . UMD . edu/~ Hal/courses/2016 f _ RL/Kimura 98 . pdf*](http://users.umiacs.umd.edu/~hal/courses/2016F_RL/Kimura98.pdf)

*5。* *老林。(2018 年 4 月 27 日)。高频做市的强化学习。*[](https://discovery.ucl.ac.uk/id/eprint/10116730/)

**6。**【38】&# 38；陆(未注明)。ECCV 2018 开放存取知识库。检索 2022 年 8 月 4 日，来自*[*https://open access . the CVF . com/content _ ECCV _ 2018/html/于波 _ 陈 _ 实时 _ 演员-评论家 _ 追踪 _ ECCV _ 2018 _ 论文. html*](https://openaccess.thecvf.com/content_ECCV_2018/html/Boyu_Chen_Real-time_Actor-Critic_Tracking_ECCV_2018_paper.html)*

**7。**【Flet-Berliac，y .】Ferret，j .【Piet quin，o .】Preux，p .&# 38；m .艾斯特(2021 年 2 月 8 日)。敌对导向的演员兼评论家。ArXiv.Org。*[【https://arxiv.org/abs/2102.04376】T21](https://arxiv.org/abs/2102.04376)*

*8。*&# 38；莱文，S. (2018 年 7 月 3 日)。带有随机参与者的非策略最大熵深度强化学习。PMLR。*[*https://proceedings.mlr.press/v80/haarnoja18b*](https://proceedings.mlr.press/v80/haarnoja18b)*

**9。* *增量多步 q 学习。(未注明)。科学指导。2022 年 8 月 4 日检索，来自*[*https://www . science direct . com/science/article/pii/b 9781558603356500350*](https://www.sciencedirect.com/science/article/pii/B9781558603356500350)*

**10。* *彼得斯，维贾古玛，&# 38；沙尔。(2005 年 1 月 1 日)。天生的演员兼评论家。施普林格柏林海德堡。*[*https://link.springer.com/chapter/10.1007/11564096_29*](https://link.springer.com/chapter/10.1007/11564096_29)*

**11。* *蒂尔贝，阿尼尔。(2022 年 8 月 3 日)。金融工程顶级 q-learning 深度学习。数据驱动投资者。*[*https://medium . datadriveninvestor . com/q-learning-methods-for-financial-engineering-all-you-need-to-know-you-need-to-know-AE 730 AE 8 b 0b*](https://medium.datadriveninvestor.com/q-learning-methods-for-financial-engineering-all-you-need-to-know-ae730ae8b0b)*

**12。* *蒂尔贝，安尼尔。(2022 年 8 月 3 日)。金融三大基本无监督学习。数据驱动投资者。*[*https://medium . datadriveninvestor . com/top-3-essential-unsupervised-learning-methods-for-financial-engineering-FB 5356 a7a 401*](https://medium.datadriveninvestor.com/top-3-essential-unsupervised-learning-methods-for-financial-engineering-fb5356a7a401)*

**13。* *蒂尔贝，阿尼尔。(2022 年 7 月 28 日)。金融工程中 10 个必不可少的深度学习。数据驱动投资者。*[*https://medium . datadriveninvestor . com/top-10-essential-deep-learning-models-for-financial-engineering-ca 550 fcff 91*](https://medium.datadriveninvestor.com/top-10-essential-deep-learning-models-for-financial-engineering-ca550fcff91)*

**14。* *蒂尔贝，阿尼尔。(2022 年 7 月 26 日)。金融工程 5 大 AI ML 库。走向 AI。*[*https://pub . toward sai . net/top-5-essential-machine-learning-libraries-for-financial-engineering-d1ad 6d 7a 8195*](https://pub.towardsai.net/top-5-essential-machine-learning-libraries-for-financial-engineering-d1ad6d7a8195)*

*15。 *蒂尔贝，阿尼尔。(2022 年 7 月 27 日)。金融工程的 10 个基本 NLP 模型。*[*https://medium . com/mlearning-ai/top-10-essential-NLP-models-for-financial-engineering-f78f 2536 a 2 a 9*](https://medium.com/mlearning-ai/top-10-essential-nlp-models-for-financial-engineering-f78f2536a2a9)*

*16 岁。 *哈尔诺贾、t、周、a、哈蒂凯宁、k、塔克、g、哈、s、谭、j、库马尔、v、朱、h、古普塔、a、阿比尔、p、&# 38；莱文，S. (2018 年 12 月 13 日)。软演员-评论家算法和应用。ArXiv.Org。*[](https://arxiv.org/abs/1812.05905)*

***17。* *【黑斯·n·西尔维】&# 38；Teh，Y. W. (2013 年 1 月 12 日)。基于能量的政策强化学习。PMLR。*[*https://proceedings.mlr.press/v24/heess12a*](https://proceedings.mlr.press/v24/heess12a)**

***18。* *格雷斯比，j .，&# 38；齐，于(2021 年 6 月 16 日)。走向持续控制的自动演员-评论家解决方案。ArXiv.Org。*[*https://arxiv.org/abs/2106.08918*](https://arxiv.org/abs/2106.08918)**

**19。 *萨恩科，A. L .，罗伯特·普拉特，凯特。(未注明)。等级演员-评论家。***

**20。 *托马斯·p .(2014 年 1 月 27 日)。自然演员-评论家算法中的偏见。PMLR。*[](https://proceedings.mlr.press/v32/thomas14.html)**

****21。* *【邵、刘亦菲、尤、杨、阎、米、袁、s、孙、&# 38；Bohg，J. (2022 年 1 月 11 日)。GRAC:自我引导和自我规范的演员兼评论家。PMLR。*[*https://proceedings.mlr.press/v164/shao22a.html*](https://proceedings.mlr.press/v164/shao22a.html)***

***22。* *【莫汉蒂，n .】&# 38；南卡罗来纳州孙达拉姆(2020 年 11 月 13 日)。禁闭逃生问题强化学习框架中的支架反射。ArXiv.Org。*[【https://arxiv.org/abs/2011.06764】T21](https://arxiv.org/abs/2011.06764)**

**23。 *Mnih，v .，Badia，A. P .，Mirza，m .，Graves，a .，Lillicrap，T. P .，Harley，t .，Silver，d .，&# 38；k . kavukcuoglu(2016 年 2 月 4 日)。深度强化学习的异步方法。ArXiv.Org。*[*https://arxiv.org/abs/1602.01783*](https://arxiv.org/abs/1602.01783)**

**24。 *优势演员评论家(A2C)。(未注明)。检索到 2022 年 8 月 4 日，转自*[*https://huggingface.co/blog/deep-rl-a2c*](https://huggingface.co/blog/deep-rl-a2c)**

***25。*、【周、王、王、胡、&# 38；邓，K. (2021)。改进的异步优势行动者批判强化学习模型在异常检测中的应用。熵，23(3)。[*https://doi.org/10.3390/e23030274*](https://doi.org/10.3390/e23030274)**

***26。* *沈，h，张，k，洪，m，&# 38；陈(2020 年 12 月 31 日)。走向理解异步优势行动者-批评家:收敛和线性加速。ArXiv.Org。*[*https://arxiv.org/abs/2012.15511*](https://arxiv.org/abs/2012.15511)**
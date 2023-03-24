# 游戏开发的三大基本 NLP 模型；一个是必须学习的

> 原文：<https://levelup.gitconnected.com/top-3-essential-nlp-models-for-gaming-development-one-is-a-must-learn-df1d441a297b>

如何使用这三种最重要的自然语言处理方法开发和部署前所未有的游戏，其中一种是必须学习的

![](img/d5e83e849be096f40ad148ed57a90818.png)

来自 Pexels 的布拉德利·胡克

**游戏开发的 3 种基本 NLP 方法，简单解释**

# 一条离开水的鱼:这是我第一次了解到零距离学习的思维弯曲能力时的感受。

本质上，零射击学习背后的想法是它变成了什么:在训练期间获得的关于标记实例的知识(与深度学习相比，它可能会令人困惑。)另一方面，深度学习是在评估阶段学习的关于未标记的训练实例的知识[1]。

NLP(自然语言处理)是计算机科学、人工智能和语言学的交叉领域，涉及计算机和人类(自然)语言之间的交互。

![](img/2e0e041e85f06929738e98098f7d8895.png)

来自 Unsplash 的安德烈·梅特列夫

## 我预测游戏的未来将集中在高度的同理心上，这样玩家将感受到人工智能模型的真实性、优化的真诚性和自然性(部署来与用户互动)。

随着视频游戏的日益流行，对更真实和逼真的游戏体验的需求也在增加。这导致开发者寻找新的方法来创造更可信的游戏世界，以及创造玩家能够感同身受的角色。

我们可以预见用户-人工智能交互在游戏中的重要渗透，例如:

—将玩家的语音分类，如命令、问题或所用语言的整体适当性

![](img/203657c58aa5ccd19892d9d5ea0ba98b.png)

来自 Unsplash 的 Clint Bustrillos

# —自动实现非玩家角色(NPC)和玩家之间的对话

—理解文本段落，为玩家提取关键信息

## **进一步分解**

— NLP 技术可用于帮助分析游戏中玩家的对话和手势，以便在游戏过程中提供更好的反馈。举例来说，如果玩家在玩某个游戏角色时一直使用脏话，该信息可以被传送回应用于近实时调整的代码。

# —潜力在于创造出逼真逼真的游戏角色。

通过理解玩家之间如何互动以及他们说了什么(例如，书面或口头)，NLP 可以帮助创建对玩家输入做出真实响应的 NPC。这为玩家带来了更加身临其境和可信的体验。

— NLP 可以根据玩家行为生成新的任务或目标。通过跟踪玩家在游戏中的行为，NPL 可以识别模式，并为每个玩家提供量身定制的任务或目标。后一种实现方法通过向玩家提供在游戏过程中不断变化的动态内容来帮助他们保持参与。

下面是三种自然语言处理方法。

![](img/cb37194d55180a0b9cf2b93ea7d2aa26.png)

来自 Unsplash 的卡尼·贝内迪克托娃

# **1。词性标注**

这种方法用适当的语法类别标记句子中的每个单词。例如，“我”被标记为代词，“写”被标记为动词，“the”被标记为冠词。该信息可用于识别玩家所说或所写的句子的句法结构(在单词一起形成短语的上下文中)。

## **三个算法示例**

隐马尔可夫模型(HMM) [5]、最大熵模型(MEM) [6]和支持向量机(SVM) [7]。以下是对每一种语言的简单解释(它们可能与 NLP 直接相关，也可能不直接相关——以下是对每一种语言的简明描述):

# HMM 可以根据当前已知的和以前的走法预测游戏中的下一步走法[8]。

一个统计模型，考虑国际象棋，这样它可以应用于棋盘上基于所有已知位置的后续移动。

MEM 用于预测玩家根据其过去的行为做出某一举动的可能性[9]。这可以用来为给定的一组数据或观察结果找到最可能的解释。回到 HMM 国际象棋用例，MEM 可以用来计算在国际象棋中某个移动序列[10]将导致将死的概率，给定过去实现将死的游戏。

SVM 用于确定两个玩家是否旗鼓相当，或者一个玩家是否比另一个玩家有优势[11]。分类和回归挑战是用例，例如识别场景中的不同对象或面部识别系统。

![](img/165f07e3333c15c05bdd14ad6ee7d419.png)

来自 Unsplash 的 Erica Li

# **2。命名实体识别**

该方法检查文本中特定类型的实体，例如专有名称(例如，人或地点)、组织名称和数字表达式(日期或货币值)。识别这些实体对于游戏开发任务非常有用，例如自动生成任务对话或从非结构化文本源中提取数据(例如根据游戏中的故事情节从网站上搜集的物品描述)。

一种方法的三个算法示例可以包括 RNN 和 CRF。

— RNN:递归神经网络(RNN)通过考虑单词的排序来模拟文本[12]。RNN 可以学习文本中单词之间的长期依赖关系[13]。由于它们可以处理序列数据，如文本，这种能力使它们非常适合于命名实体识别等任务，在这些任务中，我们需要根据上下文来识别句子中的实体。

— CRF:条件随机字段(CRF) [14]既可以考虑局部信息(单个单词及其标签)，也可以考虑全局信息(标签序列)。CRF 可以应用于未知数据(标注)，如预测词性标签或命名实体[15]。

![](img/950ca2dd473056aaae6d45a8631cf75a.png)

来自 Unsplash 的 Alperen yazg

## **其他算法途径**

卷积神经网络(CNN)类似于 rnn，但它们一次性对整个句子或段落进行操作，而不是顺序处理[16]。这意味着它们可以学习关于输入数据的全局特征，并且可能更适合于某些类型的命名实体识别任务。最后，另一种显示出命名实体识别前景的算法是长短期记忆网络(LSTMs)。LSTMs 是一种递归神经网络[3]，用于模拟序列数据中的长期相关性[17]。在包括命名实体识别在内的一些自然语言处理任务上，与其他方法相比，它们可以表现得很好。

![](img/9060560b755763516b4021a3192f6f00.png)

由来自 Unsplash 的[克里斯托弗·保罗·高](https://unsplash.com/@christopherphigh)

# **3。情绪分析**

**这是我必须学会的:**使用这种方法来确定某段文字是否表达了对主题或对话的积极/消极看法(相对于玩家的输入可能有多中立)。

情感分析是“一种识别给定文本极性的自然语言处理技术”[2][4]。

虽然不是所有游戏都严格要求，但许多现代游戏利用玩家反馈来改进未来版本；因此，能够准确地接收和处理用户评论可以让开发人员对他们的产品需要改进的地方有宝贵的见解。

## **激活**的能力

—马尔科夫决策过程(MDP):它们是一个用于建模决策问题的数学框架，可以被激活来解决游戏开发中的情感分析[18]用例。

-蒙特卡罗树搜索(MCTS):一种优化技术，可以快速找到困难问题的近似最优解。它已经成功地应用于许多领域，包括游戏人工智能和机器人控制[19]。

强化学习；RL 算法通过反复试验来学习，使用来自环境的反馈来随着时间的推移提高它们的性能。

![](img/2ba7c84ea9df203872d155a9c3b029eb.png)

来自 Unsplash 的 Christopher Paul High

# **离别的思念**

我谈到了情感分析、命名实体识别和词性标注对基于自然语言处理的游戏开发的影响。

如果你对这篇文章的编辑有任何建议，或者对进一步扩展这个主题领域有什么建议，请和我分享你的想法。

**另外，请考虑订阅我的每周简讯:**

[](https://pventures.substack.com) [## 产品。风险时事通讯

### 产品和人工智能交汇处的想法。让我先读一下产品。风险投资时事通讯…

pventures.substack.com](https://pventures.substack.com) 

**我写了以下与这篇文章相关的内容:他们可能和你有相似的兴趣:**

# 零射击学习深潜:如何选择一个和当今的挑战

[](https://pub.towardsai.net/zero-shot-learning-deep-dive-how-to-select-one-and-challenges-60ae243e040a) [## 零射击学习深潜:如何选择一个和当今的挑战

### 零射击学习演练

pub.towardsai.net](https://pub.towardsai.net/zero-shot-learning-deep-dive-how-to-select-one-and-challenges-60ae243e040a) 

# 零投与少投学习:2022 年更新的关键见解

[](https://pub.towardsai.net/zero-shot-vs-few-shot-learning-50-key-insights-with-2022-updates-17b71e8a88c5) [## 零射击与少射击学习:2022 年更新的关键见解

### 这些是当今关于零射击和短距离学习设置的定义和见解。

pub.towardsai.net](https://pub.towardsai.net/zero-shot-vs-few-shot-learning-50-key-insights-with-2022-updates-17b71e8a88c5) 

参考资料:

1.阿尼尔·蒂尔贝(2022，7 月 21 日)。零射击学习深潜。走向 AI。[https://pub . toward sai . net/zero-shot-learning-deep-dive-how-to-select-one-and-challenges-60ae 243 e 040 a](https://pub.towardsai.net/zero-shot-learning-deep-dive-how-to-select-one-and-challenges-60ae243e040a)

2.阿尼尔·蒂尔贝(2022，7 月 26 日)。16 种用于情感分析的自然语言处理模型。走向 AI。[https://pub . toward sai . net/16-open-source-NLP-models-for-sense-analysis-one-rises-on-top-b 5867 e 247116](https://pub.towardsai.net/16-open-source-nlp-models-for-sentiment-analysis-one-rises-on-top-b5867e247116)

3.计算教程:张量流中 LSTMs 的介绍。[https://cbmm . MIT . edu/learning-hub/tutorials/computational-tutorial/introduction-lst ms-tensor flow](https://cbmm.mit.edu/learning-hub/tutorials/computational-tutorial/introduction-lstms-tensorflow)

4.使用 Python 开始情感分析。[https://huggingface.co/blog/sentiment-analysis-python](https://huggingface.co/blog/sentiment-analysis-python)

5.莫瓦尔，s .，贾汉，n .，&乔普拉，d .(未注明)。基于隐马尔可夫模型的命名实体识别。检索于 2022 年 8 月 2 日，来自[https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3758852](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3758852)

6.最大熵谱分析和自回归分解。[https://agu pubs . online library . Wiley . com/doi/ABS/10.1029/rg 013 I 001 p 00183](https://agupubs.onlinelibrary.wiley.com/doi/abs/10.1029/RG013i001p00183)

7.高尚。(未注明)。什么是支持向量机？自然生物技术，24(12)，1565–1567。【https://doi.org/10.1038/nbt1206-1565 号

8.华盛顿蒙特(2009 年)。使用隐马尔可夫模型来排列多个序列。冷泉港协议，2009(7)。【https://doi.org/10.1101/pdb.top41 

9.考古遗址位置的预测模型:在以色列北部和中国东北比较逻辑回归和最大熵。(未注明)。考古科学杂志，92，28-36。[https://doi.org/10.1016/j.jas.2018.02.001](https://doi.org/10.1016/j.jas.2018.02.001)

10.时间序列建模和最大熵。(未注明)。地球和行星内部物理学，12(2–3)，188–200。[https://doi . org/10.1016/0031-9201(76)90047-9](https://doi.org/10.1016/0031-9201(76)90047-9)

11.基于深度迁移学习和支持向量机的视觉人体行为识别。(未注明)。IEEE Xplore。检索于 2022 年 8 月 2 日，来自[https://ieeexplore.ieee.org/abstract/document/9667661/](https://ieeexplore.ieee.org/abstract/document/9667661/)

12.扎伦巴，w .，苏茨基弗，I .，&维尼亚尔斯，O. (2014 年 9 月 8 日)。递归神经网络正则化。ArXiv.Org。[https://arxiv.org/abs/1409.2329](https://arxiv.org/abs/1409.2329)

13.学习 NARX 递归神经网络中的长期相关性。(未注明)。IEEE Xplore。检索于 2022 年 8 月 2 日，来自[https://ieeexplore.ieee.org/abstract/document/548162](https://ieeexplore.ieee.org/abstract/document/548162)

14.曼宁，J. R. F。A. K .未注明日期。高效的基于特征的条件随机字段解析。[https://aclanthology.org/P08-1109.pdf](https://aclanthology.org/P08-1109.pdf)

15.使用条件随机场的古吉拉特语词性标注。[https://aclanthology.org/I08-3019.pdf](https://aclanthology.org/I08-3019.pdf)

16.Kalchbrenner，e . Grefenstette，& Blunsom，P. (2014 年 4 月 8 日)。用于句子建模的卷积神经网络。ArXiv.Org。[https://arxiv.org/abs/1404.2188](https://arxiv.org/abs/1404.2188)

17.蒂尔贝，阿尼尔。(2022 年 7 月 24 日)。10 个最重要的递归神经网络。走向 AI。https://medium.com/p/8de9989db315

18.使用文本的神经模糊和隐马尔可夫模型的情感分析。(未注明)。IEEE Xplore。检索日期:2022 年 8 月 2 日，发自 https://ieeexplore.ieee.org/abstract/document/6567382

19.英国盖娜；英国；D. M。英国航空公司；名词。未注明日期。基于蒙特卡罗树搜索的类人自然语言生成。[https://aclanthology.org/W16-5502.pdf](https://aclanthology.org/W16-5502.pdf)
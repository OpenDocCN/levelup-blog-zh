# 区块链开发—入门

> 原文：<https://levelup.gitconnected.com/blockchain-development-which-roads-are-there-f78b2ec6f320>

![](img/c417bceff49fde680a91ea58be464a8c.png)

小大卫·马丁在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这份指南是给那些想在区块链[开始开发，但是不知道从哪里开始的开发者的。我会帮你指出你可以选择的最有趣的道路。但是……当你在这篇文章之后做出选择的时候，还有很多研究和自我教育要做。这篇文章不是为了说服你使用区块链而不是其他技术，它只是为了帮助你找到正确的方向，如果你已经决定要在区块链发展的话。](https://en.wikipedia.org/wiki/Blockchain)

# 一些你可以去的方向

*   [智能合约](https://en.wikipedia.org/wiki/Smart_contract)(例如[可靠性](https://github.com/ethereum/solidity)、[链码](https://github.com/IBM-Blockchain-Archive/learn-chaincode))
*   [分布式总账](https://en.wikipedia.org/wiki/Distributed_ledger)(例如[超总账结构](https://github.com/hyperledger/fabric)、 [Corda](https://github.com/corda/corda) )
*   支付协议(如[比特币](https://github.com/bitcoin/bitcoin)、[涟漪](https://github.com/ripple)、[恒星](https://github.com/stellar/stellar-core))
*   区块链编程(例如 C++、Golang、Java)

有更多的方向可供选择，但上面列出的是最常见的。

# 智能合同

智能合同是完全按照合同制定者设定的方式执行的程序。例如，它们可以作为用户之间的协议，为其他合同提供基础，存储信息，ICO 或类似的选举投票。

[](https://medium.com/coinmonks/solidity-smart-contract-anatomy-fb8bfb72e7ec) [## 可靠智能合同剖析

### 今天，我将向你解释一个样本 Solidity 智能合同的剖析。本文将就此作一简要说明

medium.com](https://medium.com/coinmonks/solidity-smart-contract-anatomy-fb8bfb72e7ec) 

最广为人知的**以太坊语言**建造契约是 [**坚固性**](https://medium.com/coinmonks/solidity-smart-contract-anatomy-fb8bfb72e7ec) 。它与 JavaScript 或 c 最为相关。它应用广泛，易于学习，在工作机会中要求很多。稳固性主要基于采矿和加密货币，加上一切都是公开的和无许可的。为了制作**去中心化 app(Dapps)**，Solidity 可以作为以太坊后端。还有另一种更广为人知的以太坊语言是 **Serpent，**这种语言看起来更像 Python。

**链码**用 **Golang 和 Node.js** 编写，以后会支持更多语言。它用于 Hyperledger Fabric(将在下一节中解释)，它比 Solidity 这样的封闭智能契约语言更灵活。Chaincode 在一个安全的 Docker 容器中运行，该容器与签署对等进程相隔离。Chaincode 通过应用程序提交的交易来初始化和管理分类帐状态。”

关于 Chaincode 的更多信息，请看这篇文章。

# 分布式分类帐

它基本上是一个由网络中的每个参与者(节点)共享、复制和更新的数据库。没有中央管理员。它可以是私有的或公共的，有许可或没有许可，并且可以有各种一致同意的机制。每个参与者处理每笔交易，并做出多数以达成共识。当达到这一点时，分类账得到更新，每个人都有一份副本。之后，事务永远不能被修改。**【DL】**分布式总账是动态的，具有远远超越传统总账的能力和属性。

我在 LinkedIn 上看到的几乎每份工作邀请中都问到 IBM 的 Hyperledger Fabric (HLF) 区块链框架的**实现。这个DL 是私有的，有权限。此外，在 HLF 中没有交易费，因为没有相关的加密货币。对于那些希望使用区块链技术，但又不想向公众和竞争对手透露有价值数据的公司来说，私有区块链是完美的选择。**

HLF 的一大优点是模块化，因此可以从更多的共识机制中进行选择。最杰出的两个是**拜占庭容错(BFT)** 和**容错(FT)** 。例如，如果您希望每秒处理超过 1000 个事务，那么 FT 将是您的选择。但是，如果您希望在恶意节点存在的情况下一切运行顺利，那么 BFT 会更合适。

**R3 的 Corda，**也是 DL 事实上不是区块链，但具有区块链的许多特征。在这篇来自 [ConsenSys](https://medium.com/u/6c7078bf7b01?source=post_page-----f78b2ec6f320--------------------------------) 的[文章中，他们解释道:](https://media.consensys.net/blockchain-vs-distributed-ledger-technologies-1e0289a87b16)

> “R3 Corda 架构框架依赖于一个节点结构，该节点结构依赖于称为公证人的子模块，这些子模块有助于维护网络的有效性，类似于其他平台中抽象共识功能的验证器结构。节点伴随着附加在数据结构中的关系数据库，允许使用 SQL 进行查询。事务通信被限制在称为流的子协议中。

在 Corda 和 HLF 之间进行开发的选择取决于用例。Corda 主要关注银行客户，HLF 的客户领域更广(包括资本市场、法律、医疗保健和物流)。

# 支付协议

第一个可用的区块链**支付协议**(也可以称为**加密货币**)是比特币。其次是莱特币、以太币和后来更有用的加密货币，如 Ripple 和 Stellar。自去年以来，与较新的项目相比，比特币的交易变得更加昂贵和缓慢。

**Ripple** ( [开发者中心](https://ripple.com/build/))正在使用**正确性证明(PoC)** 共识(解释可以在[这里找到](https://ripple.com/files/ripple_consensus_whitepaper.pdf))。 **Stellar** ( [开发者指南](https://www.stellar.org/developers/))正在使用他们自己的 **Stellar 共识协议(SCP)** 在这里详细解释[。这两者之间最大的区别是产生利润(涟漪)和非盈利(恒星)。](https://www.stellar.org/papers/stellar-consensus-protocol.pdf)

支付协议用于处理交易，增加了所有参与者的透明度。它是分散的，所以没有中央权威。因为没有额外的信息随交易一起发送，所以防止了身份盗窃。举例来说，大多数情况下，费用比贝宝(Paypal)等服务低。

# 区块链本身

您也可以通过自选共识(例如 [PoW、PoS、pBFT、SCP](https://hackernoon.com/an-overview-of-cryptocurrency-consensus-algorithms-9d744289378f) )开发自己的区块链。最广为人知的区块链是用 **C++** 写的比特币，有工作证明(PoW)共识。通过 PoW，最快的矿工解决了一个障碍，并得到其他节点的验证，然后达成共识，矿工因此获得奖励。其他用于开发区块链的通用语言有 **GoLang** 和 **Java** ，但是你可以使用任何你想要的语言。因此，如果你熟悉这些语言中的一种，你就已经有了开始开发区块链的坚实基础。

除了理解编程语言之外，您还需要了解:

*   [**密码学**](https://en.wikipedia.org/wiki/Cryptography)
*   **安全**
*   **算法**

# 你能在哪里学习

## [Udemy](https://www.udemy.com/)&[edX](https://www.edx.org/)

> “Udemy 是一个全球性的在线学习和教学市场，在这里，学生们通过从由专家讲师讲授的超过 65，000 门课程的庞大库中学习，掌握新技能并实现他们的目标。”

Udemy 是一个学习的好地方，与其他主题相比，这里的课程数量不多，但增长速度很快。你可以花很少的钱购买一门课程，有时打折后只需 10 美元。完成一门课程后，你会得到结业证书。

> “edX 由哈佛大学和麻省理工学院于 2012 年创立，是一个在线学习目的地和 MOOC 提供商，为世界各地的学习者提供来自世界上最好的大学和机构的高质量课程。”

如果你真的想深入内容，那么 edX 就是你要去的地方。价格更高，但内容的质量会补偿这一点。证书比 Udemy 的更重要，因为它们是由大学制作的，而大多数 Udemy 课程是由在该特定领域有一些知识的普通人制作和提供的。

## [Youtube](https://www.youtube.com/)

在 Youtube 上，你可以找到足够多的关于区块链、智能合同等的视频。并不是所有的东西都是好材料，但是有一些人能够真正解释清楚，并且对这个主题有很好的了解。其中一个就是 [Siraj Raval](https://medium.com/u/54526471f9bf?source=post_page-----f78b2ec6f320--------------------------------) 。我看了很多他关于区块链的内容，非常激励人心，是一个很好的推荐。

## 项目文件

我见过的几乎每个项目都有大量的文档。我个人的偏好是学习一门课程，熟悉一切。过了一会儿，当我感觉更舒服的时候，我查阅文档来获得关于特定项目的更深入的知识。它总是非常有用的材料。

# 对开发者的需求

根据 TechCrunch 的一篇文章，目前每个区块链开发者有 14 个工作机会。正在寻找区块链人才的公司希望大部分时间的短期任务所需的人才。

> “对区块链人才的需求正在飙升。去年，自由职业人才市场 Upwork 看到区块链成为自由职业者计费方面 5000 多项技能中增长最快的技能，同比增长超过 35000%。这些请求涉及 ICO 咨询服务、工程项目和整个区块链咨询。”— [TechCrunch](https://techcrunch.com/2018/02/14/blockchain-engineers-are-in-demand/)

# 你能在哪里提供你的区块链知识？

如果你已经在区块链开发的某个方面有了一些经验，比如智能合同，你已经可以开始提供你作为开发人员的技能了。自由职业者的两个平台例子是 [**Toptal**](https://www.toptal.com/) 和 [**Upwork**](https://www.upwork.com/) 。

# 我的个人兴趣

从去年开始，我对区块链在发展中的应用产生了兴趣。我把大部分时间花在学习**编写智能合同**和体验**Hyperledger Fabric**的实现上。几乎在我看到的每份工作邀请中，都有人问我，我不是说这是最好的选择，但对我来说，这似乎是最好的选择。

“你可以选择购买加密货币，然后看着它涨跌，但还有很多其他方式可以涉足这个领域。如果你在这个领域投入时间并积累一些智力资本，你的努力将会得到回报。

这个行业的高点和低点有可能让你感到活着，潜在的趋势在指明方向，而且有钱可赚。冒险进入区块链空间。“— [戴夫·卡普斯特](https://medium.com/u/170fa6cab7b2?source=post_page-----f78b2ec6f320--------------------------------)在[你应该转行进入区块链领域吗？](https://medium.com/@davekaj/why-you-should-change-your-career-and-enter-blockchain-now-a69f129ee6e3)

![](img/4876c1387b10213e26ba6418d19c9447.png)

# 感谢您的阅读！如果你有意见，请告诉我。我的一些项目和贡献:

[](https://github.com/jeroenouw/AngularMaterialGo) [## jeroenouw/AngularMaterialGo

### AngularMaterialGo -带 Angular 6、材料设计和 Go 的全栈入门应用程序

github.com](https://github.com/jeroenouw/AngularMaterialGo) [](https://github.com/jeroenouw/HyperledgerFabricAngular) [## jeroenouw/hyperledgerfabriccarular

### HyperledgerFabricAngular 带有 Hyperledger 和 Angular 5 的分布式分类帐平台(正在开发中)

github.com](https://github.com/jeroenouw/HyperledgerFabricAngular) [](https://github.com/FileNation/FileNation) [## 文件化/文件化

### FileNation -使用 IPFS 向世界各地发送文件的最简单方式。✏️ 🗃

github.com](https://github.com/FileNation/FileNation)
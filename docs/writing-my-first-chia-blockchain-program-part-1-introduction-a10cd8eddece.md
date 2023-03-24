# 编写我的第一个 Chia 区块链程序:第 1 部分—简介

> 原文：<https://levelup.gitconnected.com/writing-my-first-chia-blockchain-program-part-1-introduction-a10cd8eddece>

我最近开始学习 Chialisp。请和我一起将我的第一个程序部署到 Chia 区块链，并随时提问。

![](img/6174a8607440e4d4331cd54634328497.png)

照片由 Unsplash 上的 Launchpresso 拍摄

# 介绍

在这个系列的第一部分，我们将探索 Chia 区块链的基本概念，以熟悉我们将在整个过程中使用的不同组件和术语。第 1 部分将不涉及编码。

如果你已经知道这一点，请随意跳到[第 2 部分](https://thepaulo.medium.com/writing-my-first-chia-blockchain-program-part-2-setup-fda79486248)！

# **什么是 Chia？**

[Chia](https://github.com/Chia-Network/chia-blockchain) 是一种新的加密货币，旨在以比同行更低的每次交易能耗实现去中心化共识。这是通过[空间验证算法](https://docs.google.com/document/d/1tmRIb7lgi4QfKkNaxuKOBHRmwbVlGL4f7EsBDr_5xZE/edit)而不是[工作验证](https://en.wikipedia.org/wiki/Proof_of_work)来实现的。

矿工(称为农民)以基于他们的总专用(标绘)存储空间与网络存储空间的比率生成新的 chia(通过称为收获的过程)。要了解更多关于 Chia 养殖的信息，请参阅下面的教程:

[](/building-a-200tb-chia-farming-rig-c9478ed7b92f) [## 建造一个 200TB 的 Chia 农业钻机

### 对 Chia 加密货币农业(采矿)系统从组件选择到软件配置的完整回顾。

levelup.gitconnected.com](/building-a-200tb-chia-farming-rig-c9478ed7b92f) 

# **什么是 Chialisp？**

[Chialisp](https://chialisp.com/) 是用于开发 Chia 智能硬币的语言。它类似于[以太坊](https://ethereum.org/en/)智能合约的[可靠性](https://docs.soliditylang.org/en/v0.8.9/)。类似于[以太坊虚拟机(EVM)](https://ethereum.org/en/developers/docs/evm/) ，Chialisp 被向下编译到 [CLVM](https://github.com/Chia-Network/clvm) ，运行在区块链上。

## **什么是智能币模型？**

如今，区块链的两种主要模式是比特币(被称为 [UTXO](https://en.wikipedia.org/wiki/Unspent_transaction_output) )和以太坊(账户[模式)。](https://academy.horizen.io/technology/expert/utxo-vs-account-model/)

[UTXO 或未用交易输出](https://en.wikipedia.org/wiki/Unspent_transaction_output)模型围绕着硬币本身的主要数据结构。这意味着价值(或信息)的存储项目通常被称为“硬币”。

在帐户模型中，主要存储的信息是包括其余额的帐户。账户可以由个人通过私钥控制，也可以直接通过智能合约控制。

在下面的链接中可以找到对这两个模型的更深入的研究。

[](https://academy.horizen.io/technology/expert/utxo-vs-account-model/) [## UTXO 与客户模型

### 为了让数字货币变得有用，它必须是可转移的。区块链上的资金转移是由…

academy.horizen.io](https://academy.horizen.io/technology/expert/utxo-vs-account-model/) 

Chia 是使用 UTXO 模型构建的，但是做了一些改进，使它更通用化，更适合运行分布式应用程序。根据[官方在这里找到的 Chia 帖子](https://www.chia.net/2019/11/27/chialisp.en.html)，这些改进包括:

> 硬币是第一类对象，交易是摧毁其中一些和创造其他的短暂理由，不像在比特币中，交易是第一类对象，硬币(UTXOs)被表示为交易 id 和输出编号。
> 
> 硬币(UTXOs)的格式大大简化了。它只是一个主要的输入，难题散列和数量。
> 
> 事务是同时发生的，而不是顺序发生的。
> 
> 签名是使用 [BLS](https://en.wikipedia.org/wiki/Boneh%E2%80%93Lynn%E2%80%93Shacham) 完成的，这是一种非交互式的可聚合格式，聚合总是会完成的。
> 
> 使用的语言没有副作用。这允许以比比特币当前的 taproot 提议更普遍和更强大的方式进行委托和部分委托。
> 
> 所有 puzzle ( [scriptpubkey](https://bitcoin.org/en/glossary/pubkey-script) 用比特币的说法)解决方案(scriptsigs)的需求都是用它们的返回值来表示的。
> 
> 这种语言是图灵完备的。这比人们想象的要简单得多，因为执行是短暂的。正是这种复杂的持续状态导致了固体的复杂性。
> 
> 该语言具有必要的原语来计算硬币 id，硬币能够断言自己的 id，这允许显式的自引用，并避免了对 [quines](https://en.wikipedia.org/wiki/Quine_(computing)) 的需要。

# **什么是 Chia 智能币？**

每枚 Chia 智能硬币由三部分组成:

1.  A **父 id** :创建这个硬币的硬币的 id。
2.  一个**谜题散列**:运行在这枚硬币上的代码(又名**谜题**)的散列。
3.  一个**数量**:这个硬币代表魔咒的数量，其中一个魔咒是一个 Chia 的万亿分之一(就像比特币中的 Satoshis)。

**硬币 id** (如父 id 中使用的值)是上述三个值的散列。

```
coinID = sha256(parent_id + puzzlehash + amount)
```

# **一枚金币的生命周期是多久？**

花掉一枚金币只会导致三种可能结果中的一种:

1.  什么都没发生。例如坏演员试图花费不属于他们自己的硬币的情况。任何人都可以尝试在区块链上花费任何硬币，因此这取决于内部谜题(硬币代码)来确保预期的行为。
2.  新的硬币被创造出来。例如，当一个用户拥有一枚价值 10 mojos 的硬币，并向另一个用户转让 6 mojos 时，就会发生这种情况。这将导致为受让人创建一个 6 mojos 硬币，并为所有者创建一个新的 4 mojos 硬币。注意:归还给所有者的 4 mojos 变化是一种需要在硬币中定义的行为。
3.  硬币被销毁或烧毁。注意:区块链中的总金额不能由单个玩家更改，因此，烧毁硬币的价值将奖励给保护包含交易区块的农民。

使用[消费包](https://chialisp.com/docs/coins_spends_and_wallets/#coin-aggregation-and-spend-bundles)消费硬币。花费包指定花费哪些硬币，并可用于将[解决方案](https://chialisp.com/docs/coins_spends_and_wallets/#puzzles-and-solutions)(又名参数)传递给硬币的谜题(又名内部代码)。

难题返回一组[条件](https://chialisp.com/docs/coins_spends_and_wallets#conditions) ，可以是以下两种类型之一:

1.  为了花掉硬币而必须满足的断言。
2.  当硬币被花掉时应该发生什么的指示(创造新的硬币，等等)。

本质上，当你成功地[花掉](https://chialisp.com/docs/coins_spends_and_wallets/#spends)一枚你创造的硬币时，唯一的保证就是硬币的谜题代码将会运行并且硬币将会被花掉，但是如何定义硬币内的谜题代码取决于我们。它可以创造一个新硬币，多个新硬币，或者根本没有新硬币！

## 一枚硬币是怎么花的？

一旦用户使用客户端程序向网络提交花费捆绑包，完整节点将执行难题并将该捆绑包转发给试图做同样事情的其他完整节点。重复此过程，直到农民成功耕种包含支出捆绑包的地块，其中包含您在区块链的交易。

如果在消费完成后还有剩余的钱，除了他们的耕种奖励(有点像额外的小费)之外，剩余的钱会给耕种这块地的农民。

现在，由于您的支出捆绑包在实际提交到某个区块之前，在几个可能不安全(甚至是敌对)的节点之间来回跳跃，所有这些农民都可以看到您的内部谜题代码，并且他们可能会更改您的谜题解决方案(参数)，以在谜题提交之前尝试提取更多费用！

这就是为什么我们在我们的拼图程序中编写良好的条件来防止任何恶意篡改并确保我们的硬币按预期执行是非常重要的。

# **结论**

在本教程中，我们简要介绍了 Chia 和 Chialisp。在[第 2 部分](https://thepaulo.medium.com/writing-my-first-chia-blockchain-program-part-2-setup-fda79486248)中，我们将看看如何设置我们的开发环境来开始编写 Chialisp！

*合著* [*凯蒂博士*](https://www.linkedin.com/in/kygandomi/)

*如果你觉得这篇文章很有帮助，请点击*👏按钮或*捐一些 XCH 的钱到我的地址:*

xch 159 qvpvafcx 4 jxllk 9 xe 9 NPH 42 xw 50j 56 mpt 03 DSA 05 svll 7 kmd LQ 04 UCM 8

# 参考

有关 Chia 和 Chialisp 的更多信息，请访问以下网站:

1.  [https://www.chia.net](https://www.chia.net)
2.  [https://chialisp.com](https://chialisp.com)
3.  [https://github.com/Chia-Network/chia-blockchain](https://github.com/Chia-Network/chia-blockchain)

*如果你有任何问题或者只是想聊聊创业、创业、承包或工程，就发邮件给我吧保罗@*[*【avantsoft.com.br】*](https://avantsoft.com.br/)*。*
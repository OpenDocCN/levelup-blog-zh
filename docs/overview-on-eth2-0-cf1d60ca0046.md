# ETH2.0 概述

> 原文：<https://levelup.gitconnected.com/overview-on-eth2-0-cf1d60ca0046>

## 让我们看看 ETH2.0 的开发，深入这个兔子洞，甚至更多，来理解它是多么迷人🤩

如果你在以太坊呆了几年或几个月，你就进入了这个兔子洞。对我们所有人来说，观看以太坊的发展进程是一件非常令人着迷的事情，这也是我最喜欢的事情之一。伊斯坦布尔硬叉对我来说就像是新年前夜🎉🎆

随着您对 ETH2.0 的了解越来越多，您将会对许多新术语越来越熟悉。让我们来看看这些术语，让我用一种简单、易于理解的方式来解释 ETH2.0 是如何工作的，即使对于非技术人员来说也是如此，🧑‍💻

![](img/3e4ba655b701873e3c85e7c50994be4e.png)

图片来自 Unsplash 的@phoebezzf

以太坊计划迁移到 ETH2.0 的一个主要原因很简单——生态系统正在有机增长，因此网络需要升级才能保持运行。

这就是为什么现在的以太坊网络上正在发生硬分叉。以太坊要从工作证明(POW)走向利益证明(POS)的原因是，这样生态系统即使在更基础的计算机(节点)上也会更容易运行。

ETH2.0 信标链现在由 Prysm labs 在 [Sapphire](https://prylabs.net/participate) testnet 上运行，到目前为止，ETH2.0 有 3 个客户端，但加在一起将是 7 个(主网络和测试网络)，它们都在一起交谈，不，你不需要时间机器来运行它们。测试网现在运行在 gETH (Goerli ETH)上。你现在可以加入测试网并下注了。如何在[灯塔客户端](https://lighthouse-book.sigmaprime.io/intro.html) [上成为 ETH2.0 信标链上的验证器分步文档](https://lighthouse-book.sigmaprime.io/become-a-validator.html)。

# ETH2.0 中的阶段

> ETH2.0 将分阶段推出，每个短语将位于不同的碎片上。阶段= shard 的软件 ETH2.0 生态系统的硬件。

**阶段 0** 称为信标链，它是关于核心部分、网络、签名方案和随机性。它有自己的由 Prysm 实验室创建的蓝宝石测试网。如果你有一些 Goerli ETH，你可以加入 [testnet](https://prylabs.net/) ，参与其中。

**阶段 1** 面向独立操作 64 个碎片链的机制。每个分片就像整个网络的一个状态，所以每个分片可以运行网络的一个状态。因此，举例来说，你可以在一个分片上有 Maker，在另一个分片上有其他 DeFi 应用程序，在另一个分片上有 ETH1，在另一个分片上有 Crypto kit 等等…

**第二阶段**是关于执行引擎、空间交易和账户模型。执行引擎最小化了系统的复杂性。

不确定是否会推出更多的阶段。但是这仍然是开发的早期阶段，尽管研究人员希望看到更多的阶段在 ETH2.0 碎片链上滚动。

# 让我们来看看以太坊的现状

以太坊在[伊斯坦布尔硬分叉](https://magazine.cointelegraph.com/ethereum-hard-fork-istanbul-2019/)之后状态很好，你不必担心，这只是一次常规的网络升级，以保持链的可持续——在尽可能最好的条件下。

**ETH1.x 链**现在叫做 ETH1.x，“x”表示升级。在 ETH2.0 全面推出之前的几个月内，将会有一些计划中的硬分叉式升级。ETH1.x 链仍然是活跃的，可用的，你不必担心不能使用当前的以太坊。

ETH1.x 团队有自己的 [**工作组**](https://ethresear.ch/t/introductions-for-the-eth1-x-research-group/6430/2) 正在积极地将以太坊转变为无状态模型。这个小组是在布拉格的第四届发展大会期间成立的，当时以太坊的规模正在扩大，这是正常的，因为网络正在有机增长。在 [ethresearch](https://ethresear.ch/c/eth1x-research) 处观察 ETH1.x 链条的进程。

说到状态，随着以太坊上发生更复杂的交易，它正在增长。所谓复杂，我指的是更多的用户——地址在网络上，更多的令牌、cryptokitties 和所有随机的智能合约，存储这些数据需要使用以太坊状态。由 [Piper](https://github.com/pipermerriam) 领导的工作组提出了以下解决方案，以避免国家膨胀并保持 ETH1.0 链的活力和健康:

*   从根本上提高运行以太坊客户端的 UX
*   提高以太坊网络上的客户端多样性
*   为从 1.x 平稳过渡到 2.0 执行环境打下基础

这些只是 ETH1.x 开发将如何前进的几个要点。如果你有兴趣了解更多关于 ETH1.x 工作组的信息，请查看[最近由](https://medium.com/@pipermerriam/stateless-clients-a-new-direction-for-ethereum-1-x-e70d30dc27aa)[以太坊 2.0 阶段 0 —诚实验证器](https://medium.com/u/8bb8b999b3b8#beacon-chain-responsibilities) — GitHub 回购

[针对人类的第 0 阶段](https://notes.ethereum.org/@djrtwo/Bkn3zpwxB?type=view)——通过技术概述说明第 0 阶段将如何工作

[运行 lighthouse 验证器](https://lighthouse-book.sigmaprime.io/become-a-validator.html)——如果你想成为 Lighthouse 上的 ETH2.0 验证器，你所需要知道的一切

*这篇文章是和❤️一起为以太坊社区写的，为不太懂技术的人传播知识。没有人付钱给我写这篇文章。请随时支持我，向✨Thank 安耐特捐款*
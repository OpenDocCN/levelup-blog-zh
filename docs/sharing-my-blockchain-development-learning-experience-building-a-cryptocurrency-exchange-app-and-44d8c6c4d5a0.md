# 分享我的区块链开发学习经验:构建加密货币交换应用程序和 ERC-20 令牌(第一部分)

> 原文：<https://levelup.gitconnected.com/sharing-my-blockchain-development-learning-experience-building-a-cryptocurrency-exchange-app-and-44d8c6c4d5a0>

> 新的一年，新的编程语言，新的技术，新的技能，新的我！

我目前正在学习区块链和 web3 开发，所以我想我应该记录我创建自己的令牌和加密交换应用程序的整个过程的经验。这也是我第一次尝试写作，所以非常欢迎大家的想法、建议和批评。我还包含了我用来学习的其他资源的链接。

在这个项目之前，我最初已经学习了坚实的基础，我已经相当胜任(如果我自己这么说的话😜所以，如果出于某种奇怪的原因，您决定将这篇文章作为指南，请考虑一下。

我开始了构建这个项目的过程，就像任何其他经验丰富的开发人员一样，通过试验代码，因为当你可以浪费一整天来调试可避免的错误时，为什么要花几分钟来浏览官方文档呢，我说的对吗？因此，当这不可避免地失败时，我谦卑地决定回归基础，学习区块链如何工作，以太坊，密码学，钱包，Dapps(去中心化应用)，智能合约和 web3 的基础，最后是以太坊令牌和加密货币交易所。基本上，我的目标是创建我自己的 ERC-20 令牌，这基本上是一个基于以太币的令牌，使用预定义的协议(称为 ERC-20)，然后创建一个基本的加密货币交易所，人们可以将我的令牌交换为以太币。

一旦我学习完区块链的基础知识，我就开始设置我的本地开发环境。我一直在使用 [Remix](https://remix.ethereum.org/) ，这是一个在线 IDE(对于学习 solidity 的基础非常有用)，但它在功能方面有点有限，我真的怀疑它是否被用来开发生产应用程序。我运行的是 Windows 11 操作系统，所以对于 Linux 或 Mac 用户来说，这个过程可能会有点不同。我安装了 [node js](https://nodejs.org/en/download/) (主要用于安装其他包和依赖项) [Truffle](https://trufflesuite.com/docs/truffle/getting-started/installation) 用于编译、测试和部署你的智能合约以及 [Ganache](https://trufflesuite.com/ganache/) ，它基本上是一个运行在你电脑上的个人区块链。对于我的文本编辑器，我选择坚持使用传统的 Visual Studio 代码[。我已经在我的系统上安装了](https://code.visualstudio.com/) [python](https://www.python.org/downloads/) 和 [React](https://reactjs.org/) ，所以没有必要重复。

使用 create-react-app 创建新的 React 应用程序后😅，我安装了几个依赖，比如 [web3.js](https://web3js.readthedocs.io/en/v1.5.2/getting-started.html) (咄！)、 [react-redux](https://react-redux.js.org/) 用于前端状态管理、 [bootstrap](https://react-bootstrap.github.io/) 、 [openzeppelin](https://openzeppelin.com/contracts/) 用于构建灵活且可重用的 solidity 组件，等等(这是一个很长的列表)。由于这是一个正在进行的项目，如果需要的话，更多的依赖关系将会增加。

为了完成这个项目的初始阶段，我决定在我的项目中配置 truffle 和 Ganache，以确保在我的测试区块链上部署契约以及运行迁移的过程是无缝的。当前代码可以在这里找到[。你也可以在这里](https://github.com/andyriles/Crypto-Exchange)查看我的其他项目[。](https://andrew-efurhievwe.netlify.app/)

对于区块链发展的基本面，我推荐 [Dapp 大学](https://www.youtube.com/c/DappUniversity)，但是你可以一直 DYOR。

在不久的将来，希望在下一篇文章中，我将通过为我的合同创建基本测试来采用测试驱动开发(主要是为了避免编写虚构的令人厌恶的代码)，并创建我自己的 ERC-20 令牌。在那之前…..

![](img/2dc2ff09b0f3444fd28b687a732a2f34.png)
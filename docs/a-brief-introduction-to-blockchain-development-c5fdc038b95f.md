# 区块链发展简介

> 原文：<https://levelup.gitconnected.com/a-brief-introduction-to-blockchain-development-c5fdc038b95f>

## 创建 Dapp 所需的一切

![](img/1d866229e8e66963a6be21e27487726d.png)

谢谢 [Pexels](https://www.pexels.com/photo/code-coding-computer-cyberspace-270373/) ！

这篇文章会给你一个简要的概述，当你开始着手**区块链发展**时，你*应该*知道的一切。

让我们先弄清楚一些事情。我们不是在这里建造一个区块链。这就是区块链*核心*开发者所做的。我们在这里学习区块链*应用*开发的基础知识。这些是由区块链支持的分散式应用程序。

有许多不同的区块链为我们的 Dapp 提供动力。但是最大的是[以太坊](https://ethereum.org/en/)。

为了与以太坊生态系统互动，你需要一个以太坊钱包和一些以太坊。最受欢迎的以太坊钱包之一是 [Metamask](https://metamask.io/) 。

以太坊钱包有三个部分

1.  以太坊账户——可以发送和接收以太坊的实体
2.  公钥——用户可以向其发送以太坊的地址
3.  私钥—访问您的以太坊

您将使用您的以太坊钱包与以太坊网络中的 Dapps 进行交互。假设你想把你的以太币换成另一种加密货币。你可以通过分散交易平台 Uniswap 进行交易。

[Uniswap](https://uniswap.org/) 由以太坊区块链公司提供动力。它依靠一种叫做智能合约的东西运行。但是那些是什么？

**智能合约** —区块链上的程序

智能合同有 3 个主要部分。

*   用户—使用智能合同的人。
*   代码—使用智能合约时会发生什么。
*   存储—智能合约将更改的对象。

需要说明的是，Dapps 写在许多智能合约中。不止一个。例如， [Uniswap](https://github.com/Uniswap) 是一个由许多智能合约支持的 Dapp。

Dapps 与 web 开发中的后端或服务器端相当。客户端将连接到以太坊虚拟机上的智能合约，而不是连接到中央服务器。这就是我们的智能合约所在。

那么，我们如何为我们的 Dapp 创建智能合约呢？

# **介绍坚固性**

Solidity 是一种设计用于 EVM 的高级编程语言。你可以用 Solidity 来写你的智能合约。但是还有其他选择。

我们将探索智能合约的一些基本语法和一般结构。然后，我们将为我们自己的令牌写一个简单的契约。

坚固性使用原始类型。如果你熟悉另一种编程语言，你会认识一些。

*   **布尔型** —真或假
*   **Uint** —整数(数字决定大小的 X 次方)
*   **地址** —以太坊地址
*   **字符串** —文本

这很简单。以下是您将在 Solidity 中使用的基本数据结构。

*   **数组** —从零开始的项目列表(可以是固定数量或动态的)
*   **映射** —用键值对存储数据
*   **结构** —用于定义存储变量的新方式
*   **枚举** —创建自己的数据结构

设置可见性是智能合约开发的重要部分。你不希望你的私人地址公开！

这些关键字可以应用于变量和函数。

*   **Public** —任何人都可以调用这个函数
*   **私有** —只有契约可以调用该函数
*   **视图** —该函数返回数据，不修改数据
*   **纯** —函数不会修改甚至读取合同数据
*   **应付** —功能可以接收以太坊

你将会经常使用公共和私有。但是记住其他人很重要。

这样一来，我们就可以开始写代码了！

# 制作媒体令牌

首先，我们使用 *Pragma* 关键字*，*声明 solidity 的版本，然后指定 Solidity 为编译器以及什么版本。让我们看一个例子。

```
**pragma solidity** >=0.4.22 <0.6.0;
```

类似这样的东西会出现在你写的每一份聪明的合同的顶部。

这是开发过程中的重要一步。编写了一个惊人的智能契约，却发现编译器无法与您使用的 Solidity 版本兼容，这不是很糟糕吗？

我们将为 Medium 创建一个令牌。姑且称之为 *MediumToken* 。

接下来，我们需要实际声明合同。

```
**pragma solidity** >=0.4.22 <0.6.0;contract MediumToken {}
```

这里没什么特别的。只是为我们的令牌添加基本结构。

智能合约需要有一个构造函数。当我们的代码被部署到区块链时，这个函数将运行一次。

我们来补充一下。

```
**pragma solidity** >=0.4.22 <0.6.0;contract MediumToken {constructor() public {
address owner = msg.sender;
    }}
```

这里，我们声明了一个*地址*变量，它等于 msg.sender。这是调用这个函数的人的地址。也就是你。

我们需要在构造函数中做的另一件事是创建中间令牌。基本上，设置供给。

```
**pragma solidity** >=0.4.22 <0.6.0;contract MediumToken {constructor() public {
address owner = msg.sender;
balance[owner] = 420;   }}
```

恭喜你。你刚刚铸造了 420 个中型代币。这个数字可以是你想要的任何数字。可能是 5 万亿或 1 万亿。以及两者之间的任何地方。

我们还把所有的代币都给了所有者(你)。

在我们的构造函数运行之后，我们需要创建所有余额的映射。

```
**pragma solidity** >=0.4.22 <0.6.0;contract MediumToken {constructor() public {
address owner = msg.sender;
balance[owner] = 420;   }mapping (address => uint256) public balances;}
```

这是一个*公共*映射，意味着任何人都可以看到它并与之交互。

接下来，我们需要编写一个允许用户发送他们的媒体令牌的函数。这看起来会像这样。

```
**pragma solidity** >=0.4.22 <0.6.0;contract MediumToken {constructor() public {
address owner = msg.sender;
balance[owner] = 420;   }mapping (address => uint256) public balances;function send(uint amount, address recipient) public {require(balances[msg.sender] >= amount);
require(balances[msg.sender] -amount <= balances[msg.sender]);  require(balances[recipient] + amount >= balances[recipient]);balances[msg.sender] -= amount; // Always subtract first  balances[recipient] += amount; // Add amount to recipient
    }}
```

让我们来分析一下这是怎么回事。

我们的 *send* 函数将以 uint 的形式接受金额。它还将接受收件人的地址。而且这个功能是公开的，任何人都可以看到。

我们做的第一件事是创建一些 require 语句。这样我们就可以检查发送者的钱包中是否真的有足够的代币。

另外两个需求陈述是安全措施。智能合约开发越多越好。

第一个是确保发送方的余额在发送令牌后小于或等于。

```
require(balances[msg.sender] -amount <= balances[msg.sender]);
```

第二个是确保收件人的地址大于或等于交易前的地址。

就是这样！我们创造了 420 个 MediumTokens，把它们给了主人，创造了把它送给人的能力！

如果你想再看一遍，这里有完整的代码。

```
**pragma solidity** >=0.4.22 <0.6.0;contract MediumToken {constructor() public {
address owner = msg.sender;
balance[owner] = 420;   }mapping (address => uint256) public balances;function send(uint amount, address recipient) public {require(balances[msg.sender] >= amount);
require(balances[msg.sender] -amount <= balances[msg.sender]);  require(balances[recipient] + amount >= balances[recipient]);balances[msg.sender] -= amount; // Always subtract first  balances[recipient] += amount; // Add amount to recipient
    }}
```

一旦你写好了你的最终合同，你就可以把它部署到区块链。*但是首先把它部署到测试网上总是一个好主意。*

如果你有兴趣的话，你可以看看这个教程。
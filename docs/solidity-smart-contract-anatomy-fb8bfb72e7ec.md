# 可靠智能合同剖析

> 原文：<https://levelup.gitconnected.com/solidity-smart-contract-anatomy-fb8bfb72e7ec>

![](img/a856497ff83ef10a1eabdbc829e05328.png)

[Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [Rich Tervet](https://unsplash.com/@richtervet?utm_source=medium&utm_medium=referral) 的“灰色笔记本电脑开机”

今天我就来给大家讲解一个样本的解剖[坚固度](https://solidity.readthedocs.io/en/v0.4.24/) [智能合约](https://en.wikipedia.org/wiki/Smart_contract)。这篇文章将是一个简短的指南，所以你可以得到一个概念，在一个坚实的合同中使用了什么，没有深入的东西。我的代码并不完美！所以如果你发现了什么或者有问题，请联系我。

什么是 ***坚固度*** :

> “Solidity 是一种面向契约的高级语言，用于实现智能契约。它受到了 C++、Python 和 JavaScript 的影响，旨在针对以太坊虚拟机(EVM)。”

该示例合同名为“***freelancer . sol***”，其想法是自由职业者可以**设置:**

*   拥有数据
*   拥有资产

自由职业者可以**获得:**

*   一看自己的账户数据。

一个自由职业者**有:**

*   一个名字
*   姓氏
*   大量的“硬币”
*   一笔“现金”
*   并提供“服务”

## [版本](https://solidity.readthedocs.io/en/v0.4.21/layout-of-source-files.html#version-pragma)

一个实性契约总是以 ***pragma 实性【版本】开头；***

目前最新版本是我们在本合同中使用的 ***0.4.24*** 。避免在版本号前设置插入符号(^ ),因为这会自动将您的合同更新到最新版本，这可能会破坏合同。在这个例子中，我们使用了两个 ***实验版*** 版本。这些是可选的。通过设置 ***pragma 实验“v 0 . 5 . 0”；我们正在使用一个尚未发布的版本，所以我们可以使用最新的功能。最后一行***pragma experimental abiencoder v2；*** 是需要的，因为我们将在函数中返回一个 struct(将在函数部分显示)。***

## 合同

设置好你的版本后，你进入下一个部分，那就是对 ***使用名称*** ***自由职业者*** 和添加 ***全局变量进行约定。*** 合同可以用给定的名称调用。一个变量由一个 ***【类型】【函数调用】【变量名称】组成。***

在 contact 中，我们声明了四个变量:

*   公共所有者地址；
*   uint256 私币；
*   uint256 私人现金；
*   bytes32 私服；

你可以看到我们使用 ***公有和私有，*** 除了这两个你还可以使用 ***内部或者外部。***

*   **外部**——不能从内部进入，只能从外部进入(与公用相比，节省大约 50%的[气体](https://solidity.readthedocs.io/en/v0.4.24/introduction-to-smart-contracts.html?highlight=gas#gas))。
*   **公共** —每个人都可以访问。
*   **内部** —只有该合同及其派生的合同可以访问。
*   **私有** —只能从该合同访问。

我使用了 ***字节 32*** 类型，而不是 ***字符串*** 类型，以节省更多的气体。

## [事件](https://solidity.readthedocs.io/en/v0.4.21/structure-of-a-contract.html?highlight=modifier#events)

为了在每个事务中获得一些额外的信息，我们可以用一个事件记录额外的数据。在这种情况下，我们有两个事件叫做:`logFreelancerChanged`和`logAssetsChanged`。一个事件可以有 ***三个索引*** 项。有了索引，您可以使用索引参数作为过滤器来搜索这些事件。要使用事件，您可以通过`emit [event_name(parameters)];`来完成。

## [结构](https://solidity.readthedocs.io/en/v0.4.21/structure-of-a-contract.html?highlight=modifier#struct-types)

通过 structs，你可以定义一个新的类型，每个自由职业者都有自己的`FreelancerData`。在这个例子中，我们想要一些自由职业者的数据，包括:

*   bytes32 名；
*   bytes32 姓氏；
*   uint256 硬币；
*   uint256 现金；
*   bytes32 服务；

## [映射](https://solidity.readthedocs.io/en/v0.4.21/types.html#mappings)

一张地图由`mapping([key_type] => [value_type]) [mapping_name];`组成

> “映射可以被看作是虚拟初始化的[散列表](https://en.wikipedia.org/wiki/Hash_table)，使得每个可能的键都存在，并被映射到字节表示全为零的值:类型的[默认值](https://solidity.readthedocs.io/en/v0.4.24/control-structures.html#default-value)。不过，相似之处到此为止:关键数据实际上并不存储在映射中，只是它的`keccak256`散列用于查找值。”

我们制作了一个名为`FreelancersData`(注意复数)的映射。我们可以使用这个地址来获得我们想要使用的自由职业者的`FreelancerData`。

## [修改器](https://solidity.readthedocs.io/en/v0.4.21/structure-of-a-contract.html?highlight=modifier#function-modifiers)

修饰符通常是可以多次使用的代码，可以添加到函数中。我们做了一个修改器叫`onlyFreelancer()`。这仅允许数据所有者访问他/她自己的数据。如果其他人试图访问这些数据，他们会收到一条消息“发送者未经授权”。`_;` 是说你的函数代码从哪里开始。

## 构造器

构造函数通过创建协定来执行，它总是需要设置为 public。我们把`msg.sender`设为`owner`，给自由职业者零硬币和现金，给一个空的服务角色。

## [功能](https://solidity.readthedocs.io/en/v0.4.21/contracts.html#functions)

函数在被调用时被执行。在我们的合同中，我们声明了三个函数:`setFreelancer()`、`setAssets()`和`myAccount()`。在代码中，你可以看到注释，它描述了每个函数做什么，包含什么和返回什么。

一个函数看起来像这样:

*   **功能**
*   **【函数名(【参数】)】**—函数名
*   **【外部/公共/内部/私有】** —给出访问量。
*   **【修改器:可选】** —给出一个你之前创建的修改器
*   **【查看/纯/应付:可选】** —严格一点或者给个[退路](https://solidity.readthedocs.io/en/v0.4.24/contracts.html#fallback-function)。
*   **returns(【类型】【名称:可选】)** —函数内部返回什么。
*   **{[功能代码] }** 。—调用时要执行的代码。

在`myAccount()`函数内部我们还利用了 [***内存***](https://solidity.readthedocs.io/en/v0.4.21/introduction-to-smart-contracts.html#storage-memory-and-the-stack)***:***

> **“内存**，其中一个契约为每个消息调用获得一个新清除的实例。存储器是线性的，可以在字节级寻址，但是读取限于 256 位宽，而写入可以是 8 位宽或 256 位宽。当访问(读取或写入)先前未接触的存储字(即一个字内的任何偏移)。在扩建时，必须支付燃气费用。内存越大，成本就越高(它以平方的方式扩展)。”

示例:

## 你可以很容易地用 [Remix](https://remix.ethereum.org/) 在线测试这个智能合约。Freelancer.sol 完整代码:

![](img/4876c1387b10213e26ba6418d19c9447.png)

# 感谢您的阅读！查看我的 [Github](https://github.com/jeroenouw/) 或者我的关于区块链开发入门的帖子:

[](https://medium.com/coinmonks/blockchain-development-which-roads-are-there-f78b2ec6f320) [## 区块链开发—入门

### 本指南是为那些想开始区块链开发，但不知道从哪里开始的开发者准备的。我…

medium.comm](https://medium.com/coinmonks/blockchain-development-which-roads-are-there-f78b2ec6f320)
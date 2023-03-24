# 以太坊智能合约升级挑战介绍

> 原文：<https://levelup.gitconnected.com/introduction-to-ethereum-smart-contract-upgradability-with-solidity-789cc497c56f>

![](img/8ad24aed195d5c30ed384ded6bab795d.png)

照片由[福蒂斯·福托普洛斯](https://unsplash.com/@ffstop?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

在开发软件时，我们经常需要发布新版本来添加新功能或错误修复。在智能合约开发方面没有什么不同。虽然，将智能合约更新到新版本通常不像更新同样复杂的其他类型的软件那样简单。

大多数区块链，尤其是像以太坊这样的公共场所，都实现了固有的不变性概念，理论上，不允许任何人改变区块链的“过去”。不变性适用于区块链中的所有事务，包括用于部署智能合约和相关代码的事务。换句话说，一旦智能合约的代码被部署到区块链，它将永远“按原样”存在，没有人能够改变它。如果发现 bug 或需要添加新功能，我们不能替换已部署合同的代码。

如果一个智能合约是不可变的，那么如何将它升级到新的版本呢？答案在于在区块链部署新的智能合同。

但是这种方法提出了一些需要解决的挑战。最基本和最常见的有:

*   所有使用智能合约的用户都需要引用新合约版本的地址
*   应该禁用第一个合同的版本，强制每个用户使用新版本
*   通常，您需要确保旧版本的数据(状态)被迁移到新版本，或者以某种方式对新版本可用。在最简单的场景中，这意味着您需要将状态从旧版本复制/迁移到新契约的版本

以下各节将更详细地描述这些挑战。为了更好地说明它，我们将使用下面两个版本的`MySmartContract`作为参考:

版本 1

```
contract MySmartContract {
  uint32 public counter; constructor() public {
    counter = 0;
  } function incrementCounter() public {
    counter += 2; // This "bug" is intentional.
  }
}
```

版本 2

```
contract MySmartContract {
  uint32 public counter; constructor(uint32 _counter) public {
    counter = _counter;
  } function incrementCounter() public {
    counter++;
  }
}
```

## 用户引用新合同的地址

部署到区块链时，智能合约的每个实例都被分配到一个唯一的地址。此地址用于引用智能协定的实例，以便调用其方法并从协定的存储(状态)中读取数据或向其写入数据。

当您将协定的更新版本部署到区块链时，协定的新实例将部署到新地址。这个新地址不同于第一个合同的地址。这意味着与智能合约交互的所有用户、其他智能合约和/或 dApps(分散式应用程序)将需要更新，以便它们使用更新版本的地址。剧透:有一些选项可以避免这个问题，你会在这一部分的结尾看到。

因此，让我们考虑以下场景:

你用上面的代码`Version 1`创建了`MySmartContract`。它被部署到区块链的地址`A1`(这不是一个真实的以太坊地址——仅用于说明目的)。所有想要与`Version 1`交互的用户都需要使用地址`A1`来引用它。

现在，过了一段时间，我们注意到方法`incrementCounter`中的错误:它将计数器递增 2，而不是递增 1。实施了一个修复，导致了`MySmartContract`的`Version 2`。这个新合同的版本被部署到地址为`D5`的区块链。此时，如果用户想要与`Version 2`交互，需要使用地址`D5`，而不是`A1`。这就是为什么所有与`MySmartContract`交互的用户都需要更新，以便他们引用新地址`D5`的原因。

考虑到更新智能合约的版本应该对使用它的用户尽可能透明，您可能同意强制用户更新不是最好的方法。

有不同的策略可以用来解决这个问题。一些设计模式如[注册表](https://consensys.github.io/smart-contract-best-practices/software_engineering/#upgrading-broken-contracts)，不同类型的[代理](https://blog.openzeppelin.com/proxy-patterns/)可以用来使升级更容易，并向用户提供透明性。另一个很好的选择是使用[以太坊名称服务](https://ens.domains/)并注册一个用户朋友名称，该名称可以解析为你的合同地址。使用这个选项，契约的用户不需要知道契约的地址，只需要知道它的用户友好的名称。因此，升级到新地址对您的合同用户来说是透明的。所采用的具体策略取决于智能合约的使用场景。

## 禁用旧版本的合同

我们在上一节中了解到，所有用户都需要更新才能使用`Version 2`的地址(`D5`)，或者我们的合同应该实现某种机制，使这个过程对用户透明。尽管如此，如果你是合同的所有者，你可能希望强制所有用户只使用最新版本的`D5`。如果用户无意中或没有使用`A1`，你要保证`Version 1`是过时的和不可用的。

在这种情况下，您可以实现一种技术来停止`MySmartContract`的`Version 1`。这项技术是通过名为[断路器](https://consensys.github.io/smart-contract-best-practices/software_engineering/#circuit-breakers-pause-contract-functionality)的设计模式实现的。通常也称为*可中止合约*或*紧急停止*。

一般来说，断路器会停止智能合同功能。此外，它可以启用仅在合同终止时才可用的特定功能。这种模式通常实现某种类型的[访问限制](https://solidity.readthedocs.io/en/latest/common-patterns.html#restricting-access)，因此只有被允许的参与者(如管理员或所有者)拥有触发断路器和终止合同所需的权限。

可以使用这种模式的场景有:

*   发现错误时停止合同的功能
*   达到某个状态后停止一些契约的功能(经常与[状态机](https://solidity.readthedocs.io/en/latest/common-patterns.html#state-machine)模式一起使用)
*   在升级过程中停止契约的功能，这样外部参与者就不能在升级过程中更改契约的状态；
*   部署新版本后，停止不推荐使用的合同版本

现在让我们看看如何实现一个断路器来停止`MySmartContract`的`incrementCounter`功能，这样`counter`在迁移过程中就不会改变。当第一次部署时，这个修改需要在`Version 1`中就位。

```
// Version 1 implementing a Circuit Breaker with access restriction to owner
contract MySmartContract {
  uint32 public counter;
  bool private stopped = false;
  address private owner; /**
  @dev Checks if the contract is not stopped; reverts if it is.
  */
  modifier isNotStopped {
    require(!stopped, 'Contract is stopped.');
    _;
  } /**
  @dev Enforces the caller to be the contract's owner.
  */
  modifier isOwner {
    require(msg.sender == owner, 'Sender is not owner.');
    _;
  } constructor() public {
    counter = 0;
    // Sets the contract's owner as the address that deployed the contract.
    owner = msg.sender;
  } /**
  @notice Increments the contract's counter if contract is active.
  @dev It will revert if the contract is stopped. See modifier "isNotStopped"
   */
  function incrementCounter() isNotStopped public {
    counter += 2; // This is an intentional bug.
  } /**
  @dev Stops / Unstops the contract.
   */
  function toggleContractStopped() isOwner public {
      stopped = !stopped;
  }
}
```

在上面的代码中，你可以看到`MySmartContract`的`Version 1`现在实现了一个修饰符`isNotStopped`。如果合同停止，此修改量将恢复事务处理。函数`incrementCounter`被更改为使用修饰符`isNotStopped`，所以它将只在合同没有停止时执行。

有了这个实现，就在迁移开始之前，契约的所有者可以调用函数`toggleContractStopped`并停止契约。注意，这个函数使用修饰符`isOwner`来限制对合同所有者的访问。

要了解更多关于断路器的信息，请务必查看 Consensys 关于[断路器](https://consensys.github.io/smart-contract-best-practices/software_engineering/#circuit-breakers-pause-contract-functionality)和 OpenZeppelin 关于[暂停](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/lifecycle/Pausable.sol)合同的参考实现的帖子。

## 合同的数据(状态)迁移

大多数智能合约需要在其内部存储中保存某种状态。每个契约所需的状态变量的数量根据用例的不同而有很大的不同。在我们的例子中，最初的`MySmartContract`的`Version 1`只有一个状态变量`counter`。

现在考虑到`MySmartContract`的`Version 1`已经用了一段时间了。当你在`incrementCounter`函数中发现 bug 的时候，`counter`的值已经在`100`了。这种情况会引起一些问题:

*   你会怎么处理`MySmartContract Version 2`的状态？
*   您能否在`Version 2`中将计数器重置为 0(零),或者您是否应该从`Version 1`迁移状态以确保`counter`在`Version 2`中用`100`初始化？

这些问题的答案将取决于用例。在本文的例子中，这是一个非常简单的场景，`counter`没有重要的用法，如果`counter`被重置为`0`，你不会有任何问题。但是，在大多数情况下，这并不是理想的方法。

假设您无法将值重置为`0`，需要在`Version 2`中将`counter`设置为`100`。在像`MySmartContract`这样的简单合同中，这并不困难。您可以更改`Version 2`的构造函数来接收`counter`的初始值作为参数。在部署时，您可以将值`100`传递给构造函数，这将解决您的问题。

实现这种方法后，`MySmartContract Version 2`的构造函数将如下所示:

```
constructor(uint32 _counter) public {
    counter = _counter;
  }
```

如果您的用例像上面介绍的那样简单(或类似)，从数据迁移的角度来看，这可能是正确的方法。实现其他方法的复杂性不值得。但是，请记住，大多数生产就绪型智能合约并不像`MySmartContract`那样简单，通常具有更复杂的状态。

现在考虑一个使用多个[结构](https://solidity.readthedocs.io/en/latest/types.html#structs)、[映射](https://solidity.readthedocs.io/en/latest/types.html#mapping-types)和[数组](https://solidity.readthedocs.io/en/latest/types.html#arrays)的契约。如果您需要在具有如此复杂存储的合同版本之间拷贝数据，您可能会面临以下一个或多个挑战:

*   要在区块链上处理的一组事务，这可能需要相当长的时间，具体取决于数据集
*   处理从"`Version 1`"读取数据并将其写入"`Version 2`"的附加代码(除非手动完成)
*   花大钱买汽油。请记住，在区块链进行交易时，您需要支付汽油费。根据[以太网黄纸-附录 g .费用计划表](https://ethereum.github.io/yellowpaper/paper.pdf)，`SSTORE`操作，用于向以太网写入数据的 upcode 花费 20000 [气体单位](https://solidity.readthedocs.io/en/latest/introduction-to-smart-contracts.html#gas) *“当存储值从零设置为非零时”*和 5000 [气体单位](https://solidity.readthedocs.io/en/latest/introduction-to-smart-contracts.html#gas) *“当存储值为零不变时”*。
*   通过使用某种机制(比如断路器)冻结`Version 1`的状态，以确保在迁移过程中没有更多的数据被附加到`Version 1`中。
*   实施访问限制机制，避免外部方(与迁移无关)在迁移过程中调用`Version 2`的功能。这将需要确保`Version 1`的数据能够被复制/迁移到`Version 2`而不会在`Version 2`中被泄露和/或破坏；

在状态更复杂的合同中，执行升级所需的工作非常重要，在区块链复制数据可能会产生相当大的天然气成本。使用[库](https://solidity.readthedocs.io/en/latest/contracts.html#libraries)和[代理](https://blog.openzeppelin.com/proxy-patterns/)可以帮助您开发更容易升级的智能合同。采用这种方法，数据将保存在一个存储状态但没有逻辑的合同中(*状态合同*)。第二个契约或库实现了逻辑，但是没有状态(*逻辑契约*)。所以当发现逻辑中有 bug 时，只需要升级*逻辑契约*，不用担心迁移*状态契约*中存储的状态(见下面*注*)。

*注意:*这种方式一般使用 [Delegatecall](https://solidity.readthedocs.io/en/latest/introduction-to-smart-contracts.html#delegatecall-callcode-and-libraries) 。*状态契约*使用 *delegatecall* 调用*逻辑契约*中的函数。*逻辑契约*随后在*状态契约*的上下文中执行其逻辑，这意味着*“存储、当前地址和余额仍然引用调用契约，只是代码取自被调用地址。”*(来自上述提及的实体文件)。

# 使`MySmartContract`更容易升级

下面你可以看到如果我们实现了本文中描述的改变，`Version 1`和`Version 2`会是什么样子。再次提及`MySmartContract`使用的策略是可以接受的，因为它很简单:状态变量和逻辑。

首先，让我们看看`Version 1`的变化:

版本 1 —没有可升级的机制

```
contract MySmartContract {
  uint32 public counter; constructor() public {
    counter = 0;
  } function incrementCounter() public {
    counter += 2; // This "bug" is intentional.
  }
}
```

在下面的代码中，`Version 1`实现了一个带有[访问限制](https://solidity.readthedocs.io/en/latest/common-patterns.html#restricting-access)机制的[断路器](https://consensys.github.io/smart-contract-best-practices/software_engineering/#circuit-breakers-pause-contract-functionality)，一旦合同被否决，该机制允许所有者停止该合同。

版本 1 —具有弃用机制

```
contract MySmartContract {
  uint32 public counter;
  bool private stopped = false;
  address private owner; /**
  @dev Checks if the contract is not stopped; reverts if it is.
  */
  modifier isNotStopped {
    require(!stopped, 'Contract is stopped.');
    _;
  } /**
  @dev Enforces the caller to be the contract's owner.
  */
  modifier isOwner {
    require(msg.sender == owner, 'Sender is not owner.');
    _;
  } constructor() public {
    counter = 0;
    // Sets the contract's owner as the address that deployed the contract.
    owner = msg.sender;
  } /**
  @notice Increments the contract's counter if contract is active.
  @dev It will revert is the contract is stopped. See modifier "isNotStopped"
   */
  function incrementCounter() isNotStopped public {
    counter += 2; // This is an intentional bug.
  } /**
  @dev Stops / Unstops the contract.
   */
  function toggleContractStopped() isOwner public {
      stopped = !stopped;
  }
}
```

现在让我们看看`Version 2`会是什么样子:没有升级机制的版本 2

```
contract MySmartContract {
  uint32 public counter; constructor(uint32 _counter) public {
    counter = _counter;
  } function incrementCounter() public {
    counter++;
  }
}
```

在下面的代码中`Version 2`实现了与`Version 1`相同的[断路器](https://consensys.github.io/smart-contract-best-practices/software_engineering/#circuit-breakers-pause-contract-functionality)和[访问限制](https://solidity.readthedocs.io/en/latest/common-patterns.html#restricting-access)机制。此外，它实现了一个构造函数，允许在部署期间设置`counter`的初始值。可以使用这种机制，在升级期间可以使用这种机制从旧版本复制数据。

版本 2 —具有简单的可升级机制

```
contract MySmartContract {
  uint32 public counter;
  bool private stopped = false;
  address private owner; /**
  @dev Checks if the contract is not stopped; reverts if it is.
  */
  modifier isNotStopped {
    require(!stopped, 'Contract is stopped.');
    _;
  } /**
  @dev Enforces the caller to be the contract's owner.
  */
  modifier isOwner {
    require(msg.sender == owner, 'Sender is not owner.');
    _;
  } constructor(uint32 _counter) public {
    counter = _counter; // Allows setting counter's initial value on deployment.
    // Sets the contract's owner as the address that deployed the contract.
    owner = msg.sender;
  } /**
  @notice Increments the contract's counter if contract is active.
  @dev It will revert is the contract is stopped. See modifier "isNotStopped"
   */
  function incrementCounter() isNotStopped public {
    counter++; // Fixes bug introduced in version 1.
  } /**
  @dev Stops / Unstops the contract.
   */
  function toggleContractStopped() isOwner public {
      stopped = !stopped;
  }
}
```

尽管上述更改实现了一些有助于升级智能合约的机制，但本文开头描述的第一个挑战，*用户引用新合约的地址*，并没有用这些简单的技术解决。需要更高级的模式，如[代理](https://blog.openzeppelin.com/proxy-patterns/)和[注册](https://consensys.github.io/smart-contract-best-practices/software_engineering/#upgrading-broken-contracts)，或者使用 [ENS](https://ens.domains/) 为您的合同注册一个用户友好的名称，以避免所有用户升级到引用新地址`Version 2`。

# 结论

以太坊白皮书的 [DAO 章节](https://github.com/ethereum/wiki/wiki/White-Paper#decentralized-autonomous-organizations)中描述了可升级智能合约的原理，内容如下:

" *虽然代码在理论上是不可变的，但是通过将代码块放在单独的契约中，并将要调用的契约的地址存储在可修改的存储中，可以很容易地绕过这一点并具有事实上的可变性。*"

虽然这是可以实现的，但升级智能合约可能相当具有挑战性。区块链的不变性增加了 smart contract 升级的复杂性，因为它迫使您仔细分析使用 smart contract 的场景，了解可用的机制，然后决定哪些机制非常适合您的合同，这样潜在和可能的升级将会顺利进行。

智能合同升级是一个活跃的研究领域。相关的模式、机制和最佳实践仍在不断的讨论和发展中。使用*库*和一些设计模式如[断路器](https://consensys.github.io/smart-contract-best-practices/software_engineering/#circuit-breakers-pause-contract-functionality)、[访问限制](https://solidity.readthedocs.io/en/latest/common-patterns.html#restricting-access)、[代理](https://blog.openzeppelin.com/proxy-patterns/)和[注册表](https://consensys.github.io/smart-contract-best-practices/software_engineering/#upgrading-broken-contracts)可以帮助你解决一些挑战。然而，在更复杂的场景中，单靠这些机制可能无法解决所有问题，您可能需要考虑更复杂的模式，如本文中没有提到的[永久存储](https://blog.colony.io/writing-upgradeable-contracts-in-solidity-6743f0eecc88/)。

您可以在这个 [github 资源库](https://github.com/fodisi/solidity-design-patterns)中查看完整的源代码，包括相关的单元测试(为了简单起见，本文没有提及)。
# 固体中的事件与功能

> 原文：<https://levelup.gitconnected.com/events-vs-functions-in-solidity-3d6e797f349e>

## 两者的区别以及何时使用。

![](img/d13f10d25c33451b9b8630d65df061d9.png)

函数是任何编程语言的核心特性。它们允许我们通过在一个函数中编写一次代码来简化我们的代码库。然后在我们需要的时候调用这个函数。

在 [Solidity](https://docs.soliditylang.org/en/v0.8.3/index.html) 中，函数可以读取、写入、更改和存储智能合约中的数据。您可以将自变量(参数)传递给函数。如果你想的话，这个函数可以返回值。

让我们看一个例子。

```
function owner() public view returns(address) {return _owner;}
```

这是一个简单的函数，返回所有者的地址。它返回你传递给它的任何东西的地址。但是它实际上什么都不做。这就是它使用*视图*关键字的原因。它也是*公开*的，这意味着任何人都可以看。这里没有敏感数据。

只要想使用这个函数，就可以简单地调用它。

```
owner();
```

很简单，对吧？现在，让我们看看事件。

事件看起来很像函数。但是它们非常不同。

事件也接受参数。并将它们存储在事务日志中。请将此视为区块链中的一种特殊数据结构。

事件用于通知区块链之外的服务，让用户知道发生了一些事情。他们不存储这些数据。他们开火后就忘记了。事件内部的内容对于智能合约是*不*可见的。甚至是引发事件的智能合约。

可以把它想象成 JavaScript 中的 console.log()。

您可以作为连接到节点的应用程序订阅事件通知。仅依靠通知来获得有关更改的提醒，而无需自己进行更改。

让我们看一个例子。

```
event Transfer(address indexed _from, address indexed _to, uint256 indexed _tokenId);
```

该事件在事务日志中存储 3 个参数。发件人地址、收件人地址和令牌 ID。*索引的*关键字用于更容易地找到事务日志中的数据。

当您在智能合约中将 ERC721 令牌从一个用户转移到另一个用户时，您可以触发此事件。

调用事件就像调用一个函数，有一个很大的不同。事件使用*发出*关键字。这让开发者知道一个事件被调用，而不是一个函数。否则，很难发现不同之处。

我们把刚才写的事件叫做。

```
emit Transfer(_from, _to, _tokenId)
```

*emit* 关键字是 2017 年的必要更新。

当一名黑客成功从一个分散自治组织(DAO)窃取了 360 万以太币时，以太币的价格下跌。这一切都源于这个特定智能契约中事件和函数之间的混淆。

混乱导致了[硬分叉](https://www.investopedia.com/terms/h/hard-fork.asp#:~:text=What%20Is%20a%20Hard%20Fork,version%20of%20the%20protocol%20software.)并催生了[以太坊经典](https://ethereumclassic.org/)以及以太坊。

你可以在 Github 上的[这一期](https://github.com/ethereum/solidity/issues/2877)中找到介绍 *emit* 的提议。

有了这次更新，就不太可能有人再犯同样的错误了。
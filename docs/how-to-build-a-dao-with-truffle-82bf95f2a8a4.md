# 如何用块菌造刀

> 原文：<https://levelup.gitconnected.com/how-to-build-a-dao-with-truffle-82bf95f2a8a4>

![](img/2ee52b6cfcc859eb47b5f39b954c2c3c.png)

在 [Unsplash](https://unsplash.com/s/photos/partnership?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上 [Vardan Papikyan](https://unsplash.com/es/@varpap?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

## 咖啡店里的编码

## 亲身体验 Ganache 和 Truffle 套件

自从小本智(Satoshi Nakomoto)在他 2009 年的开创性白皮书中介绍了比特币和区块链以来，创新的数量令人震惊。如今，我们有了加密货币、NFT 和其他无需中介就能创造、拥有和转移的数字资产。

然而，也许区块链和智能合同取得的最重大进步之一是治理和工作的未来。前述技术使**、**也称为[、 **DAOs** 、](https://consensys.net/blog/blockchain-explained/what-is-a-dao-and-how-do-they-work/)成为可能。

Dao 是由社区领导的实体，没有中央权威。想想公司，但全球化，由每个成员控制和拥有，并有智能合同来执行链上的规则。Dao 允许来自世界各地的人们走到一起，实现一个共同的目标，甚至在这个过程中因他们的贡献而获得报酬。

Dao 已经被用来维护和管理我们这个时代一些最激动人心的 Web3 项目。一些例子包括[复合道](https://compound.finance/)，它决定其同名的借出协议如何操作；KlimaDAO ，通过碳资产支持的令牌解决气候变化问题；以及 [FWB](https://www.fwb.help/) ，旨在塑造 web3 的文化景观。

在本文中，我们将看看 DAO 是如何工作的，现有的 DAO 的类型，以及 DAO 的组件。然后，我们将使用 [Ganache](https://trufflesuite.com/docs/ganache/) 和 [Truffle 套件](https://trufflesuite.com/)构建我们自己的全功能、链上治理 DAO。

# Dao 是如何工作的？

如前所述，Dao 是一种机制，它允许多个人走到一起，为一个共同的目标而努力。DAO 成员通过使用治理令牌在区块链上投票和处理事务来实现这一点。

在引擎盖下，Dao 只不过是智能合约的集合。即使是最简单的 Dao 通常也有以下三个合同:

1.  **治理令牌**
    一个 [ERC-20](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/) 或一个具有投票权的 ERC-721 令牌。在大多数情况下，每个代币代表一票。你拥有的代币越多，你对某个特定决定的影响力就越大。
2.  管理者
    一个智能契约，负责创建提案，允许成员使用他们的令牌对提案进行投票，并根据投票结果执行提案。
3.  **主 DAO 或国库**
    智能合约持有资金和 DAO 的数据，由管理者根据投票结果进行修改。

概括地说，Dao 有两种风格:链上治理和链外治理。

前者通常在网上进行投票，并要求参与者支付汽油费才能投票。后者进行离线投票，并取消参与者的汽油费——但引入了现在必须信任的非链元素。

# 构建具有链上治理的道

现在我们已经了解了 DAOs，让我们来构建自己的 DAOs 吧！

在本教程中，我们将使用 [Truffle 套件](https://trufflesuite.com/)构建一个简单但功能齐全的 DAO。为此，我们将构建以下内容:

1.  实施 ERC 20 治理令牌的智能合约
2.  赋予上述令牌投票权的调控器，允许创建、投票和执行提案。
3.  持有 DAO 的状态和资金的 DAO 契约
4.  一个模拟脚本，用于空投治理令牌、创建提案、进行投票以及执行投票结果。在这里，我们将创建一个提议，向贡献者发送几个 DAO 令牌作为奖励。

# 步骤 1:安装 NPM 和节点

我们将使用 node 和 npm 来构建我们的项目。如果您的本地机器上没有安装这些软件，您可以在这里[安装](https://nodejs.org/en/download/)。

要确保一切正常运行，请运行以下命令:

```
$ node -v
```

如果一切顺利，您应该会看到 node 的版本号。

# 步骤 2:创建一个节点项目并安装依赖项

让我们通过运行以下命令来设置一个空的项目存储库:

```
$ mkdir truffle-dao && cd truffle-dao
$ npm init -y
```

我们将使用 EVM 智能合约的世界级开发环境和测试框架 [**Truffle**](https://trufflesuite.com/) 来构建和部署我们的智能合约。它将为我们提供完成教程所需的所有工具。

运行以下命令安装 Truffle:

```
$ npm install -g truffle
```

我们现在可以通过运行以下命令来创建一个准系统 Truffle 项目:

```
$ truffle init
```

要检查一切是否正常，请运行:

```
$ truffle - version
```

我们现在已经成功配置了松露。接下来，让我们安装[**OpenZeppelin**](https://www.openzeppelin.com/)**contracts 包。这是一组经过实战检验的智能合同，它们将使我们能够访问 ERC-20 治理令牌和链上调控器基础实现，在此基础上，我们将构建我们的组件合同。**

```
$ npm install -s @openzeppelin/contracts
```

**记住，智能合约是不可变的。一旦它们被部署在 mainnet 上，就不能被修改。因此，在部署之前确保它们能够工作是很重要的。为了解决这个问题，我们将在以太坊区块链的本地实例上测试我们的 DAO 的功能。这是由 Truffle 的 [**Ganache**](https://trufflesuite.com/ganache/) 实现的——一键创建个人和本地以太坊区块链。**

**通过运行以下命令进行全局安装:**

```
$ npm install -g ganache
```

# **步骤 3:创建 ERC 20 治理令牌**

**我们准备开始编码了！**

**在你喜欢的代码编辑器(比如 VS 代码)中打开 truffle 项目。在`contracts`文件夹中，创建一个新文件，并将其命名为`GovToken.sol`。**

**我们将编写一个简单的实现投票功能的 ERC 20 协议。我们还将允许合同的所有者铸造新的代币，对供应没有任何限制。**

**理想情况下，我们应该将这个令牌契约的所有权转移到我们将在后面的步骤中创建的主 DAO 契约，并允许 DAO 成员对新令牌的创建进行投票。在典型的 DAO 中，让一个人/钱包拥有所有的控制权并不是一个好的做法。然而，为了保持简单和快速，我们将坚持将所有权分配给正在部署的钱包。**

**将以下代码添加到`GovToken.sol`:**

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/draft-ERC20Permit.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Votes.sol";

contract GovToken is ERC20, Ownable, ERC20Permit, ERC20Votes {
  constructor() ERC20("GovToken", "GT") ERC20Permit("GovToken") {}

  function mint(address to, uint256 amount) public onlyOwner {
    _mint(to, amount);
  }

  // The following functions are overrides required by Solidity.
  function _afterTokenTransfer(address from, address to, uint256 amount)
    internal
    override(ERC20, ERC20Votes)
  {
    super._afterTokenTransfer(from, to, amount);
  }

  function _mint(address to, uint256 amount)
    internal
    override(ERC20, ERC20Votes)
  {
  super._mint(to, amount);
  }

  function _burn(address account, uint256 amount)
    internal
    override(ERC20, ERC20Votes)
  {
  super._burn(account, amount);
  }
}
```

**通过运行以下命令，确保合同编译正确:**

```
$ truffle compile
```

# **步骤 4:创建调控器**

**在`contracts`文件夹中，创建一个新文件，命名为`DaoGovernor.sol`。**

**我们将创建一个实现 [OpenZeppelin 的链上治理](https://wizard.openzeppelin.com/#governor)的调控器契约，其灵感来自于 Compound 的治理模型。该合同将允许我们创建提案，对提案进行投票，并在其驻留的区块链上执行任何代码。**

**将以下代码添加到`DaoGovernor.sol`。**

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

import "@openzeppelin/contracts/governance/Governor.sol";
import "@openzeppelin/contracts/governance/extensions/GovernorCountingSimple.sol";
import "@openzeppelin/contracts/governance/extensions/GovernorVotes.sol";
import "@openzeppelin/contracts/governance/extensions/GovernorVotesQuorumFraction.sol";

contract DaoGovernor is Governor, 
         GovernorCountingSimple, 
         GovernorVotes,
         GovernorVotesQuorumFraction {

  constructor(IVotes _token)
    Governor("DaoGovernor")
    GovernorVotes(_token)
    GovernorVotesQuorumFraction(4)
  {}

  function votingDelay() public pure override returns (uint256) {
    return 1; // 1 block
  }

  function votingPeriod() public pure override returns (uint256) {
    return 5; // 5 blocks
  }

  // The following functions are overrides required by Solidity.
  function quorum(uint256 blockNumber)
    public
    view
    override(IGovernor, GovernorVotesQuorumFraction)
    returns (uint256)
  {
    return super.quorum(blockNumber);
  }
}
```

**关于我们的州长，有几件事需要注意:**

1.  **构造函数接受一个`token` 作为参数。我们将在这里传递上一步中创建的治理令牌，以便在这个调控器中赋予它投票权。**
2.  **我们已经将`votingDelay` 设置为一个块。这意味着对提案的投票将在创建提案的街区之后的一个街区开始。**
3.  **我们将`votingPeriod` 设置为五个块。这意味着投票将只在五个街区内开放(大约 1 分钟)。在现实世界的 Dao 中，这个数字通常设置为一周。**
4.  **`quorumFraction` 已经设置为 4%。这意味着至少有 4%的投票者必须投*是*才能通过提案。**

**请随意试验这些数字，并根据您的使用情况修改它们。**

**再次运行以下命令，确保合同编译正确:**

```
$ truffle compile
```

# **步骤 5:创建主 DAO 契约**

**我们需要创建的最终契约代表主 DAO，并且通常保存 DAO 的状态和/或资金。这个道通常也是总督契约所拥有的，转移资金或者修改状态的函数都标有`onlyOwner`。**

**我们将通过在我们的 DAO 契约中只存储一个数字，并编写一个修改其值的函数来简化事情。**

**在`contracts`文件夹中创建一个名为`Dao.sol`的新文件，并添加以下代码:**

```
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.9;

import "@openzeppelin/contracts/access/Ownable.sol";

contract Dao is Ownable {
  uint public daoVal;

  constructor(address _govContract) {
    transferOwnership(_govContract);
  }

  function updateValue(uint256 _newVal) public onlyOwner {
    daoVal = _newVal;
  }
}
```

**请注意，在部署时，我们将合同的所有权转移给了我们的调控器合同。**

**像往常一样，通过运行以下命令来确保合同正在编译:**

```
$ truffle compile
```

# **步骤 6:更新 Truffle 配置文件**

**为了在 Ganache 上部署和运行我们的合同，我们需要用以下内容更新`truffle.config.js`文件:**

```
module.exports = {
  networks: {
    development: {
      host: "127.0.0.1",
      port: 8545,
      network_id: "*"
    },
  },
  compilers: {
    solc: {
      version: "0.8.13",
    }
  }
};
```

# **第七步:模拟刀**

**我们已经准备好部署 DAO，并通过它执行提议。**

**通常情况下，在像 Goerli 这样的测试网上测试一个 DAO 是很困难的，因为它的核心功能通常需要多方长期合作。幸运的是，使用 Ganache，我们可以通过在本地旋转以太坊实例来模拟时间旅行和多个钱包。**

**通过打开一个新的终端并运行以下命令来启动 ganache 实例:**

```
$ ganache
```

**在 migrations 文件夹中，创建一个新文件`1_simulation.js`。这些是我们将模拟的步骤:**

1.  **创建三个钱包:所有者、投票者 1 和投票者 2。**
2.  **部署 ERC-20 合同。**
3.  **铸造 100 个代币到三个钱包，并自我委派每个代币的投票权。**
4.  **使用 ERC-20 合约作为投票令牌来部署调控器合约。**
5.  **部署 DAO 协定，将所有权分配给调控器。**
6.  **所有者创建一个建议，将 DAO 值更改为 42。**
7.  **1 号选民和 2 号选民投赞成票。**
8.  **业主在提案成功后执行提案。**

**这里发生了很多事情！将以下代码添加到上述文件中:**

```
// Import necessary libraries
const ethers = require('ethers');

// Proposal states as defined by OpenZeppelin/Compound
oz_states = ['Pending', 'Active', 'Canceled', 'Defeated',
             'Succeeded', 'Queued', 'Expired', 'Executed'];

// Ganache localhost URL
const url = "http://localhost:8545";
const provider = new ethers.providers.JsonRpcProvider(url);

// Helper function to fast forward blocks
const ff_blocks = async (n) => {
  for (let i = 0; i < n; i++) {
    await provider.send('evm_mine');
  }
  console.log("\nMoved", n, "blocks\n");
}

// Helper function to get current block
const getBlock = async () => {
  const blockNum = await provider.getBlockNumber();
  console.log("Block:", blockNum);
  return blockNum;
}

// Helper function to get proposal state
const getProposalState = async (gov, proposalId) => {
  let propState = await gov.state(proposalId);
  console.log("Proposal State:", oz_states[propState.toNumber()]);
}

// Getting three wallets from Ganache provider
const owner = provider.getSigner(0);
const voter1 = provider.getSigner(1);
const voter2 = provider.getSigner(2);

// Get instances of all three contracts
const tokenContract = artifacts.require("GovToken");
const govContract = artifacts.require("DaoGovernor");
const daoContract = artifacts.require("Dao");

module.exports = async function (deployer) {
  // Deploy the governance token contract
  await deployer.deploy(tokenContract);
  const token = await tokenContract.deployed();
  console.log("Token Contract deployed to:", token.address);

  // Get addresses of all three accounts
  const ow = await owner.getAddress();
  const v1 = await voter1.getAddress();
  const v2 = await voter2.getAddress();

  // Mint 100 tokens to owner, voter1, and voter2
  await token.mint(ow, 100);
  await token.mint(v1, 100);
  await token.mint(v2, 100);
  console.log("Minted 100 tokens to owner, voter1, and voter2", "\n");

  // Delegate voting power to themselves
  await token.delegate(ow, { from: ow });
  await token.delegate(v1, { from: v1 });
  await token.delegate(v2, { from: v2 });

  // Deploy the governor contract
  await deployer.deploy(govContract, token.address);
  const gov = await govContract.deployed();
  console.log("Governor contract deployed to:", gov.address, "\n");

  // Deploy the dao contract
  await deployer.deploy(daoContract, gov.address);
  const dao = await daoContract.deployed();
  console.log("DAO contract deployed to:", dao.address, "\n");

  // Owner creates a proposal to change value to 42
  let proposalFunc = dao.contract.methods.updateValue(42).encodeABI();
  let proposalDesc = "Updating DAO value to 42";
  console.log("Proposing:", proposalDesc);
  let proposalTrxn = await gov.propose(
    [dao.address],
    [0],
    [proposalFunc],
    proposalDesc,
  );

  // Move past voting delay
  await ff_blocks(1)

  // Get proposal ID and make proposal active
  let proposalId = proposalTrxn.logs[0].args.proposalId;
  console.log("Proposal ID:", proposalId.toString());
  await getProposalState(gov, proposalId);

  // Voter 1 and voter 2 vote in favor
  let vote = await gov.castVote(proposalId, 1, { from: v1 });
  console.log("V1 has voted in favor.")
  vote = await gov.castVote(proposalId, 1, { from: v2 });
  console.log("V2 has voted in favor.")

  // Move 5 blocks
  await ff_blocks(5);

  // Get final result
  console.log("Final Result");
  await getProposalState(gov, proposalId);

  // Execute task
  let desHash = ethers.utils.id(proposalDesc);
  execute = await gov.execute(
    [dao.address],
    [0],
    [proposalFunc],
    desHash
  );
  console.log("\nExecuting proposal on DAO")

  // Check if value on dao has changed
  const daoVal = await dao.daoVal.call();
  console.log("daoVal:", daoVal.toNumber());
};
```

**现在，让我们使用以下命令运行这个模拟:**

```
$ truffle migrate --quiet
```

**如果一切顺利，您应该会看到如下所示的输出:**

```
Token Contract deployed to: 0x21d530ED509D0b44F94b5896Fb63BDa2d8943E4e
Minted 100 tokens to owner, voter1, and voter2

Governor contract deployed to: 0x1C63D60Dcb50015d3D9598c45F48C7De16E0f925

DAO contract deployed to: 0xFC952D50576064C882bE9E724C5136F774B8f66e

Proposing: Updating DAO value to 42

Moved 1 blocks

Proposal ID: 89287417867871262838116386769359148381852036852071370084586471483495168348362
Proposal State: Pending
V1 has voted in favor.
V2 has voted in favor.

Moved 5 blocks

Final Result
Proposal State: Succeeded

Executing proposal on DAO
daoVal: 42
```

**请注意，在成功传递和执行建议后，DAO 值更改为 42。**

# **结论**

**Dao 是区块链最伟大的创新之一。他们有潜力在塑造未来的工作中发挥巨大的作用。他们能够将陌生人聚集在一起，并为陌生人的贡献支付报酬，而不管他们的地理位置如何，这使他们比传统的同行具有明显的优势。**

**在本教程中，您已经学习了 Dao 是什么以及它们是如何工作的。然后，您部署了一个全功能的 DAO，并执行了提案的整个生命周期。为什么不试着打造自己的？**
# 使用 Node.js 生成以太坊地址

> 原文：<https://levelup.gitconnected.com/generate-ethereum-address-using-node-js-a6a73f42a4cf>

![](img/ae315ba14aa720e1ac35db1f70ba5c9a.png)

用[以太网钱包](https://github.com/ethereumjs/ethereumjs-wallet)库生成一个地址非常简单。它可以用不超过 4 行代码来完成。

# 安装以太网钱包

```
npm install ethereumjs-wallet --save
```

创建一个 index.js 文件并使用以下代码。

```
var Wallet = require('ethereumjs-wallet');
const EthWallet = Wallet.default.generate();
console.log("address: " + EthWallet.getAddressString());
console.log("privateKey: " + EthWallet.getPrivateKeyString());
```

完成后，使用下面的命令运行代码。

```
node index.js
```

您将看到如下输出:

```
address: 0xf1c64a260c427d678b3358bface9fa2b6c439453
privateKey: 0xbdcaa9c90ae131f3321d3da91b49ccb169bf4c0175027929d0b2d42129a3446f
```

如果你仍然想要完整的工作代码，这里有 [Github](https://github.com/shivapendem/ethereumjs_wallet_nodejs.git) 源代码。使用命令`npm install`下载源代码并安装节点模块。

再次使用下面的命令运行代码。

```
node index.js
```

如果你觉得这篇文章是有帮助的，如果你能给下面的地址提示乙醚，将不胜感激。谢谢大家！

```
0xe8312ec868303fc3f14DeA8C63A1013608038801
```

更多信息请打我的电报 id [@chigovera](https://t.me/chigovera) 联系我。
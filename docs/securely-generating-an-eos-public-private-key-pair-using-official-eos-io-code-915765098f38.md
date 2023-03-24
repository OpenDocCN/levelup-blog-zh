# 使用 EOS 生成 EOS 公钥/私钥对。IO 和 Node.js

> 原文：<https://levelup.gitconnected.com/securely-generating-an-eos-public-private-key-pair-using-official-eos-io-code-915765098f38>

![](img/397513eb3c78b15d3dff2f90b6a901d6.png)

有许多方法可以生成 EOS 公钥/私钥对。尽管有许多第三方选项，但对于使用官方 EOS 没有提供的东西要格外小心是可以理解的。IO 团队。

本视频中描述的方法仅依赖于来自 EOS 的代码和命令。IO GitHub 组织。虽然它确实需要执行一些 JavaScript 代码，但我们的首席软件工程师 Mike Haggerty 一丝不苟地完成了每个步骤，让非程序员也能执行这一重要功能。

```
// Step 1\. Open command prompt / terminal//Step 2\. Check if Node.js is installed
$ node -v
$ npm -v//Step 3\. Download Node.js (if needed)
[https://nodejs.org](https://nodejs.org/)//Step 4\. Setup workspace
$ mkdir eos_keypair//change to working directory
$ cd eos_keypair//Step 5\. Download eosjs-ecc NPM package then turn off Wi-Fi
$ npm i eosjs-ecc//Step 6\. Start Node.js interactive shell
$ node//Step 7\. Execute “four commands”
//The “four commands” listed on the eosjs-ecc README:let {PrivateKey, PublicKey, Signature, Aes, key_utils, config} = require(‘eosjs-ecc’)let privateWifPrivateKey.randomKey().then(privateKey => privateWif = privateKey.toWif())pubkey = PrivateKey.fromWif(privateWif).toPublic().toString()//Step 8\. Output private key
privateWif
```

将 EOS 公钥映射更新到您的以太坊钱包

*   打开[这篇文章](https://steemit.com/eos/@sandwich/eos-io-contract-address-and-abi-code)获取**合同地址**和 **ABI / JSON 接口**
*   转到[https://www.myetherwallet.com/#contracts](https://www.myetherwallet.com/#contracts)
*   粘贴合同地址和 ABI/JSON 接口代码
*   点击`Access`
*   在“读/写合同”下，从下拉列表中选择`register`。
*   在名为 *Key* 的字段中，输入您之前生成的 **EOS 公钥**。**不要**，我重复一遍，**不要**在这里输入你的 EOS 私钥。
*   根据您可能需要输入密码的方法，使用列出的任何一种方法加载您的钱包。
*   点击`Unlock`
*   点击`Write`
*   设置*发送*到`0`的数量，并允许钱包提示*气体限制*。如果没有自动填充，输入`90000`或更多。如果你的交易失败，增加汽油限额。如果你希望你的交易进行得更快，相应地调整你的汽油限额。
*   点击“生成交易”
*   查看交易详情，如果您有信心，请点击`Yes, I am sure! Make transaction`
*   如果一切顺利，页面底部会出现一个绿色条，其中包含您在区块链上交易的链接。

您刚才输入并广播的 EOS 密钥现在将被映射到与确认交易后发送交易的钱包相关联的以太坊地址。

如果你觉得这篇文章是有帮助的，如果你能给下面的地址提示乙醚，将不胜感激。谢谢大家！

```
0xe8312ec868303fc3f14DeA8C63A1013608038801
```

更多信息请打我的电报 id [@chigovera](https://t.me/chigovera) 联系我。
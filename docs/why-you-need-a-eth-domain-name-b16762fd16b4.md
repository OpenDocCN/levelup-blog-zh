# 为什么你需要一个。“ETH”域名

> 原文：<https://levelup.gitconnected.com/why-you-need-a-eth-domain-name-b16762fd16b4>

## 1.这会节省你的时间和金钱

![](img/ae2f5103617f7ccd1a1ba3203748db04.png)

由 Canva Design 打造。鸣谢:作者。

我正在学习区块链技术的应用，智能合同，第三代密码和 IPFS 的工作原理，然后我遇到了**以太坊名称服务(ENS)** 。ENS 的想法对我来说似乎很明显，但我发现只有少数人真正使用它。所以，我决定与全世界分享它，为他们节省一些钱和时间。

# 以太坊名称**服务**是什么

在互联网的早期，用户必须键入一长串字符才能访问网页。这个 IP 地址看起来像这样`142.250.183.46`你能认出它是哪个网站吗？

那是谷歌。复制该地址并将其粘贴到浏览器中进行确认。

**域名服务(DNS)** 提供了一种将人类可读的地址转换为计算机可读的一长串不可读的 IP 地址的方法。这些服务跟踪所有域名，并在需要时提供正确的 IP 地址。

ENS 在哪里出现？

困扰早期 web 的同样问题现在也困扰着 web 3.0。将密码从一个帐户转移到另一个帐户需要一个相对较长的加密地址，这是不可能记住的，也容易混淆。

不像访问网站，如果你搞砸了 IP 地址，你可以重新输入一次，没有任何后果；如果你弄乱了加密地址，你可能会失去转移的金额。更糟糕的是，现在收件人因为没有收到货款而生气，现在寄件人破产了。

> 以太坊名称服务(ENS) 是一个基于以太坊区块链的分布式、开放、可扩展的命名系统。

ENS 是一个查找系统，它将长的不可读的地址转换成类似于`[shubhpatni.eth](https://app.ens.domains/name/shubhpatni.eth)`的东西(你实际上可以发送一些 eth 到那里😉).因此，你可以将 ETH 或任何其他由接收者注册的加密货币发送到这个地址，它会自动将钱发送到所需的加密帐户。

*注意:确保检查接收方是否注册了您要发送的加密货币的地址。你可以通过访问* `*https://app.ens.domains/name/{reciepient_name.eth}*`来查看

## ENS 如何工作

ENS 有一个注册管理机构智能合同，可以跟踪所有域名、子域、所有者、解析器等，并允许所有者更改这些数据。注册中心是 ENS 的主干。

ENS 的另一个关键组件是解析器。解析器负责将名称转换成地址。因此，解析器必须处理不同类型的数据，如加密货币地址、IPFS 内容哈希、用户资料、头像等。如果需要，我们还可以通过 EIP 标准化过程添加其他数据类型，而无需更改 ENS 注册中心或任何其他解析器。

*解析器和注册表如何协同工作？*

从 ENS 获取数据分为两步。首先，我们需要从注册表中找到正确的解析器。为此，我们将 ENS 域名传递给寄存器，寄存器返回解析器的地址。现在，我们向这个解析器请求所需的数据。

# ENS 的好处

技术细节说够了。现在让我们来看看 ENS 域实际上可以做什么，以及它如何不仅仅是一个分布式 web 的 DNS。

## 主办 IPFS 网站

[IPFS(星际文件系统)](http://ipfs.io/)通过 ENS，你可以以一种真正去中心化的方式托管任何网站。这意味着你的网站将是抗审查的，不可改变的，在一个分散的网络中。如果你不想让你的网站以`.eth`结尾，或者想把你现有的域名连接到你的 IPFS 托管网站，你可以这样做。

这是卢卡斯·科霍斯特关于如何托管你的网站的一个很好的指导

[](https://towardsdatascience.com/decentralizing-your-website-f5bca765f9ed) [## 分散你的网站

### IPFS + ENS

towardsdatascience.com](https://towardsdatascience.com/decentralizing-your-website-f5bca765f9ed) 

## 存储您的电子邮件、个人资料、简历和其他信息

ENS 允许您存储的不仅仅是您的加密货币地址或 IPFS 网站。你可以添加其他 [ENS 支持的区块链](https://github.com/ensdomains/address-encoder)的地址，你的 Twitter 个人资料，简历，头像，电子邮件，以及许多其他东西。人们只要访问你的`.eth`域名就能找到所有这些细节。

下面是布兰特利·米莱根关于如何设置的指导

[](https://medium.com/the-ethereum-name-service/guide-for-soulja-boy-to-setting-up-his-eth-name-and-beyond-b382717bdc71) [## 酱爆弟弟建立自己的公司指南。以太网名称及其他

### 酱爆弟弟今天早些时候在推特上发布了他的以太坊地址。

medium.com](https://medium.com/the-ethereum-name-service/guide-for-soulja-boy-to-setting-up-his-eth-name-and-beyond-b382717bdc71) 

## 接收位于区块链的资产

您可以使用您的一个 ENS 域接收超过 100 区块链的本地资产。这意味着你可以收到这些加密货币和任何其他资产的代币，例如生活在区块链的 NFT。所以，让我们说你添加了一个 BNB 地址到你的域，你现在可以接收任何 BEP20 令牌。您在 ENS 上注册的任何其他受支持的区块链也是如此

以下是所有支持的区块链列表

[](https://github.com/ensdomains/address-encoder) [## ens domains/地址编码器

### 这个 typescript 库编码和解码各种加密货币的地址格式。文本格式的地址是…

github.com](https://github.com/ensdomains/address-encoder) 

## ENS 作为 NFT

所有的 ENS 域名都是在 ERC-721 标准下注册的，所以它们是 NFT，这意味着你可以像对待 NFT 一样对待这些域名。你可以买卖它，把它转到另一个账户，或者把它作为提供该服务的项目的抵押品。

## 如果您输入了错误的地址，请不要担心

ENS 域名的另一个小但有用的功能是，它会告诉你是否输入了未注册的域名。这是什么意思？

假设你想送一些乙醚给你的朋友或员工。如果您输入长的散列字符串，应用程序不会告诉您该地址是否存在或是否正确。但是如果你输入一个 ENS 域名，它会告诉你这个地址是否存在，如果这个地址不属于任何人，它会抛出一个错误。

*还有一件事！在以太坊区块链上存储任何数据都要耗费汽油。所以，我建议你一次性添加你的加密货币地址、个人信息等，以节省油费。*

这是你在创建自己的域名之前需要了解的所有关于 ENS 的信息。下面是[布兰特利·米莱根](https://medium.com/u/6b11550a602f?source=post_page-----b16762fd16b4--------------------------------)关于如何设置的一步一步的指导。

[](https://medium.com/the-ethereum-name-service/step-by-step-guide-to-registering-a-eth-name-on-the-new-ens-registrar-c07d3ab9d6a6) [## 注册. ETH 名称的分步指南

### 本指南将逐步指导您如何注册和设置新的。带有官方 ENS 的 ETH 名称…

medium.com](https://medium.com/the-ethereum-name-service/step-by-step-guide-to-registering-a-eth-name-on-the-new-ens-registrar-c07d3ab9d6a6) 

*你可以在* [*Gumroad*](https://shubhpatni.gumroad.com/) *上查看我的免费策划资源列表，了解更多关于加密货币的信息，或者访问我的*[*socials*](https://y.at/%F0%9F%92%BB%F0%9F%8E%A5%E2%9C%8D%E2%98%95)*。*
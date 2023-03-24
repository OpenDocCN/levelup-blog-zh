# AWS 及其数十亿的 IPv4 地址

> 原文：<https://levelup.gitconnected.com/aws-and-their-billions-in-ipv4-addresses-8b1820fb24c1>

![](img/9b88615aac6890d1a0d4ff2455e45d59.png)

希曼舒·查南在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

**本文原载于我的个人博客** [**Toonk.io**](https://toonk.io/aws-and-their-billions-in-ipv4-addresses/)

本周早些时候，我在 AWS 上做了一些工作，想知道正在使用哪些 IP 地址。对我来说幸运的是，AWS 在这里发表了这一切[https://ip-ranges.amazonaws.com/ip-ranges.json](https://ip-ranges.amazonaws.com/ip-ranges.json)。当您浏览这个列表时，您会很快看到 AWS 拥有大量的 IPv4 分配资产。只是快速数了数，我注意到了很多大前缀！

然而，该列表上的 IPv4 范围只是 AWS 目前正在使用和分配的范围。是时候深入挖掘一下了。

# AWS 获取 IPv4 地址

多年来，AWS 获得了大量的 IPv4 地址空间。大多数收购都没有引起太多关注，但是有一些值得注意的收购，我将在下面快速总结一下。

## 2017 年:麻省理工学院向 AWS 出售 800 万个 IPv4 地址

2017 年[麻省理工学院向 AWS 出售了其 18.0.0.0/8](https://www.internetsociety.org/blog/2017/05/mit-goes-on-ipv4-selling-spree/) 配额的一半。这个 18.128.0.0/9 范围包含大约 800 万个 IPv4 地址。

## 2018:通用电气向 AWS 出售 3.0.0.0/8

2018 年，IPv4 前缀 3.0.0.0/8 从 GE 转移到 AWS。凭借这一点，AWS 成为其首台/8 的自豪拥有者！这相当于 1600 万个新的 IPv4 地址来满足我们饥渴的 AWS 客户。[https://news.ycombinator.com/item?id=18407173](https://news.ycombinator.com/item?id=18407173)

## 2019: AWS 收购 AMPRnet [44.192.0.0/10](http://44.192.0.0/10)

2019 年，AWS 从 AMPR.org 业余无线电数字通信公司(ARDC)购买了 a /10。IPv4 范围 44.0.0.0/8 是 1981 年分配给业余无线电组织的，称为 AMPRNet。这次出售引起了相当多的讨论，查看这里的 [nanog 讨论。](https://mailman.nanog.org/pipermail/nanog/2019-July/thread.html#102103)

就在这个月，它[成为众所周知的](http://www.southgatearc.org/news/2020/october/sale-of-amateur-radio-amprnet-tcp-ip-addresses.htm) AWS 为此/10 支付了 1.08 亿美元。每个 IP 地址 25.74 美元。

这些只是几个例子。显然，AWS 的 IP 地址比我在这里列出的三个例子多得多。IPv4 传输市场非常活跃。看看这个网站，了解一下所有的转会:[https://account.arin.net/public/transfer-log](https://account.arin.net/public/transfer-log#NRPM-8.3IPv4)

# 所有 AWS IPv4 地址

有了上面的信息，很明显不是所有 AWS 拥有的范围都在 AWS 发布的 JSON 中。例如，缺少 3.0.0.0/8 范围的部分内容。可能是因为其中一些是留作将来使用的。

我做了一些调查，试图找出 AWS 真正拥有多少 IPv4 地址。AWS 发布的 Json 是一个很好的开始。然后，我将它与我能找到的所有 ARIN、APNIC 和成熟的亚马逊条目结合起来。一些例子包括:

https://rdap.arin.net/registry/entity/AMAZON-4[https://rdap.arin.net/registry/entity/AMAZO-4](https://rdap.arin.net/registry/entity/AMAZON-4)[https://rdap.arin.net/registry/entity/AT-88-Z](https://rdap.arin.net/registry/entity/AT-88-Z)T4

组合所有这些 IPv4 前缀，通过聚合它们来移除重复和重叠，从而产生 AWS 拥有的唯一 IPv4 地址的以下列表:[https://gist . github . com/ATO oonk/b 749305012 AE 5b 86 BAC ba 9 b 01160 df9 f # all-prefixes](https://gist.github.com/atoonk/b749305012ae5b86bacba9b01160df9f#all-prefixes)

该列表中的 IPv4 地址总数刚刚超过 1 亿(100，750，168)。那是**相当于刚过 6/8 的，**不错！

如果我们按分配大小细分，我们会看到以下内容:

```
1x /8     => 16,777,216 IPv4 addresses
1x /9     => 8,388,608 IPv4 addresses
4x /10    => 16,777,216 IPv4 addresses
5x /11    => 10,485,760 IPv4 addresses
11x /12   => 11,534,336 IPv4 addresses
13x /13   => 6,815,744 IPv4 addresses
34x /14   => 8,912,896 IPv4 addresses
53x /15   => 6,946,816 IPv4 addresses
182x /16  => 11,927,552 IPv4 addresses
<and more>
```

完整的细分可以在这里找到:[https://gist . github . com/ATO oonk/b 749305012 ae5b 86 BAC ba 9 b 01160 df9 f # break-by-IP v4-prefix-size](https://gist.github.com/atoonk/b749305012ae5b86bacba9b01160df9f#breakdown-by-ipv4-prefix-size)

# 对 AWS 的 IPv4 资产进行估值

> 没问题的..这只是为了好玩…

由于 AWS 是 IPv4 地址的最大买家之一，他们已经花费了大量资金来积累 IPv4 资源。作为一个局外人，不可能知道 AWS 为每笔交易支付了多少钱。然而，为了好玩，我们可以试着给 AWS 当前的 IPv4 资产标上一个美元数字。

这些年来，IPv4 地址的平均价格一直在上涨。从几年前的 10 美元一个 IP 回到现在的 25 美元一个 IP。
请注意，这些是市场价格，因此如果 AWS 突然决定出售其 IPv4 地址，并以供应淹没市场，价格将会下降。但这不会发生，因为我们仍然沉迷于 IPv4)

无论如何，让我们坚持 25 美元，做数学只是为了好玩。

```
100,750,168 ipv4 addresses x $25 per IP = $2,518,754,200
```

仅仅是**价值超过 25 亿美元的 IPv4 地址，**还不错！

# 窥视未来

很明显，AWS 正在幕后努力工作，以确保我们能够继续在 AWS 上做更多的工作。我们可以考虑的最后一个问题是:*AWS 有多少缓冲？* ie。他们的 IPv4 储备状况如何？

根据他们[公布的数据](https://ip-ranges.amazonaws.com/ip-ranges.json)，他们已经为现有的 AWS 服务分配了大约 5300 万个 IPv4 地址。我们发现他们所有的 IPv4 地址加起来相当于大约 1 亿个 IPv4 地址。这意味着他们仍有约 4700 万个 IPv4 地址，或 47%可用于未来分配。那是相当健康的！最重要的是，我相信他们会继续获取更多的 IPv4 地址。IPv4 市场依然火爆！
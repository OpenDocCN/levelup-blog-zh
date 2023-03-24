# LastPass 搞砸了——他们也对我们撒了谎

> 原文：<https://levelup.gitconnected.com/lastpass-screwed-up-and-they-also-lied-to-us-62d9afb6c29>

## 为什么您应该尽快迁移到新的密码管理服务

![](img/f747c087abdc2352cf0f3d420ec75701.png)

这封邮件在圣诞节周末前的周五早上 6 点到达。LastPass 对最近的一次安全“事故”进行了“更新”,并让我点击他们的[博客](https://blog.lastpass.com/2022/12/notice-of-recent-security-incident/)的链接以了解更多信息。这个链接用一个弹出窗口欢迎我，愉快地要求我订阅未来的更新。

如果一个人想掩盖坏消息，可能没有更好的办法了。

## 发生了什么事？

回到 2022 年 8 月，一群黑客进入了 LastPass 的一个开发环境，窃取了源代码和技术信息。然后，他们使用这些信息直接攻击员工的机器，并获得 LastPass 数据服务器的访问凭证。有了这些凭证，他们窃取了 LastPass 用户的个人数据，包括

*   他们的电子邮件地址，
*   他们的账单地址(如果他们为高级服务付费)，
*   网站网址(未加密！)用于存储在其保管库中的登录，以及
*   这些登录的加密用户名和密码。

不管出于什么原因，LastPass 没有透露受影响的 3000 万用户的确切数量，也没有透露数据泄露的确切日期。

## 那是什么意思？

很糟糕。

首先，如果你是 LastPass 用户，坏人现在可以访问你的电子邮件地址以及你存储在 LastPass 保险箱中的所有网址。如果您购买了 LastPass Premium，他们可能也有您的实际地址。这样，他们就可以针对你进行网络钓鱼攻击(例如，联系你并伪装成亚马逊)或凭据填充攻击(尝试使用其他现有数据漏洞的密码进入你的在线帐户)。

第二，阻挡在坏人和你所有的用户名和密码之间的是数据加密。加密可以被暴力攻击破解(尝试所有可能的密码组合)。这可能需要几天到几个世纪的时间，取决于你的主密码的强度和这些坏家伙在这个问题上投入的计算能力。

然而，即使你的主密码足够强大，以今天的技术进行暴力攻击需要几个世纪，这可能会随着量子计算机的出现而改变。现在，坏演员可能会故意窃取并保留加密数据，希望一旦量子技术足够成熟，就能够解密这些数据。作案手法是“[现在偷，以后解密](https://www.economist.com/the-world-ahead/2022/11/18/jack-hidary-says-you-cant-afford-to-ignore-quantum-computing)”。

## LastPass 的客户应该怎么做？

如果你听 LastPass，只要你按照他们的建议使用 12 个字符的主密码(这是默认设置，但自 2018 年以来没有强制执行)，“目前没有建议你需要采取的行动”。

我不同意。尽管我确实有一个强大的主密码，但它(和 LastPass 的单词)现在是阻挡在坏人和我所有在线账户之间的唯一东西，这让我感到不安。就我个人而言，我检查了我的整个 LastPass 库，登录了每个帐户，更改了密码，并将新密码保存在不同的密码管理服务中。

似乎我不是唯一一个。一位 [Redditor](https://www.reddit.com/r/Lastpass/comments/ztemzy/recommended_actions_for_the_lastpass_security/) 说:

> “我将无法和家人一起庆祝圣诞节，而是要修改数百个账户的密码，感谢 LastPass！”

安全研究员弗拉基米尔·帕兰特称 LastPass 的“不推荐行动”言论[是重大疏忽](https://palant.info/2022/12/26/whats-in-a-pr-statement-lastpass-breach-explained/)。他警告说，决心足够大的攻击者将能够为几乎任何人解密数据。

## 有一点很清楚:LastPass 骗了我们

LastPass 是不是故意试图掩盖这个坏消息，在一年中最重要的节日的前一天发了一个公告，甚至没有直接在电子邮件中给我们信息，而是让我们指向他们的博客？八月和圣诞节之间到底发生了什么？数据泄露的确切日期是什么时候？

缺乏明确性令人愤怒。然而，已经清楚的是，LastPass 对我们撒了谎。下面是他们如何宣传他们的[“零知识](https://www.lastpass.com/security/what-is-a-cyberattack)”安全模型:

> “零知识”意味着除了您之外，没有人可以访问您的解密主密码、保险库或保险库数据

正如我们现在所知，这种说法是错误的:事实上，登录 URL(其中*是* vault 数据)是未经加密存储的，现在在黑客手中。

注意这个空间。加入(最有可能到来的)集体诉讼。LastPass 明显歪曲了他们的服务，他们把你的敏感信息暴露给了不良演员。

*👉* [*在 Medium 上关注*](https://medium.com/@samuel.flender) *我可以在您的订阅中看到我的更多内容。
📫* [*订阅*](/subscribe/@samuel.flender) *把我的下一篇文章直接发到你的收件箱。
💪* [*成为中等会员*](/@samuel.flender/membership) *并解锁无限权限。*

如果你喜欢这个故事，你可能也会喜欢我对另一个公司谎言的深入探究，即大众柴油丑闻

[](https://medium.com/swlh/the-lie-of-clean-diesel-f55b75031066) [## 清洁柴油的谎言

### 大众汽车如何欺骗全球数百万消费者

medium.com](https://medium.com/swlh/the-lie-of-clean-diesel-f55b75031066)
# 为什么应该在 Ruby 应用程序中扫描包

> 原文：<https://levelup.gitconnected.com/why-you-should-scan-packages-in-ruby-applications-327dd8324f05>

## 了解您的应用程序是否安全。很关键！

![](img/bd39eb8780cf5282a08273c9aa8aeeb1.png)

照片由[飞:D](https://unsplash.com/@flyd2069?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 介绍

开源是大多数现代软件构建的驱动力。如果没有开源，现在很难想到开发任何解决方案。现在，开发人员在使用开源解决他们的问题之前不会三思。

当您安装软件包时，它会在您的应用程序环境中运行。恶意或易受攻击的软件包可能会导致严重的后果，如侵入您的服务器。已经有过恶意代码隐藏在开源包中的例子。

作为一名开发人员，我们每天都在许多战线上战斗。开源库和软件包可以方便地解决我们的问题。但是这些包中的任何一个都有漏洞。而且，作为问题解决者，我们没有时间去探究软件包的来源。

例如，已经有[几份关于在 Ruby gems 中发现漏洞的报告](https://www.cvedetails.com/vulnerability-list/vendor_id-12065/Rubygems.html)。

2020 年 12 月，RubyGems 移除了几个带有恶意代码的[宝石](https://www.securityweek.com/two-malware-laced-gems-found-rubygems-repository)。这些漏洞非常严重。这些 gem 包含恶意代码，用攻击者提供的地址替换剪贴板中的加密货币钱包地址。因此，攻击者可以访问用户的交易并窃取资金。

你可以很容易地找到一个宝石，可以解决手头的问题，只要谷歌一下。一般你看许可证，GitHub 上的星数，把宝石加到你的项目里。开发人员不要试图理解 gem 代码，只要它能解决他们的问题。

好吧，如果你对开源软件包带来的漏洞没有洞察力，你就是在瞎跑。

以下是一些威胁、事实和数字，以及降低风险的方法。

# 主要威胁

盲目地使用开源包或 gems，你将自己置于危险之中。有许多错误会导致攻击:

*   使用老化和未维护的开源包。
*   低估开源漏洞可能造成的影响。
*   在不了解更改的情况下升级软件包。
*   不使用工具来找出您的应用程序由于开源而可能存在的漏洞。

# 事实和数字

[octo verse](https://octoverse.github.com/static/github-octoverse-2020-security-report.pdf)的 2020 年状态报告让您深入了解如果您使用错误的软件包可能会对您造成伤害的漏洞:

*   17%的漏洞是故意恶意的。这清楚地表明，开源开发者故意将恶意软件隐藏在包中。但是，这也说明 83%的漏洞都不是故意的。但是，它们仍然是弱点。
*   许多漏洞多年来都未被发现。
*   具有受支持的包生态系统的活动 Ruby 库有 81%的机会在下一年收到安全警报。

# 风险缓解

好吧，有办法在这些弱点严重打击你之前减轻它们。这些方法简单而繁琐。

*   定期检查您使用的 gem 中的漏洞。获得一个可以发现漏洞和恶意软件并向您发出警告的工具。
*   保持 gem 更新，因为有时他们会修复新版本中的漏洞。
*   如果你有解决办法，别忘了投稿。这会帮助别人。
*   构建一个监控系统，检查后端发生的任何攻击，并在受到攻击时发出警报。

# 工具

市场上有许多工具可以扫描开源包，检测漏洞，并通过漏洞修复降低网络风险。

以下是市场上一些流行的工具:

*   [瓦肯](https://vulcan.io/lp/vulcan-free/):瓦肯协调修复生命周期。它发现开源软件中的漏洞并修复它们。
*   [AppTrana](https://www.indusface.com/vulnerability-management.php?) : AppTrana 是一款 Web 应用防火墙。它扫描 Web 应用程序以查找漏洞。它还提供 WAF、托管 DDOS 和 Bot 缓解服务以及 CDN。
*   Netsparker : Netsparker web 应用安全扫描程序检测漏洞，如 SQL 注入、跨站点脚本(XSS)。

我一直在使用 [WhiteSource Diffend](https://www.whitesourcesoftware.com/whitesource-diffend/) 来寻找我在各种项目中使用的包中的漏洞，无论是 Ruby 还是任何其他语言。该工具被许多著名的组织使用和信任，如微软、康卡斯特和诺基亚。它也是免费的，这很好。

![](img/6daa5534f56735b4e4adc851a60c503b.png)

特别是对于 Ruby，每当我添加一个新的 gem 或更新现有的 gem 时，我都会运行 WhiteSource Diffend 来扫描漏洞或恶意软件。

我使用 Diffend 发现了相当多的漏洞，因此能够在投入生产之前消除它们并找到替代方案。

WhiteSource Diffend 保护您免受各种攻击，如恶意接管、ATO 攻击、makefile 污染、[比特币挖矿](https://azure.microsoft.com/en-us/blog/how-azure-security-center-detects-a-bitcoin-mining-attack/)、意外注入等。

在更新您的应用程序中的 gem 之前，您可以浏览这些更改并决定是否要升级。

起初，我从免费版本开始，看看它有多有用。从那以后，我转向了付费版本，它提供了一种灵活的定价模式。

# 最后的话

你不能忽视开源在当今软件开发中的重要作用。但是，与此同时，您不应该忽视开源可能带来的漏洞。由于开源的采用正在飞速增长，所以在添加新的包或宝石或升级包时，你应该勤奋。

开发人员发现漏洞并不容易。然而，有各种各样的工具可以帮你完成这项工作。您可以通过在每次添加/升级软件包或 gem 时运行这些工具来降低风险。当漏洞扫描集成到 CI/CD 流程中时，您会获得最佳结果。
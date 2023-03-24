# 凭据填充😈🐝

> 原文：<https://levelup.gitconnected.com/credential-stuffing-ff58ee8c3320>

![](img/3c5304fb416a27a0582a42c324a995bd.png)

Max van den Oetelaar 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

凭据填充是对服务用户帐户的暴力攻击。不是一个具体的账户，而是很多。通常通过使用在其他黑客中找到的凭证。作为用户，你可以通过 haveibeenpawned.com 的[查看你的账户是否被入侵。很有可能是。让我们学习你能做什么！](https://haveibeenpwned.com/)

# 为什么你应该关心

*   凭据填充是“破坏的身份验证”的一部分，因此排在 **OWASP 前 10 名** ( [来源](https://owasp.org/www-project-top-ten/2017/A2_2017-Broken_Authentication))的第 2 位
*   2020 年:350 万美元的欺诈性支票提款和凭证填充([更多细节](https://www.zdnet.com/article/fbi-says-credential-stuffing-attacks-are-behind-some-recent-bank-hacks/))
*   2020 年:发现约 500，000 个 Zoom 用户凭证，凭证填充([来源](https://www.forbes.com/sites/daveywinder/2020/04/28/zoom-gets-stuffed-heres-how-hackers-got-hold-of-500000-passwords/))
*   Akamai 称，2020 年:“零售、旅游和酒店行业吸引了令人吃惊的 63%的凭据填充攻击”[。](https://www.akamai.com/us/en/resources/our-thinking/state-of-the-internet-report/global-state-of-the-internet-security-ddos-attack-reports.jsp)
*   2021 年:AVM (FritzBox)注册了许多凭证填充攻击([来源](https://t3n.de/news/avm-angriffe-router-fritzbox-1363050/))

# 凭证填充攻击是如何工作的？

1.  攻击者获得数百万人的有效凭证列表**，例如(用户名、密码)。[有许多泄密事件](https://haveibeenpwned.com/)。**
2.  攻击者**在大型服务(Gmail、脸书、Twitter、银行、Reddit……)上尝试它们**

就是这样。真的很猥琐。凭证填充不是针对你个人，而是同时针对很多人。但是，当你的银行账户在一天结束时是空的，这并不能帮助你，不是吗？

# 我如何防御凭据填充攻击？

作为用户，有两个完美的防御措施:

1.  强密码:好的密码可能与你想象的不一样。但是[很容易生成可记忆的强密码](https://medium.com/geekculture/what-is-a-secure-password-97263aeedea9)。
2.  **唯一密码**:不要在服务之间共享密码。从来没有。

为了方便地实现这两个功能，您应该使用密码管理器。第三点是使用多因素身份认证(MFA)。拥有至少一个第二因子(2FA)大有帮助。然而，攻击者最有可能知道哪个密码是正确的，并且能够破解第二个因素，例如通过 SIM 交换。

作为服务提供商，防御措施并不完美，而且更加复杂:

*   **速率限制**:如果一个 IP 进行了太多次无效密码尝试，减慢该 IP 的速度。比如让他们先[解个验证码](https://medium.com/plain-and-simple/captcha-500991bd90a3)。或者告诉他们接下来的 30 秒内不允许登录。请注意，这也可能阻止合法用户，例如在学校或其他更大的开放网络。
*   **Web 应用防火墙(WAF)** :此处声明全文:我在这里没有实践经验。我听说过“主动僵尸防御”，这可能对其他攻击场景也很有意思。然而，它当然也有检测所有攻击(误报)和只拦截攻击(误报)的问题。
*   **双因素身份验证(2FA)** :强制用户使用第二个因素会使凭据填充不太可能奏效，即使用户与易受攻击的服务共享密码，甚至密码很弱。
*   **单点登录(SSO)** :让另一个服务来处理认证，避免了所有这些麻烦。一个众所周知的变体是“社交登录”。这只是脸书、Twitter、Github 或 LinkedIn 等大型社交网站的单点登录。
*   **检查用户密码是否违规** : [已启用](https://haveibeenpwned.com/Passwords)允许您检查凭证组合是否违规。

也有一些具体的问题让 bot 开发者的生活变得更加艰难，例如，需要 JavaScript，阻止无头浏览器，或者阻止来自 AWS 的流量。Jarrod Overson 就此写了一篇非常好的文章:

[](https://jsoverson.medium.com/10-tips-to-stop-credential-stuffing-attacks-db249cac6428) [## 阻止凭据填充攻击的 10 个技巧

### 购买反自动化服务前应采取的 10 个步骤(+ 1 个额外提示)。

jsoverson.medium.com](https://jsoverson.medium.com/10-tips-to-stop-credential-stuffing-attacks-db249cac6428) 

# 下一步是什么？

在这个关于应用安全(AppSec)的系列文章中，我们已经解释了攻击者的一些技术😈以及防守队员的技术😇：

*   第 1 部分: [SQL 注入](https://medium.com/faun/sql-injections-e8bc9a14c95)😈🐝
*   第二部:[不要泄露秘密](/leaking-secrets-240a3484cb80)😇
*   第 3 部分:[跨站脚本(XSS)](/cross-site-scripting-xss-fd374ce71b2f) 😈🐝
*   第 4 部分:[密码哈希](/password-hashing-eb3b97684636)😇
*   第五部分: [ZIP 炸弹](https://medium.com/bugbountywriteup/zip-bombs-30337a1b0112)😈
*   第六部分:[验证码](https://medium.com/plain-and-simple/captcha-500991bd90a3)😇
*   第 7 部分:[电子邮件欺骗](https://medium.com/bugbountywriteup/email-spoofing-9da8d33406bf)😈
*   第 8 部分:[软件组成分析](https://medium.com/python-in-plain-english/software-composition-analysis-sca-7e573214a98e) (SCA)😇
*   第九部分: [XXE 袭击事件](https://medium.com/faun/xxe-attacks-750e91448e8f)😈🐝
*   第十部分:[有效的访问控制](/effective-access-control-331f883cb0ff)😇
*   第十一部分: [DOS via 十亿次大笑](https://medium.com/bugbountywriteup/dos-via-a-billion-laughs-9a79be96e139)😈
*   第十二部分:[全磁盘加密](https://medium.com/faun/full-disk-encryption-2090489f9760)😇
*   第 13 部分:[不安全的反序列化](https://medium.com/bugbountywriteup/insecure-deserialization-5c64e9943f0e)😈
*   第 14 部分:[码头工人安全](/docker-security-5f4df118948c)😇
*   第 15 部分:凭据填充😈🐝

这即将到来:

*   CSRF😈
*   磁盘操作系统😈
*   雷多斯😈
*   密码劫持😈
*   单点登录😇
*   双因素认证😇
*   备份😇

如果您对更多关于 AppSec / InfoSec 的文章感兴趣，请告诉我！
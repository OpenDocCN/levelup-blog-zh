# 在生产中使用 Express 应用程序的安全最佳实践—代码和依赖关系

> 原文：<https://levelup.gitconnected.com/more-security-best-practices-for-using-express-apps-in-production-198b628d3134>

![](img/961b1022e0280e9c7161f3ceeca99fca.png)

[Icons8 团队](https://unsplash.com/@icons8?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

当我们将 Express 应用程序用于生产用途时，即它由外部用户使用时，我们必须小心安全性，因为它对外部世界是可用的。

在本文中，我们将了解在生产环境中运行 Express 应用程序时的一些安全性最佳实践。

# 确保依赖性是安全的

当我们安装软件包时，我们可以通过使用 NPM 的自动安全检查来检查我们在应用程序中使用的节点软件包是否安全。

我们也可以使用`npm audit`进行手动安全检查。

这两个命令都检查我们使用的节点包的安全漏洞。

为了更加安全，我们可以使用 Snyk。它提供了一个命令行工具和 Github 集成，可以根据 Snyk 的一系列安全漏洞自动检查我们的库。

它以节点包的形式提供。为了安装它，我们运行`npm i -g snyk`。

我们可以使用`snyk test`来测试我们的应用程序的漏洞。

`snyk wizard`是一个命令行向导，引导我们应用更新来修复发现的漏洞。

# 避免其他已知的漏洞

我们可以查看[节点安全项目](https://nodesecurity.io/advisories)网站或 [Snyk](https://snyk.io/vuln/) 公告，它们可能会影响我们在应用中使用的 Express 和其他节点模块。

这些数据库是学习节点安全性的绝佳资源。

我们还必须意识到网络上普遍存在的网络漏洞。

# 跨站点请求伪造

跨站点请求伪造(CSRF)是一个漏洞，攻击者可以以我们的应用程序信任的用户身份向我们的应用程序发出未经授权的请求。

攻击者可以通过许多方式做到这一点，如特制的命令、图像标签、隐藏表单、JavaScript XMLHttpRequests 等。

我们可以使用 csurf 中间件来保护我们的应用免受 CSRF 攻击。它添加了一个`req.csrfToken()`函数，这样我们可以在处理请求时对照 CSRF 令牌进行检查，以确保请求确实来自我们信任的用户。

# 过滤和净化输入

我们应该过滤和净化输入，这样攻击者就不能在我们的系统中运行代码。

净化输入有助于这一点，因为在某些地方，带有特殊字符的字符串可以直接注入到代码中。

为了防止输入字符串包含属于某些恶意代码片段的特殊字符，我们应该对这些字符进行转义，这样它们就变成了更无害的字符。

# SQL 注入

防止 SQL 注入攻击是净化输入的另一个原因。我们不仅应该净化输入，还应该确保输入字符串不会直接注入 SQL 语句。

如果我们让字符串直接插入到 SQL 命令中，那么攻击者就可以随心所欲地运行。

我们绝对不希望这种情况发生，因为他们可能会运行命令来窃取数据、破坏数据、破坏我们的数据库等等。

为了防止 SQL 注入，我们应该使用参数化查询和预处理语句。

准备好的语句将在运行输入之前对其进行净化，这样我们就不必直接将值插入到 SQL 命令字符串中。

例如，如果我们在 Express 应用程序中使用 SQLite 作为数据库，我们可以编写如下代码:

```
app.post('/', (req, res) => {
    const { name, age } = req.body;
    db.serialize(() => {
        const stmt = db.prepare('INSERT INTO persons (name, age) VALUES (?, ?)');
        stmt.run(name, age);
        stmt.finalize();
        res.json(req.body);
    })
})
```

在这种情况下，`name`和`age`在准备好的语句之前被清理:

```
INSERT INTO persons (name, age) VALUES (?, ?)
```

就是跑。

`name`和`age`被净化，并在我们运行时替换问号:

```
stmt.run(name, age);
```

`db.prepare`是 SQLite3 包中的一个方法。其他数据库库也应该支持预准备语句。

我们可以使用 [sqlmap](http://sqlmap.org/) 工具来检测我们应用程序中的 SQL 注入漏洞。

![](img/4be96bd8f9dbe5dfd2dd3aadb4e731f7.png)

弗兰克·王在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 测试 TLS 安全性

我们应该使用 [nmap](https://nmap.org/) 和 [sslyze](https://github.com/nabla-c0d3/sslyze) 工具来测试我们的 SSL /TLS 密码、密钥、重新协商和证书验证的配置。

nmap 是一个用于网络发现和安全审计的工具。它进行各种扫描来检查我们系统中的漏洞。

sslyze 专门用于 SSL/TLS 扫描，以检查 SSL/TLS 设置的配置问题。

# 检查正则表达式拒绝服务攻击

正则表达式拒绝服务攻击(ReDoS)是一种利用以下事实的攻击，即大多数正则表达式实现可能会达到极端情况，使它们的工作速度非常慢。

攻击者可以发送输入，使我们的应用程序进入这些极端情况，并使我们的应用程序挂起。

具有各种重复的分组是易受此类漏洞攻击的正则表达式的例子。

我们可以使用 [safe-regex](https://www.npmjs.com/package/safe-regex) 来检查我们的正则表达式不容易受到(ReDos)攻击。

# 结论

当我们将 Express 应用程序投入生产环境时，需要考虑的不仅仅是让我们的应用程序正常工作。

我们必须意识到 SQL 注入攻击和其他代码注入攻击，这样我们的应用程序就不会运行恶意代码。为了做到这一点，我们净化输入，并作为准备好的语句运行 SQL 查询。

此外，我们必须意识到 SSL/TLS 的安全性，并通过使用各种实用程序检查我们的服务器配置来检查我们的服务器配置是否安全。

我们的应用程序的依赖性应该检查漏洞，以便我们可以更新它们，以避免安全问题。

最后，我们应该确保我们使用的正则表达式不容易受到(ReDoS)攻击，这可能会导致我们的应用程序崩溃。
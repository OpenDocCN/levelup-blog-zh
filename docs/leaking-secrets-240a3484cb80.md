# 泄露秘密

> 原文：<https://levelup.gitconnected.com/leaking-secrets-240a3484cb80>

## 它是如何发生的，你如何预防它

![](img/319574ce643f386772e5cf6fab3139be.png)

图片来自 [Pixabay](https://pixabay.com/de/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5137228) 的[图米苏](https://pixabay.com/de/users/tumisu-148124/)

在应用程序安全性方面，人们可能犯的最严重的错误之一就是公开发布秘密。这可以是 API 密钥、数据库凭证、服务令牌或用于非对称加密(如用于 GPG 的 RSA)的私钥。

最好完全防止凭据泄露，但是如果泄露了，您需要直接更改它们。你不能指望没人注意到它。人们在公共资料库中搜寻承诺的秘密。

# 为什么它很重要

泄露机密和凭证的情况比人们想象的要频繁得多。我有点惊讶，我们在新闻报道中并不经常看到这种情况，但肯定有一些黑客攻击可以追溯到泄露的秘密:

*   **2017** :优步向获得 5700 万客户个人数据的黑客支付了 10 万美元。
*   **2019** :研究人员在 GitHub 上发现超过 20 万个独特的秘密。他们在“[中描述了他们的方法和发现，它能坏到什么程度？描述公共 GitHub 库的秘密泄露](https://www.ndss-symposium.org/wp-content/uploads/2019/02/ndss2019_04B-3_Meli_paper.pdf)
*   **2020** :戴姆勒内部 Gitlab 对外开放([来源](https://www.zdnet.com/article/mercedes-benz-onboard-logic-unit-olu-source-code-leaks-online/))。如果在任何存储库中有任何凭证，它们现在也是公开的。这就是为什么[纵深防御](https://en.wikipedia.org/wiki/Defence_in_depth)有意义。不要把你的秘密储存在储存库中，即使这个储存库是私有的。

# 泄密是如何发生的

这是知识缺失、懒惰和人为错误的混合物。如果人们不知道如何正确地储存秘密，他们只是以他们知道的方式储存。即使人们知道如何做好它，但直接复制存储库中的秘密要简单得多。当然，添加不打算添加的东西也会发生。

向公共库添加秘密是一个人可能犯的最明显的错误。**向日志消息中添加秘密**更加间接，应该不会立即引起问题。但是，它可以允许多步攻击。由于这个原因，“深度防御”是有意义的，因此秘密不应该是日志消息的一部分。攻击者甚至不需要直接访问日志文件。也许开发人员会公开分享部分日志来调查某个问题。问题是，人们并不倾向于将日志视为软件领域的一个安全关键部分。

# 怎样才能防止泄密？

首先，你需要确保人们不再认为有必要以不安全的方式使用秘密。然后你可以通过**预提交**钩子来增加提交秘密的难度。最后，您**检查服务器端**何时添加了秘密。

## 记录

要么根本不记录环境变量，要么确保里面没有秘密。您还可以将模式列入黑名单，如 Joe Crobak 在他的文章“[将敏感数据排除在日志之外的七个最佳实践](https://medium.com/@joecrobak/seven-best-practices-for-keeping-sensitive-data-out-of-logs-3d7bbd12904)”中所示。

## 在本地存储机密:Direnv

`[direnv](https://direnv.net/)`是一个 shell 扩展，如果您在文件夹或子文件夹中，它可以让您的 shell 执行一个`.envrc`文件。这样的文件可能如下所示:

```
export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfYEXAMPLEKEY
export AWS_DEFAULT_REGION=us-west-2
```

通过将`.envrc`文件添加到`.gitignore`文件中，确保该文件是私有的。

人们在代码中使用秘密的地方，现在可以使用环境变量。

将秘密存储在环境变量中并不是最佳选择，因为每个进程都可以很容易地访问它们。但是，这比将它们存储在其他系统也可以访问的文件中要好。在最坏的情况下甚至连到公共互联网。

## 提交前挂钩

`[pre-commit](https://pre-commit.com/)`是一个帮助你应用 git 钩子的应用程序。这些是在代码被添加到 git 存储库之前执行的。

您可以通过创建下面的`.pre-commit-config.yaml`文件来确保没有 AWS 凭证或私有密钥被添加到存储库中:

```
repos:
-   repo: [https://github.com/pre-commit/pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks)
    rev: v3.2.0
    hooks:
    -   id: detect-aws-credentials
    -   id: detect-private-key
-   repo: git@github.com:Yelp/detect-secrets
    rev: v0.14.3
    hooks:
    -   id: detect-secrets
        args: ['--baseline', '.secrets.baseline']
        exclude: .*/tests/.*
```

然后执行`pre-commit install`就大功告成了🙂

yelps[**detect-secrets**](https://github.com/Yelp/detect-secrets)试图通过寻找高熵字符串来寻找源代码中的秘密，而其他人则寻找常见的文件格式/字符串。

通过预提交，你可以做[许多其他很酷的事情](https://towardsdatascience.com/pre-commit-hooks-you-must-know-ff247f5feb7e)。

## 存储机密服务器端:环境变量

有许多 secrets vault 解决方案，包括源代码托管提供商的解决方案:

*   [AWS SSM](https://docs.aws.amazon.com/systems-manager/latest/userguide/what-is-systems-manager.html)
*   [天蓝色钥匙金库](https://azure.microsoft.com/de-de/services/key-vault/)
*   [GitlabCI 环境变量](https://docs.gitlab.com/ee/ci/variables/)和[Hashi Corp Vault Server for Secrets](https://docs.gitlab.com/ee/ci/secrets/)
*   [GitHub](https://docs.github.com/en/free-pro-team@latest/actions/reference/encrypted-secrets) :加密的秘密
*   [位桶](https://support.atlassian.com/bitbucket-cloud/docs/variables-and-secrets/):安全变量

## 存储机密服务器端:保管库

通过环境变量提供秘密有两个主要缺点:(1)每个进程都可以访问它们(2)开发人员可能希望记录环境变量，从而将秘密泄露到日志中。

拥有一个专门的秘密存储库，并且只在必要的时候获取秘密是解决这个问题的一个方法。

AWS SSM 是一种非常常见的解决方案。下面是如何在 Python 中使用它:

```
**import** boto3**def** get_ssm_param(name: str) -> str:
    client = boto3.client("ssm")
    param = client.get_parameter(Name=name, WithDecryption=True)
    return param["Parameter"]["Value"]
```

## 源主机秘密检测

大多数资源托管服务提供了检查泄露秘密的可能性。 [GitLab](https://docs.gitlab.com/ee/user/application_security/secret_detection/) 称之为“秘密检测”， [GitHub](https://docs.github.com/en/free-pro-team@latest/github/administering-a-repository/about-secret-scanning) 称之为“秘密扫描”， [GitGuardian](https://www.gitguardian.com/) 提供秘密检测&补救。

人们可以将 Yelps `[secret-detection](https://github.com/Yelp/detect-secrets)`集成到 CI 管道中。对于 Python，SAST 工具 bandit 也可以集成到 CI 管道中。它提供[基本秘密检测](https://bandit.readthedocs.io/en/latest/plugins/b105_hardcoded_password_string.html)。请记住:如果 CI 管道因为发现秘密而失败，您必须更改该秘密。

# 测试过去

确保新的提交是安全的是好的，但是您还需要知道在引入安全性改进之前，过去是否发生过事故。有两个工具可以帮助您:

[**GitLeaks**](https://github.com/zricethezav/gitleaks) 扫描你的整个储存库寻找泄露的秘密。这包括已提交和删除但仍在提交历史记录中的凭据。

下面是在 Linux 上安装它的方法:

```
# You can also go to 
# [https://github.com/zricethezav/gitleaks/releases](https://github.com/zricethezav/gitleaks/releases)
# and download the version you need in the browser
$ wget [https://github.com/zricethezav/gitleaks/releases/download/v6.1.2/gitleaks-linux-amd64](https://github.com/zricethezav/gitleaks/releases/download/v6.1.2/gitleaks-linux-amd64)$ **mv** gitleaks-linux-amd64 gitleaks
$ **chmod** +x gitleaks
$ sudo **mv** gitleaks /usr/local/bin/$ **cd** your-repo
$ **gitleaks** --repo=. -v
INFO[2020-10-13T17:38:49+02:00] cloning... .
Enumerating objects: 115, done.
Counting objects: 100% (115/115), done.
Compressing objects: 100% (42/42), done.
Total 115 (delta 68), reused 115 (delta 68)
INFO[2020-10-13T17:38:49+02:00] **No leaks detected**. 29 commits scanned in 111 milliseconds 984 microseconds
```

Keyhacks 是一个向你展示泄露的密钥是否仍然有效以及攻击者可以用它们做什么的项目。

[**havebeenpwned**](https://haveibeenpwned.com/)对你的私人账号有意思。您可以注册，如果您的电子邮件出现数据泄露，您将收到一封电子邮件。这种事经常发生😱为此:**不要重复使用密码！重复使用的密码是泄漏的密码！**

# 关于环境变量的一个注记

环境变量远非无懈可击。几个恶意的第三方
包简单地将带有环境变量的主机名发送到服务器([源](https://github.com/rsc-dev/pypi_malware#malware-packages))。

# 下一步是什么？

在这个关于应用安全(AppSec)的系列文章中，我们已经解释了攻击者的一些技术😈以及防守队员的技术😇：

*   第 1 部分: [SQL 注入](https://medium.com/faun/sql-injections-e8bc9a14c95)😈
*   Part 2: [不要泄密](/leaking-secrets-240a3484cb80)😇
*   第 3 部分:[跨站点脚本(XSS)](/cross-site-scripting-xss-fd374ce71b2f) 😈
*   第四部分:[密码哈希](/password-hashing-eb3b97684636)😇
*   第五部分: [ZIP 炸弹](https://medium.com/bugbountywriteup/zip-bombs-30337a1b0112)😈
*   第六部分:[验证码](https://medium.com/plain-and-simple/captcha-500991bd90a3)😇
*   第七部分:[电子邮件欺骗](https://medium.com/bugbountywriteup/email-spoofing-9da8d33406bf)😈
*   第八部分:[软件组成分析](https://medium.com/python-in-plain-english/software-composition-analysis-sca-7e573214a98e) (SCA)😇

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
# Log4Shell: Lua + Nginx 缓解

> 原文：<https://levelup.gitconnected.com/log4shell-lua-nginx-mitigation-52b1072c19be>

防止请求到达易受攻击的 java 应用程序[ [CONFIG](https://gist.github.com/johnhpatton/fd43a9cae9301693c8e39f4c42e9e08e) ]

代码更新于 2021 年 12 月 15 日 17:30(美国中部时间)

![](img/949f1c42707258f6caa9cd50398a4580.png)

由[凯尔·格伦](https://unsplash.com/@kylejglenn?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 更新

> 额外的模式被添加到 lua 代码中。

# CVE-2021–44228

如果您是任何使用 log4j 的 java 后端系统的系统管理员，这个 CVE 可能会毁了您的周五夜晚。以下是一些细节:

*   [https://logging.apache.org/log4j/2.x/security.html](https://logging.apache.org/log4j/2.x/security.html)
*   [https://issues.apache.org/jira/browse/LOG4J2-3201](https://issues.apache.org/jira/browse/LOG4J2-3201)
*   [https://nvd.nist.gov/vuln/detail/CVE-2021-44228](https://nvd.nist.gov/vuln/detail/CVE-2021-44228)

阻止不良的流量模式是赢得一些时间使更新的库就位的一个快速的方法。如果您将 nginx 作为受影响的 java 应用程序的反向代理运行，这很容易被这种方法阻止。

> 注意:**应该检查整个有效负载:**第一行请求、头部和主体。请求的任何部分都有可能被记录到易受攻击的 java 上游。

# 要求

为了使该解决方案能够工作，web 主机需要以下软件:

*   Nginx、Nginx-Plus 或 Openresty
*   Luajit 5.1 和所有 nginx lua 依赖项(如果没有安装的话)

# Lua 代码

确保 nginx 阻塞所有包含 log4shell 有效负载的请求的最简单方法是在重写阶段检查整个请求头和正文有效负载。这需要将第 1 行、请求头和请求体提取到一个可以检查的变量中。

在每个面向公众的服务器块中，以下代码将检测并阻止情感模式。

> 注意:这将更新一个 nginx 变量， **$cve_2021_44228_log** ，该变量用于日志记录，必须在调用 block 函数的服务器块中设置。

## 细节

这种攻击的主要手段是发送以下示例模式:

> $ { JNDI:[协议]://[攻击者站点]/a}

实现这一点的部分是开头的" **${jndi:** "，因此在请求体或头中查找任何匹配的子串都将捕捉到这一模式。“**【协议】**”可以是 ldap、ldaps、rmi、dns 和各种其他协议，但这并不是缓解所必需的。此外，变量可用于混淆模式，如下所示:

> $ { $ { lower:j } ndi:[协议]://[攻击者站点]/a}

甚至是这个:

> $ { $ {::-j } $ {::-n } $ {::-d } $ {::-I }:[协议]://[攻击者站点]/a}

为了确保不会遗漏，必须检查整个请求有效负载，包括第 1 行、请求头和请求体(如果存在的话)。

为了尽可能简单地捕捉这一点，第一次检测以非递归方式检查请求有效负载中的“ **${jndi:** ”，以捕捉最简单的模式。如果匹配，请求将被拒绝。

第二次检测由分成两部分的递归匹配组成。第一部分捕获在有效负载中找到的任何“${}”组的内容。例如，外层的${ }将被剥离，如果找到的话，以得到这个:

> $ {::-j } $ {::-n } $ {::-d } $ {::-I }:[协议]://[攻击者站点]/a

然后对照两个模式检查第二部分，这两个模式将匹配攻击字符串中任何可能的混淆方法。如果匹配，请求将被拒绝。

# 完全解

修改您的配置，使用上面的 lua 模块将 nginx-plus 示例添加到/etc/nginx 下的默认配置中。也将与 openresty 或支持 lua 的开源 nginx 一起使用。

> 注意:一个日志变量 **$cve_2021_44228_log** 被添加到默认访问日志中，如果需要，可以在配置中使用。这个变量需要在使用主 log_format 并调用 lua 块函数的服务器块中设置。

## Nginx 主配置

与 nginx 一起安装的缺省 nginx.conf 相比，唯一的调整是 load_module 指令和添加到 log_format 指令中的 log 变量。

## 包括:Lua 配置

下面将启用 lua 配置，并创建一个 lua 模块实例，供请求处理器使用:

## 包括:默认配置

nginx 对 default.conf 的示例调整，调用 lua 模块中的 block 函数，删除了额外的注释指令:

# 祝你好运！

我希望这有所帮助。甘巴特！
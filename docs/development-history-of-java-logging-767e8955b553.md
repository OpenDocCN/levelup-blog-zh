# Java 日志记录的发展历史

> 原文：<https://levelup.gitconnected.com/development-history-of-java-logging-767e8955b553>

现在有这么多 Java 日志框架。它们之间是什么关系，我们应该如何使用它们？

![](img/146acf61b1a8c8928b0ce0da02c042ce.png)

日志对于 Java 开发者来说太熟悉了，但是相信很多人都有这样的疑惑。Java 中有几个日志框架，比如 Log4j、Logback、JCL 和 slf4j。这些日志框架之间有什么区别和联系？我们应该如何使用它们？本文将着眼于 Java 世界日志系统的开发。

## 原始阶段

在早期，没有日志框架。大家用最原始最粗糙的方式记录日志。如`System.out.println`、`System.err.println`。虽然这种原始方法也可以解决基本的日志记录问题，但是这种方法有许多缺点。

*   系统的标准输出是同步的。我们可以看到`println()`的源代码。

我们可以看到源代码使用了 synchronization 关键字，对象锁是`PrintStream`。如果在高并发环境下输出大量日志，会导致大量应用线程因日志而被阻塞，严重影响系统性能。

*   日志粒度太粗，只有信息和错误级别。
*   无法根据环境确定是否输出日志。

## Log4j 诞生了

出于这些原因，ceki Gulcü在 2001 年开发了 log4j。Log4j 一出现就赢得了开发者的青睐，后来成为 Apache 基金会的顶级项目。Log4j 是 Java 日志框架发展的里程碑。提出了`Appender`、`Level`、`Logger`等概念，为测井框架奠定了基础。到目前为止，log4j 仍然是许多开发人员首选的日志框架。Apache 曾经建议 sun 团队将 log4j 作为 JDK 的首选日志框架，但是 sun 直接拒绝了(我猜是嫉妒)。

## 七月

后来 Sun 公司借鉴 log4j 开发了自己的日志标准库 JUL (Java util logging)。但由于 API 不完善，对开发者不友好，开发者需要自己编写 Appenders(被 sun 称为处理程序)，只有两个处理程序可用(控制台和文件)，很少使用。Log4j 仍然是 Java 开发者的首选。但毕竟 JUC 是一个官方的日志框架，仍然被一些人使用。这时候，就出现了一个问题。系统中存在两种不同的日志框架。应该用哪个？

## 作业控制语言

为了统一日志标准，Apache 推出了 JCL (Jakarta common logging)，这是一个日志门面接口，避免了系统与具体日志框架的直接耦合，类似于 JDBC 接口。将来，业务应用程序只需要使用 JCL 接口来打印日志。JCL 可以动态地找到当前使用的日志框架，动态地决定是使用 log4j 还是 JUL，如果不是，就使用默认的`SimpleLog`。但是 JCL 对日志框架的配置兼容性很差，而且由于 JCL 是通过定制类加载器来判断并加载类路径下的日志框架，所以很容易造成内存泄漏。

## Slf4j

基于以上问题，log4j 的作者 ceki Gulcü开发了新的日志标准接口规范 slf4j(Java 的简单日志门面)，后来又开发了 logback 日志框架。为了方便开发者统一使用 slf4j，他还开发了各种桥接 SDK。到目前为止，无论您的系统使用多少日志框架，您都可以桥接到 slf4j，最终，您可以使用统一的日志框架。

## Java 日志记录最佳实践

这么多日志框架，用哪个？答案不是依赖特定的日志实现而是使用 facade 日志接口 slf4j，强烈建议使用 slf4j+logback。为了与其他日志框架兼容，您可以使用 slf4j 的桥接库通过 logback 打印日志。Maven 依赖于以下内容。

终于，Java 日志记录的历史结束了。本文还给出 Java 日志记录的最佳实践。希望这篇文章能帮助你了解 Java 日志系统。感谢您的阅读。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)
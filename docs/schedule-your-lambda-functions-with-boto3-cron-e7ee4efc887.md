# 用 boto3 (CRON)调度你的 Lambda 函数

> 原文：<https://levelup.gitconnected.com/schedule-your-lambda-functions-with-boto3-cron-e7ee4efc887>

这是一个关于如何使用 Python 和 boto3 以编程方式安排 AWS Lambda 函数执行的快速教程。如果您想在部署功能后添加/删除规则，例如，基于用户的输入，这将非常有用。

如果你只需要设置一次时间表，看看[棒极了的无服务器工具](https://www.serverless.com/framework/docs/providers/aws/events/schedule/)吧。

![](img/5ba522a52ed34dabf41edd5d0595418a.png)

由 [Eric Prouzet](https://unsplash.com/@eprouzet?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

[在 textcloud，](https://www.textcloud.co)我们在 AWS Lambda 的基础上构建大部分后端。这使我们能够为用户提供快速而强大的工作流自动化，无论他们是每月运行一次简单的工作流，还是需要每五分钟触发一次复杂的抓取和机器学习作业。

今天，我们包含了一个调度特性，可以在某个时间间隔触发一个工作流。例如，如果您希望每天通过电子邮件从 UpWork 获取一次最新的相关工作邀请。

# 清楚明白的细节

## 目标:EventBridge 规则→ Lambda 调用

我们可以用 [AWS EventBridge](https://us-east-1.console.aws.amazon.com/events/home?region=us-east-1#/rules) 规则轻松调度 Lambda 函数。每个规则接收一个 CRON 格式或使用“rate()”语法的时间表。一个规则可以有多个目标，这些是您希望在规则激活时触发的资源。在我们的例子中，每个规则应该触发一个 Lambda 函数。

很简单，对吧？

# 问题是:规则无声无息地失效了

一开始这一切看起来很容易。我们已经向 EventBridge 添加了一个规则，并创建了一个目标，向 Lambda 函数的 ARN 发送输入。

但是后来:什么都没有。

函数的日志是空的，每个规则调用似乎都失败了。但是为什么呢？

## 解决方案的第一部分:权限

这听起来很像是权限问题。可能 EventBridge 不允许调用 Lambda 函数吧！

让我们[为 Lambda 创建一个角色](https://console.aws.amazon.com/iam/home#/roles)，并赋予它‘CloudWatchEventsFullAccess’和‘AWS Lambda _ full access’权限。请记住定期检查访问顾问，看看哪些权限是不需要的！

现在我们需要向角色添加另一个受信任的实体:'[events.amazonaws.com](http://events.amazonaws.com)'。信任策略应该如下所示:

我们复制角色 ARN，并将其添加到我们的 Python 代码中:

现在看起来相当不错！让我们删除旧规则并再次添加它，并继续观察我们的 Lambda 函数的日志。

![](img/6b196ebb87063e18de881dc5bf7d2d97.png)

没什么！这怎么可能呢？我们已经觉得自己像 AWS 奇才了！

## 解决方案的第二部分:将其添加到 Lambda 中

我给你省下了翻文档、咒骂、哭泣和阅读教程的时间。

结果是我们需要明确地告诉我们的 Lambda 函数，EventBridge 规则被允许触发它，即使它对 Lambda 有完全的访问权限！

这应该是这样的:

而且很管用！

# 懒人的完整代码

就是这样！

编码快乐！或者如果 AWS 已经让你落泪，[看看 textcloud，看看工作流自动化+自然语言处理](https://www.textcloud.co)如何通过自动化复杂的工作来帮助你的公司节省时间和金钱:)
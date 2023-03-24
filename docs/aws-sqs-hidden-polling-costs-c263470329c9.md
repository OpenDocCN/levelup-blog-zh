# AWS SQS 隐藏投票成本

> 原文：<https://levelup.gitconnected.com/aws-sqs-hidden-polling-costs-c263470329c9>

对 Lambda 使用队列

![](img/a29de95ea6ae1a9ddf202f7841413b4c.png)

安东·达利斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在构建事件驱动的应用程序时，处理上传到 S3 桶的对象是很常见的。有几种方法可以使用 S3 通知收听 S3 事件。你可以在每次上传后直接调用 lambda 函数，或者将通知发送到 SNS 主题/SQS 队列，作为两者之间的中介。

对于处理错误的高性能、可扩展的解决方案，SQS 是最佳选择。

Lambda 每秒有 1000 个并发调用的限制。

[*SNS 不保证每个通知只发送一次。*](https://aws.amazon.com/sns/faqs/)

[*如果交付出现错误，SQS 允许您根据您的重新驱动政策重试向消费者交付(例如 Lambda)。如果重试次数用尽，那么您可以使用死信队列(DLQ)单独处理错误。*](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html)

设置很简单。

AWS 每月免费给你一百万条消息。之后，每百万条信息只需 0.40 美元，所以成本很低。

Lambda 不断轮询队列，监听队列中的新消息，这就带来了问题。如果没有空消息，这种轮询会产生空消息。这些消息计入 AWS 空闲层请求。Lambda 函数将有五个实例每 20 秒轮询一次。

## 每分钟 15 条信息。

## 每小时 900。

## 每天 21600。

## 每个月 64.8 万。

# 这已经是免费层的一半以上。

如果你有两个队列，那么你已经开始为 SQS 付费了。还好它相当便宜。

正如我之前提到的，您可以直接从 S3 通知中调用 Lambda 函数。如果您知道负载不会很多，您可以安全地使用此选项。

有一个好的，并继续建设真棒的东西🎉

[](https://blog.jagonzalr.com/membership) [## 加入我的介绍链接媒体-何塞安东尼奥冈萨雷斯罗德里格斯

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

blog.jagonzalr.com](https://blog.jagonzalr.com/membership)
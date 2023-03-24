# 如何成为一名敏捷开发者？

> 原文：<https://levelup.gitconnected.com/5-great-ways-to-improve-your-development-schedule-510a5816f9c9>

## 关于组织 sprint 的故事，以及如何从开发人员的角度改进它

![](img/e88bcea500909507028ef42cacc6d8c5.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[卡斯滕·沃思](https://unsplash.com/@karsten_wuerth?utm_source=medium&utm_medium=referral)拍摄的照片

到目前为止，我和几个团队一起工作，我的冲刺组织很差。这会导致延迟、头痛和糟糕的软件。

我以前的雇主甚至没有敏捷结构。我们靠自己做事。没有业务分析，搞清楚业务需要什么。这是你的报酬，这是口头禅。几个月后离开了那份工作。

> 你也是这种情况吗？

真的吗？看看这个故事吧。现在学习，将来预防。每个软件开发人员都有这个问题，并处理其后果。

不做进一步介绍，直接说故事本身。

## 防止范围蔓延

到目前为止，我已经完成了 150 多张 JIRA 门票。其中大约有 15%存在某种范围蔓延。

假设您正在修复登录表单上的验证。几天后，一位业务分析师或企业希望对另一个表单进行同样的操作。他们更新票证的范围。这是范围蔓延。

你需要阻止范围蔓延。添加备注，说明这些更改需要单独的票证。估计新票，继续自己的票。

范围蔓延会影响你的健康。你正在做一些单位的工作，但是工作一直在堆积。你永远不会结束，因为罚单还在增加。

如果范围蠕变不变，则增加估计值。始终将意外情况添加到机票评估中。这个缓冲区将有助于处理票证时出现的错误，或者解决问题的新方法。

## 总是争论评估

> “软件中有一个基本规则，那就是所有事情都要比你想象的时间长 3 倍，即使你知道这一点并考虑到这一点。”
> —罗伯特·马丁

评估从来都不是一成不变的。你永远不知道会发生什么。这就是为什么你应该总是争论估计。永远不要满足于最低的数字。

例如，我们有一个报告特性。您需要发送一份报告，并将其保存在 S3 存储桶中。您已经有了框架。有一个类似的特征，所以估计这个特征是容易的。

> 估计正确吗？不对。

方法改变。您不需要 S3 存储桶，您需要将文件附加到附件中。您没有将文件添加到附件中的逻辑，这需要额外的工作。

这种事发生在我身上，想想会发生在你身上。绝不同意以下观点:

> "这份报告票就像前一份，因此我们可以降低估计."

即使是相似的特征也可以有不同的方法。我的报告单的要求有所改变。条件可能会有所不同，测试时间会长得多。

> 当我落后于计划时，我感到注定要失败，沮丧，没有动力。当我比计划提前*工作的时候，我很开心，也很有效率。—乔尔·斯波尔斯基*

## 问题作业

我没有问任何关于报告票的问题。这就是我陷入棘手境地的原因。

询问关于需求的问题，即使是愚蠢的问题也能揭示领域。问一下否定，这会说你不需要做什么。团队成员可能知道这是常见的逻辑，或者帮助你解决这个问题。

通过提问来减少工作量，将会改善你的时间表。

## 分解特征

将你的功能分解成易于管理的部分。为每件事设定一个期限，然后开始工作。你可以看看我是如何进行特征分解的:

[](https://medium.com/dev-genius/what-no-one-tells-you-about-simple-decent-personal-journal-4a9de184696a) [## 没人告诉你的简单体面的个人日记

### 为什么你应该有一个稳定的个人发展日志

medium.com](https://medium.com/dev-genius/what-no-one-tells-you-about-simple-decent-personal-journal-4a9de184696a) 

你需要为每项任务设定一个截止日期。这将防止[帕金森定律](https://en.wikipedia.org/wiki/Parkinson%27s_law)的发生。霍夫施塔特定律将永远有效，所以你需要先解决你的最后期限。

> "工作不断扩大，以填补完成工作的时间."—帕金森定律
> 
> “它总是比你想象的要长，即使你考虑到霍夫施塔特定律”——霍夫施塔特定律

分解罚单，设定截止日期。如果截止日期超过了 sprint 截止日期，请求分离票。

## 遵循 YAGNI 原则

> 简单——最大化未完成工作数量
> 的艺术——至关重要。— [敏捷宣言](http://agilemanifesto.org/principles.html)

你必须避免过度设计。这会打乱你的开发进度。这导致了浪费，并没有为客户带来实际价值。

不需要优化那么多。看真实的用例，如果没有必要做，就避开。想想看，如果有性能问题，企业会提出来。

## 资源:

[](https://www.joelonsoftware.com/2007/10/26/evidence-based-scheduling/) [## 基于证据的调度

### 软件开发人员不太喜欢制定时间表。通常情况下，他们会试图逃脱。“这件事会在……的时候完成

www.joelonsoftware.com](https://www.joelonsoftware.com/2007/10/26/evidence-based-scheduling/) [](https://softwareengineering.stackexchange.com/questions/37339/choosing-between-two-programmers-experience-vs-passion/37397#37397) [## 在两个程序员之间选择:经验还是激情

### 我在一个位置，我必须雇用一名程序员，并有 2 个候选人的选择，第一个有经验，但…

softwareengineering.stackexchange.com](https://softwareengineering.stackexchange.com/questions/37339/choosing-between-two-programmers-experience-vs-passion/37397#37397) [](https://softwareengineering.stackexchange.com/questions/263589/how-to-break-up-a-programming-project-into-tasks-for-other-developers/263665#263665) [## 如何将一个编程项目分解成任务给其他开发者？

### 我建议如下:首先，使用 Git(或类似的并发版本控制系统)。只要你在编辑…

softwareengineering.stackexchange.com](https://softwareengineering.stackexchange.com/questions/263589/how-to-break-up-a-programming-project-into-tasks-for-other-developers/263665#263665) [](https://plswiderski.medium.com/effective-estimation-review-of-uncle-bobs-presentation-a2150f5f68ac) [## 有效的评估——鲍勃叔叔演讲回顾

### Robert C. Martin 在他关于估算和根据软件估算的鼓舞人心的演讲中解释了什么是…

plswiderski.medium.com](https://plswiderski.medium.com/effective-estimation-review-of-uncle-bobs-presentation-a2150f5f68ac) [](https://www.leadingagile.com/2019/03/maximizing-the-amount-of-work-not-done/) [## 最大化未完成的工作量|引导图表字段注释

### 价值流由向客户交付产品或服务的所有必要步骤组成，从…

www.leadingagile.com](https://www.leadingagile.com/2019/03/maximizing-the-amount-of-work-not-done/)  [## 敏捷宣言背后的原则

### 我们遵循这些原则:工作软件是进步的主要衡量标准。持续关注技术…

agilemanifesto.org](http://agilemanifesto.org/principles.html)
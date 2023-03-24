# 解决正确问题的 3 种编码实践

> 原文：<https://levelup.gitconnected.com/3-coding-practices-for-solving-the-right-problem-526b188241a2>

解决错误的问题往往比构建错误的解决方案代价更高。

![](img/25044ce0e8aeef7e038ec567a91bb052.png)

[尤里察·科莱蒂](https://unsplash.com/@juricakoletic?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

作为一名软件开发人员，我是一个天生的问题解决者。我看到一个问题，立即开始在脑子里思考如何解决它。然而，在没有看到全局的情况下一头扎进解决方案通常会适得其反。

在本文中，我们将看看三个软件开发实践，它们可以帮助我们找到正确的问题来解决。但是在我们谈论编码之前，让我们先从产品管理中吸取一页——这个职业专门寻找问题。

# 什么是问题空间

产品开发过程包括两个阶段——问题空间和解决方案空间。问题空间是您尝试确定客户需求的地方。解决方案空间是解决这一需求的产品。在问题空间工作包括倾听客户，捕捉他们的需求，并提出问题来描述他们的情况。

> 一个简单的例子是，当你意识到你的同事今年没有休假时，你决定自己去了解更多关于人们为什么不休假的信息。你可以开始采访你的同事，问一些问题:
> 
> 你知道你今年还有多少带薪休假吗？有人告诉你不要向经理请假吗？你旷工的时候有可以依靠的队友吗？

你收集的观察和事实成为你问题空间的边界。它们构成了您满足客户需求的假设的基础。

# 留在问题空间

在问题空间投入时间的明显好处是，你会更好地了解问题是什么，并制定相关的解决方案。软件开发人员通常不会在问题空间花太多时间，因为我们经常被告知要解决问题，而不是寻找问题。

下面是我最喜欢的三个编码实践，让我在问题空间中立足。

## 领域驱动设计

领域驱动设计的哲学是，如果你的代码的语言和结构与你的业务领域相匹配，你可以更有效地管理你的软件的复杂性和可维护性。

> 例如，如果我要构建一个软件产品来跟踪员工全年的带薪休假余额，我将在事件源架构之后建模我的软件，该架构跟踪所有调整员工带薪休假余额的事件。我将这些事件命名为*应计*、*重置*和*休假*，以匹配实际的人力资源术语。
> 
> 将这些业务领域的概念写在代码中使我更容易与人力资源领域的专家合作开发算法来聚集和分析员工的假期数据。

与领域专家用同样的语言思考和交流的能力降低了你进入他们的问题空间的障碍，增加了你识别正确问题的几率。

## 用户故事

用户故事是从最终用户的角度描述软件特性的工作单元。比起传统的业务需求，我更喜欢阅读用户故事，因为它们抓住了我们为最终用户解决的问题。

业务需求或系统规范可能是这样的:

```
System should have functionality to calculate employee paid-time-off balance based on a given date range.
```

这种说法没有错；然而，它并没有告诉你**为什么**或者**你为谁**构建这个特性。您可以以用户故事的形式重写相同的需求:

```
As a employee, I would like to know my paid-time-off balance given a date range, so that I can plan for my vacation.
```

这个用户故事告诉你谁会使用这个特性以及他们的动机。你可以感同身受这个人的愿望，为他们即将到来的假期做计划，并根据他们的情况实施解决方案。

## 测试驱动开发

当您实践测试驱动开发时，您总是在添加更多代码之前编写一个测试用例。编写测试用例的过程给了你在沉浸于实现细节之前彻底探索问题空间所需要的停顿。

以下面的测试案例为例:

```
describe(PTOCalculator, () => {
  describe('annual accrual policy', () => {
    it('accrues on the first day of the year', () => {
      const options = {
        startDate: new Date(2019, 0, 1),
        endDate: new Date(2019, 11, 31),
        accruedAmount: 14,
        period: 'annually',
        accrualDate: 1,
      }; expect(new PTOCalculator(options).balance()).toEqual(14);
    });
  });
});
```

这个测试根据类和变量在现实世界中的对应物来命名它们。测试描述读起来就像你在和一个终端用户说话。编写一个合适的测试，你会很快发现一个适合你的问题的定制解决方案。

# 超越你的角色

我想给那些寻求职业发展的开发人员一个建议——研究你的业务领域。与你的产品人员交流，并设身处地为你的最终用户着想，找到最佳解决方案。

任何一个普通的软件程序员都可以编写出一个风暴，但是只有我们中最优秀的人知道如何找到正确的问题。

*一个* [*工作坊*](https://less.works/conferenza/sessions/2018-less-conference-new-york-problem-vs-solution-space-89) *由 Viktor Grgi 在 2018 年举办的* [*LeSS 大会 New York*](https://less.works/less-conferences/2018-new-york/index) *启发了这篇文章。我的另一个灵感来源是我的客户的社会政治治理模式，* [*一个世界的编码者*](https://www.oneworldcoders.com/) *，在提出公司政策之前总是提出一个组织* [*驱动者*](https://patterns.sociocracy30.org/describe-organizational-drivers.html) *。*
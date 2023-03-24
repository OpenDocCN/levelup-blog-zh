# 戈朗的实践基础:单一责任原则

> 原文：<https://levelup.gitconnected.com/practical-solid-in-golang-single-responsibility-principle-20afb8643483>

## 坚实的原则

## 我们通过介绍最广为人知的原则——单一责任原则，开始了软件开发的基本原则之旅。

![](img/76dfad4fd72bd6e334fdcf39236d9602.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[猎人哈利](https://unsplash.com/@hnhmarketing?utm_source=medium&utm_medium=referral)拍摄的照片

软件开发突破的可能性不太多。它们大多是在错误的初始学习后重新连接逻辑，或者填补我们知识中缺失的一块。

我喜欢那种更深刻的领悟的感觉。有时是在编码过程中，有时是在网上阅读书籍或文章时，有时是坐在公交车上。

内在的声音总是跟着它。*啊，是啊，就是这么回事*。突然间，所有过去的错误都有了逻辑上的原因。所有未来的需求都有一个形状。

我有这样一个坚实的原则突破。这些原则第一次出现在鲍勃叔叔的文件中。后来他在他的书《干净的建筑》中塑造了他们。

在这篇文章中，我计划通过提供 Go 中的例子来开始我的旅程。列表中的第一个，代表单词 *SOLID* 中的字母 *S* ，是单一责任原则。

```
Are you interested in Domain-Driven Desing? Check these articles from DDD series:**1\.** [**Practical DDD in Golang: Value Object**](/practical-ddd-in-golang-value-object-4fc97bcad70)**2\.** [**Practical DDD in Golang: Entity**](/practical-ddd-in-golang-entity-40d32bdad2a3)**3\.** [**Practical DDD in Golang: Domain Service**](/practical-ddd-in-golang-domain-service-4418a1650274)**4\.** [**Practical DDD in Golang: Domain Event**](/practical-ddd-in-golang-domain-event-de02ad492989)**5\.** [**Practical DDD in Golang: Module**](/practical-ddd-in-golang-module-51edf4c319ec)**6\.** [**Practical DDD in Golang: Aggregate**](/practical-ddd-in-golang-aggregate-de13f561e629)**7\.** [**Practical DDD in Golang: Factory**](/practical-ddd-in-golang-factory-5ba135df6362)**8\.** [**Practical DDD in Golang: Repository**](/practical-ddd-in-golang-repository-d308c9d79ba7)**9\.** [**Practical DDD in Golang: Specification**](/practical-ddd-in-golang-specification-6523d14438e6)
```

[](https://blog.ompluscator.com/membership) [## 通过我的推荐链接加入媒体——马尔科·米洛耶维奇

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

blog.ompluscator.com](https://blog.ompluscator.com/membership) 

# 当我们不尊重单一责任时

> 单一责任原则(SRP)规定每个软件模块应该有且只有一个变更的理由。

上面的句子是鲍勃叔叔自己写的。它的意义最初是绑定到一个模块，并通过将责任映射到组织的日常工作来划分责任。

今天，SRP 的范围很广，它涉及软件的不同方面。我们可以在类、函数、模块中使用它的用途。当然，在 Go 中，我们可以在结构中使用它。

让我们从上面检查代码块。这里我们有一个结构`EmailService`，只有一个方法`Send`。我们使用这项服务发送电子邮件。即使它看起来很好，当我们触及表面时，我们意识到这段代码破坏了 SRP 的每个方面。

`EmailService`的职责不仅仅是发送电子邮件，而是将电子邮件信息存储到数据库**中，然后**通过 SMTP 协议发送出去。

仔细看看上面的句子。“和”这个词是有目的的加粗。使用这样的表述看起来不像我们描述单一责任的情况。

> 一旦描述某个代码结构的责任需要使用“和”这个词，它就已经打破了单一责任原则。

在我们的例子中，我们在许多代码级别上违反了 SRP。第一个是功能层面的。函数`Send`既负责在数据库中存储消息，也负责通过 SMTP 协议发送电子邮件。

第二层是一个结构`EmailService`。正如我们已经得出的结论，它也有两个责任，存储在数据库和发送电子邮件。

这样的守则会有什么后果？

1.  当我们改变表结构或存储类型时，我们需要改变通过 SMTP 发送电子邮件的代码。
2.  当我们想要集成 [Mailgun](https://www.mailgun.com/) 或 [Mailjet](https://www.mailjet.de/) 时，我们需要更改一段代码，用于在 MySQL 数据库中存储数据。
3.  如果我们在应用程序中选择发送电子邮件的不同集成，每个集成都需要有一个逻辑来存储数据库中的数据。
4.  假设我们决定将应用程序的职责分成两个团队，一个团队负责维护数据库，另一个团队负责集成电子邮件提供商。在这种情况下，他们将使用相同的代码。
5.  这个服务实际上无法通过单元测试来测试。
6.  …

因此，让我们重构这段代码。

# 我们如何尊重单一责任

为了在这种情况下划分职责，并使代码块只有一个存在的理由，我们应该为每个代码块定义一个结构。

它实际上意味着在一些存储器中有一个单独的结构用于存储数据，并且通过使用与电子邮件提供商的一些集成有一个不同的结构用于发送电子邮件。下面的代码块中有更多信息:

这里我们提供了两个新的结构。第一个是作为`EmailRepository`接口实现的`EmailDBRepository`。它支持在底层数据库中持久化数据。

第二个结构是`EmailSMTPSender`，它实现了`EmailSender`接口。这个结构只负责通过 SMPT 协议发送电子邮件。

最后，新的`EmailService`包含来自上面的接口，并委托发送电子邮件的请求。

可能会出现一个问题:`EmailService`是否仍然有多重职责，因为它仍然拥有存储和发送电子邮件的逻辑？看起来像是我们只是做了一个抽象，但职责仍然在那里吗？

在这里，情况并非如此。`EmailService`不负责储存和发送电子邮件。它将它们委托给下面的结构。它的职责是将处理电子邮件的请求委托给底层服务。

> 承担责任和委派责任是有区别的。如果一个特定代码的改编可以消除责任的整个目的，我们就说持有。如果这种责任在删除特定代码后仍然存在，那么我们就谈到了委托。

如果我们去掉 complete `EmailService`，我们仍然需要一个负责在数据库中存储数据和通过 SMTP 发送电子邮件的代码。这意味着我们可以肯定地说，电子邮件服务不承担这两项责任。

# 更多的例子

正如我们之前看到的，SRP 适用于编码的许多不同方面，而不仅仅是结构。我们可以看到，我们可以为了函数而破坏它，但是这个例子已经在一个 struct 中破坏 SRP 的阴影下了。

为了更好地了解 SRP 原理在函数中的应用，让我们看下面的一个例子:

函数`extractUsername`没有太多行。它支持从 HTTP 头**中提取原始的 [JWT](https://jwt.io/) 令牌，并且**返回用户名的值(如果它存在于其中的话)。

你可能会再次注意到粗体字“和”。这个方法有多重责任。我们如何重构它的描述并不重要。我们不能避免使用“和”这个词来描述这个方法的作用。

我们应该更多地参与方法本身的重构，而不是重构一种描述函数目的的方式。下面你可以找到一个新代码的建议:

现在我们有两个新功能。第一个是`extractRawToken`，包含从 HTTP 头中提取原始 JWT 令牌的责任。如果我们改变了头中保存令牌的键，我们应该只接触一个方法。

第二个是`extractClaims`。该方法负责从原始 JWT 令牌中提取声明。最后，在将令牌提取请求委托给底层方法之后，我们的旧函数`extractUsername`从声明中获取特定值。

还有更多例子。其中许多是我们日常使用的。我们使用其中一些是因为一些框架规定了错误的方法，或者我们懒得提供正确的实现。

上面的例子显示了模式[活动记录](https://en.wikipedia.org/wiki/Active_record_pattern)的典型实现。在这种情况下，我们还在`User`结构中添加了一个业务逻辑，而不仅仅是将数据存储到数据库中。

这里我们混合了活动记录的目的和来自领域驱动设计的[实体](/practical-ddd-in-golang-entity-40d32bdad2a3)模式。为了正确地交付代码，我们需要提供单独的结构:一个用于在数据库中持久存储数据，另一个充当实体的角色。下面的例子也犯了同样的错误:

这里我们又有了两个职责，但是现在，第二个职责(数据库中一个表的映射，通过 [Gorm](https://gorm.io/index.html) 包)没有直接表示为算法，而是表示为 Go 标签。

即使是现在，钱包结构也违反了 SRP 原则，因为它扮演着多重角色。如果我们改变一个数据库模式，我们需要调整这个结构。如果我们改变取款的业务规则，我们需要调整这个类。

上面的代码片段是破坏 SRP 的另一个例子。而且，在我看来，这是最悲剧的一个！我们不能提供一个更小的结构来承担更多的责任。

通过查看`Transaction`结构，我们意识到它描述了到数据库中表的映射，并且是 REST API 中 JSON 响应的持有者，但是由于验证部分，它也可以是 API 请求的 JSON 主体。*一个结构统治所有的*。

所有这些例子迟早都需要调整。只要我们把它们保留在代码中，它们就是无声的问题，很快就会打破我们的逻辑。

# 结论

单一责任原则是坚实原则中的第一个原则。它代表单词 SOLID 中的一个字母 S。它声称一个代码结构必须只有一个存在的理由。

我们将这些原因视为责任。一个结构可以承担责任或委托责任。每当我们的结构包含多个职责时，我们应该重构这段代码。
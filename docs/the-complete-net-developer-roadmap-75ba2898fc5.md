# 完整的。NET 开发人员路线图

> 原文：<https://levelup.gitconnected.com/the-complete-net-developer-roadmap-75ba2898fc5>

## 为了脱颖而出，你需要知道的一切

![](img/870eb4f40788875f85385040af17d1e9.png)

照片由[上的](https://unsplash.com/photos/XLqiL-rz4V8)[砌墙](https://unsplash.com/@walling)拍摄

如果你前往[栈溢出](https://insights.stackoverflow.com/survey/2021#most-popular-technologies-webframe-prof)，快速浏览他们的开发者调查，你会注意到。开发人员非常喜欢. NET，这也是有充分理由的。微软在语言方面做得非常出色，并且越来越受欢迎。

在这篇文章中，我将试着列出一些你应该考虑学习的东西，以便从人群中脱颖而出，成为一名全面发展的开发人员。我从 YouTube 上的 Nick Chapsas 那里得到了这篇文章的灵感，我认为 Github 上也有类似的路线图，如果你感兴趣的话，你也可以看看。虽然我同意我们的大部分观点，但我想把它写在一篇文章中，并试着举例说明如何准确地学习这些技能，这样你就可以马上开始了解如何到达终点。所以事不宜迟，我们开始吧。

## C#和。净基本面

先把显而易见的去掉。你应该看一下 C#和. NET。这两者的文档都很棒，YouTube 上有大量的视频介绍了基础知识，并帮助你在整个视频过程中构建简单的控制台应用程序，帮助你很好地巩固基础知识。

## 一般技能

*   你肯定会使用 Git 来跟踪代码中的变化，所以可以自由地查找 Github、Azure DevOps 等。从高层次上理解 Git 好的一面是，它真的不会占用你太多时间。
*   HTTP 协议——您可能会对这一协议了如指掌，所以只需从基础开始，理解客户端和服务器端如何通信。

## 了解流行的软件设计实践

首先，只要看看固体原则，此外检查干，吻和 YAGNI 原则。所有这些都很简单，但是它们可以帮助你保持代码的整洁。

我也写了关于他们的文章，所以你可以看看下面。

[](https://medium.com/codex/solid-principles-in-c-df43187697f4) [## C#中的坚实原则

### 坚实的原则

在 C#实心 Principlesmedium.com 中](https://medium.com/codex/solid-principles-in-c-df43187697f4) [](https://medium.com/codex/become-a-better-programmer-with-these-software-engineering-principles-204fa93e8094) [## 用这些软件工程原则成为更好的程序员

### 干，吻，YAGNI 解释道

medium.com](https://medium.com/codex/become-a-better-programmer-with-these-software-engineering-principles-204fa93e8094) 

## ASP。净基本面

好了，现在我们知道了 C#和。NET，我们熟悉一些设计实践。现在可能是研究 Web 和 REST APIs 的好时机了。除此之外，我们还可以了解路由、认证和中间件是如何工作的。

好消息是。NET 引入了非常容易设置的最小 API——你可以在 5 分钟之内拥有一个可用的 API。一定要下载像 Postman 这样的东西，这样你就可以向你的后端发送 API 请求，看看奇迹是如何发生的。

## 没有 SQL 就走不了多远

SQL 是编程的一个基础部分，你肯定应该在某种程度上了解它。首先，只需了解基本的插入、更新、连接和约束是如何工作的，这样，当您使用初学者项目时，至少可以添加和更新数据。

更多关于加入的信息如下:

[](/sql-joins-in-plain-english-c43379368396) [## SQL 用简单的英语连接

### 内、左、右和全联接是如何工作的

levelup.gitconnected.com](/sql-joins-in-plain-english-c43379368396) 

我推荐 SQLite，因为它非常容易学习和设置，所以它是进入数据库世界的一个很好的敲门砖。

## 对象关系映射

对象关系映射使开发人员能够以面向对象的方式处理数据，方法是执行在应用程序中的对象和数据源中存储的数据之间进行映射所需的工作。

因为 C#是面向对象的，所以你会想要使用 ORM。

而实体框架是你的最佳选择。它被设计成轻量级的、可扩展的，并作为. NET 的一部分支持跨平台开发。它使用简单，并提供了很好的性能。

## 关系数据库

现在，我认为你还不需要查看 NoSQL 数据库，但是关系数据库是其中之一，我的建议是 PostgreSQL。

我的一个帮助你理解 PostgreSQL 的建议实际上是关于 Udemy 的这门课程。创作者做了一项惊人的工作，有很多深入的解释，它真的会帮助你掌握概念。

如果你不想上一门课，YouTube 上有很多免费的材料，但是这门课有很多练习，你不会只是看而不应用新知识。

如果你对 PostgreSQL 有所了解——我有一篇关于索引是如何工作的很好的文章，所以一定要去看看。

[](/an-in-depth-look-at-database-indexing-for-performanceand-what-are-the-trade-offs-for-using-them-e2debd4b5c1d) [## 深入了解数据库索引的性能以及使用它们的利弊

### 通过 PostgreSQL 示例了解如何充分利用索引

levelup.gitconnected.com](/an-in-depth-look-at-database-indexing-for-performanceand-what-are-the-trade-offs-for-using-them-e2debd4b5c1d) 

## Serilog

一个值得了解的是日志框架，Serilog 是最流行的一个。在这一点上没有太多要补充的，但是相信我，学习一个日志框架并实际使用它是非常重要的。如果你不使用它，当你没有记录错误的时候，你可能会尖叫着结束，你可以在几天内找到问题所在。

## 测试

尽管事实上没有人喜欢写测试，你仍然需要学习如何写它们。

好的一面是学习曲线不会很陡，你不需要很长时间就能明白谁能写出好的测试。

那么学什么呢？

*   单元测试——例如 xUnit
*   嘲弄的
*   剧作家—端到端测试
*   spec flow——用于自动化测试的 BDD(行为驱动开发)框架。

## 对象映射

另一个简单的方法——考虑一下自动映射器。自动映射器所做的只是将一个对象映射到另一个对象。让我给你举个例子:

假设我们有一个使用 EntityFramework 连接到数据库的 UserEntity。现在，当我们获取一个用户并将其返回到我们的域层时，我们希望将该用户实体映射到 UserDomainEntity，它本质上具有相同的属性。

## 微服务

微服务变得越来越受欢迎，明智的做法是了解它们是如何工作的，尽管事实上你选择为之工作的公司不一定会使用它们。

首先，你需要了解一个云提供商——就我个人而言，我使用 Azure 的次数最多，我也非常喜欢，所以这是我的推荐。

你需要学习的其他东西:

*   docker——允许您使用为您提供应用程序和服务的本地容器来标准化环境。Docker 容器对于持续集成和持续交付(CI/CD)也非常有用，因为它们易于使用，构建、部署和管理都非常简单和安全。
*   Kubernetes——另一方面，Kubernetes 允许您管理 Docker 容器，这有助于工作量和自动化。
*   消息总线——微服务设计的核心是使用消息来实现服务之间的通信，从而使服务不相互依赖。既然我们选择了 Azure 作为云提供商，那么在消息方面看一下 Azure Service Bus 是明智的，但是你也可以点击下面的文章来简单了解一下什么是消息代理。

[](https://blog.devgenius.io/message-broker-architecture-what-is-it-and-how-does-it-work-60a62f8ce784) [## 消息代理架构，它是什么，如何工作

### 什么是队列、主题和订阅

blog.devgenius.io](https://blog.devgenius.io/message-broker-architecture-what-is-it-and-how-does-it-work-60a62f8ce784) 

*   Azure 事件中心——最后是事件中心。消息和事件的区别在于，当发布事件时，发布者并不期望事件如何处理。例如，如果我们的系统创建了一个新用户，并希望发送一封欢迎电子邮件，我们可能会向服务总线发送一条消息，期望它会处理这条消息，并将其传递给电子邮件服务，然后电子邮件服务会发送一封电子邮件。
    借助事件中心提供分析服务，在某些情况下，事件中心可以:
    *欺诈检测
    *日志记录

## 结论

尽管一开始有这么多东西要学，这可能会令人望而生畏，但不要感到气馁，因为大多数东西都很容易掌握。

如果你有任何问题，请不要犹豫留下评论，我会尽快回复。

如果你喜欢你所拥有的，并且想加入 Medium，请考虑使用我的推荐链接。

[](https://medium.com/@ivan.zstoev/membership) [## 通过我的推荐链接加入 Medium-Ivan Stoev

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

medium.com](https://medium.com/@ivan.zstoev/membership)
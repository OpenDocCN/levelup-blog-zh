# 设计消息传递系统时要问的 7 个问题

> 原文：<https://levelup.gitconnected.com/7-questions-to-ask-when-designing-a-messaging-system-baf6df258876>

## 提高你的系统设计技能。

![](img/064948d2eeb9ad77139bb1597ec6add4.png)

Emile Perron 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

设计一个消息传递系统，或者仅仅是在两个微服务之间建立一个消息传递交互，需要考虑一些重要的问题，以便在第一次就把事情做好，避免将来可能出现的大规模返工。

## 1.微服务之间的消息传递集成适合我的具体情况吗？

消息传递并不是微服务之间建立通信的唯一方式。还可以考虑其他方法[，比如 gRPC、RESTful APIs 或流媒体。在做最后决定之前，权衡所有可行方案的利弊。](/4-ways-to-establish-communication-between-microservices-984207f29497)

## 2.邮件的最大长度是多少？

消息代理对可以发送到队列的最大消息大小施加了一些限制。在某些情况下，只需在发布者端压缩消息，在使用者端解压缩，就可以绕过限制，而无需重新设计。

在其他情况下，对于非常大的消息，可能需要改变设计来绕过消息代理的限制，比如使用[声明检查](https://www.enterpriseintegrationpatterns.com/patterns/messaging/StoreInLibrary.html)模式。

## 3.谁是消息消费者？

在设计阶段选择正确的消费者类型(Web 服务、FaaS 等)是很重要的。)并确保它能够高效地处理消息。

选择消费者类型时需要考虑的一些问题:

*   随着邮件数量的增长，消费者能否高效地进行扩展？
*   消费者是否有足够的处理器、RAM 和其他资源来有效地处理消息？
*   消费者是否遭遇冷启动问题？

## 4.我应该保证消息的有序处理吗？

消息可能需要按照发送到消息代理队列的顺序进行处理。为了支持这些需求，架构师可以选择在每条消息中包含一个增量标识符或时间戳，或者使用一些消息代理提供的内置功能来确保有序处理。

## 5.队列的最大吞吐量是多少？

或者每秒、每分钟或每小时有多少消息将进入消息代理队列？如果需要支持高吞吐量，架构师可以决定在多个订阅者之间分割消息处理([竞争消费者模式](https://www.enterpriseintegrationpatterns.com/CompetingConsumers.html))和/或在发布者或消费者端实现[批处理](/how-to-improve-the-performance-of-the-messaging-architecture-e1f31a2e9762)。

## 6.我计划使用的消息代理能够完全满足技术和业务需求吗？

各种消息代理是相似的，它们提供的大多数基本特性可能几乎是相同的。然而，当在消息传递体系结构中实现复杂的情况时，与市场上的其他消息代理相比，一些消息代理可能不提供对高级特性的支持。因此，理解主要的和最复杂的系统需求并了解哪种消息代理最适合满足它们是很重要的。

## 7.微服务是否应该合并成一个，而不是实现它们之间的消息通信？

但是在选择和实施任何类型的微服务集成之前，开发人员或架构师也可以快速问自己一个问题:

*“我要集成的两个微服务不应该合并成一个吗，这样就完全没有集成的必要了？”*

让我解释一下。有时在设计和开发微服务时会发生这种情况，一些微服务是在没有真正需求的情况下创建的。每个微服务都会带来额外的部署管道、存储库、共享包等。解决方案。因此，系统变得更加复杂。

因此，在通过实现额外的消息传递通道或任何其他类型的微服务交互来使系统变得复杂之前，如果微服务的单独存在不能解决任何业务或技术问题，那么简单地将它们组合成一个是有意义的。

我所列出的问题完全基于我的个人经验。我也很想听听您在设计微服务之间的消息传递集成时会考虑哪些问题。

感谢阅读。如果你喜欢你所读到的，看看下面这个故事:

[](/50-software-engineering-best-practices-for-technical-leaders-and-seniors-cfcdf6a17e44) [## 面向技术领导者和资深人士的 50 个软件工程最佳实践

### 最佳工程师的最佳实践。

levelup.gitconnected.com](/50-software-engineering-best-practices-for-technical-leaders-and-seniors-cfcdf6a17e44) 

还有，考虑成为[中等成员](https://esashamathews.medium.com/membership)。
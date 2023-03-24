# 什么是 Cloudflare 的超级云？

> 原文：<https://levelup.gitconnected.com/what-is-cloudflares-supercloud-1dac17cbfa5b>

## 了解 Cloudflare 为托管各种应用程序而不断增加的产品和服务列表。

![](img/cc7ff38a1866502d859c4d03a4abc6fc.png)

纳斯蒂亚·杜尔希尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我们现在都很熟悉云—你会相信 AWS 是在 16 年前推出的吗？但是和计算领域的大多数事情一样，总是有新的技术和方法来挑战现状。

Cloudflare 将下一代云称为*超级云*。那么他们对超级云的解释到底是什么，你应该在未来考虑它吗？

在本文中，我们将了解 Cloudflare 不断增长的产品和服务列表，这些产品和服务用于托管广泛的应用程序。

# 什么是云？

![](img/c41b6a1e73e5cd83d8b101acab806e85.png)

欧内斯特·布里洛在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在我们谈论超级云之前，让我们先回顾一下今天的云是什么。

云的功能是按需提供计算资源。你不必担心服务器和维护，你只需根据资源(如 CPU 和内存)定义你需要什么，云就会为你提供这些，*为*和*当*你需要它的时候。

主要的云提供商 AWS、GCP 和 Azure 提供了一长串的功能和产品，而不仅仅是运行应用程序本身的地方(比如在 EC2 实例上，或者在 AWS 上的 ECS 中的容器上)。云提供商几乎提供了运行应用程序所需的一切:数据库、缓存、文件存储、消息队列、发送电子邮件、人工智能工具等等。

云带来的不同是，一切都是随需应变的。 你不需要安装任何东西，也不需要购买任何服务器，它随时准备就绪。你现在就可以创建一个免费的 AWS 账户，并在几分钟内启动你需要的任何东西。

根据 Cloudflare 的说法，接下来会发生什么？

# 超级云的愿景

![](img/cce7208f92b1b034a1c7de7caa2f4523.png)

丹尼尔·勒曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

Cloudflare 最近举办了[开发者周](https://www.cloudflare.com/en-gb/developer-week/)，会上宣布了新产品和服务。除此之外，他们[提出了他们对 Cloudflare 未来的愿景](https://blog.cloudflare.com/welcome-to-the-supercloud-and-developer-week-2022/)。

值得注意的是，Cloudflare 不一定创造了超级云这个术语，康乃尔大学的[提到了它](http://supercloud.cs.cornell.edu/)。然而，他们的观点与康奈尔的略有不同，这一点我们很快就会看到。

在他们的理想世界中，这一过程将被进一步简化。不需要担心你在什么硬件上运行，你需要多少内存(在合理的范围内)，或者你需要多少 CPU 能力(同样，在合理的范围内)。

Cornell 的观点是超级云跨越了多个云提供商，但您仍然负责管理 EC2 实例或容器。而 Cloudflare 的观点是你不需要担心这些。您只需部署您的应用程序，Cloudflare 会处理剩下的工作。

如果你听说过*无服务器*，它将这种运动发挥到了极致。您编写代码，然后在几秒钟内将其部署到世界各地。没有容器的旋转，也没有实例的供应。Cloudflare 担心所有的基础设施，而您作为一名工程师，专注于您最擅长的事情:构建应用程序。

当前的云提供商确实提供无服务器功能。Cloudflare 的不同之处在于，它完全采用了这种方法，因此它的工具和产品都是为了充分利用这种环境而专门制作的。

对我来说听起来不错，但是超级云能提供你需要的一切吗？

# Cloudflare 的超级云功能

![](img/48f3f062d3d78f81ef28c1f843e1faa4.png)

埃米利奥·古兹曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

正如我们前面提到的，其他云提供商提供数百种产品和服务。Cloudflare 正处于其超级云之旅的开端，那么到目前为止它到底提供了什么呢？

我认为 Cloudflare 为大多数应用程序提供了基础。以下是迄今为止它提供的主要功能:

*   [**工人**](https://developers.cloudflare.com/workers/) **。** Cloudflare 的无服务器执行环境，您将在其中部署应用程序，这些应用程序的逻辑也将在其中运行。例如，任何进来的 HTTP 请求都将由一个工人来处理。你可以用多种语言创建工人。
*   [](https://developers.cloudflare.com/workers/learning/how-kv-works/)****。**一个全局的、低延迟的键值存储，它有效地提供了与 [Redis](https://redis.io/) 相似的功能。例如，KV 可以用来存储用户会话或缓存数据。然而，它最终是一致的，因此写入可能需要 60 秒才能反映在您处理的所有后续请求中，因为这取决于哪个数据中心处理您的请求。**
*   **[**耐久物体**](https://developers.cloudflare.com/workers/learning/using-durable-objects/) **。**一个低延迟的键值存储，但是有一致性保证。与 KV 不同的是，它具有很强的一致性，因此一旦写入，后续请求就能保证访问这些数据。**
*   **[**R2**](https://developers.cloudflare.com/r2/) **。**提供简单的存储机制，类似于 AWS 的 S3 产品。R2 旨在存储更大的数据，如 JSON 文档或 pdf。**
*   **[**流**](https://developers.cloudflare.com/stream/) **。**允许您上传、存储和传送视频内容。**
*   **[**队列**](https://developers.cloudflare.com/queues/) **。一个测试产品，正如它听起来的那样:提供消息队列以允许工作人员进行异步处理。****
*   **[](https://developers.cloudflare.com/d1/)****。** Cloudflare 的最新产品(测试版)为您的员工提供 SQL 功能。使您能够快速、持久地访问存储，以及数据库事务等功能。****

****这些功能涵盖了大多数应用程序的现代用例。当然，它没有 AWS 提供的一些更花哨的产品，也肯定没有任何人工智能产品。实际上，大多数应用程序都可以使用超级云工具。****

****您可能想知道如何部署和管理所有这些服务。幸运的是，Cloudflare 通过 [Wrangler](https://developers.cloudflare.com/workers/wrangler/) 也让这变得简单了。这是一个 CLI 工具，允许您快速轻松地部署您的员工，并与他们提供的任何产品进行交互。它使用起来非常简单，但那是另一篇文章的主题！****

****我要说的最后一点是，它还可以根据您的需要进行扩展。无需增加内存、CPU 或容器。您可以在一夜之间从 0 个客户增加到 100，000 个，而 Cloudflare 可以为您扩展一切。您的应用程序代码或数据库设计可能不适合这种激增，但 Cloudflare 及其基础架构可能适合。****

# ****摘要****

****围绕超级云还有很多内容要介绍，但您现在已经对 Cloudflare 的产品和愿景有所了解。我绝不是建议你马上把现有的服务都搬到那里去。然而，我认为它已经足够成熟，或者接近足够成熟，可以考虑你是否可以在那里建设绿地项目。****

****同样，如果你是一家初创公司，并且正在挑选一套技术，那么 Cloudflare 是一个很好的入门选择。一般来说，对于低流量的网站，[无服务器被证明是非常划算的](https://techbeacon.com/enterprise-it/economics-serverless-computing-real-world-test)，因为你只需为你使用的东西付费，而不是让一切全天候运行。有一个断点，但大多数公司不太可能达到它，如果他们做到了，这是一个很好的问题。****

****在 Cloudflare 的免费层(任何人都可以注册)，您每天会收到 100，000 个使用请求，无需任何费用。其他一些产品，如 KV，也有一个免费层。如果你是一家初创企业，你可能一开始每月不用支付任何费用。或者，如果你使用付费产品，你可以以很低的费用开始。****

****如果你想试一试，我这里有一个关于 Cloudflare workers [的入门指南](https://medium.com/better-programming/create-and-host-apis-for-free-using-cloudflare-workers-808830a4e5f5)。****

****[](https://pragprog.com/titles/apdiag/creating-software-with-modern-diagramming-techniques/) [## 我的新书《用现代图表技术创建软件》已经出版了

### 了解如何使用基于文本的标记工具 Mermaid 为各种用例创建图表，以便比文字更直接、更清晰地交流信息。

pragprog.com](https://pragprog.com/titles/apdiag/creating-software-with-modern-diagramming-techniques/)****
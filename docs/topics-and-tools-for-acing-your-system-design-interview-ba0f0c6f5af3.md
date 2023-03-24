# 应对系统设计面试的主题和工具

> 原文：<https://levelup.gitconnected.com/topics-and-tools-for-acing-your-system-design-interview-ba0f0c6f5af3>

## 权威指南

## 在你下一次系统设计面试之前，有一些必须知道的话题可以让你有一个基本的了解

![](img/c78325a55de2e2f7869b43fd4b306214.png)

Agnieszka Boeske 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果你在我之前的博客中关注我，我在那里解释了在你下一次系统设计面试之前的心态和准备方法，那么你在接下来的博客中承诺的地方是正确的。

我将列出重要的主题，提供一个简要的概述和额外的基础阅读材料，以提高你的系统设计和解决问题的能力。虽然这是一个很大的列表，但绝不是一个详尽的列表。所以我要做的是一个一个地看，并对每个概念做一行解释。显然，我不能说得太详细，因为坦率地说，它们中的每一个都可以自己编一个故事(如果你想知道更多细节，也许我会写一篇关于它们的博客)。让我们开始吧

**垂直与水平刻度**

![](img/838cf625c31d7b96f50f7414950d591c.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Stijn Swinnen](https://unsplash.com/@stijnswinnen?utm_source=medium&utm_medium=referral) 拍摄

因此，如果您需要纵向扩展您的系统，您可以进行您所说的扩展，这意味着您可以向现有主机添加更多的内存 CPU 和硬盘，或者您可以进行横向扩展，即保持一个较小的系统，但向其添加另一个系统。

因此，当您进行垂直扩展时，成本可能会很高，并且您可以向单个主机添加的内存和 CPU 数量也有限制，但它不存在分布式系统问题。另一方面，水平扩展再次无限地增加更多的主机，但是您必须处理所有的分布式系统挑战。显然，水平缩放比垂直缩放更受欢迎。

**上限定理**

CAP 代表一致性、可用性、分区容忍度。

1.  一致性表示您的读取具有最近的写入
2.  可用性说明您会收到回复，但可能是也可能不是最近一次写入。
3.  分区容差是指在两个节点之间，您可能会丢失网络数据包。

所以 CAP 定理说，你只能实现这三个属性中的两个，分区容错是不可避免的，因为你会丢弃网络数据包。所以基本上你是在一致性和可用性之间做选择。

有传统的关系数据库，他们选择一致性而不是可用性，这意味着他们可能不太可用。但是他们的数据总是一致的。另一方面，非关系数据库更喜欢可用性或一致性。

**酸对碱**

![](img/aab4af7e4ddc5d72ca88bb985612b481.png)

由[粘土银行](https://unsplash.com/@claybanks?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

酸代表**原子性、一致性、隔离性、耐久性**，碱代表**基本可用、柔软状态、最终一致性**。acid 更多地用于关系数据库，而 BASE 更多地用于 NoSQL 数据库，您需要了解两者的区别，因为一旦开始使用，您需要了解您愿意牺牲 ACID 属性的哪一部分

**分区/分片数据**

假设您有数万亿条记录，并且您无法将数万亿条记录存储在数据库的一个节点中，您需要将它们存储在数据库的许多不同节点中，这就是分片的作用所在，它回答了一个问题，例如您如何分割这些数据，以便数据库的每个节点负责这些数万亿条记录中的一些记录的一部分，一种常用的技术是一致缓存，您**肯定**需要知道一致缓存是如何工作的。它有什么优点，一致性哈希提供了什么？

**乐观锁与悲观锁**

![](img/d167ab46967e34eeef35d78f83aafe5e.png)

照片由[张家瑜](https://unsplash.com/@danielkcheung?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

让我们假设您正在以一种乐观的方式进行一个数据库事务，并且您不需要任何锁。但是当您准备提交事务时，您注意到是否没有其他事务更新您正在处理的记录。另一方面，在悲观锁中，您预先获取所有锁，然后提交事务。这两种方法各有优缺点，您确实需要了解在每种情况下何时使用

**强势 vs 最终一致性**

强一致性意味着您的读取将总是看到最新的写入，而最终一致性意味着您的读者将看到一些写入，最终您将看到最新的写入。强一致性显然用于关系数据库，而不是 SQL 数据库，您必须决定是否需要强一致性和最终一致性，最终一致性的好处是它提供了更高的可用性，这一切都可以追溯到 CAP 定理。

**关系数据库与非 SQL 数据库**

![](img/f925620f5388e586d698f6645cb67c5a.png)

由[乔治·博内夫](https://unsplash.com/@spktwo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

这些天来，我看到大多数人更喜欢使用 NoSQL，这很好，但不要放弃关系数据库。请记住，关系数据库提供了所有这些良好的 ACID 属性，而 NoSQL 数据库的伸缩性稍好一些，并且具有更高的可用性。所以根据情况和问题，试着看看两者哪个更合适。

NoSQL 数据库的类型。

1.  键-值:这是最简单的一种，你有一个键和一个值，它将这个键-值对存储到数据库中。
2.  宽列数据库:像 [Bigtable](https://en.wikipedia.org/wiki/Bigtable) 和 [Apache Cassandra](https://en.wikipedia.org/wiki/Apache_Cassandra) 这样的宽列存储在术语的原始意义上不是列存储，因为它们的两级结构不使用列数据布局
3.  基于文档的数据库:这种。如果你有一个半结构化的数据，或者如果你有一个 Excel 或 JSON 数据，如果你想把它放入数据库，那么你会使用基于文档的数据库。
4.  图形数据库是一种被设计成将数据之间的关系视为与数据本身同等重要的数据库。它旨在保存数据，而不是将其限制在预定义的模型中。相反，数据的存储就像我们第一次提取数据一样——显示每个单独的实体如何与其他实体连接或相关。

**缓存**

![](img/233bf29a6273ff286627feda4428d089.png)

照片由[雷·轩尼诗](https://unsplash.com/@rayhennessy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

缓存是用来加速你的请求。如果您知道一些数据将被更频繁地访问，那么它将被存储在缓存中，以便可以快速访问。

缓存有两种类型。

1.  如果每个节点都有自己的缓存。因此节点之间不共享缓存。
2.  Spited 缓存，现在的缓存在不同的节点之间共享。

如果你正在做缓存，你必须考虑一些事情。首先，一个缓存不可能是真相的来源。第二，缓存必须非常小，因为缓存倾向于将所有数据保存在内存中，直到您不得不考虑缓存周围的一些回收策略。

**Http vs Http2 vs Websockets**

HTTP 是客户端和服务器之间的请求和响应类型的架构，几乎整个 web 都运行在 HTTP 上。其中，HTTP2 是对 HTTP 的改进，它通过单个连接处理多个请求，我们有 WebSockets，它是客户端和服务器之间的完全双向连接，因此了解它们之间的一些差异以及它们的一些内部工作方式会很有帮助

**TCP/IP 模型**

在 TCP/IP 模型中，这个模型有四层，了解每层的作用是很好的。

**IP4 vs IP6**

如果您知道 IP before 具有 32 位地址，而 IP6 具有 128 位地址，那么我们将用完 IP before 地址。因此，这个词正在向 IP6 迁移，了解这方面的一些细节很有好处。以及 IP 路由是如何工作的。

**TCP vs UDP**

TCP 是面向连接的可靠连接，而 UDP 是不可靠的连接。因此，如果你正在做视频流，那么你最好使用 UDP，因为即使它是不可靠的，但它是超级快的。另一方面，如果你正在发送一些文件，那么你最好使用 TCP

**域名系统**

DNS 查找，域名服务器查找。因此，如果你在浏览器中输入[www.google.com](/www.google.com)，那么 DNS 请求会发送到 DNS，DNS 会将该地址转换为 IP 地址。所以知道这些东西是如何工作的很好。他们周围的层次结构是什么，他们如何进行缓存等等

**HTTP & TLS**

传输层安全性。它用于保护客户端和服务器之间的通信，包括隐私和数据完整性。当与 HTTP 一起使用时，它非常 HTTPS

**对称和非对称加密**

非对称加密在计算上更昂贵，因此它应该用于发送少量数据，最好是非对称密钥。非对称加密的一个例子是公钥-私钥加密，对称加密的一个例子是 AES

**负载平衡器**

![](img/e2b169619bc1b38fe14f0085ee0b1200.png)

兹登尼克·马赫切克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

负载平衡器位于服务的前端，将客户端请求委派给服务后面的一个节点。这种委托可以基于循环或该服务后面的节点上的平均负载。负载平衡器可以在 L4 或 L7 上运行，这些是 OSI 模型的级别。

因此，对于 L4，负载平衡器考虑客户端和目标 IP 地址以及端口号来进行路由，而在 L7，这是一个 HTTP 级别，使用 HTTP URI 来进行路由，并且大多数节点平衡器在 L7 运行。

**CDN &边缘**

让我们假设你正在伦敦观看网飞。所以网飞要做的是把电影和电视剧放在伦敦离你很近的一个内容传输网络中，这样当你播放电影的时候， 电影可以直接从您附近的 CDN 流式传输，而不是从数据中心一路传输，这有助于最终用户的性能和延迟，EDGE 也是一个非常类似的概念，您可以在靠近最终用户的地方进行处理。Edge 的另一个优势是，它有一个从边缘到数据中心的专用网络，因此您的请求可以路由到这个专用网络，而不是通过互联网。

**布隆过滤器和计数最小草图**

![](img/dcc4aacd2f5cab6d802a23b1ba0145b1.png)

照片由 [Charles Deluvio](https://unsplash.com/@charlesdeluvio?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Bloom filter 和 count-min sketch 是节省空间的、基于概率的数据结构，Bloom filter 用于决定一个元素是否是集合的成员。布隆过滤器可以有假阳性，但你永远不会有假阴性。因此，如果你的设计可以容忍假阳性，你应该考虑使用布隆过滤器，因为它非常节省空间。

count-min 草图是一个类似的数据结构，但是它用于计算事件的频率。让我们假设你有数百万个事件，并且你想要保持谈话事件的轨迹，那么你可以考虑使用对抗草图，而不是保持到目前为止所有事件的计数。它会给你一个足够接近实际答案的答案，但会有一些误差。

**Paxos**

Paxos 用于在分布式主机上获得共识。在 Paxos 出现之前，寻找共识是一个非常困难的问题，共识的一个例子是在分布式主机中进行选举，了解 Paxos 解决的一些用例是很好的

**设计模式和面向对象设计**

对于设计模式，了解工厂方法和单件是值得的，对于面向对象设计，抽象和继承是你应该了解的一些事情

**虚拟机&容器**

虚拟机是一种在共享资源之上为您提供操作系统的方式，让您感觉自己是该硬件的唯一所有者，而实际上该硬件是在不同的隔离操作系统之间共享的。虽然容器远离运行应用程序及其依赖项和隔离环境，但容器已经变得极其重要，它们在生产环境中运行。

**发布者—订阅者/队列**

你有一个发布者发布一个消息到一个队列，一个订阅者从那个队列接收那个消息，这种模式在现在的系统设计中变得非常重要，只要有机会，你一定要使用它们。

以下是一些工具/技术，不仅对系统设计面试有用，在现实生活中也有用。如果你打算在一个高技能系统上工作，显然这是一个很小的列表，还有很多很多其他的工具，但是为了文章的长度，我已经把它限制在这个小的数字上。

**卡珊德拉**

Cassandra 是一个宽列、高度可伸缩的数据库，它用于不同的用例，如简单的键值存储或存储时序数据，或者只是您的更传统的角色。有了许多列，Cassandra 可以在整个过程中提供最终的和强大的一致性 Cassandra 使用一致的散列来分片您的数据，还使用闲聊来让所有节点了解集群。

**Mongo/Couchbase**

如果你有类似 JSOM 的结构，如果你想保持这种结构，那么 Mongo DB 会工作得非常好。它们在文档级别提供了 ACID 属性，并且伸缩性也很好

如果您有一个更传统的用例，其中有许多表和这些表中的关系，并且如果您想要一套完整的 acid 属性，那么我会继续使用 MySQL 数据库，MySQL 数据库也有主/从架构，所以它的伸缩性也很好。

**Memcache 和 REDIS**

Memcached 和 REDID 是分布式缓存，它们将数据保存在内存中。Memcached 是一个简单快速的键值存储版本，也可以做键值存储。REDIS 也可以设置成一个集群，这样它就可以收集更多的可用性和数据复制。REDIS 还可以刷新硬盘上的数据，如果您配置它这样做的话。使用分布式缓存时要记住的要点首先是，永远不应该有真实的来源，它们只能保存有限的数据量，这受到主机上内存量的限制

**动物园管理员**

![](img/043de9aa14baeb11e59f80555426e3a0.png)

照片由 [Darran Shen](https://unsplash.com/@darranshen?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这是一个集中式配置管理工具。它也用于领导者选举和分布式锁定。zookeeper 对于读操作来说伸缩性很好，但是对于写操作来说伸缩性不是很好。此外，因为动物园管理员将所有数据保存在内存中，所以您不能在动物园管理员中存储太多数据。如果你想存储少量的数据，这些数据具有很高的可用性和很大的读取量，那么 zookeeper 是你应该使用的。

**卡夫卡**

Kafka 是一个容错、高度可用的队列，使用发布者、订阅者或流应用程序，这取决于您的使用情况，它可以准确地传递一次消息。它还在主题之后的分区内保持所有消息的有序。

**NGINX 和 HAProxy**

例如，NGNIX 和 HAproxy 是负载平衡器，非常高效，NGNIX 可以从一个实例管理来自一个客户端的数千甚至数万个连接。

**太阳能和弹性搜索**

太阳能和弹性搜索，两者都是在 Lucine 之上的搜索平台。它们都是高度可用、高度可扩展和容错的搜索平台，并且它们确实提供了诸如全文搜索之类的功能。

**码头工人**

Docker 是一个容器软件平台，你可以在里面开发和运行你的分布式应用程序。这些容器可以在你的笔记本电脑、数据中心甚至云端运行。Kubernetes 和 Mesos 是用于管理和协调该容器的软件工具

这就是了。这是我对系统设计的介绍和参加系统设计面试前需要准备的题目。我知道我在很高的层次上浏览了这些概念，但我的意图不是给你太多的细节，而是介绍它们，以便你可以在自己的时间里阅读它们。所以我会把所有额外的阅读材料放在下面的底部，让你们按顺序阅读

![](img/77bf9b4fe1db81cfe353008cb39ba5e4.png)

Juan Davila 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

阅读参考链接:

[https://docs . datas tax . com/en/archived/Cassandra/2.1/Cassandra/architecture/architecture intro _ c . html](https://docs.datastax.com/en/archived/cassandra/2.1/cassandra/architecture/architectureIntro_c.html)

[http://cloudurable.com/blog/kafka-architecture/index.html](http://cloudurable.com/blog/kafka-architecture/index.html)

【https://zookeeper.apache.org/doc/r3.6.2/index.html 号

[https://www . all things distributed . com/files/Amazon-dynamo-sosp 2007 . pdf](https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf)

[https://research.google/pubs/pub27898/](https://research.google/pubs/pub27898/)

[https://en.wikipedia.org/wiki/CAP_theorem](https://en.wikipedia.org/wiki/CAP_theorem)

[https://en.wikipedia.org/wiki/Consistent_hashing](https://en.wikipedia.org/wiki/Consistent_hashing)

[https://www.mongodb.com/mongodb-architecture](https://www.mongodb.com/mongodb-architecture)

[https://en.wikipedia.org/wiki/HTTP/2](https://en.wikipedia.org/wiki/HTTP/2)

[https://en.wikipedia.org/wiki/Transport_Layer_Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)
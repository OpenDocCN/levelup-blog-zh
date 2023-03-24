# 面向 DevOps 工程师/ SRE 的 Redis 第 1 部分

> 原文：<https://levelup.gitconnected.com/redis-for-devops-engineers-dca8c548e9ef>

![](img/498c3973607c00c2778fccd5561186b1.png)

我一直想知道作为 DevOps 工程师，我需要了解 Redis 多少。为什么开发人员使用它，为什么它在技术社区很热门？

Redis 是一个开源的键值存储，它主要用于缓存，但是 Redis 不仅仅是一个缓存。它有一个内存中的数据结构存储，用作数据库、缓存和消息代理。

# **关于 Redis 你必须知道的事情**

**升级 Redis 版本** 较新版本的 Redis 可以在很多方面获得性能优化的好处，不仅仅是在内存管理方面。Redis 版或更高版本，5.0 版有更好的策略。如果您使用的 Redis 版本低于 4.0，请考虑将其升级到更高版本。

**完全禁用交换功能** 如果操作系统配置了交换功能，它可以将一些 Redis 数据转储到磁盘，稍后 Redis 试图访问这些数据时，可能需要很长时间才能将它们读回内存。

```
sysctl -w vm.swappiness=0
```

**更多内核！= Better++** Redis 是一个单线程进程，如果启用了持久性，它最多消耗两个内核。对于 Redis 实例，您不需要两个以上的核心。

**规模化成本高** Redis 不是银弹，规模化成本确实很高，因为它将数据存储在内存中，在这种情况下，您不需要那种内存级别的性能，请避免使用它，并转向更经济高效且易于操作的方式。

# 关于 Redis 您需要了解的命令。

**要了解平均延迟** 要快速检查 Redis 实例的平均延迟，可以使用:

```
redis-cli — latency
```

**Redis-Benchmark To study latency** 要确保您的 redis-server 行为正常，快速运行一个基准来研究延迟，您可以启动:

```
redis-benchmark -q -n 10000 -c 1 -d average_size_of_your_objects_in_bytes
```

这不是真正的“负载测试”。
除 Redis-benchmark 以外。很少有其他工具可以对 Redis 进行基准测试。
-Memtier-Benchmark
-YCSB
-perf kit 基准

**找出 Redis** 变慢的原因，因为 Redis 没有详细的日志。通常很难跟踪实例内部到底发生了什么。Redis 为您提供了 commandstat 实用程序来展示这一点:

```
127.0.0.1:6379> INFO commandstats
cmdstat_get:calls=12,usec=608,usec_per_call=2.4
cmdstat_setex:calls=15,usec=71,usec_per_call=4.20
cmdstat_keys:calls=21,usec=42,usec_per_call=1.00
```

Commandstats 为您提供了所有命令的明细、执行所用的微秒数(每次调用的总数和平均值)以及它们已经运行了多少次。

**数据库** 监视器发生了什么是一个调试命令，它将 Redis 服务器处理的每个命令流回。它有助于了解数据库发生了什么。

```
redis-cli monitor
```

但是运行 MONITOR 是有代价的，因为它流回所有命令，它的使用是有代价的。

**重置 Redis 统计数据** 重置这个简单的运行配置 RESETSTAT，你就有了一个全新的状态。

```
CONFIG RESETSTAT
```

**了解内存相关问题** MEMORY DOCTOR 命令报告 Redis 服务器遇到的不同内存相关问题，并建议可能的补救措施。

```
MEMORY DOCTOR
```

**服务器的内存使用情况。** MEMORY STATS 命令返回一个关于服务器内存使用情况的数组回复。它报告的内存统计列表。点击了解更多信息[。](https://redis.io/commands/memory-stats)

```
MEMORY STATS
```

**了解一个 Redis 实例的角色** 角色命令返回该实例当前是主、从还是哨兵的信息。该命令还返回关于复制输出状态的附加信息，作为以下三个字符串之一:
【主】
【从】
【哨兵】

# 使聚集

**不要随着生产工作负载的增加而淹没一个实例** 将工作负载分散到多个 Redis 实例上。Redis 集群现在从版本 3.0.0 开始可用。Redis Cluster 允许您根据键的范围在给定的主/从组之间拆分键。
如果集群不是一个选项，那么考虑在多个实例中分配名称空间和你的键。在 redis.io 网站上可以找到一篇关于数据分区的精彩文章。

**高可用性和复制**

Redis 有两个运行多个实例的主要类别。有标准复制和 Redis 集群。要管理复制，您可以使用 Redis Sentinel。集群主要是自我管理，但是您可以将两者结合起来以获得扩展体验，如果复杂的话，还可以进行 HA+复制。

Redis Sentinel 是一个久经考验的高可用性解决方案，许多用户已经在生产中运行它。始终将 Redis Sentinel 视为 HA(高可用性)解决方案。

如何在 Redis 中设置复制或集群，以分散负载并超越单实例内存能力。

> 进入第二部分

**开发者为什么要用 Redis？**

*   它快得惊人！
*   他们使用它进行缓存，这有助于节省数据库调用，减少数据访问延迟，增加吞吐量，并减轻关系数据库或
    NoSQL 数据库的负担。这也有助于节省一些成本，
*   Redis 有助于以亚毫秒级的响应时间提供频繁请求的项目。它使开发人员能够轻松地扩展更高的负载，而不会增加更昂贵的后端。
*   不同的开发人员将它用于不同的用例，如数据库查询结果缓存、持久会话缓存、网页缓存和常用对象(如图像、文件和元数据)的缓存，这些都是 Redis 缓存的常见示例。
*   一些开发者甚至用它来进行实时分析。Redis 使用 Apache Kafka 等流解决方案作为内存中的数据存储，以亚毫秒级延迟接收、处理和分析实时数据。
*   我们都知道程序中的数据结构非常重要，开发人员广泛使用他们各自编程语言的数据结构，但在某些例外情况下，他们使用 Redis 作为数据结构服务器。是的，Redis 有数据结构，如字符串、哈希、列表、集合、带范围查询的排序集合、位图、超级日志、带 radius 查询的地理空间索引和流

**进一步阅读** [https://Redis labs . com/WP-content/uploads/2016/03/15-Reasons-Caching-is-best-with-Redis-Redis-1 . pdf](https://redislabs.com/wp-content/uploads/2016/03/15-Reasons-Caching-is-best-with-Redis-RedisLabs-1.pdf)
[https://gist . github . com/Jon cole/925630 df 72 be 1351 b 2140625 ff 2671 f](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f)
[https://rtfm . co . ua](https://rtfm.co.ua/en/draft-eng-redis-main-configuration-parameters-and-performance-tuning-overview/)

## 如果这篇文章有帮助，请点击拍手👏按钮下面几下，以示你对作者的支持！⬇
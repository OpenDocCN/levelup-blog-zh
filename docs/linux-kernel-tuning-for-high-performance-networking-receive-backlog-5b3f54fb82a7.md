# 接收积压队列

> 原文：<https://levelup.gitconnected.com/linux-kernel-tuning-for-high-performance-networking-receive-backlog-5b3f54fb82a7>

## 高性能网络系列的 Linux 内核调优

![](img/40215f8045db3b3ed22c7f583365c6ba.png)

由[托马斯·詹森](https://unsplash.com/@thomasjsn?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

**接收积压队列**是三次握手期间存储数据包的第一个队列。要回顾三次握手，这里是入门的链接:

[](/linux-kernel-tuning-for-high-performance-networking-5999a13b3fb4) [## 高性能网络的 Linux 内核调优:TCP/IP 连接初级读本

### TCP/IP 连接初级程序

TCP/IP 连接 Primerlevelup.gitconnected.com](/linux-kernel-tuning-for-high-performance-networking-5999a13b3fb4) 

> 本文将介绍 linux 内核 v2.4.20 到当前版本中与接收积压队列相关的信息，尽管内核设置可能出现在早期版本中。

# TCP 接收队列和 netdev_max_backlog

在网络堆栈能够处理数据包之前，每个 CPU 内核可以在环形缓冲区中保存大量数据包。如果缓冲区填满的速度比 TCP 栈能够处理它们的速度快，则丢弃的数据包计数器将增加，它们将被丢弃。应该增加`**net.core.netdev_max_backlog**`设置，以最大化在具有高突发流量的服务器上排队等待处理的数据包数量。

> 这是针对每个 CPU 内核的设置，因此需要记住这一点。

为了确定该设置是否需要增加，可以监视 softnet_stat 文件中丢失的数据包。此时只有前三列是有趣的:

```
# cat /proc/net/softnet_stat
000dfbfa 000000df 00000022 ...
000f2d7a 000000b1 0000003e ...
...
```

该文件中的每一行都对应于一个 CPU 内核，第一列对应于内核处理的数据包数量，第二列保存丢弃的数据包数量，第三列是 time_squeeze，即超出数据包处理预算的次数。

## 接收积压

如果列 2 在负载下增加，这表明内核设置`**net.core.netdev_max_backlog**`应该增加。继续增加，直到该列稳定。

推荐的方法是通过在 10 秒内改变第 2 列+一些缓冲来增加 backlog 值。示例:

t0:000000 DF
T10:000001 B2
T20:00000291
增加:DF (223)

在上面的例子中，backlog 增加 256 可能会为接收队列提供足够的容量。增加此设置，并在负载下监控一段时间，以确保设置正确。

## 时间紧迫

如果第 3 列在负载下增加，这表明 TCP 处理器无法在 CPU 预算耗尽之前处理所有可用的数据包。网络处理器有两个中断用于处理发送和接收。轮询新数据包的接收中断称为接收软中断，轮询称为 NAPI(新 API)轮询。唯一可以从网络接口卡(NIC)移除接收数据包进行处理的时间是在 NAPI 轮询期间。

这些内核设置影响时间压缩:

1.  `net.core.dev_weight` 每个 CPU 在 NAPI 中断期间驱动程序可以接收的最大数据包数量。
2.  `net.core.netdev_budget` 在一个 NAPI 轮询周期中接收的最大数据包数，所有接口/CPU 的总数。不能超过
3.  `net.core.netdev_budget_usecs` 一个 NAPI 轮询周期中以微秒计的时间。

NAPI 轮询在*网络开发 _ 预算 _ 使用秒*过去或*网络开发 _ 预算*达到后结束。

当 softnet_stat 中的第 3 列增加时，通常是由于 10Gbps 的高带宽向接口接收缓冲区添加了比 NAPI 轮询期间可以接收的更多的数据包。要调整处理器，在不超出预算的情况下删除更多数据包，请缓慢增加`net.core.netdev_budget`，直到第 3 列稳定。NAPI 处理的数据包数量也受到 *netdev_budget_usecs* 时间的限制，因此可能需要对两者进行一些调整。

# 结论

请记住，netdev 值需要 CPU 来处理，如果设置得太高，可能会对性能产生不利影响，因此保持较小的变化。

如果这篇文章中的任何信息不准确，请发表评论，我会更新文章以纠正信息。
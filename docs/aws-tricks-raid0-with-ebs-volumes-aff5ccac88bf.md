# AWS 技巧:带 EBS 卷的 RAID0

> 原文：<https://levelup.gitconnected.com/aws-tricks-raid0-with-ebs-volumes-aff5ccac88bf>

![](img/a8125a6881d9cefed4496e015d0be34d.png)

帕特里克·林登伯格(Patrick Lindenberg)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片——固态硬盘的照片看起来就没那么酷了。

(更新 2021:这项技术不再相关。如果您需要额外的 IOPS 或吞吐量，您更适合使用 gp3。)

今年早些时候，我帮助在 EC2 实例上对 gunicorn 应用程序进行了负载测试。这是运行现代 Linux 发行版的第五代 EC2 实例，gunicorn 在单个 [EBS gp2](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-volume-types.html) 卷上写日志文件。令我们惊讶的是，我们注意到在记录到磁盘上的文件时，它是 I/O 绑定的。

*甚至*更令人惊讶的是，将 EC2 实例更改为在两个 EBS 卷上使用 RAID0 配置*效果非常好*，我们能够将该 EC2 实例上的 gunicorn 工作线程数量增加一倍，直到它达到下一个限制因素。

## Python 中的日志记录是同步的

在 Python 2 和 Python 3 中，默认的日志处理程序是同步的和 I/O 阻塞的。记住这一点很重要。此外，StreamHandler 还将[获取一个锁](https://github.com/python/cpython/blob/master/Lib/logging/__init__.py#L1058-L1067)，用于阻塞写入的持续时间。这对于延迟和并发性来说是个坏消息，尤其是如果您有一个多线程应用程序，它通过一个共享的 [FileHandler](https://github.com/python/cpython/blob/master/Lib/logging/__init__.py#L1121) 写入磁盘上的一个日志文件。

```
def flush(self):
    """
    Flushes the stream.
    """
    self.acquire()
    try:
        if self.stream and hasattr(self.stream, "flush"):
            self.stream.flush()
    finally:
        self.release()
```

有几种方法可以将应用程序迁移到异步日志，比如使用 Python 3 中内置的 [QueueHandler](https://docs.python.org/3.2/library/logging.handlers.html#queuelistener) 、 [aiologger](https://github.com/B2W-BIT/aiologger) 、logbook 的 [ThreadedWrapperHandler、](https://logbook.readthedocs.io/en/stable/api/queues.html#logbook.queues.ThreadedWrapperHandler)一些更激烈的方法，比如 [ZeroMQHandler](https://logbook.readthedocs.io/en/stable/api/queues.html#zeromq) 。尽管如此，其中一些需要对调用点进行重大更改，而 QueueHandler 只在 Python 3.2 中可用。不是所有人都那么幸运。

## gevent 不在磁盘 I/O 上切换

我意识到，当我第一次了解 gevent 时，我只是简单地记住了“gevent 在遇到阻塞 I/O 时会切换 greenlets”。我还愚蠢地假设阻塞 I/O 包括磁盘 I/O(很明显)，但从未真正测试过我的理解。

我花了一段时间才注意到 gevent 文档中的所有例子都使用了网络套接字*例如*查询 DNS，或者写入 TCP 套接字。

写入磁盘 I/O 不会切换 greenlets。

原来 gevent 在写入磁盘时并不切换 greenlets。

当 gunicorn 与 gevent workers 一起使用时，这意味着同一进程中的所有 greenlets 是:

*   使用共享 FileHandler 追加到同一个日志文件中，
*   在写入磁盘之前获取互斥体，
*   在等待冲洗完成的同时保持锁定，以及
*   在通过 EBS 网络发送字节时暂停。

每次调用`logger.info`时都会发生这种情况，而另一端的客户端正在等待 HTTP 响应。

![](img/b1c8d2f86e296691f0e50e535e72052c.png)

Christian Fregnan 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 资源视角:解读 CloudWatch

在尝试优化日志记录器之前，我们希望在 CloudWatch 上明确证明我们在单个 EBS 卷上达到了资源限制。这种努力是徒劳的。`VolumeQueueLength`、`VolumeWriteBytes`和`VolumeWriteOps`指标有损耗(可能是抽样的？)，并且经常无法捕获我们正在寻找的短暂 I/O 峰值。此外，我们在图表中找不到任何天花板或平台，这表明我们达到了一些 EBS 上限。我们也没有动用我们的`BurstBalance`。

我们后来了解到，EC2 和 EBS 都有多层限制，这使研究变得混乱。

*   每个 [EBS 卷都受到 IOPs(每 GiB 3 IOPS)和吞吐量(250 MiB/s)的限制](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-volume-types.html)，这取决于 I/O 大小和卷大小。
*   每个 [EC2 实例都受到 IOPs 和吞吐量的限制](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-optimized.html)，这些也取决于 I/O 大小和实例大小。

对于小于`4xlarge`的 EC2 实例，吞吐量限制有以下警告:

> 这些实例类型每 24 小时至少可以支持 30 分钟的最高性能。如果您的工作负荷需要持续 30 分钟以上的最高性能，请根据下表所示的基准性能选择一个实例类型。

然而，我们发现随着负载测试的增加，平均写入延迟(毫秒/操作)增加，这是 EBS 上资源争用的迹象。也有足够的线索可以推断，在一些 HTTP 请求中，gunicorn 向磁盘发出了足够多的写入，达到了单个 EBS 卷的吞吐量限制(但不是 IOPs 限制)。这些峰值通常持续不到 1 秒，但足以导致受影响请求的响应时间下降。

```
Avg Write Latency = (Sum(VolumeTotalWriteTime) / Sum(VolumeWriteOps)) * 1000
```

## 工作负载视角:分析主机

在 EC2 实例上，我们运行了经典工具(iostat、dstat、iotop ),并努力隔离问题。我们已经知道`%iowait`很高；具体来说，我们希望确定使用磁盘 I/O 的进程，以及我们需要的写吞吐量，以便我们可以适当地调整 EBS 卷的大小。

这些工具提供了进一步的证据来支持我们的假设，即 gunicorn 的磁盘写入量短暂超过 EBS 限制，并且还帮助我们识别和减少浪费，例如来自辅助进程的磁盘写入(*例如* sendmail)，以及重复日志记录(*例如* supervisord 日志记录、docker 日志记录)。

我也尝试使用[新的 bpf 工具，](http://www.brendangregg.com/Perf/bpf_book_tools.png)但是对 bpf 的理解不足以解释和使用结果。

## RAID0 来救援

针对 EBS 吞吐量限制的建议解决方法是[将 RAID0](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/raid-config.html) 与 mdadm 配合使用，以便在多个 EBS 卷之间进行条带化写入。每个 EBS 卷的大小都应该能够提供最大 250 MiB/s 的吞吐量。条带化使应用程序可用的总写入吞吐量翻倍，但也增加了系统故障的可能性，因为当任何一个条带化的 EBS 卷出现故障时，整个装载都会失败。

一个合理的替代方案是使用 ZFS 进行条带化，而不是 mdadm/ext4。事实上，通过在 ZFS *启用压缩，即*在将数据块发送到 EBS 之前对其进行压缩，ZFS 可以进一步降低所需的写入吞吐量。这需要 CPU，非常适合受 I/O 限制且未充分利用主机上可用 CPU 资源的应用程序。

## 参考

*   [https://www . rhytrifice tech . com/blog/AWS/understanding-GP2-volume-performance/](https://www.rhythmictech.com/blog/aws/understanding-gp2-volume-performance/)
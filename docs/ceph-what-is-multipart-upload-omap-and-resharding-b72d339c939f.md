# FCeph——什么是多部分上传、OMAP 和重新共享？

> 原文：<https://levelup.gitconnected.com/ceph-what-is-multipart-upload-omap-and-resharding-b72d339c939f>

![](img/40f6c4502b4d0a3366d3ed6cd91dad9e.png)

# 什么是多部分？

通常，使用 multipart，您可以分部分上传大型对象文件。多部分包括三个步骤，如下所述。

多部分上传的好处如下:

*   暂停/恢复必要的部分；
*   提高性能—更高的吞吐量
*   在创建过程中上传对象。

# 三个步骤:

*   **Multipart Upload Initiation:**当请求上传一个对象文件时，首先得到的是上传 ID。这是您上传的唯一编号/标识符。
*   **零件上传:**重要的是要记住，除了上传 ID，我们还需要零件 ID。这意味着每次上传都有上传 ID 和零件 ID。请注意，如果您使用现有零件 ID 上传新文件，此零件将被覆盖。
*   **多部分上传完成或中止**:为了完成多部分流程，我们需要完成所有部分的上传。只有当过程完成时，我们得到所有部分都没问题的 ACK，然后我们才能将上传标记为完成。请注意，如果上传过程被中止，则多部分过程会停滞，永远不会结束，除非有生命周期规则，或者您再次重新上传多部分对象文件。

# 我们来练习一下:

首先，您可以使用 Ceph-Nano 项目创建一个 Ceph 集群。在这里你可以找到更多关于它的细节:[https://github.com/ceph/cn](https://github.com/ceph/cn)。

一旦集群开始工作并准备就绪，请按照以下步骤来理解 Ceph 池中的多部分是什么样子的。

让我们看看一个对象上传后在池中的样子。从上传对象开始:

```
aws s3 cp avi_test s3://test --endpoint-url [http://localhost:8080](http://localhost:8080)
```

然后，让我们使用以下命令查看名为: **default.rgw.buckets.data** 的池:

```
rados ls -p default.rgw.buckets.data
```

默认情况下，该池包含用户通过 RGW 上传到集群的所有数据。输出应该类似于:

```
cdeb898f-18fb-4509-b886-5bd67c627abb.14119.1_avi_test
```

请注意**cdeb 898 f-18fb-4509-b886–5bd 67 c 627 abb . 14119 . 1**是铲斗标记。

为了实现多部分，我们可以使用 awscli 工具。不得不说，用户端应该为多部分 chuncks 设置多部分阈值。让我们用 dd 命令创建一个:

```
dd if=/dev/zero of=testfile bs=1024 count=10240
```

您也可以使用 truncate 命令:

```
truncate -s 20M text.txt
```

让我们上传这个文件。默认情况下，awscli 工具支持多部分上传。

```
aws s3 cp testfile s3://test --endpoint-url [http://localhost:8080](http://localhost:8080)
```

再次键入命令“rados ls -p pool name ”,请注意这一次，它看起来像是:

```
cdeb898f-18fb-4509-b886-5bd67c627abb.14119.1__multipart_testfile.2~Ebr8ghHTE-SGwufDxS_GaDp6WnZ9AA7.1
```

正如我前面提到的，我们有一个上传 ID 和一个零件 ID:

***UploadID:***. 2 ~ EBR 8 ghhte-SGwufDxS _ gadp 6 wnz 9 aa 7 |***PartID:***。1(在行尾)

此外，如果我们运行命令:

```
radosgw-admin bucket stats | grep "bucket name" -A 15
```

我们将看到存储桶 id 没有变化:

```
"id": "cdeb898f-18fb-4509-b886-5bd67c627abb.14119.1"
```

当使用 multipart 时，对象被上传到名为:**" default . rgw . buckets . non-EC "**的池中。当多部分未被使用时，该池将为空。只有当 multipart 运行时，我们才会看到该池中的对象。

要查看所有多部分上传状态，请运行以下命令:

```
aws s3api list-multipart-uploads --bucket my-bucket
```

# 垃圾收集器:

当用户删除文件或上传同名文件时，文件会被覆盖(也是在 re-multipart 中)，Ceph 会将它们插入到一个叫做 GC 的东西中。

Ceph 不会立即删除文件，我们可以使用以下命令列出所有计划删除的文件:

```
radosgw-admin gc list
```

并且:

```
radosgw-admin gc list --include-all
```

指定-包含-全部列出所有条目，包括未过期的条目。

默认情况下，Ceph 在 gc 周期之间等待 2 个小时。要手动运行 gc 删除过程，请运行:

```
radosgw-admin gc process --include-all
```

# Ceph 索引和重新分级

## 什么是 OMAP？

索引对象的 OMAP 包含池中所有对象的键值。

重要的是这个池叫做:**" default . rgw . buckets . index "**。基本上它是桶的索引。让我们运行命令:

```
rados ls -p default.rgw.buckets.index
```

我们将看到桶 id，从״.dir.״:开始

```
.dir.cdeb898f-18fb-4509-b886-5bd67c627abb.14119.1
```

要列出该存储桶中的对象，请编写以下命令:

```
rados listomapkeys -p default.rgw.buckets.index .dir.cdeb898f-18fb-4509-b886-5bd67c627abb.14119.1
```

输出:

```
avi_test
```

有些情况下，OMAP 会变得越来越大，例如:一个 OMAP 中有一千万个对象。这可能会影响我们的表现。基本上，我们在发送写请求时读取索引，然后它搜索应该写入对象的正确位置。

正因为如此，我们可以使用重阴影选项，这个命令获取一个大的 OMAP 并将其切割成碎片。因此，对于一个桶，我们将有几个 OMAPs。最佳实践是每个分片 100，000 个对象。

要检查每个存储桶的状态，我们可以使用以下命令:

```
radosgw-admin check limit | grep "OVER 100.000000%"
```

这个命令为我们找到具有大 OMAPs 的存储桶。

部分输出:

```
"fill_status": "OVER 100.000000%"
```

这意味着桶超过了限制，我们需要进行重新授权过程。

重要的事情:请注意，当重散列过程正在运行时，这个桶没有 I/O，尽管对于不太大的桶来说，这是一个相当快的过程。我的建议是，在每一个 reshard 过程之前，请咨询支持人员。

要列出所有存储桶，请使用以下命令:

```
radosgw-admin bucket list
```

一旦我们发现有问题的存储桶，我们可以使用命令:

```
radosgw-admin bucket reshard --num-shard 2 --bucket=test
```

我们将看到，现在我们有两个 OMAPS:

```
rados ls -p default.rgw.buckets.index.dir.cdeb898f-18fb-4509-b886-5bd67c627abb.14270.1.0
.dir.cdeb898f-18fb-4509-b886-5bd67c627abb.14270.1.1
```

基本上，我们会看到部分文件位于第一个 OMAP，而其他文件位于第二个。

要更改每个分片的最大对象数的值，请编辑该值(请事先咨询支持人员):

```
rgw_max_objects_per_shard = 100000 to something else.
```

# 什么是 RGW 动态桶指数重排序？

基本上，在大型环境中工作，重新共享过程可能会非常令人沮丧。这个特性可以帮助解决这个问题，它检测有问题的存储桶，并在后台运行进程，为这个存储桶创建所需的碎片。

## 它是如何工作的？

当一个新对象被添加到桶中时。此外，还有一个定期运行的自动后台进程。一旦我们有了所需的存储桶，这个存储桶就被添加到 reshard 队列中。

要列出列表中当前的所有存储桶，请键入以下命令:

```
radosgw-admin reshard list
```

要检查流程的状态，请执行以下操作:

```
radosgw-admin reshard status --bucket <bucket_name>
```

启用/禁用动态存储桶索引重散列，在/etc/ceph/ceph.conf 的 rgw 部分下进行更改。

```
rgw_dynamic_resharding = true
```
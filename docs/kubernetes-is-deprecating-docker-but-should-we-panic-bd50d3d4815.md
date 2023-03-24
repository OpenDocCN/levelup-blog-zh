# Kubernetes 是在贬低 Docker，但我们应该恐慌吗？

> 原文：<https://levelup.gitconnected.com/kubernetes-is-deprecating-docker-but-should-we-panic-bd50d3d4815>

## Docker 一开始就应该在里面吗？

![](img/f4844787f4ba1b613fa29426f7f087c5.png)

由 [Kelvin Ang](https://unsplash.com/@kelvin1987?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/server?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在发行说明中提到 Kubernetes 放弃对 Docker 的支持引起了很多人的关注。

> kubelet 中的 Docker 支持现在已被否决，并将在未来的版本中删除。kubelet 使用了一个名为“dockershim”的模块，它实现了对 Docker 的 CRI 支持，并且在 Kubernetes 社区中发现了维护问题。我们鼓励您评估迁移到一个容器运行时，它是 CRI 的一个成熟的实现

Kubernetes SIG 安全联席主席 Ian Coldwater 的一条[推文](https://twitter.com/IanColdwater/status/1334149283449352200)也于事无补。

> Docker 支持在 Kubernetes 中已被否决。你需要注意这一点，并为此做好计划。这将打破你的集群

对于那些一直忙于生产集装箱而没有关注 Kubernetes 发展的人来说，这一举动可能会令人震惊。但是这真的没什么大不了的。

因此，在我们深入研究这一变化之前，让我们先来看看 Docker 在高层做了些什么:

1.  Docker 可以构建容器映像
2.  Docker 可以从容器注册表中推和拉
3.  Docker 可以创建容器进程
4.  Docker 是一个容器运行时。

变化是 Kubernetes 在 1.20 版之后不再把 Docker 作为**容器运行时**，这是 kubernetes.io [帖子](https://kubernetes.io/blog/2020/12/02/dont-panic-kubernetes-and-docker/)中解释的。因此，您仍然可以构建容器，在注册表中使用它们，以及推和拉。所有发生的事情就是 Docker 作为一个底层运行时被弃用，而支持 CRI 运行时。

有了这些知识，让我们更详细地了解 Kubernetes 是如何使用 Docker 作为底层运行时的，以及这个特殊的变化。

Kubernetes 是一个编排工具，它将许多不同的计算资源(如虚拟/物理机器)组合在一起，并使其看起来像一个巨大的计算资源，供您的应用程序运行并与其他人共享。在当前的架构中，Docker 作为一个容器运行时，仅用于在实际的主机中运行那些应用程序。

Docker 是该运行时的一个流行选择，但是 Docker 并没有被设计成嵌入到 Kubernetes 中。Docker 为程序员提供了许多用户增强功能，但是 Kubernetes 不需要这些。Kubernetes 需要的部分就是' *containerd* '。

此外，Docker 不符合 CRI，即[容器运行时接口](https://kubernetes.io/blog/2016/12/container-runtime-interface-cri-in-kubernetes/)。正因为如此，Kubernetes 使用了一种叫做“ *dockershim* ”的桥接服务。维护 dockershim 已经成为 Kubernetes 维护者的沉重负担。Dockershim 一直是一个临时的解决方案。创建 CRI 标准就是为了减轻这种负担，并允许不同容器运行时之间的平稳互操作性。Docker 本身目前没有实现 CRI，因此出现了问题。

总结一下，Kubernetes 反对 Docker 的两个主要原因是:

1.  dockershim 的管理开销。
2.  码头工人对中国国际广播电台不满意

# 那么，影响是什么呢？

对于 Kubernetes 的最终用户来说，这一举动应该不会有太大的影响，因为开发人员解释说“ *Docker 生成的映像将继续在所有运行时在您的集群中工作，因为它们总是有*”。

这并不意味着 Docker 的死亡，也不意味着你不能，或者不应该再使用 Docker 作为开发工具。Docker 仍然是构建容器的有用工具，运行`docker build`得到的图像仍然可以在 Kubernetes 集群中运行。

所以没什么好担心的。无论如何，你都有足够的时间来解决这个问题。在 1.20 中唯一改变的是如果使用 Docker 作为运行时，在 kubelet 启动时打印一个警告日志。

有关更多详细信息，请参考这些文档:

[](https://kubernetes.io/blog/2020/12/02/dont-panic-kubernetes-and-docker/) [## 不要惊慌:Kubernetes 和 Docker

### 作者:豪尔赫卡斯特罗，达菲库利，凯特科斯格罗维，贾斯汀加里森，诺亚坎特罗威茨，鲍勃基伦，雷伊莱亚诺，丹“流行”…

kubernetes.io](https://kubernetes.io/blog/2020/12/02/dont-panic-kubernetes-and-docker/) [](https://kubernetes.io/blog/2020/12/02/dockershim-faq/) [## Dockershim 弃用常见问题

### 本文档介绍了一些常见问题，这些问题与 Dockershim 作为……

kubernetes.io](https://kubernetes.io/blog/2020/12/02/dockershim-faq/)
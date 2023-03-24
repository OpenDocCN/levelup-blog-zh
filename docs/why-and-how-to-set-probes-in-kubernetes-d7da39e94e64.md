# 为什么以及如何在 Kubernetes 中设置探头？设计强大的 K8s 集群

> 原文：<https://levelup.gitconnected.com/why-and-how-to-set-probes-in-kubernetes-d7da39e94e64>

![](img/fbbc44b865296aa9664997ef9c24042b.png)

照片由 [L N](https://unsplash.com/@younis67?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/probe?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

很难设计一个具有**多个相互依赖的服务**的健壮 K8s 集群。通常，如果某个核心服务崩溃，依赖于它的所有服务都会失败……这是意料之中的，但是我们应该能够查明导致失败的服务，并在无需任何手动操作的情况下重启/回滚。K8s **活性**、**准备就绪**探头和`**minReadySeconds**`设置前来救援。

让我们逐一查看这些设置，看看为什么、如何以及在何种组合中使用它们:

# **K8s 活性探针**

`kubelet`使用 [**活性探测器**](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) 来知道**何时重启容器**。例如，活跃度探测器可以捕获死锁，此时应用程序正在运行，但无法取得进展。在这种状态下重启容器有助于提高应用程序的可用性。许多长时间运行的应用程序最终会转换到中断状态，除非重新启动，否则无法恢复。Kubernetes 提供了活性探针来检测和补救这种情况。

活性可以通过以下方式检查:

*   活跃度命令(`exec`

```
livenessProbe:
   exec:
     command:
     - cat
     - /tmp/healthy
   initialDelaySeconds: 5
   periodSeconds: 5
```

*   活跃度 HTTP GET 请求(`httpGet`

```
livenessProbe:
   httpGet:
     path: /healthz
     port: 8080
     httpHeaders:
     - name: Custom-Header
       value: Awesome
   initialDelaySeconds: 3
   periodSeconds: 3
```

*   活跃度 TCP 探测(`tcpSocket`

```
readinessProbe:
   tcpSocket:
     port: 8080
   initialDelaySeconds: 5
   periodSeconds: 10
 livenessProbe:
   tcpSocket:
     port: 8080
   initialDelaySeconds: 15
   periodSeconds: 20
```

**重启策略:**

注意:在 Pod 中重新启动容器(它刷新容器上的所有内容)不应与重新启动 Pod 相混淆。Pod 不是一个进程，而是一个运行容器的环境。Pod 会一直存在，直到被删除。

一个 PodSpec 有一个带有可能值`Always`、`OnFailure`和`Never`的`restartPolicy`字段。默认值为`Always`。`restartPolicy`适用于 Pod 中的所有容器。`restartPolicy`仅指在同一节点上通过`kubelet`重启容器。由`kubelet`重启的已退出容器以五分钟为上限的指数后退延迟(10 秒、20 秒、40 秒……)重启，并在成功执行十分钟后重置。正如 Pods 文档中所讨论的，一旦绑定到一个节点，一个 Pod 将永远不会重新绑定到另一个节点。

# **在 K8s 中配置探头**

[**探测器**](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#probe-v1-core) 有许多字段，您可以使用这些字段来更精确地控制活动和就绪检查的行为:

*   `initialDelaySeconds`:容器启动后，活动或准备就绪探测启动前的秒数。默认为`0`秒。最小值为`0`。
*   `periodSeconds`:执行探测的频率(秒)。默认为`10`秒。最小值为`1`。
*   `timeoutSeconds`:探头超时前的秒数。默认为`1`秒。最小值是`1`。
*   `successThreshold`:探针失败后被认为成功的最少连续成功次数。默认为`1`。必须是`1`才能活跃度。最小值是`1`。
*   当一个探测器失败时，Kubernetes 会在放弃前尝试失败阈值次。在活性探测的情况下放弃意味着重新启动容器。如果是准备就绪探测，Pod 将被标记为未准备就绪。默认为`3`。最小值是`1`。

HTTP 探针具有可在`httpGet`上设置的附加字段:

*   `host`:要连接的主机名，默认为 pod IP。您可能希望在 httpHeaders 中设置“Host”。
*   `scheme`:用于连接主机的方案(HTTP 或 HTTPS)。默认为 HTTP。
*   `path`:HTTP 服务器上的访问路径。
*   `httpHeaders`:请求中要设置的自定义头。HTTP 允许重复的头。
*   `port`:集装箱上要访问的端口的名称或编号。数字必须在`1`到`65535`的范围内。

对于 TCP 探针，`kubelet`在节点上建立探针连接，而不是在 pod 中，这意味着您不能在`host`参数中使用服务名，因为`kubelet`无法解析它。

# **K8s 就绪探测器**

`kubelet`使用 [**准备就绪探测器**](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) 来知道什么时候**集装箱准备好开始接受交通**。当一个**箱**的所有容器都准备好时，该箱被视为准备就绪。该信号的一个用途是控制哪些 pod 用作服务的后端。当一个 Pod 未就绪时，它将从服务负载平衡器中删除。

有时，应用程序暂时无法提供流量服务。例如，应用程序可能需要在启动时加载大量数据或配置文件，或者在启动后依赖外部服务。在这种情况下，您不希望终止应用程序，但也不希望向它发送请求。

就绪探测器的配置类似于活跃度。唯一的区别是您使用了`readinessProbe`字段而不是`livenessProbe`字段。

就绪和活性探测可以并行用于同一容器。使用这两种方法可以确保流量不会到达没有准备好的容器，并且容器在失败时会重新启动。

# K8s 启动探测器

`kubelet`使用 [**启动探测器**](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) 来了解容器应用程序何时启动。如果配置了这样的探测器，它会禁用活性和就绪性检查，直到成功，确保这些探测器不会干扰应用程序的启动。这可用于对缓慢启动的容器进行活性检查，避免它们在启动和运行前被`kubelet`杀死。

# Pod 的最小就绪秒数

`[.spec.minReadySeconds](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#min-ready-seconds)`是一个可选字段，它指定了一个新创建的 Pod 在没有任何容器崩溃的情况下被读取的最小秒数，这样它才被认为是可用的。默认为`0`(Pod 一准备好就被认为是可用的)。

一旦一个 Pod 的容器被启动，如果它通过了`readinessProbe`(如果它通过了)，它就不会被认为是准备好了(接受流量)。然后，当 Pod 的所有容器都准备好时，就认为 Pod 准备好了。但是，如果为 Pod 定义了`[.spec.minReadySeconds](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#min-ready-seconds)`设置，那么直到 Pod 经过了`.spec.minReadySeconds`定义的秒而没有任何容器崩溃，它才仍然被认为是就绪的。

**这里有一些相关的有趣故事，你可能会觉得有帮助:**

*   [让我们让 Kubernetes CLI (](https://medium.com/@goyalmunish/lets-make-kubernetes-cli-easier-5ba7f9c0509a) `[kubectl](https://medium.com/@goyalmunish/lets-make-kubernetes-cli-easier-5ba7f9c0509a)` [)变得更简单](https://medium.com/@goyalmunish/lets-make-kubernetes-cli-easier-5ba7f9c0509a)
*   [docker exec 命令中的主机与容器环境变量](https://medium.com/@goyalmunish/passing-host-vs-container-environment-variables-to-docker-exec-5c1b18e6de8e)
*   [Docker 简体！](https://medium.com/@goyalmunish/docker-simplified-ad1f8a7350bf)
*   [Kubernetes 简体！](https://medium.com/@goyalmunish/kubernetes-simplified-300fef5fb0e6)
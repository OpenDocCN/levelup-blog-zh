# 排除 Kubernetes 上应用程序故障的 5 个技巧

> 原文：<https://levelup.gitconnected.com/5-tips-for-troubleshooting-apps-on-kubernetes-835b6b539c24>

在经历了从 [Docker](https://www.docker.com) 到 Docker Swarm，再到 [Kubernetes](https://kubernetes.io) 的转变，并处理了这些年来各种 API 的变化之后，我已经能够很轻松地找出我的部署中的问题并找到解决方法。

事情并不总是那样，你必须从某个地方开始，也许那就是你今天所处的位置——开始？无论你在旅途中的哪个地方，我都想给你 5 个我认为有用的故障排除技巧，以及一些额外的使用技巧。

# 介绍你的“瑞士军刀”

![](img/8368a8821ca00f9047ff78d44b89cd29.png)

好的工具显示出使用的迹象。

你的瑞士军刀(如果你来自美国，读“皮革人”)是一种多用途工具，像所有好的工具一样，应该好好使用，有点磨损。

而且你猜对了，是`kubectl`。让我们从 5 个“附件”开始，以及当事情出错时你如何使用它们。

场景将会是:**我的 YAML 被接受，但是我的服务没有启动**和**它启动了，但是不能正常工作**。

1.  `kubectl get deployment/pods`

这是您的第一个访问端口，您可能已经知道了，但是它如此重要的原因是它提供了顶级信息，而无需您进行大量输入。

如果您正在为您的工作负载使用一个部署(您应该这样)，那么您有几个选择:

*   `kubectl get deploy`
*   `kubectl get deploy -n namespace`
*   `kubectl get deploy --all-namespaces [or "-A"]`(不客气)

理想情况下，你希望看到 1/1 或等价的，2/2 等。这将表明您的部署已被接受，并已尝试部署某些东西。

接下来，您可能想要查看`kubectl get pods`以查看部署的后备 Pod 是否正确启动。

2.`kubectl get events`

我很惊讶我不得不经常向那些对 Kubernetes 有问题的人解释这个小宝贝。这个命令打印出给定名称空间中的事件，对于发现关键问题非常有用，比如崩溃的 pod 或无法提取的容器图像。

Kubernetes 中的日志是*未排序的*因此，您将需要添加以下内容，摘自 [OpenFaaS 文档](https://docs.openfaas.com/deployment/troubleshooting/)。

```
$ kubectl get events --sort-by=.metadata.creationTimestamp
```

`kubectl get events`的表亲是 kubectl describe，就像`get deploy/pod`一样，它使用对象的名称:

```
kubectl describe deploy/figlet -n openfaas
```

你会在这里得到非常详细的信息。在我们忘记之前，您可以描述大多数事情，包括`nodes`，它将显示您是否由于资源限制或其他问题而无法安排 Pod。

3.`kubectl logs`

你知道这一天要来了，但还是有很多人用错了方法。

如果您有一个部署，比如说在`cert-manager`名称空间中的`cert-manager`，那么许多人认为他们首先必须找到 Pod 的长(唯一)名称，并将其用作他们的参数。不对！

```
kubectl logs deploy/cert-manager -n cert-manager
```

要跟踪日志，请添加`-f`

```
kubectl logs deploy/cert-manager -n cert-manager -f
```

为了避免获取每个可能的日志条目，您还可以使用`--tail`将它限制在最后几行:

```
kubectl logs deploy/cert-manager -n cert-manager --tail 100
```

你可以把这三者结合起来。

如果您的部署或 Pod 有任何标签，您可以使用`-l app=name`或任何其他标签集来附加到一个或多个匹配 Pod 的日志中。

```
kubectl logs -l app=nginx
```

有一些像 [stern](https://github.com/wercker/stern) 和 [kail](https://github.com/boz/kail) 这样的工具可以帮助你匹配模式并节省一点打字时间，但是我发现它们会分散注意力。

4.`kubectl get -o yaml`

当你开始使用由另一个项目或者像 Helm 这样的工具生成的 YAML 时，你会很快需要它。在生产中检查图像的版本或您在某处设置的注释也很有用。

```
kubectl run nginx-1 --image=nginx --port=80 --restart=Always
```

我是用“总是”重启还是“从不”重启来部署的？

```
kubectl get deploy/nginx-1 -o yaml
```

现在我们知道了。更重要的是，我们可以添加`--export`并将 YAML 保存在本地，以编辑它并再次附加它。

编辑 YAML 现场直播的另一个选项是`kubectl edit`，如果你被`vim`卡住了，不知道发生了什么，在命令前面加上`VISUAL=nano`会更容易编辑。

5.`kubectl scale` —你又开又关了吗？

`kubectl scale`可用于将部署及其 pod 缩减至零个副本，实际上是杀死所有副本。当您将比例放大到 1/1 时，将创建一个新的 Pod，重新启动您的应用程序。

语法非常简单，您可以重新启动代码并再次运行测试。

```
kubectl scale deploy/nginx-1 --replicas=0
kubectl scale deploy/nginx-1 --replicas=1
```

您可以使用的替代方法是`kubectl rollout restart deploy/nginx-1.`

要知道一个部署是否“准备好”,使用`kubectl rollout status deploy/nginx-1`,它将阻塞，直到通过健康检查。你可能还需要使用一个延长的超时，或者一个无限的超时`--timeout=0s` / `--timeout=60s`。

6.端口转发

我知道我说过有 5 条建议，但我们还需要一条。通过 kubectl 的端口转发允许我们在自己的计算机上公开本地或远程集群上的服务，以便在任何已配置的端口上访问它，而无需在互联网上公开它。

下面是一个访问 Nginx 部署的例子，但是是在本地:

```
kubectl port-forward deploy/nginx-1 8080:80
```

有些人认为这只适用于部署或 pod，他们错了。服务是公平的游戏，通常是正确的选择，因为它们会模仿生产集群中的配置。

如果你想在互联网上公开一个服务，你通常会使用一个负载平衡器服务，或者运行`kubectl expose:`

```
kubectl expose deployment nginx-1 --port=80 --type=LoadBalancer
```

另一种选择是使用云本地隧道，如[入口运营商](https://github.com/inlets/inlets-operator)。

## 现在尝试一下

我希望这 6 个命令和提示对你有用。现在轮到您在真实的集群上测试它们了。

您可以在笔记本电脑上创建集群，或者使用托管云产品。

我最喜欢的地方发展选项是:

*   [带 k3sup 的树莓 Pi 集群](https://medium.com/@alexellisuk/walk-through-install-kubernetes-to-your-raspberry-pi-in-15-minutes-84a8492dc95a)
*   [k3d (k3s 在 Docker 中运行)](https://github.com/rancher/k3d/releases)
*   [k3sup —通过 SSH 将 k3s 安装到任何虚拟机或机器上](https://k3sup.dev/)

当然还有[KinD——Docker](https://blog.alexellis.io/be-kind-to-yourself/)里的 Kubernetes。

你喜欢这篇文章吗？你想看更多吗？[通过 GitHub 赞助商订阅我的优质简讯](https://github.com/sponsors/alexellis)或[在 Twitter/LinkedIn 上关注我](https://www.alexellis.io/)
# 面向工程师和开发人员的 Kubernetes 备忘单命令

> 原文：<https://levelup.gitconnected.com/top-kubernetes-cheat-sheet-commands-for-engineers-and-devops-b64da4b1834>

## 所有方便的命令都在一个地方

![](img/f4844787f4ba1b613fa29426f7f087c5.png)

由 [Kelvin Ang](https://unsplash.com/@kelvin1987?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/server?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Kubernetes 是一个开源的容器编排平台，可以自动化应用程序的部署、管理和扩展。在 2014 年开源之前，Kubernetes 最初是由谷歌的工程师开发的。许多大公司现在都采用 Kubernetes 作为他们的容器编排选项。

我使用 Kubernetes 已经有一段时间了，我整理了一个我经常使用的命令列表。我已经为我的自助创建了一个文档，现在与这里的工程师们分享。

我根据使用模式将命令分成了多个部分。

# 上下文和名称空间

Kubernetes 支持由同一个物理集群支持的多个虚拟集群。这些虚拟集群被称为**名称空间**。它们为名称提供了一个范围，用于在不同用户之间分隔资源。

**获取当前名称空间**

```
kubectl get namespace
*NAME              STATUS   AGE
default           Active   1d
....*
```

**永久设置上下文以备将来使用**

这将防止在任何未来的 kubectl 命令中添加 namespace 标记。

```
kubectl config set-context --current --namespace=<namespace-name>
*Context "cluster-1" modified.*
```

# 创建对象

*Apply* 是在生产中管理 Kubernetes 应用程序的推荐方式。apply 命令可以创建任何对象或资源，如 pod、副本集、服务等

**来自 YAML**

如果存在现有的 YAML 文件(或 JSON ),我们可以创建所需的资源

```
kubectl apply -f createStatefulSet.yaml *// create from single file**service/nginx created
statefulset.apps/myweb created**kubectl apply -f ./file1.yaml -f ./file2.yaml // create from multiple files*kubectl apply -f ./dirName *// create from a directory*
```

**创建 YAML**

如果 YAML 文件不存在，我们可以创建一个 YAML 文件，然后根据我们的要求进行修改。下面是我们如何创建一个样本 YAML

```
kubectl run nginxpod --image nginx:1.11 --port 80 -o yaml --dry-run > nginxpod.yamlvi nginxpod.yamlapiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    run: nginxpod
  name: nginxpod
spec:
  replicas: 1
  selector:
    matchLabels:
      run: nginxpod
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        run: nginxpod
    spec:
      containers:
      - image: nginx:1.11
        name: nginxpod
        ports:
        - containerPort: 80
        resources: {}
status: {}
```

这个 YAML 文件可用于通过 apply 构建所需的资源。

# **删除对象**

如果不需要资源，我们可以通过删除资源直接杀死它们。

```
kubectl delete pod/task-pv-pod *// delete a pod* pod "task-pv-pod" deletedkubectl delete pvc <pvc-name> // deletes a particular volume
```

如果 pod 由部署或有状态集支持，我们需要删除它。否则豆荚会被重新创造。

```
kubectl delete deployment <deployment-name>
```

**使用 YAML**

我们还可以使用 YAML 删除资源。这将删除使用 YAML 文件创建的所有资源

```
kubectl delete -f statefulSet.yaml*service "nginx" deleted
statefulset.apps "myweb" deleted*
```

# 跟踪资源的状态

一旦创建了资源，就会有命令来检查它们的状态。

```
kubectl get pods // Lists all podsNAME                     READY   STATUS    RESTARTS   AGEdata-0                    1/1     Running   0         8d
data-1                    1/1     Running   0         7d4h
master-79fd87b5dc-s9vxl   1/1     Running   0         37h
query-85d86c88db-fnjwb    1/1     Running   0         12d
busybox1                  1/1     Running   239        9d
```

> 名称:资源名称
> 就绪:表示 pod 是否启动并运行。如果有一些问题，数值将为 0/1
> 状态:pod 调出的状态。其他值可以包含创建、逐出等
> 重新启动:pod 在其历史中重新启动的次数
> 年龄:pod 启动的持续时间

其他类似的命令如下:

```
kubectl get pods -o wide *// detailed description of the pods*
kubectl get pod data-0 -o yaml *// returns the yaml of the pod*
kubectl get deployments *// returns the deployments controlling the pods* kubectl get pvc *// returns the volumes*
```

# 资源日志和调试

既然已经创建了资源，那么了解它是启动了还是失败了是至关重要的。如果资源有任何问题，以下命令将有助于破译。

```
kubectl describe pod/busybox1
Events:Type    Reason   Age From   Message----    ------   --------  --------- Normal  Pulled   49m (x24 over 23h)  kubelet, nodename  **Container image "busybox" already present on machine**Normal  Created  49m (x24 over 23h)  kubelet, nodename  **Created container busybox**Normal  Started  49m (x24 over 23h)  kubelet, nodename  **Started container busybox**
```

上面的命令给出了创建过程中的事件。如果在创建 pod 的过程中有任何失败，应该可以在这里看到。

下面提到了一些其他相关命令:

```
kubectl logs busybox1 *// dump the logs of the pods to* *stdout* kubectl logs -l name=myLabel *// logs by label*
kubectl logs busybox1 -c container1 *//dump pod* *busybox1* *container logs (for multi-container case)* 
```

# 与豆荚互动

有时，获取有关正在运行的 pod 的详细信息非常重要。这可以通过与 pod 的交互来实现。

**运行 Pod 中的命令**

```
kubectl **exec** busybox1 -- lsbin
...
usr
var
```

**进入集群进行更多交互式查询**

该命令允许您进入容器，直接对应用程序进行故障排除。如果您无法通过查看日志进行调试，这将很有帮助。

```
kubectl exec -it busybox1 /bin/sh/ #/ # ls -lrt
total 16
drwxr-xr-x    2 root     root         12288 Sep  8 18:09 bin
...
drwxrwxrwt    2 root     root             6 Sep  8 18:09 tmp
drwxr-xr-x    3 root     root            18 Sep  8 18:09 usr
```

**将数据从你的机器复制到 pod**

有时我们需要将文件从您的机器复制到 pod。虽然在 prod 中，这需要通过编程来完成，但是出于测试和调试的目的，您可以使用这个命令来复制。

```
kubectl cp /dir/file namespace/pod-data:/dir1/
```

**获得吊舱的生命值**

有时需要检查 pod 的内存和 CPU。这可以通过 top 命令来实现。

```
kubectl top pod data-0NAME               CPU(cores)   MEMORY(bytes)
data-0   1484m        24Mi
```

这些是我在日常工作中经常使用的命令

→创建 pod 和其他资源

→调试盒和其他资源

→删除 pod 和其他资源

希望这些备忘单命令能够帮助您更加熟悉 Kubernetes。你练习得越多，你就变得越好。
# Kubernetes 的“芝麻开门”

> 原文：<https://levelup.gitconnected.com/the-open-sesame-of-kubernetes-d656639f330f>

命令行工具充当用户和系统之间的桥梁。有一个构建良好的 CLI 来使一项技术或工具更加强大并惠及社区是至关重要的。 **kube-see-tee-el** 或 **kube-cuttle** 就是一个被证明的例子。Kubectl 是 Kubernetes 成功背后的主要支柱之一。

> 命令行工具是相应应用程序的“芝麻开门”咒语。如果我们有效地使用它们，我们的生活会变得更轻松。

在我们日复一日的 Kubernetes 任务中，大部分时间我们把自己限制在 kubectl **获取、描述、删除、创建、应用**、和**编辑**。在这篇文章中，我将分享我最近遇到的一些有趣的(隐藏的宝藏)kubectl 用例。

*   **kubectl 首次展示重启**

有些情况下，我们必须重新启动部署或状态集中所有正在运行的 pod。我们要么一个一个删除它们，要么缩小到 0，然后扩大规模(如果可以接受停机时间)。我们可以通过卷展重新启动命令使这个过程更容易和更快。
`**$ kubectl rollout restart [deployment|sts] <deployment/sts>**`

*   **kubectl 详细**

Verbose 是 CLI 非常需要的命令。它节省了我们排除故障的时间。说到 kubectl，我们通常使用**描述**命令来收集详细信息。但是 kubectl 有自己的冗长子命令。默认的 kubectl 详细级别是 1。我们可以看到，它在一次操作后立即向控制台打印一个简短的响应，像`pod/nginx created`、`Error from server (NotFound): pods “mysql” not found`。我们可以提高详细级别，以获得更全面的日志，也可以看到正在发生的事情。


*   **kubectl 补丁**

当我们使用 Kubernetes 时，在许多情况下我们必须修改清单。在这种情况下，`kubectl edit`是最常用的命令。如果我们做一个小的更新，Edit 是好的，但是有时当我们修改一长串 YAML 清单的行时，它会唤醒 YAML。如果是这样的话，我们可能不得不花今天剩下的时间来修正 YAML 的错误。我们可以通过使用`kubectl patch`命令来避免这种情况。
`**$ kubectl patch [kind] <resource> -p <yaml content>**` **。**

通过应用`kubectl patch`，我们也可以将一个元素合并到一个数组中。

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
 name: developers
roleRef:
 apiGroup: rbac.authorization.k8s.io
 kind: ClusterRole
 name: developers
subjects:
- kind: ServiceAccount
  name: dev
  namespace: dev
```

现在，我想向主题添加/合并一个新的服务帐户。如果我使用`kubectl patch`，它将覆盖现有的内容。但是我不想。

```
$ kubectl patch clusterrolebinding devevloper — type=’json’ -p=’[{“op”: “add”, “path”: “/subjects/1”, “value”: {“kind”: “ServiceAccount”, “name”: “qa”,”namespace”: “qa” } }]’
```

> *看一下这个命令，在* `*p=*` *参数下我已经指定了* `*op*` *:这是一个添加操作* `*path*` *:将一个元素添加到数组主题的位置一* `*value*` *:服务账号详情*

样本输出，

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
 name: dev
roleRef:
 apiGroup: rbac.authorization.k8s.io
 kind: ClusterRole
 name: dev
subjects:
- kind: ServiceAccount
  name: dev
  namespace: dev
- kind: ServiceAccount
  name: qa
  namespace: qa
```

*   **库贝特尔差异**

这个`kubectl diff`已经和 Kubernetes 住在一起很久了。但是在 1.18 版中，它已经作为一个稳定的特性被提升了。它显示了活动对象和我们要应用的对象之间的变化。我们可以确保在更新对象之前没有做任何不必要的更改。
`**$ kubectl diff -f <file>.yml**`

> 尽管它在 1.18 版中被宣布为一个稳定的功能，但实际上并不是。在 1.18 版中，如果你输入`*kubectl diff*`，它会自动应用修改，而不是显示差异。在 v1.18.1 中已经修复了，所以，在使用`*kubectl diff*`之前，请确保你的 Kubernetes 版本是 1.18.1 或以上。

*   **kubectl 转换**

我们可以在每个 Kubernetes 版本中找到至少一个 API 弃用。在 Kubernetes 升级期间，当我们不得不更新现有对象时，这是很痛苦的。`kubectl convert`将根据我们的集群版本更新对象。
`**$ kubectl convert -f <file>.yml**`

以后就好办了。

> kubectl convert 已被弃用，将在未来版本中删除。
> 为了进行转换，kubectl 将对象应用到集群中，然后 kubectl 得到想要的版本。

感谢您的阅读！
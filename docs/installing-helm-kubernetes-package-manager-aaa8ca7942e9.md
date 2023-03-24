# 安装 Helm，Kubernetes 软件包管理器

> 原文：<https://levelup.gitconnected.com/installing-helm-kubernetes-package-manager-aaa8ca7942e9>

![](img/11906c480f1871ccac25dbc003005ec2.png)

标志由 [helm.sh](https://helm.sh/)

了解如何使用 Helm package manager 将多个 Kubernetes 资源的部署配置为一个单元。

[Helm-CLI](#c0ac)
∘ [安装](#f20b)
∘ [卸载](#a398)
[图表](#e295)
∘ [搜索](#8403)
∘ [安装](#749c)
∘ [升级](#b4b9)
[总结](#bfa1)

> **注:**这个帖子是一个*TL；DR* 分享 Helm v3 的基本用法，这是读者入门所需的，其他信息请遵循[官方文档](https://helm.sh/docs/)。

# Helm-CLI

大多是*TL；DRs* 用于安装舵 CLI。​

## 安装

使用你最喜欢的软件包管理器安装，参考[官方文档](https://helm.sh/docs/intro/install/)获取更多信息。

```
*# tl;dr for macOS*
brew install helm
```

## 从计算机上卸载

遵循您之前使用的软件包管理器的删除说明。

```
*# tl;dr for macOS*
brew uninstall helm
```

# 图表

舵图是舵管理的实际*“包”。图表是另一个 YAML 文件，它由自定义属性组成，最终被转换成 Kubernetes YAML 资源并部署到集群中。*

***为什么？**我们可以将多个资源打包到一个 Helm chart 中，作为一个单元进行处理，也就是说，使用一个命令就可以安装/更新/卸载，而不是管理我们自己的多个资源以及它们各自的 Kubernetes YAML 资源文件。图表可以是简单的 hello-world 应用程序，也可以是成熟的 web 应用程序，如服务器、数据库、缓存等..*

> ***注意:**在接下来的章节中，我将使用`traefik`代理作为掌舵图的例子。*

## *搜索*

*为了能够搜索图表，我们必须找到返回其元数据的相应 Helm 存储库。在 [artifacthub.io](https://artifacthub.io/) 中搜索您希望使用的图表的舵库。*

*1.我们将添加托管`traefik`图表元数据的`containous` Helm 存储库*

```
*helm repo add traefik [https://containous.github.io/traefik-helm-chart](https://containous.github.io/traefik-helm-chart)*
```

*2.更新本地舵图表存储库缓存*

```
*helm repo update*
```

*3.按名称搜索特定图表*

```
*helm search repo traefik*# NAME              CHART VERSION   APP VERSION*
*# traefik/traefik   9.1.1           2.2.8**
```

## *安装*

*直接从命令行安装舵图。如需更多安装选项，请阅读此处的。*

```
*helm install traefik \
    --namespace traefik \
    traefik/traefik \
    --version 9.1.1*
```

> ***提示:**通过提供标志`--install`，可以对两个安装&升级使用相同的舵命令。如果以所提供的名称命名的版本还不存在，将会触发 install 命令。
> 将`helm install`替换为`helm upgrade --install`*

## *提升*

*使用新的图表版本升级或覆盖以前部署的图表的现有值。欲了解更多升级选项，请阅读此处的。*

```
*helm upgrade traefik \
    --namespace traefik \
    --set**=**"additionalArguments={--log.level=DEBUG}" \
    traefik/traefik \
    --version 9.1.1*
```

> ***重要提示:**当更新预装舵图上的部分属性时，将重新创建与这些特定属性相关的 Kubernetes 资源窗格，以应用新的更改。*

# *摘要*

*这篇文章只是关于舵图可能性的冰山一角，请参考官方[舵文档](https://helm.sh/docs/)进一步阅读。*

*请留下您的评论、建议或任何其他您认为与本文相关的意见。*

***喜欢这个帖子？**
您可以通过以下方式了解更多信息:*

*查看我的博客:[https://blog.zachinachshon.com](https://blog.zachinachshon.com/)
在推特上关注我: [@zachinachshon](https://twitter.com/zachinachshon)*

*感谢阅读！❤️*
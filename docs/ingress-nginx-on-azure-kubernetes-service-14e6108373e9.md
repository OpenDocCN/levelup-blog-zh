# Azure Kubernetes 服务上的入口 Nginx

> 原文：<https://levelup.gitconnected.com/ingress-nginx-on-azure-kubernetes-service-14e6108373e9>

## 如何在单个主机上公开多个服务

![](img/3e7d18a00f4ee6b25dee0be63a0367c9.png)

照片由[乔丹·哈里森](https://unsplash.com/@jordanharrison?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

将一个应用程序部署在由多个微服务组成的 Kubernetes 集群上，您可能希望公开其中的一些微服务，以便可以通过互联网进行访问。虽然这显然是为了您的 web 应用程序服务，但是您可能还想公开一些额外的 API。

在 Kubernetes 的世界里，任何与你的微服务的连接都是通过使用*服务*资源来完成的。使用 Kubernetes *服务*资源的类型*负载平衡器*利用底层云提供商创建特定于云提供商的负载平衡器，用于通过外部 IP 公开微服务。这种方法的问题是，每个微服务将暴露在一个单独的 IP 地址下。

让它们暴露在同一个主机下会方便得多，同时有不同的路径到达专用微服务，对吗？

本文展示了如何使用 Azure 上的 Kubernetes 集群和流行的 Nginx 来实现这一点。这是我关于使用 Azure 应用网关和 Traefik 的文章的后续。许多内容将基于那篇文章。

# 介绍

微服务可以使用 Kubernetes *服务*资源在 Kubernetes 内部和外部公开。到目前为止，一切顺利。但是如前所述，如果我们想在集群之外公开它们，使用类型为 *LoadBalancer* 的服务资源，我们最终会为每个微服务提供不同的 IP。这不是我们想要的，相反，我们想让它们暴露在一个和主机使用不同的路径。

这就是 Kubernetes *Ingress* 资源派上用场的地方。把一个*入口*想象成 Kubernetes 服务之上的一层。它是访问我们微服务的流量的单一入口，微服务根据指定的规则将流量路由到不同的 Kubernetes *服务*。

库伯内特斯*入口*资源的概念是抽象的。为了使用 Kubernetes *入口*，你必须安装一个特定的*入口控制器*。Kubernetes *Ingress* 抽象有很多不同的实现。Nginx 和 Traefik 是其中两个在 Kubernetes 和开源社区中非常受欢迎的工具。

当然，我们还有云提供商，你可以使用负载平衡器和网关等资源作为 Kubernetes *入口*。无论如何，在本文中，我们将重点关注 [*入口 Nginx*](https://kubernetes.github.io/ingress-nginx/) 。

# 先决条件

以下是完成整个过程所需的一些先决条件:

*   Azure CLI 已安装
*   获取您拥有全局管理员角色的 Azure 订阅
*   kubectl 已安装
*   jq 已安装
*   已安装 Helm CLI

# 准备 Azure 环境

在创建 AKS 集群之前，我们从准备 Azure 环境开始。

## 创建新的资源组

我们要做的第一件事是在 **eastus** 区域创建一个名为 **k8srg** 的新资源组。因此，您首先必须登录您的 Azure 帐户。以下命令将打开一个带有单点登录站点的浏览器，您可以在其中输入凭据。

```
az login
```

成功登录后，CLI 将列出您帐户的所有可用订阅。选择您的订阅。

```
az account set --subscription <subscription id>
```

现在我们可以实际上我们的新资源组。

```
az group create --name k8srg --location eastus
```

## 创建新的虚拟网络

当使用 *Ingress Nginx* 时，无论何时您想要公开一个微服务，Nginx 内部都会创建一个指向特定微服务的新路由。为了让连接工作，Nginx 和 Kubernetes 必须在同一个 Azure Vnet 中。否则，连接将无法建立，您最终将拥有不健康的连接。

首先，我们创建一个包含子网的新虚拟网络。此外，我们为 Nginx 创建了一个公共 IP。

# 创建 AKS 实例

现在我们可以开始创建一个简单配置的 Kubernetes 集群了。

列出的命令将在 **eastus** 区域创建一个名为 **k8srg** 的资源组，并在其中创建一个 Kubernetes 集群。Kubernetes 集群被命名为 **myk8s** ，包含 1 个 worker 节点。最后一个命令将使您的`kubectl`连接到新创建的 **myk8s** Kubernetes 集群。

# 部署入口 Nginx

将 *Ingress* Nginx 部署到您的 Kubernetes 集群基本上有两种方式。要么通过编写自己的部署清单文件来“手动”部署它，要么使用 helm。我们将在本文中使用 helm。

## 入口 Nginx 配置

像所有其他舵图一样，*入口* Nginx 的配置可以通过使用文件或 CLI 参数覆盖默认值来完成。我们将使用包含以下内容的文件:

参见[本](https://github.com/kubernetes/ingress-nginx/blob/main/charts/ingress-nginx/values.yaml)了解配置文件可能性的完整概述。不过现在先说配置。

这里没什么特别的事。我们指定入口控制器应该分配到哪个外部 IP，以及公共 IP 属于哪个资源组。使用以下命令获取我们之前创建的公共 IP:

```
az network public-ip show -g k8srg -n nginx-public-ip | jq .ipAddress -r
```

为了让*入口*控制器绑定公共 IP，我们需要使用以下命令授予 Kubernetes 身份对包含公共 IP 的资源组的访问权:

## 通过头盔安装入口 Nginx

现在我们已经有了配置文件，通过 Helm 的实际安装非常方便。只需运行以下命令:

您应该通过运行`kubectl get pods -n ingress-nginx | grep ingress-nginx`来检查安装是否成功，这应该会产生一个正在运行的 pod。

# 公开多个服务

最后，是时候使用不同的路由在同一台主机上公开多个服务了。因此，我们利用了我在 Dockerhub 上发布的一个小小的 Node.js 应用程序。这个应用程序是一个非常简单的服务器，它返回一个由环境变量指定的消息。

让我们看看部署清单是什么样子的，它公开了主机上某个路由下的服务。

文件的前两部分相对来说并不引人注意。请注意最后两个部分，它们配置了如何公开服务。在第 60 行中，我们定义了应该公开服务的路线。一个有趣的方面是第 54 行的注释，当实际与我们的微服务通信时，它去掉了路径。这是必需的，因为示例应用程序只有一个端点“/”。如果没有注释，对 *hostname/service1* 的请求将被路由到不存在的 *exampleapp/service1* 。

此时，您可以复制文件内容，创建您的文件并应用它们。我为您简化了这一过程，并创建了一个 GitHub repo，其中包含两个部署清单，每个清单公开了不同路径上的测试应用程序，而每个应用程序返回不同的响应。所以你只需要运行以下要点的命令:

如果一切顺利，我们的入口 Nginx 中应该有两个注册的路由指向每个服务。我们将使用公共 IP 对它们进行测试，并附上各自的路径:

`curl <pubIP>/service1`应返回“来自服务 1 的问候”,相应地`curl <pubIP>/service2`应返回“来自服务 2 的问候”。

# 解决纷争

由于在公开我们的微服务的过程中包含多个组件，如果某些东西不能按预期工作，您将不得不查看不同的地方。

*   首先，您应该检查所有 pod 是否都在运行。最重要的一个是*ingress-nginx-controller-**pod，而*等于随机字符串。
*   之后，您可以检查代表您的微服务的 pod 是否正常运行。此外，检查指向您的微服务的服务是否已创建并正在运行。不要忘记仔细检查它们是否指向正确的端口。
*   如果 *pods* 和*服务*运行正常，检查*入口资源*是否也被创建，并且它们分别指向正确的*服务*。接下来，您应该使用`kubectl describe ing service1-ingress -n ingress-nginx`检查*入口资源*的事件。
*   此外，检查您的*重写目标注释*是否做了正确的事情。
*   如果一切都设置正确，Nginx 控制器本身的日志可能会非常有用。因此只需运行`kubectl logs ingress-nginx-controller-*`。

# 结论

这是关于使用 Kubernetes *Ingress* 在单个主机上公开不同服务的第三篇文章。我第一篇文章用的是 Azure App Gateway，感觉很重量级。在第二篇文章中，我使用了 Traefik，感觉非常流畅。

我还没有机会长期使用 Ingress Nginx 或将其用于生产用例，但一如既往，没有最好的解决方案或技术。一切都取决于你的背景和要求。

我希望你喜欢阅读我的文章，并且你已经成功地公开了你的服务。我很想听听你对 Traefik 入口的体验。非常感谢任何反馈。

# 进一步阅读

*   [https://www.nginx.com](https://www.nginx.com)
*   [https://kubernetes.github.io/ingress-nginx/](https://kubernetes.github.io/ingress-nginx/)
*   [https://github . com/kubernetes/ingress-nginx/blob/main/charts/ingress-nginx/values . YAML](https://github.com/kubernetes/ingress-nginx/blob/main/charts/ingress-nginx/values.yaml)
*   [https://kubernetes.github.io/ingress-nginx/troubleshooting/](https://kubernetes.github.io/ingress-nginx/troubleshooting/)
*   [https://kubernetes . io/docs/concepts/workloads/controllers/deployment/](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
*   [https://kubernetes . io/docs/concepts/services-networking/ingress/](https://kubernetes.io/docs/concepts/services-networking/ingress/)
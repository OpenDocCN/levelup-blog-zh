# IKS 部署模式#4:多区域集群，通过负载平衡器公开应用，聚合整个区域的容量

> 原文：<https://levelup.gitconnected.com/iks-deployment-patterns-4-multi-zone-cluster-app-exposed-via-loadbalancer-aggregating-whole-ffc8613e0787>

我如何在我的 IBM Cloud Kubernetes 服务(IKS)中使用多区域集群中的 LoadBalancer 服务直接部署和公开我的应用程序，并通过使用整个区域(所有可用性区域)中的所有端点来聚合整个区域的容量？

# 示例部署模式

在本文中，我们将通过下面的部署模式来完成部署示例应用程序的步骤:

![](img/212c522b1c68ce0a053e9d9572fbb2df.png)

# 步伐

**1。**注册并使用 [IBM 云控制台](https://console.bluemix.net/)创建一个**多区域** IKS 集群。关于[部署集群](https://console.bluemix.net/docs/containers/cs_clusters.html#clusters)以及[多区域集群如何工作](https://console.bluemix.net/docs/containers/cs_clusters_planning.html#multizone)的文档。*重要提示:您必须使用付费层。*

**2。开个票！重要提示:**不幸的是，这需要手动操作。你必须[在门户](https://control.bluemix.net/support/unifiedConsole/tickets/add) ( *技术>基础设施>公网提问)*上开一张票，并在请求中添加以下内容:

> “请将网络设置为允许与我的帐户相关联的公共 VLANs 上的容量聚合。{如果您列出您的公共 VLANs 就更好了。}这个请求的参考票是:[https://control.softlayer.com/support/tickets/63859145](https://control.softlayer.com/support/tickets/63859145)”

**3。**下载并应用下面的[示例部署和服务资源 yaml](https://github.com/IBM-Cloud/kube-samples/blob/master/loadbalancer-alb/iks_multi-zone_cluster_app_via_LoadBalancer_and_aggregate_capacity.yaml) ，它将通过端口`1884`上的`LoadBalancer`服务在 yaml 中指定的三个可用区域中公开`echoserver`应用。
`$ kubectl apply -f [iks_multi-zone_cluster_app_via_LoadBalancer_and_aggregate_capacity.yaml](https://raw.githubusercontent.com/IBM-Cloud/kube-samples/master/loadbalancer-alb/iks_multi-zone_cluster_app_via_LoadBalancer_and_aggregate_capacity.yaml)`
注意:不要忘记编辑标有`NEED TO EDIT!`的行

**4。**检查`LoadBalancer`服务的 IP 地址:

![](img/311eb3aef0b41f75c7b8b76787b44aa7.png)

## 测试应用程序

1.  测试加载您在浏览器中指定的 IP:port 或启动`curl`命令(像我的例子):
    `$ curl [http://{your IP here}:1884/](https://echoserver.arpad-ipvs-test-aug14.us-south.containers.appdomain.cloud/)`
2.  您将看到如下所示的响应。(对每个`LoadBalancer` IP 地址运行它。)

![](img/7cbced88d5b523825d25905c5ef62b91.png)

您可以在`client_address`字段中看到源 IP 地址，因为我们在`LoadBalancer`服务资源中应用了`externalTrafficPolicy: Local`。在返回路径上，数据包使用本地网关离开 IBM Cloud。

# 摘要

随着您对工作负载的了解越来越多，您可以根据需要调整甚至切换模式。不同的应用需要不同的模式；请让我们帮助你的模式！要阅读其他模式，请点击链接到 IBM 云博客的[或 Medium.com 的](https://www.ibm.com/blogs/bluemix/2018/10/ibm-cloud-kubernetes-service-deployment-patterns-for-maximizing-throughput-and-availability/)[上的这个。](https://medium.com/@ArpadKun/ibm-cloud-kubernetes-service-deployment-patterns-for-maximizing-throughput-and-availability-88a23a99437f)

# 联系我们

如果您有任何问题，请在这里注册[，通过 Slack 加入我们的团队，并在我们的公共 IBM Cloud Kubernetes 服务 Slack 上的#general channel](https://bxcs-slack-invite.mybluemix.net/) 中加入讨论。
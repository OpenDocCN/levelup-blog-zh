# IKS ALB/入口控制器超时，Websocket 连接掉线；IBM IKS 备忘单#3

> 原文：<https://levelup.gitconnected.com/iks-alb-ingress-controller-timeouts-dropped-websocket-connections-ibm-iks-cheat-sheet-3-f82da6a8aeec>

![](img/b334b0c5c6de247c7fc5b8042d1fa41d.png)

照片由 [Neven Krcmarek](https://unsplash.com/@nevenkrcmarek?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我有超时问题，IKS ALB 过早掉线。我如何调整超时？我看到我的 websocket 连接在大约 60 秒后掉线…

IKS ALB / Ingress 控制器支持保活和 Websocket 连接。如果您看到过早断开连接，可能有许多不同的原因，但通常您会在堆栈的某个地方遇到超时。

调试时，请确保:

*   您使用的 Internet 连接没有任何不支持长期连接的代理或防火墙，
*   从多个位置进行调试和测试，如家庭 ISP、电话共享等。隔离行为是否一致。

# 超时设定

当您通过 ALB / Ingress 控制器连接到您的应用程序时，有两个配置超时的位置:

1.  `[Client] ===(*client-side-timeout*)=== [ALB]` -默认值为 8 秒，但仅针对 HTTP 保活请求，通常不会干扰 WS，但您可以测试一下。一旦在`[Client]`和`[ALB]`之间建立了连接，`[ALB]`将打开一个到运行您的应用程序的`[POD]`的新连接，因此在两者之间也有一个超时
2.  `[ALB] ===(*upstream-side-timeout*)=== [Application POD]` -默认为 60 秒

通常 Websocket (WS)连接的问题(当`[Application POD]`运行 WS 时)是与`*upstream-side-timeout*`有关的。确认的最佳方式[是检查 ALB 日志](https://console.bluemix.net/docs/containers/cs_troubleshoot_debug_ingress.html#errors)。

您有两个选择:启用心跳(Websocket)，增加超时。

# 启用心跳(Websocket)

最简单和最安全的方法是确保在 Websocket 应用程序中设置某种**心跳，以确保 **TCP 连接保持活动状态**。没有明显流量的长期 TCP 连接通常会被更激进的超时丢弃，这不仅可以发生在`ALB`中，也可以发生在位于客户端前面的透明代理防火墙中。使用像 WAMP 这样的框架，你可以启用心跳。为了安全起见，建议间隔时间为 58 秒或更短。**

# 增加超时

另一种选择是增加超时，只有当您知道网络中没有任何东西现在或将来会以更低的超时运行(透明代理、防火墙等)时，才建议使用这种方法。).

## 我如何改变`client-side-timeout`？

相关官方文档可在此处找到: [*增加保活连接时间*](https://console.bluemix.net/docs/containers/cs_ingress.html#perf_tuning) :

**1。)**编辑入口配置图

```
$ kubectl edit cm ibm-cloud-provider-ingress-cm -n kube-system
```

**2。)**添加/更改`keep-alive`值至您想要的值。(默认值为 8s，默认情况下，此行不出现在配置图中。)

配置图中的`keep-alive:`设置将改变`ALB nginx`配置中的`keepalive_timeout`设置。

```
apiVersion: v1
data:
  keep-alive: "300s"
kind: ConfigMap
metadata:
  name: ibm-cloud-provider-ingress-cm
  namespace: kube-system
```

**3。)**保存并验证:

```
$ kubectl get cm ibm-cloud-provider-ingress-cm -n kube-system -o yaml
```

另一种验证的方法，只是用你的名字替换你的 ALB pod 名字(要知道你的 ALB 名字是什么[在这里阅读我之前的帖子](https://medium.com/@ArpadKun/ibm-cloud-kubernetes-service-ingress-alb-cheat-sheet-1-basics-4fbc1c86b886)或[参见官方文档](https://console.bluemix.net/docs/containers/cs_troubleshoot_debug_ingress.html#errors)):

```
$ kubectl exec -ti *public-cr24a9f2caf6554648836337d240064935-alb2-5cbb674fd5-tff54* -n kube-system -c nginx-ingress -- grep -H -R keepalive_timeout /etc/nginx/nginx.conf/etc/nginx/nginx.conf:  keepalive_timeout 300s; # <-- Changed, good
```

## 我如何改变`upstream-side-timeout`？

相关文档在这里可以找到[，在这里你也可以学习如何应用注释。](https://console.bluemix.net/docs/containers/cs_annotations.html#connection)

**1。)**向入口资源添加以下注释，将 60 秒的默认值增加到 300 秒:

```
ingress.bluemix.net/proxy-read-timeout: "serviceName=<YOUR SERVICE NAME> timeout=300s"
```

`proxy-read-timeout`注释将改变`ALB nginx`配置中的`proxy_ready_timeout`设置。

**2。)**如何检查配置是否生效？

```
$ kubectl exec -ti public-cr24a9f2caf6554648836337d240064935-alb2-5cbb674fd5-tff54 -n kube-system -c nginx-ingress -- grep -H -R timeout /etc/nginx/conf.d//etc/nginx/conf.d/default-source-ip-ingress.conf:              proxy_connect_timeout 60s;  # <-- This stayed 60s, good and expected
/etc/nginx/conf.d/default-source-ip-ingress.conf:              proxy_read_timeout 300s;    # <-- Changed, we are good.
```

更多有用的文章:
—[IKS 入口/ALB 备忘单](https://medium.com/@ArpadKun/ibm-cloud-kubernetes-service-ingress-alb-cheat-sheet-1-basics-4fbc1c86b886)上的有用命令。
- [如何隔离、维护和调试 ALB/Ingress 控制器](https://medium.com/@ArpadKun/how-can-i-isolate-do-maintenance-and-debug-an-alb-ingress-controller-ibm-iks-cheat-sheet-2-29dab86b9c7d)
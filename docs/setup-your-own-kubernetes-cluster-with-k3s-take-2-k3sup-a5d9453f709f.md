# 使用 K3s — k3sup 设置您自己的 Kubernetes 集群

> 原文：<https://levelup.gitconnected.com/setup-your-own-kubernetes-cluster-with-k3s-take-2-k3sup-a5d9453f709f>

![](img/e06be365e89097d42713820351dfa642.png)

Joshua Sortino 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

尽管建立一个`k3s`集群就像吃馅饼一样简单，但是使用一个名为`k3s`的小助手会使它变得更加简单。

## 清除

请注意:在一个`k3s`集群就位的情况下，可能是因为[这个岗位](https://twissmueller.medium.com/setup-your-own-kubernetes-cluster-with-k3s-b527bf48e36a)，也很容易摆脱一切。

在服务器上，必须运行以下脚本:

```
/usr/local/bin/k3s-uninstall.sh
```

与此类似，在代理上:

```
/usr/local/bin/k3s-agent-uninstall.sh
```

在本地机器上，`~/.kube/config`下的 Kubernetes 配置也需要清理。

## 先决条件

除了拥有一堆虚拟机或裸机服务器之外，还需要已经设置了两个无密码 SSH 登录和无密码 sudo，以便使用`k3sup`。

下面是我快速整理的两篇解释这一点的文章:

*   [无密码 SSH 登录](https://twissmueller.medium.com/ssh-key-914fa5e9b4c1)
*   [无通行证的 sudo](https://twissmueller.medium.com/passwordless-sudo-9573f5fb1b6a)

当然，工具`k3sup`也是需要的。它可以通过执行以下命令来安装:

```
curl -sLSf https://get.k3sup.dev | sh
sudo install -m k3sup /usr/local/bin/
```

## 集群安装

一切就绪后，`k3s`服务器可以安装

```
k3sup install --ip <ip-of-server> --user <user-name> --local-path ~/.kube/config --context <mycontext>
```

或者，如果您想提供主机名而不是 IP

```
k3sup install --host <hostname-of-server> --user <user-name> --local-path ~/.kube/config --context <mycontext>
```

出于某种原因，`.kube/config`中的服务器地址是`server: https://127.0.0.1:6443`，我已经手动将其更改为我的主机。

通过执行以下语句来联接代理:

```
k3sup join --host <agent-hostname> --server-host <server-hostname> --user <username>
```

在本地机器上的一个好的测试是用

```
kubectl get nodes -o wide
```

尽情享受吧！

如果你喜欢这篇文章，请随时给我买杯咖啡！

## 资源

*   [K3sup](https://github.com/alexellis/k3sup)
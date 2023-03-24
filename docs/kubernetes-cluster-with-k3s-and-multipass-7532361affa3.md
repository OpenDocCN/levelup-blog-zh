# Kubernetes 采用 k3s 和 multipass 的多节点集群

> 原文：<https://levelup.gitconnected.com/kubernetes-cluster-with-k3s-and-multipass-7532361affa3>

![](img/a71f87b7560d4109cc7177a78b44c56d.png)

与 [Kubernetes](https://kubernetes.io/) 合作，您可能碰巧需要一个本地 Kubernetes 集群来进行开发和测试。当然，Minikube 是一个选择。但是如果我们需要更强大而不增加复杂性的东西呢？例如，如果我们正在准备参加 [CKAD:认证 Kubernetes 应用程序开发人员](https://www.cncf.io/certification/ckad/)认证呢？

这里是我个人对 OS X 的解决方案(但应该也能流畅地与 GNU/Linux 一起工作)，我想与你分享: [multipass](https://github.com/CanonicalLtd/multipass) + [k3s](https://github.com/rancher/k3s) 。

# 多通道的

首先，我们需要一个虚拟化层来运行任意数量的 Kubernetes 节点。很有可能，使用 Canonical Ltd .创建的 [multipass](https://github.com/CanonicalLtd/multipass) 在 OS X 上获得 [Ubuntu VM 的最简单方法是:](https://discourse.ubuntu.com/t/multipass-now-available-for-macos/6824)

> 这是一个协调虚拟机和相关 Ubuntu 映像的创建、管理和维护以简化开发的系统。

# k3s

因为我想让事情变得小而简单，所以我利用了由牧场主 k3s 创建的令人惊叹的 Kubernetes 项目。K3s 有望成为轻量级 Kubernetes:

> K3s 是经过[认证的 Kubernetes 发行版](https://www.cncf.io/certification/software-conformance/)，专为无人值守、资源有限、远程位置或物联网设备内部的生产工作负载而设计。

# 如何创建本地 Kubernetes 集群

KISS 风格，只需 3 个简单步骤中的 9 个命令即可设置一个基本的 3 节点 k3s 集群:

1.  安装多通道
2.  创建虚拟机
3.  创建 k3s 集群

## **第一步:安装多通道**

我想当然的认为 [brew.sh](https://brew.sh/) 在你的 Mac 里是不能少的。如果没有，请跟随[链接](https://brew.sh/)。之后，无非是:

```
brew cask install multipass
```

## **第二步:创建虚拟机**

假设我们希望 Kubernetes 集群有 1 个主节点(“`k3s-master`”)和 2 个(工作)节点(“`k3s-worker1`”和“`k3s-worker2`”)。

```
multipass launch --name k3s-master --cpus 1 --mem 1024M --disk 3G
multipass launch --name k3s-worker1 --cpus 1 --mem 1024M --disk 3G
multipass launch --name k3s-worker2 --cpus 1 --mem 1024M --disk 3G
```

## **步骤 3:创建 k3s 集群**

这里的事情变得有点棘手，需要注意以下几点:

*   在 k3s 版本 0.6.0 之前，`k3s-master` i̶s̶的部署按照[官方指令](https://k3s.io/)非常简单。添加`K3S_KUBECONFIG_MODE="644"`了解详情，跳至“步骤 4。配置 kubectl "
*   如果未正确配置，k3s 安装脚本将作为单节点群集启动。因为我们不希望这样，所以在部署`k3s-worker1`和`k3s-worker2`之前，我们需要来自主节点的两个信息，它们是:
    - `k3s-master` ip 地址，存储在`K3S_NODEIP_MASTER`变量
    - `k3s-master` k3s 令牌中，存储在`K3S_TOKEN`变量
    中，以便允许工作节点加入由`k3s-master`节点创建的同一个 k3s 集群。
*   `k3s-worker1`和`k3s-worker2`的部署仅仅是基于这两个变量的正确配置的问题。

```
# Deploy k3s on the master node
multipass exec k3s-master -- /bin/bash -c "curl -sfL [https://get.k3s.io](https://get.k3s.io) | K3S_KUBECONFIG_MODE="644" sh -"# Get the IP of the master node
K3S_NODEIP_MASTER="https://$(multipass info k3s-master | grep "IPv4" | awk -F' ' '{print $2}'):6443"# Get the TOKEN from the master node
K3S_TOKEN="$(multipass exec k3s-master -- /bin/bash -c "sudo cat /var/lib/rancher/k3s/server/node-token")"# Deploy k3s on the worker node
multipass exec k3s-worker1 -- /bin/bash -c "curl -sfL https://get.k3s.io | K3S_TOKEN=${K3S_TOKEN} K3S_URL=${K3S_NODEIP_MASTER} sh -"# Deploy k3s on the worker node
multipass exec k3s-worker2 -- /bin/bash -c "curl -sfL https://get.k3s.io | K3S_TOKEN=${K3S_TOKEN} K3S_URL=${K3S_NODEIP_MASTER} sh -"
```

**检查一切:**

```
multipass listName State IPv4 Release
k3s-worker2 RUNNING 192.168.64.5 Ubuntu 18.04 LTS
k3s-worker1 RUNNING 192.168.64.4 Ubuntu 18.04 LTS
k3s-master RUNNING 192.168.64.3 Ubuntu 18.04 LTSmultipass exec k3s-master kubectl get nodesNAME          STATUS   ROLES    AGE   VERSION
k3s-master    Ready    master   48s   v1.14.1-k3s.4
k3s-worker1   Ready    <none>   16s   v1.14.1-k3s.4
k3s-worker2   Ready    <none>   6s    v1.14.1-k3s.4
```

n̶o̶t̶e̶̶t̶h̶e̶̶`<none>`̶i̶n̶̶t̶h̶e̶̶`ROLES`̶c̶o̶l̶u̶m̶n̶.̶̶i̶n̶̶t̶h̶e̶̶s̶e̶c̶o̶n̶d̶̶p̶a̶r̶t̶̶o̶f̶̶t̶h̶i̶s̶̶s̶t̶o̶r̶y̶,̶̶w̶e̶'̶l̶l̶̶g̶e̶t̶̶t̶h̶e̶r̶e̶.*更新 2019 10 11:k3s 0 . 6 . 0 版本引入了节点角色和* `--node-label`和`--node-taint`标志。

就这样，Kubernetes 集群开始运行了。尽管如此，您可能想看看以下步骤:

# 如何创建一个本地 Kubernetes 集群(有意义)

在下文中，我将添加一些有用的技巧，使集群对于开发和测试非常有用:

4.配置 kubectl
5。配置集群节点角色和污点。舵安装
7。服务类型"节点端口"
8。入口控制器 [Traefik](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=2ahUKEwjKnILS0MriAhXSzaQKHf5xAjYQFjAAegQIBRAC&url=https%3A%2F%2Ftraefik.io%2F&usg=AOvVaw204mn6RPY_Znna2JH9mdFp)

## **步骤四。配置 kubectl**

> 更新 2019 10 11:k3s 0 . 6 . 0 版本出于[安全原因](https://github.com/rancher/k3s/issues/389)将默认`*k3s.yaml*`权限从 0644 更改为 0600。请看一下[https://github . com/rancher/k3s/issues/389 # issue comment-503616742](https://github.com/rancher/k3s/issues/389#issuecomment-503616742)。为了克服这种变化(在启动阶段),主要有两种解决方案:

*   使用`--write-kubeconfig-mode 644`

```
curl -sfL [https://get.k3s.io](https://get.k3s.io) | sh -s - --write-kubeconfig-mode 644
```

*   使用变量`K3S_KUBECONFIG_MODE`

```
curl -sfL [https://get.k3s.io](https://get.k3s.io) | K3S_KUBECONFIG_MODE="644" sh -s -
```

> 更新 20191011: k3s 版本 0 . 9 . 0[`k3s.yaml`中的“localhost”已被“127 . 0 . 0 . 1”](https://github.com/rancher/k3s/pull/750)替换，导致`sed`命令失败。

如果我们想忘记 multipass CLI，配置安装在您的主机上的`kubectl`直接使用全新的 k3s 集群是非常容易的(我假设`kubectl`已经安装在您的机器上，否则:`$ brew install kubernetes-cli`)。首先，我们需要从`k3s-master`节点检索 kubectl 配置文件，并用`k3s-master` IP 地址编辑它，如下所述:

```
# Copy the k3s kubectl config file locally
$ multipass copy-files k3s-master:/etc/rancher/k3s/k3s.yaml ${HOME}/.kube/k3s.yaml# Edit the kubectl config file with the right IP address
$ sed -ie s,https://127.0.0.1:6443,${K3S_NODEIP_MASTER},g ${HOME}/.kube/k3s.yaml# Check
$ kubectl --kubeconfig=${HOME}/.kube/k3s.yaml get nodes
```

指定`--kubeconfig`很无聊，而且最重要的是，如果我们忘记这么做，并且在错误的集群中运行错误的命令，这是很危险的(有人知道吗？没有吗？实际上！).将 kubectl 配置文件`k3s.yaml`与您当前的`${HOME}/.kube/config`文件合并可能是值得的，或者，由于我们的 k3s 集群不打算写在石头上，我们有几个简单的选项(详情请参见[官方文档](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)):

```
# Create a dedicated alias:
alias k3sctl="kubectl --kubeconfig=${HOME}/.kube/k3s.yaml"
```

或者

```
# Use the KUBECONFIG variable
export KUBECONFIG=${HOME}/.kube/k3s.yaml
```

## **第五步。配置集群节点角色和污点**

> 更新 20191011: k3s 版本 0.6.0 引入了节点角色和`--node-label`和`--node-taint`标志。因此**“第五步。”不再需要了**。

正如我们之前注意到的，这 3 个节点没有角色。这是因为:

*   K3s 安装脚本不会标记它创建的任何节点
*   K3s 安装脚本不会影响`NoSchedule`的主节点

让我们来关注这两种配置:

```
# Configure the node roles:
$ kubectl --kubeconfig=${HOME}/.kube/k3s.yaml label node k3s-master node-role.kubernetes.io/master=””
$ kubectl --kubeconfig=${HOME}/.kube/k3s.yaml label node k3s-worker1 node-role.kubernetes.io/node=””
$ kubectl --kubeconfig=${HOME}/.kube/k3s.yaml label node k3s-worker2 node-role.kubernetes.io/node=””# Configure taint NoSchedule for the k3s-master node
$ kubectl --kubeconfig=${HOME}/.kube/k3s.yaml taint node k3s-master node-role.kubernetes.io/master=effect:NoSchedule
```

现在，节点角色已正确配置:

```
$ kubectl --kubeconfig=${HOME}/.kube/k3s.yaml get nodes
NAME STATUS ROLES AGE VERSION
k3s-master Ready **master** 4h12m v1.14.1-k3s.4
k3s-worker1 Ready **node** 3h57m v1.14.1-k3s.4
k3s-worker2 Ready **node** 3h57m v1.14.1-k3s.4
```

最终，我们为部署做好了准备。我想强调的是，NGiNX pods 将只在`k3s-worker1`和`k3s-worker2`节点进行调度:

```
$ kubectl --kubeconfig=${HOME}/.kube/k3s.yaml run nginx --image=nginx --replicas=3 --expose --port 80
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.
service/nginx created
deployment.apps/nginx created$ kubectl --kubeconfig=${HOME}/.kube/k3s.yaml get pods -o wide
NAME READY STATUS RESTARTS AGE IP NODE NOMINATED NODE READINESS GATES
nginx-755464dd6c-6nzvr 1/1 Running 0 3h22m 10.42.1.3 **k3s-worker2** <none> <none>
nginx-755464dd6c-rkd6r 1/1 Running 0 3h22m 10.42.1.4 **k3s-worker2** <none> <none>
nginx-755464dd6c-v5v64 1/1 Running 0 3h22m 10.42.2.3 **k3s-worker1** <none> <none>
```

## **第六步:舵安装**

[Helm](https://helm.sh/) 试图为 Kubernetes 组合一个**模板引擎**和一个**包管理器**。头盔的安装可能不像我们习惯的那样简单，事实上 k3s 需要多几个步骤:

```
$ kubectl --kubeconfig=${HOME}/.kube/k3s.yaml -n kube-system create serviceaccount tiller$ kubectl --kubeconfig=${HOME}/.kube/k3s.yaml create clusterrolebinding tiller \
--clusterrole=cluster-admin \
--serviceaccount=kube-system:tiller$ helm --kubeconfig=${HOME}/.kube/k3s.yaml init --service-account tiller$ helm --kubeconfig=${HOME}/.kube/k3s.yaml install stable/mysql
```

如果你有兴趣了解更多关于 Helm 的信息，我在下面的故事中分享了我关于 Helm 图表库主题的经验:“[用 GitHub 页面创建一个公共的 Helm 图表库](https://medium.com/@mattiaperi/create-a-public-helm-chart-repository-with-github-pages-49b180dbb417)”

## **步骤 7:服务类型“节点端口”**

其实这一步并不是 k3s 特有的。我只想强调的是，`[NodePort](https://kubernetes.io/docs/concepts/services-networking/service/#nodeport)` [服务](https://kubernetes.io/docs/concepts/services-networking/service/#nodeport)的创建和往常一样简单，因为主机和虚拟机之间的网络对用户来说是透明的:

```
$ kubectl --kubeconfig=${HOME}/.kube/k3s.yaml create deploy nginx2 --image=nginx$ kubectl --kubeconfig=${HOME}/.kube/k3s.yaml create svc nodeport nginx2 --tcp=30001:80 --node-port=30001$ curl -XGET -s -I -o /dev/null -w "%{http_code}\n" [http://$(multipass](/$(multipass) info k3s-master | grep "IPv4" | awk -F' ' '{print $2}'):30001
200
```

## 步骤 8:入口控制器 [Traefik](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=2ahUKEwjKnILS0MriAhXSzaQKHf5xAjYQFjAAegQIBRAC&url=https%3A%2F%2Ftraefik.io%2F&usg=AOvVaw204mn6RPY_Znna2JH9mdFp)

我之前没有提到，但很容易相信，为了保持轻便，k3s 做出了一些妥协，其中之一是考虑到[入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)。为了让入口资源工作，集群必须有一个运行的[入口控制器](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/)(通常是 [NGiNX](https://www.nginx.com/products/nginx/kubernetes-ingress-controller) )。K3s 包括用于此目的的 [Traefik](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=2ahUKEwjKnILS0MriAhXSzaQKHf5xAjYQFjAAegQIBRAC&url=https%3A%2F%2Ftraefik.io%2F&usg=AOvVaw204mn6RPY_Znna2JH9mdFp) 。它还包括一个简单的服务[负载均衡器](https://github.com/rancher/k3s/blob/master/README.md#service-load-balancer)，可以为集群中的服务获取外部 IP。让我们看一个如何配置入口控制器的简单示例(只需用正确的 ip 地址编辑 YAML 文件):

```
$ cat <<EOF > ingress-controller.yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress
spec:
  rules:
  - host: **192.168.64.3**.xip.io
    http:
      paths:
      - path: /
        backend:
          serviceName: nginx2
          servicePort: 30001
EOF$ kubectl --kubeconfig=${HOME}/.kube/k3s.yaml apply -f ingress-controller.yaml
ingress.extensions/ingress created$ curl -XGET -s -I -o /dev/null -w "%{http_code}\n" [http://192.168.64.3.xip.io/](http://192.168.64.3.xip.io/)
200 
```

[nip.io](https://nip.io/) 是一个“针对任何 ip 地址的简单通配符 DNS ”,允许您将任何 IP 地址映射到一个主机名。

## 提示:如何通过 SSH 连接

如果您想更灵活地通过 SSH 而不是使用`multipass shell`连接到多通道虚拟机，您需要使用具有适当权限的路径(OS X)中的密钥，并使用`ubuntu`用户:

```
$ sudo cp -a /var/root/Library/Application\ Support/multipassd/ssh-keys/id_rsa ~/.ssh/multipass
$ sudo chown $USER ~/.ssh/multipass
$ ssh -i ~/.ssh/multipass ubuntu@*192.168.64.3*
```

## **最后一步:清理一切**

这是一次愉快的旅行，现在是时候摆脱一切了:

```
$ multipass stop k3s-master k3s-worker1 k3s-worker2
$ multipass delete k3s-master k3s-worker1 k3s-worker2
$ multipass purge
```

# **结论**

创建一个本地多节点 Kubernetes 集群是非常容易的，它具有很好的复杂性，同时又不失 Minikube 的简单性。此外，由于 multipass VMs 实现保证了 Kubernetes 调整的隔离和安全性，所描述的解决方案增加了显著的灵活性。

作为最后一个提示，我创建了一个 bash 脚本，它能够建立一个 k3s 环境，并配有 metrics-server、weavescope、prometheus 和 istio 之类的所有功能。非常欢迎您从中获得灵感:

[https://github.com/mattiaperi/k3s-multipass-cluster](https://github.com/mattiaperi/k3s-multipass-cluster)

我希望这些信息对你有用，任何反馈或改进建议都会被很好地接受。
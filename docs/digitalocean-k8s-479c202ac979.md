# 数字海洋❤ K8S

> 原文：<https://levelup.gitconnected.com/digitalocean-k8s-479c202ac979>

看，妈妈！只需在 [**数字海洋**](https://www.digitalocean.com/?refcode=6dcfcc2a3392) 上点击并使用 **Kubernetes**

> 极客警报！Kubernetes 目前供货有限

## 1.登录 **DigitalOcean** 仪表盘，点击“创建 Kubernetes 集群”开始

![](img/10fca77944f340ef2a111c6f0a67a088.png)

## 2.选择一个 **Kubernetes** 版本

![](img/a2456e6f34c34da1a1f8d3aaf3fc628f.png)

## 3.添加节点(我使用默认的名称 hello-k8s)

![](img/6adcb73ac0aed4b3504d7f8c4999243e.png)

## 4.设置大约需要 4 分钟…

![](img/8eb026feb6f33a8484cf6be23f5cf28d.png)

## 5.搞定了。

![](img/3e04838483106e7a176cb1a4883c2738.png)

## 6.安装 kubectl 以连接到您的 k8s 集群

[](https://www.digitalocean.com/docs/kubernetes/how-to/connect-with-kubectl/) [## 如何使用 kubectl | DigitalOcean 产品连接到 DigitalOcean Kubernetes 集群…

### 2018 年 10 月 1 日验证&bullet;发布于 2018 年 10 月 1 日 Kubernetes 目前限量发售。学习…

www.digitalocean.com](https://www.digitalocean.com/docs/kubernetes/how-to/connect-with-kubectl/) 

```
# Install Homebrew
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"# Install kubectl
brew install kubernetes-cli
kubectl version
```

![](img/83be610a094f5c1f3203325ba1225dab.png)

## 7.点击进入下一步

![](img/f4c1bf250e03896733b5166719f9359d.png)

## 8.设置配置

*   向下滚动到“下载配置文件”并单击

![](img/b5fd548f3d1b5710b1938388f960d97a.png)

*   将下载的文件放在任何地方，只要确保在下一步之前可以在**终端**中找到该文件夹

![](img/6153df8d25d79938298864f229d86a81.png)

*   测试连接

```
kubectl --kubeconfig="hello-k8s-kubeconfig.yaml" get nodes
```

![](img/5524c5106be3c1af92a84ba6f0847615.png)

*   是的，起作用了！

![](img/c6051a66f3049bfbadc77affb6d932d1.png)

## 9.部署工作负载

```
kubectl --kubeconfig="hello-k8s-kubeconfig.yaml" apply -f ./my-manifest.yaml
```

你应该看看…

```
deployment.apps/nginx-deployment-example created
```

和

```
kubectl --kubeconfig="hello-k8s-kubeconfig.yaml" get services
```

你应该看看

![](img/dc732828896264d35fccc36c9e23677d.png)

## **10。附加材料**

您现在可以将 **CronJob** 、 **Pod** 、 **ReplicaSet** 、**块存储卷**、**负载平衡器**添加到您的集群中，但我现在不会这么做，因为我懒得做，所以我将把这部分留给您！；D

# 注意(参考)

可以免费获得 10 美元 [**数字海洋**](https://www.digitalocean.com/?refcode=6dcfcc2a3392) (即 2 个月免费托管)。
可以拿到最便宜的 ***。io*** 域和优秀的 UI/UX 在 [**NameCheap**](https://www.namecheap.com/?aff=99054) 。

# 快乐酷冰！

[![](img/9914c5dd23ac08b70eea6f4f9ba6fed2.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn) [## 了解如何编码-查找编码教程| gitconnected

### 使用我们完整的编码资源列表学习任何编程语言或框架。我们分享、汇总和排名…

gitconnected.com](https://gitconnected.com/learn)
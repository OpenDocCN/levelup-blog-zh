# 使用 Helm3(无舵柄)安装 Grafana 和 Loki

> 原文：<https://levelup.gitconnected.com/using-helm3-no-more-tiller-to-install-grafana-and-loki-b2ded0cad436>

![](img/3a980395cc79113555da9f5ef99c35d1.png)

[https://unsplash.com/photos/ZiQkhI7417A](https://unsplash.com/photos/ZiQkhI7417A)

*   第一部分:[不带舵柄的头盔 2](https://medium.com/@wuestkamp/using-helm2-without-tiller-example-with-grafana-loki-7765b9c2e716)
*   第 2 部分:(这篇文章)没有舵柄的头盔 3

**Helm3 在这里**，没有服务器端组件 *Tiller* 。我们去看看吧！

## 下载测试版

在[https://github.com/helm/helm/releases](https://github.com/helm/helm/releases)上查看最新的测试版

我用的是 beta 3:[https://github.com/helm/helm/releases/tag/v3.0.0-beta.](https://github.com/helm/helm/releases/tag/v3.0.0-beta.3)3

我下载并复制到:

```
sudo cp helm /usr/local/bin/helm3
```

有关 Helm3 checkout 变更的更多信息，请点击此链接:

[https://github . com/helm/community/blob/master/helm-v3/000-helm-v3 . MD](https://github.com/helm/community/blob/master/helm-v3/000-helm-v3.md)

# 格拉夫纳

> **Grafana** 是适用于所有数据库的开源分析&监控解决方案。

```
helm3 repo add stable [https://kubernetes-charts.storage.googleapis.com](https://kubernetes-charts.storage.googleapis.com)helm3 repo update
```

我们可以只使用现有的 Helm2 图表(大部分时间)并运行:

```
kubectl create ns grafana
helm3 upgrade --install grafana stable/grafana -n grafana
```

## 名称空间现在很重要

`helm ls`不会显示任何东西，我们必须用它来指定命名空间:

```
helm3 -n grafana ls
```

## 访问 Grafana 界面

现在我们可以获得管理员密码并创建一个端口转发:

```
kubectl get secret -n grafana grafana -o jsonpath="{.data.admin-password}" | base64 --decodekubectl port-forward -n grafana service/grafana 3000:80
```

打开 [http://localhost:3000](http://localhost:3000) 进入 *Grafana* 界面。

# 洛基

> **Loki** 是一个受[普罗米修斯](https://prometheus.io/)启发的横向可扩展、高可用、多租户日志聚合系统。

首先我们添加回购:

```
helm3 repo add loki [https://grafana.github.io/loki/charts](https://grafana.github.io/loki/charts)
helm3 repo update
```

然后我们创建一个名称空间并安装它:

```
kubectl create ns loki-stack
helm3 upgrade --install loki-stack -n=loki-stack loki/loki-stack
helm3 -n loki-stack ls
```

完成了。Helm3 看起来前途无量！
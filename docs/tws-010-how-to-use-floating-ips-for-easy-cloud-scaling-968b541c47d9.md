# 如何使用浮动 IPs 轻松实现云扩展

> 原文：<https://levelup.gitconnected.com/tws-010-how-to-use-floating-ips-for-easy-cloud-scaling-968b541c47d9>

![](img/93ccfda6cc0850d989587d51e711fbc4.png)

纠结的 Web 服务每日站立

## 了解如何在没有 Docker 或 Kubernetes 的情况下扩展到更快的 droplet

*在这一集里，Ivana 自学了如何迁移到一个更强大的 web 服务器，只需 45 分钟，无需 Docker 或 Kubernetes。*

火箭科学是为岩石城青少年开设的课后 STEM 项目。最近，Ivana 设计了一个新的门户网站，学生和他们的导师可以远程访问作业。

一切都很完美。其实太美了。是时候离开来自数字海洋的入门大小的水滴了。

伊万娜害怕去打扰正在工作的东西。由于她以前从未面对过这种情况，所以她向队友征求意见。

“集装箱化是答案，”该团队的码头工人 Devin 宣布。“将所有东西放入一个容器中，您就可以在任何地方构建一个新的实例，这对于测试和试运行来说尤其有用。”

“但是我已经测试完了，”她回答。“一切都运转得很好。为什么我需要学习如何将它容器化？”

“一旦你知道它是如何工作的，它真的很强大，”这是他唯一的论点。

网上有很多指南，所以 Ivana 开始研究。

“嗯，”她想，“选择一个基础映像，安装软件包，暴露端口，处理日志记录。这是我在 droplet 上已经完成的所有内容。我想我可以重来一次。

“但是等等，”她的警报响起，“定义一个入口——什么？定义配置方法——真的吗？将您的数据具体化—哎呀！”

在她的困惑中，伊万娜走到肯的办公桌前，寻求第二种意见。“Docker 真的是正确答案吗？”她问。

“嗯，可以，”Ken 停顿了一下，然后补充道，“但是如果您想要一流的水平可伸缩性，那么 Kubernetes 就是答案。”肯是该队的库伯内特人。

Ivana 不确定这到底是什么意思，所以她做了一些快速的研究。“哦，我明白了，水平扩展是将服务器添加到一个群集中，并在前端放置一个负载平衡器。”

是时候结束一天的工作了，开始在 85 号公路上漫长的通勤。在回家的路上，她苦思如何继续下去。

第二天早上，伊万娜开始再次考虑如何让火箭科学网站获得更好的性能。由于他们的云提供商是 Digital Ocean，她准备在 DO 论坛上寻求一些建议，这时她偶然发现了一篇关于浮动 IP 的文章。不知何故，她错过了那一个。

我想到了一个好主意。为什么不跳过所有关于集装箱化的讨论，直接投入硬件呢？她想尝试这个想法。

她直奔主题。

*上午 9:30*首先，她去数字海洋联网面板，并预订了一个浮动 IP。然后，她将这个浮动 IP 分配给托管`rocket-science.com`网站的 droplet。这只需要几分钟的时间。

*上午 9:32*接下来，她进入域面板，将`rocket-science.com`的现有 DNS 记录更改为新分配的浮动 IP。更改 DNS 记录只需要一分钟，但是等待 DNS 记录传播最终需要大约 30 分钟(域的 TTL 记录设置为 1800)。

与此同时，她给火箭科学公司的美智子发短信，告诉她网站将关闭几分钟，她要做一些快速维护。

她通过检查一些公共 DNS 服务器来监控 DNS 传播的进程。

```
[root@rocksci] nslookup rocket-science.com 1.1.1.1
[root@rocksci] nslookup rocket-science.com 8.8.8.8
[root@rocksci] nslookup rocket-science.com 64.6.64.6
```

然后，她走到星巴克去买她今天的第二杯咖啡。她与陷入一些技术汤争论的 Devin 和 Ken 交叉路径，所以她只是礼貌地点点头，并通过了。

*上午 10:00*25 分钟后，当她回到自己的办公桌时，DNS 更改已经完全传播开来。她停止了 HTTP/2 服务器并关闭了 droplet:

```
[root@rocksci] systemctl stop rwserve
[root@rocksci] shutdown --powerdown now
```

*上午 10:02*她在 droplet 关闭时拍摄了一张快照，以创建整个服务器的映像。这只花了几分钟时间(尽管 DO 指令警告说，根据所使用的磁盘空间大小，可能会花更长时间)。

*上午 10:06*快照一完成，她就以该图像为基础创建了一个液滴。她选择了与现有 1GB/1CPU droplet 位于同一数据中心的 Digital Ocean 1GB/3CPU 计划。正如承诺的那样，水滴在大约一分钟内启动并运行。

*上午 10:07*她将`rocksci`水滴的浮动 IP 重新分配给新的`rocksci2`水滴。

*上午 10:08*她登录到系统，重新配置 HTTP/2 服务器以使用新的处理器。她将集群大小从 1 增加到 3。她还了解到，与其监听真实 IP 地址或浮动 IP 地址，不如监听 0.0.0.0 更方便。这是新配置的样子:

```
server {
    ip-address 0.0.0.0
    port 443
    cluster-size 3
}
host {
    hostname       rocket-science.com
    document-root  `/srv/rocksci/public`
    tls {
        private-key    `/etc/letsencrypt/live/rocksci/privkey.pem`
        certificate    `/etc/letsencrypt/live/rocksci/fullchain.pem`
    }
}
```

上午 10 点 13 分，一切都为最后一击做好了准备。实验会成功吗？她启动了服务器。

```
[root@rocksci2] systemctl start rwserve
```

*上午 10 点 14 分*一个快速的浏览器测试证实网站已经启动并运行。她的咖啡还是热的。

那天下午，伊万娜收到了美智子的短信。

*“不知道为什么”“孩子说传送门好像更快”“Tks”*

![](img/6df0c56511e8ee566f71277b7240a7e6.png)

老板的称赞

第二天早上，在他们的每日脱口秀中，克拉丽莎问道:“那么，我们要对火箭科学做些什么呢？我们需要使用 Kubernetes 吗，还是 Docker 就足够了？”

“其实，都不是。我已经修好了，”伊万娜宣布。

"酷，"克拉丽莎惊呼道，之前德文或肯可以说什么，他们的面部扭曲表示惊讶。

后来，纠结网络服务公司的创始人克拉丽莎私下对伊万娜说:“谢谢你为美智子解决问题。干得好！从其他人的言论来看，我认为我们将为此奋斗数周，甚至更长时间。”

那天晚上，85 号公路上的通勤似乎比平时更快。

![](img/3fa0b4084a56a6a4384669ceabfafca5.png)

*没有* [*minifig 人物*](https://readwritetools.com/meet-the-team.blue?utm_term=UsingFloatingIpsForEasyCloudScaling) *在这个纠结的网络服务插曲的制作中受到伤害。*
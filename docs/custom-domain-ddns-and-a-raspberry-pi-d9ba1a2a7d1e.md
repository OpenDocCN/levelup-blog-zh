# 自定义域，DDNS，和一个树莓派

> 原文：<https://levelup.gitconnected.com/custom-domain-ddns-and-a-raspberry-pi-d9ba1a2a7d1e>

![](img/399ce15fdd1c3f0631a67efa6c037de4.png)

*如何使用自定义域指向带有动态 IP 的树莓派*

受这篇文章的启发

 [## 使用动态 DNS 管理域

### 另外请注意，本教程中描述的一些技术在正确的 DNS 方面是不“正确”的…

www.ianatkinson.net](http://www.ianatkinson.net/computing/ddns.htm) 

# 您自己的域名系统

你需要让 bind 在你的 rasp pi 上运行。网上有很多关于这个的教程(安装 bind9)。

更改/etc/bind/named.conf.local 文件

```
zone "domain.me" {
  type master;
  file "/home/pi/homedns/domain.zone";
};
```

您需要定义一个区域文件，如 my.zone:

```
$TTL 60S@ IN SOA host.domain.me. root.domain.me. (
  2017081402 ; serial
  8H ; refresh
  2H ; retry
  4W ; expire
  60S ; minimum
)NS myns1.duckdns.org.
NS myns2.duckdns.org.myns1.duckdns.org A 86.21.183.217
myns2.duckdns.org A 86.21.183.217www                 A 86.21.183.217
homepage  CNAME     www
```

你需要有两个域名服务器域，在上面的例子中是 myns1.duckdns.org 和 myns2.duckdns.org。

开始绑定

```
sudo systemctl start bind9
```

检查它是否正在本地解析

```
dig -tns domain.me @localhost
```

NS authority 部分应该显示“myns1.duckdns.org”

# 配置您的域名提供商

在您的域提供商(如 123-reg)中为您的自定义域设置名称服务器，以指向您的 ddns 中定义的两个名称服务器(如 myns2.duckdns.org 和 myns2.duckdns.org)。

例如，在 123-reg.co.uk，您可以转到“管理名称服务器”部分，将 123 个 ns1/ns2 服务器 URL 替换为新的 ddns URL。然后，您可以使用 dig 命令来检查:

```
dig -tns domain.me @resolver1.opendns.com
```

它应该使用您的 ddns 名称服务器进行响应(注意:您的域提供商的更改可能需要一段时间)。

```
dig domain.me @resolver1.opendns.com
```

IP 地址应该属于您的动态 ISP。

注意:您应该有一个来自 DDNS 提供商的脚本，以使提供商中的 ip 与 ISP 提供的动态 IP 保持同步。

# 区域 IP 更改

下一步是，每当您的 ISP 更改 IP 地址时，也要更改域的区域文件。

这是一个用于更改区域 IP 的 perl 脚本(基于 Ian Atkinsons 的 perl 脚本):

您可以使用 crontab 经常检查 IP，然后使用脚本更新区域文件。就像这样(假设上面的脚本名为 change.sh，位于您的 homedns 目录中):

```
*/6 * * * * ~/homedns/change.sh >/dev/null 2>&1
```

# 测试您的服务器

您可以轻松地在本地机器上运行服务器(如 nginx)，如果您有 docker，就更容易了。

还有一件事你需要做的是打开你的路由器，允许外界连接到 Rasp Pi(你的机器)到运行你的服务器的特定端口。

您还需要允许路由器连接到您的绑定 DNS 服务器(TCP 和 UDP 的端口都是 53)

您将无法在您的家庭网络内调用您的自定义域(除非将该域添加到/etc/hosts)。然而，一个简单的方法是关闭手机上的 Wifi，使用数据网络进行测试。

# 邮件转发

如果您需要将邮件转发到 mail@domain.me，那么您可以在文件中添加以下 mx 记录。这里的邮件服务器来自 123-reg。

```
@       MX 1 mx0.123-reg.co.uk.
@       MX 2 mx1.123-reg.co.uk.
```

您可以通过以下方式检查 MX 记录

```
dig mx domain.me
```

您应该会看到这样的回答部分

```
;; ANSWER SECTION:domain.me.  53 IN MX 1 mx0.123-reg.co.uk.
```

~瞧~
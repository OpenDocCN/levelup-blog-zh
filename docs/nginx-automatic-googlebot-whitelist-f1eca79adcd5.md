# Nginx:自动化白名单

> 原文：<https://levelup.gitconnected.com/nginx-automatic-googlebot-whitelist-f1eca79adcd5>

![](img/02e4072779ee00cd9f270ed23ff18821.png)

照片由 [Rajeshwar Bachu](https://unsplash.com/@rajeshwerbatchu7?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 nginx 配置中添加速率限制或分流规则是应对滥用或恶意 bot 流量的好方法。然而，当一个好的机器人陷入规则中时，SEO 的价值就会下降，并且在它们被列入白名单之前，处理起来会很痛苦。

# 供应商 CIDR 街区

许多知名厂商会发布并定期更新 IP 地址阻止列表，或 CIDR 阻止列表，这些列表可用于更新白名单规则。比如谷歌在这里发布了这样一个列表:
[https://developers . Google . com/search/APIs/IP ranges/Google bot . JSON](https://developers.google.com/search/apis/ipranges/googlebot.json)

关于将谷歌列入白名单的完整指南可以在这里找到:
[https://developers . Google . com/search/docs/advanced/crawling/vericking-Google bot](https://developers.google.com/search/docs/advanced/crawling/verifying-googlebot)

对于本教程，googlebot 列表将用于创建流程。

# 重载方法—开源 Nginx

> 如果使用 Nginx Plus，请参见本节下面的 KeyVal 方法。

如果正在使用 nginx 的开源版本，创建一个脚本来生成一个包含 map 变量的 nginx 配置文件。nginx 地图对比帖子提供了关于 Nginx 地图的信息。

curl 实用程序将用于检索解析列表。如果发布的列表是像 Googlebot 列表那样的 json 格式，那么可以使用 [**jq**](https://stedolan.github.io/jq/) 实用程序将 json 解析成可用的格式。

脚本:

## 脚本变量

有两个变量可以更新以满足目标环境的需求。

`**GOOGLE_WHITELIST_CONF**`

将此设置为应该写入配置文件的绝对路径。如果使用了默认的 nginx 配置，默认情况下会自动包含`**/etc/nginx/conf.d**`下的`***.conf**`文件。最好将文件放在包含 include `**conf.d/*.conf;**`指令的文件夹中。

`**RELOAD_CMD**`

更新该值以匹配重新加载 nginx 配置所需的命令。这将确保地图的任何更新都被激活。

## 属国

运行脚本需要两个二进制依赖关系: **curl** 和 **jq** 。确保在运行该脚本的系统上安装了这些程序。

## 从语法上分析

Googlebot 列表的 json 格式允许进行简单的解析，以获取用双引号括起来的 IP 地址块。提取所有通过管道传输到`.[]`的`.prefixes[]`将输出所需的值。将它包装在一个`while read`循环中可以格式化这些值。

## 计划脚本

可以将该脚本添加到需要更新白名单的每个 webtier 主机上的 crontab 中，也可以从每个 webtier 主机上的 CI/CD 系统中运行该脚本来更新环境。白名单应该定期更新，一天一次可能很好，或者一天几次，以避免访问间隙，这取决于白名单中的源的变化。

## 配置文件解释

脚本创建的 nginx 映射使用`**$remote_addr**`作为源变量，并使用“geo”映射配置进行 IP 地址比较:

`geo $remote_addr $is_google { … }`

> 默认情况下，geo map 使用$remote_addr，在定义中不需要。为了完整起见，在示例中进行了设置。

判断环境中的`**$remote_addr**`是什么的一个简单方法是记录它，并使用 Googlebot 用户代理检查来自请求的客户机 IP 地址值。如果 remote_addr 是私有 IP 或设置为防火墙、负载平衡器或 CDN IP 地址，则需要一个保存正确 IP 的不同变量。可能需要在 geo map 中设置代理配置或为环境配置 realip 实施，以可靠地获得此值。

> 如果$remote_addr 包含意外的 ip 地址，则 [ngx_http_geo_module](http://nginx.org/en/docs/http/ngx_http_geo_module.html) 或 [ngx_http_realip_module](http://nginx.org/en/docs/http/ngx_http_realip_module.html) 具有如何获取正确 IP 地址的信息。

地理地图输出格式应如下所示:

```
geo $remote_addr $is_google {
…
  "66.249.72.224/27"          1;
…
  default                     0;
}
```

# 使用

`**$is_google**` geo map 变量可以用在白名单中。创建白名单的一个好方法是构建一系列映射来形成一个`**$whitelist**`映射变量。以下是如何建立这种白名单的示例:

由此产生的$whitelist 变量可以用在排除规则中，或者作为其他变量或指令中的标志，以确保只对非白名单请求执行操作，反之亦然。

# KeyVal 方法— Nginx Plus 版本

如果正在使用 Nginx Plus，Nginx Plus 可以使用 keyval 处理实时更新，而不需要更新配置和重新加载所有 nginx webtier 实例，而不是更新文件和重新加载配置。

> Nginx Plus 配置中建议使用 [zone_sync 流配置](https://docs.nginx.com/nginx/admin-guide/high-availability/zone_sync/)，否则需要在每个实例上运行脚本来为所有 Nginx 实例设置 keyval 数据。

## KeyVal 和流

本例中保存 Googlebot IP 地址的 keyval 配置使用了一个`stream`配置来启用 keyval 数据同步。这个例子使用 DNS 解析来通知 nginx 集群中所有 nginx 实例的 IP 地址，但是如果需要的话，这可以调整为具有单独的 nginx 服务器实例。有关指南，请参见 zone_sync_server 的[文档。](http://nginx.org/en/docs/stream/ngx_stream_zone_sync_module.html#zone_sync_server)

keyval_zone 配置有 10 年的 TTL，这是一个用于完全重启时恢复的状态文件，并且设置为类型`**ip**`，以便与 CIDR 块进行 IP 地址比较。如果有匹配，`**$is_google**`将被设置为“1”。

reload 方法中的 whitelist.conf 文件将使用这种方法:

## 脚本

下面是更新 keyval 内存区域的脚本。如果 nginx 集群没有使用流配置进行数据同步，那么脚本将需要针对所有单独的 nginx 实例运行。建议使用 zone_sync 配置来简化共享数据的 nginx 操作。该脚本是为 bash 3.x 或更高版本编写的，它要求在脚本中设置或更新以下变量:

```
NGINX_CLUSTER_API_SCHEME - set to http or https (default)
NGINX_CLUSTER_API_SERVER - set to an nginx ip or internal DNS
NGINX_CLUSTER_API_PORT   - set to port number for the enabled API
```

根据需要，可以在构建作业中克隆或调度该脚本。

祝你好运！
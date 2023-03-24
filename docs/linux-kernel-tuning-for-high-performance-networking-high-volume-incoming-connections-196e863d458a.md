# 高容量传入连接

> 原文：<https://levelup.gitconnected.com/linux-kernel-tuning-for-high-performance-networking-high-volume-incoming-connections-196e863d458a>

## 高性能网络系列的 Linux 内核调优

![](img/9bca4cf80816424bf23f02291adb7381.png)

照片由[乔丹·哈里森](https://unsplash.com/@aligns?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 最大化接收连接数

需要理解的一些更令人困惑的事情是 linux 网络栈的不断变化的世界。不仅仅是因为一些术语在不完全理解这些概念的工程师的博客帖子中被误用，还因为这些设置的含义已经演变了多年。在撰写本文时，这些细节从 linux 内核 v2.2 开始就是准确的，并在可能的情况下强调了从 v5.5 开始的内核之间的设置差异。

> 本文中的示例命令依赖于 linux 命令`**netstat**` ( `net-tools`包)和`**ss**` ( `iproute2`包)。

本文中概述的内核设置可以按照下面的配置内核设置入门进行调整:

[](/linux-kernel-tuning-for-high-performance-networking-configuring-kernel-settings-96b519a3305f) [## 面向高性能网络的 Linux 内核调优:配置内核设置

### Linux 内核配置

levelup.gitconnected.com](/linux-kernel-tuning-for-high-performance-networking-configuring-kernel-settings-96b519a3305f) 

# **TCP 接收队列和 netdev_max_backlog**

在网络堆栈能够处理数据包之前，每个 CPU 内核可以在环形缓冲区中保存大量数据包。如果缓冲区的填充速度快于 TCP 堆栈处理数据包的速度，则丢弃数据包计数器将递增，数据包将被丢弃。应该增加`**net.core.netdev_max_backlog**`设置，以便在高突发流量的服务器上最大化排队等待处理的数据包数量。

> `**net.core.netdev_max_backlog**`是每个 CPU 内核的设置。

# **TCP Backlog 队列和 tcp_max_syn_backlog**

TCP Backlog 队列包含等待完成的不完整连接。

为从接收队列中拾取并被移动到 SYN Backlog 队列中的任何 **SYN** 数据包创建一个连接。该连接被标记为“SYN_RECV ”,并且一个 **SYN+ACK** 被发送回客户端。

> 这些连接在相应的 **ACK** 被接收和处理之前**不会被移动**到接受队列。

队列中的最大连接数在`**net.ipv4.tcp_max_syn_backlog**`内核设置中设置。

在正常负载下，SYN backlog 条目的数量在正常负载下应该是**不大于 1**，在高负载下应该保持在`tcp_max_syn_backlog`限制以下。要检查 TCP 端口的 SYN 积压的当前大小，运行以下命令*(示例使用 TCP 端口 80)* :

```
ss -n state syn-recv sport = :80 | wc -l
```

如果有大量的连接处于“SYN_RECV”状态，这可能会导致服务器在处理大量流量时出现问题。在增加这个限制之前，可以通过调整相关的 TCP 设置来减少 SYN 数据包在这个队列中的时间。

## *SYN Cookies*

对此进行调整可以减少 SYN 数据包在接收队列中的停留时间。如果 SYN cookies 没有启用，客户端将简单地重试发送一个 **SYN** 数据包。如果启用了 SYN cookies(`**net.ipv4.tcp_syncookies**`)，则不会创建连接，也不会将其放入 SYN backlog 中，但是会向客户端发送一个 **SYN+ACK** 数据包，就好像它是。在正常流量下，SYN cookies 可能是有益的，但是在大量突发流量期间，一些连接细节将会丢失，并且在建立连接时客户端将会遇到问题。除了 SYN cookies 之外，还有其他一些东西，但是这里有一篇名为“ [SYN cookies 吃了我的狗](https://kognitio.com/blog/syn-cookies-ate-my-dog-breaking-tcp-on-linux/)”的文章，作者 Graeme Cole 详细解释了为什么在高性能服务器上启用 SYN cookies 会导致问题。

## *SYN+ACK 重试次数*

对此进行调整可以显著减少 SYN 数据包在接收队列中的停留时间。当发送了一个 **SYN+ACK** 但从未收到响应 ACK 包时会发生什么？在这种情况下，服务器上的网络堆栈将重试发送 **SYN+ACK** 。计算尝试之间的延迟是为了允许服务器恢复。

> 如果服务器接收到一个 **SYN** ，发送一个 **SYN+ACK** ，并且没有接收到 **ACK** ，重试所花费的时间长度遵循[指数补偿](https://en.wikipedia.org/wiki/Exponential_backoff)算法，因此重试次数取决于重试计数器。

定义 **SYN+ACK** 重试次数的内核设置是`**net.ipv4.tcp_synack_retries**`，默认设置为 5。这将在第一次尝试后按以下时间间隔重试:1 秒、3 秒、7 秒、15 秒、31 秒。第一次尝试后大约 63 秒后，最后一次重试将超时，这相当于重试次数为 6 次时进行下一次尝试的时间。仅这一项就可以在包超时之前将一个 **SYN** 包保留在 SYN backlog 中超过 60 秒。如果 SYN backlog 队列很小，不需要大量的连接就可以在网络堆栈中导致放大事件，在这种情况下，半开连接永远不会完成，也不会建立任何连接。将 SYN+ACK 重试次数设置为 0 或 1，以避免在高性能服务器上出现这种情况。

## *同步重试次数*

对此进行调整可以显著减少 SYN 数据包在接收队列中的停留时间。虽然 SYN 重试指的是客户端在等待 **SYN+ACK** 时重试发送 **SYN** 的次数，但它也会影响进行代理连接的高性能服务器。由于流量高峰，nginx 服务器与后端服务器建立几十个代理连接，这可能会使后端服务器的网络堆栈在短时间内过载，重试可能会在后端的接收队列和 SYN 积压队列上造成放大。这反过来会影响所服务的客户端连接。SYN 重试的内核设置是`**net.ipv4.tcp_syn_retries**`，默认为 5 或 6，具体取决于发行版。将 SYN 重试次数限制为 0 或 1，而不是重试 63-130 秒以上(指数回退)。

有关解决反向代理服务器上的客户端连接问题的更多信息，请参见以下内容:

[](/linux-kernel-tuning-for-high-performance-networking-f3256ffecf98) [## 针对高性能网络的 Linux 内核调优:临时端口

levelup.gitconnected.com](/linux-kernel-tuning-for-high-performance-networking-f3256ffecf98) 

# **TCP 接受队列和 somaxconn**

当通过指定一个“backlog”参数调用`listen()`时，应用程序负责在打开一个监听器端口时创建它们的接受队列。从 linux kernel v2.2 开始，这个参数从设置一个套接字可以容纳的最大未完成连接数改为等待接受的最大已完成连接数。如上所述，现在用内核设置`net.ipv4.tcp_max_syn_backlog`来设置未完成连接的最大数量。

## *TCP listen()积压*

虽然应用程序对它打开的每个侦听器上的接受队列大小负责，但是侦听器的接受队列中的连接数是有限制的。有两种设置可以控制队列的大小:

1.  应用程序发出的 TCP listen()调用上的 backlog 参数
2.  来自内核 sysctl: `**net.core.somaxconn**`的内核限制最大值

## *接受队列默认值*

`net.core.somaxconn`的默认值来自于`**SOMAXCONN**`常量，在 v5.3 之前的 linux 内核中该常量被设置为 [**128**](https://github.com/torvalds/linux/blob/v5.3/include/linux/socket.h#L266) ，而在 v5.4 中`SOMAXCONN`被提升为 [**4096**](https://github.com/torvalds/linux/blob/v5.4/include/linux/socket.h#L266) 。然而，在撰写本文时，v5.4 是最新版本，尚未被广泛采用，因此在许多尚未修改`net.core.somaxconn`的生产系统中，接受队列将被截断为 128。

如果没有在应用程序配置中设置，或者有时只是在服务器软件中硬编码，应用程序在为侦听器配置默认 backlog 时通常会使用`SOMAXCONN`常量的值。一些应用程序设置了自己的默认值，比如 nginx 将它设置为 511——在 linux 内核上，通过 v5.3，它被无声地截断为 128。查看应用程序文档以配置监听器，了解使用了什么。

要检查为开放 TCP 侦听器端口配置的 accept()队列大小，请运行以下命令(示例端口 80):

```
ss -plnt sport = :80|cat
```

## *接受队列最大值*

net.core.somaxconn 的最大值在内核 v2.2 到 v4.0.x 中是 **65535** ，在内核 v4.1.0+中是 **4294967295** 。

## *接受队列覆盖*

许多应用程序允许在配置中指定接受队列的大小，方法是在监听器指令上提供一个“backlog”值，或者在调用`listen()`时使用一个配置。例如，nginx 有一个 backlog 参数，可以添加到 listen 指令中，用于调整侦听器端口的接受队列的大小:

```
listen 80 backlog=65535;
```

> 如果应用程序使用大于`**net.core.somaxconn**`的 backlog 值调用`listen()`，那么该侦听器的 backlog 将被静默地截断为 somaxconn 值。

## *申请工人*

如果接受队列很大，还要考虑增加应用程序中可以处理队列接受请求的线程数量。例如，为高容量 nginx 服务器在 HTTP 侦听器上设置 20480 的 backlog，而不允许足够的 worker_connections 来管理队列，这将导致来自服务器的连接拒绝响应。

# 文件描述符(文件句柄、连接)

在 linux 系统上，一切都是文件。这包括实际的文件和文件夹、符号链接、管道和套接字等。因此，配置进程的最大连接数也需要配置进程可以打开的文件数。

> 连接中的每个套接字也使用一个文件描述符。

## *打开文件系统限制*

可以分配给系统的所有文件句柄的最大数量由内核设置`**fs.file-max**`设定。

> `**fs.file-max**`设置是系统上可以分配和使用的文件句柄的最大总数。

要查看当前分配的文件描述符数量和允许的最大数量，请查看以下文件:

```
# cat /proc/sys/fs/file-nr
1976      0       2048
```

输出显示正在使用的文件描述符的数量是 1976，已分配但空闲的文件描述符的数量是 0(在内核 v2.6+上这将总是显示“0”，意味着已使用和已分配的总是匹配)，最大值是 2048。在高性能系统上，这个值应该设置得足够高，以处理系统上所有进程的最大连接数和任何其他文件描述符需求。2048 对于这类系统来说是非常低的，1976 已经非常接近最大值了。

## *打开文件过程限制*

单个进程可以打开的最大文件数量由内核设置`**fs.nr_open**`决定。该设置应不大于`fs.file-max`的三分之一。默认情况下，`fs.nr_open`应该足够大，可以容纳系统上运行的任何单个进程，而不需要调整它。

> `**fs.nr_open**`设置是可以为“打开文件数”或无文件用户限制设置的最大值。

## *打开文件用户限制*

除了文件描述符系统和进程限制之外，每个用户还被限制打开文件描述符的最大数量。这是用系统的 limits.conf (nofile)设置的，或者如果在 systemd (LimitNOFILE)下运行进程，则在进程 systemd 单元文件中设置。要查看默认情况下用户可以打开的文件描述符的最大数量:

```
$ ulimit -n
1024
```

而在 systemd 下，以 nginx 为例:

```
$ systemctl show nginx | grep LimitNOFILE
4096
```

# 将打开文件设置更新为所需值

有许多指南可以解释如何使这些设置适用于文件系统需要的进程。这是一个详细的方法，在大容量系统上有效，应该对任何系统都有效。

## *1。配置打开文件系统限制*

选择一个 ***系统限制*** ，它将容纳系统上所需的打开文件总数。将单个工作负载进程所需的打开文件数乘以预期运行的进程数。将`**fs.max-file**`内核设置设为这个值，加上一些缓冲。例如，一个系统正在运行 4 个进程，需要 800，000 个打开的文件，如果该设置还不够高，可以使用值 3200000。

```
fs.file-max = 3400000 # (800000 * 4) + 200000
```

## *2。配置*打开文件*过程限制*

选择一个 ***进程限制*** 来容纳单个工作负载进程所需的最大打开文件数。例如，工作负载流程最多需要 800，000 个打开的文件:

```
fs.nr_open = 801000
```

## *3。配置*打开文件*用户限制*

要调整 ***用户限制*** 以利用系统限制，请将`**nofile**`值设置为所有侦听器的连接套接字所需的打开文件的最大数量加上工作进程所需的任何其他文件描述符，并包括一些缓冲区。用户限制设置在/etc/security/limits.conf 下，或/etc/security/limits.d/下的 conf 文件中，或服务的 systemd 单元文件中。示例:

```
# cat /etc/security/limits.d/nginx.conf
nginx soft nofile 800000
nginx hard nofile 800000

# cat /lib/systemd/system/nginx.service 
[Unit]
Description=OpenResty Nginx - high performance web server
Documentation=https://www.nginx.org/en/docs/
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target
[Service]
Type=forking
LimitNOFILE=800000
PIDFile=/var/run/nginx.pid
ExecStart=/usr/local/openresty/nginx/sbin/nginx -c /usr/local/openresty/nginx/conf/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID
[Install]
WantedBy=multi-user.target
```

# 工作线程限制(线程/执行器)

与文件描述符限制一样，一个进程可以创建的工作线程数也受到内核设置和用户限制的限制。

## *线程系统限制*

进程可以加速工作线程。可以创建的所有线程的最大数量由内核设置`**kernel.threads-max**`设置。要查看系统上执行的最大线程数和当前线程数，请运行以下命令:

获取当前最大线程数:

```
cat /proc/sys/kernel/threads-max
```

默认值是内存页数除以 4。

运行的线程总数:

```
$ ps -eo nlwp | awk '$1 ~ /^[0-9]+$/ { n += $1 } END { print n }'
```

只要线程总数低于最大值，服务器就能够为进程创建新的线程，只要它们在用户限制之内。

## 线程进程限制

与打开文件限制的内核设置不同，线程没有直接的进程限制设置。这由内核间接处理。

影响线程分叉数量的一个设置是`**kernel.pid_max**`。这将通过限制可用进程 id 的数量来设置可以同时执行的最大线程数量。增加这个值将允许系统同时执行更多的线程。

另一个设定是`**vm.max_map_count**`。这控制了每个线程的映射内存区域的数量。一般的经验法则是增加这个值，使系统中预期的并发线程数加倍。

## 线程*用户限制*

除了 max threads 系统限制之外，每个用户进程都有一个最大线程数限制。这再次用系统的 limits.conf ( `**nproc**`)设置，或者如果在 systemd ( `**LimitNPROC**`)下运行一个进程，则在进程 systemd 单元文件中设置。要查看一个进程可以派生的最大线程数()，请执行以下操作:

```
$ ulimit -u
4096
```

而在 systemd 下，以 nginx 为例:

```
$ systemctl show nginx | grep LimitNPROC
4096
```

## 将线程设置更新为所需值

在大多数系统中， ***系统限制*** 已经设置得足够高，可以处理高性能服务器所需的线程数量。然而，为了调整系统限制，将`**kernel.threads-max**`内核设置为系统需要的最大线程数，加上一些缓冲。示例:

```
kernel.threads-max = 3261780
```

要调整 ***用户限制*** ，请将该值设置得足够高，以满足处理流量(包括一些缓冲区)所需的工作线程数量。与`nofile`一样，`**nproc**`用户限制设置在/etc/security/limits.conf 下，或/etc/security/limits.d/下的一个 conf 文件中，或服务的 systemd 单元文件中。例如，使用 nproc 和 nofile:

```
# cat /etc/security/limits.d/nginx.conf
nginx soft nofile 800000
nginx hard nofile 800000
nginx soft nproc 800000
nginx hard nproc 800000# cat /lib/systemd/system/nginx.service 
[Unit]
Description=OpenResty Nginx - high performance web server
Documentation=[https://www.nginx.org/e](https://nginx.org/en/docs/)n/docs/
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target[Service]
Type=forking
LimitNOFILE=800000
LimitNPROC=800000
PIDFile=/var/run/nginx.pid
ExecStart=/usr/local/openresty/nginx/sbin/nginx -c /usr/local/openresty/nginx/conf/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID[Install]
WantedBy=multi-user.target
```

## TIME_WAIT 中的 TCP 反向代理连接

在高容量突发流量下，在关闭连接握手期间，陷入“TIME_WAIT”的代理连接可能会占用许多资源。这种状态表示客户端已经从服务器(或上游工作器)接收到最后的 **FIN** 包，并且正被保留给任何延迟的传输中的包以进行适当的处理。默认情况下，连接在“TIME_WAIT”中存在的时间是 2 x MSL(最大分段长度)，即 2x 60 秒。在许多情况下，这是正常的预期行为，默认值 120 秒是可以接受的。但是，当处于“TIME_WAIT”状态的连接数量很大时，这可能会导致应用程序用完临时端口来连接客户端套接字。在这种情况下，通过减少 **FIN** 超时，让这些超时更快。

控制这个超时的内核设置是`**net.ipv4.tcp_fin_timeout**`，对于高性能服务器来说，一个好的设置是在 5 到 7 秒之间。

# 将这一切结合在一起

**接收队列**的大小应该能够处理尽可能多的数据包，因为 linux 可以在不导致丢包的情况下处理网卡，包括一些小的缓冲区，以防峰值比预期的高一点。应该监视 softnet_stat 文件中丢失的数据包，以发现正确的值。一个好的经验法则是使用为 tcp_max_syn_backlog 设置的值，至少允许尽可能多的 **SYN** 数据包可以被处理以创建半开连接。请记住，这是每个 CPU 在其接收缓冲区中可以拥有的包的数量，所以用期望的总数除以 CPU 的数量是保守的。

**SYN backlog 队列**的大小应该考虑到高性能服务器上的大量半开连接，以处理突发的偶然峰值流量。一个好的经验法则是，至少将它设置为侦听器在接受队列中可以拥有的最大已建立连接数，但不要高于侦听器可以拥有的已建立连接数的两倍。还建议在这些系统上关闭 SYN cookie 保护，以避免来自合法客户端的高突发初始连接的数据丢失。

**接受队列**的大小应允许在高突发流量期间作为临时缓冲区保存大量等待处理的已建立连接。一个好的经验法则是将其设置为工作线程数的 20–25%。

## 配置

本文使用 nginx 讨论了以下内核设置。

```
# /etc/sysctl.d/99-nginx.conf

# /proc/sys/fs/file-max
# Maximum number of file handles that can be allocated.
#  aka: open files.
# NOTES
# - This should be sized to accommodate the number of connections
#    (aka: file handles or open files) needed by all processes.
# RECOMMENDATION
# - Increase this setting if more high connection processes are
#    started.
# SEE ALSO
# - /proc/sys/fs/file-nr
fs.file-max = 3400000

# /proc/sys/fs/nr_open
# Maximum number of file handles that a single process can
#  allocate, aka: open files or connections.
# NOTES
# - Each process requires a high number of connections to operate.
# RECOMMENDATION
# - None
# SEE ALSO
# - net.core.somaxconn
# - user limits: nofile
fs.nr_open = 801000

# /proc/sys/net/core/somaxconn
# Accept Queue Limit, maximum number of established connections
#  waiting for accept() per listener.
# NOTES
# - Maximum size of accept() for each listener.
# - Do not size this less than net.ipv4.tcp_max_syn_backlog
# SEE ALSO
# net.ipv4.tcp_max_syn_backlog
net.core.somaxconn = 65535

# /proc/sys/net/ipv4/tcp_max_syn_backlog
# SYN Backlog Queue, number of half-open connections
# NOTES
# - Example server: 8 cores, can handle over 65535 total half-open
#    connections.
# - Do not size this more than net.core.somaxconn
# SEE ALSO
# - net.core.netdev_max_backlog
# - net.core.somaxconn
net.ipv4.tcp_max_syn_backlog = 65535

# /proc/sys/net/core/netdev_max_backlog
# Receive Queue Size per CPU Core, number of packets.
# NOTES
# - Example server: 8 cores, each core should at least be able to
#    receive 1/8 of the tcp_max_syn_backlog.
# RECOMMENDATION
# - Size this to be double the number needed; in the example, 1/4.
# SEE ALSO
# - net.ipv4.tcp_max_syn_backlog
net.core.netdev_max_backlog = 16386

# /proc/sys/net/ipv4/syn_retries
# /proc/sys/net/ipv4/synack_retries
# Maximum number of SYN and SYN+ACK retries before packet
#  expires.
# NOTES
# - Reduces connection time to fail
net.ipv4.tcp_syn_retries = 1
net.ipv4.tcp_synack_retries = 1

# /proc/sys/net/ipv4/tcp_fin_timeout
# Timeout in seconds to close client connections in TIME_WAIT
#  after receiving FIN packet.
# NOTES
# - Improves socket availability performance, allows for closed
#    connections to be resused more quickly.
net.ipv4.tcp_fin_timeout = 5

# /proc/sys/net/ipv4/tcp_syncookies
# Disable SYN cookie flood protection.
# NOTES
# - Only disable this on systems that require a high volume of
#    legal connections in a short amount of time, ie: bursts.
net.ipv4.tcp_syncookies = 0

# /proc/sys/kernel/threadsmax
# Maximum number of threads system can have, total.
# NOTES
# - Commented, may not be needed; check system.
# SEE ALSO
# - user limits.
#kernel.threads-max = 3261780 
```

本文讨论了以下用户限制设置:

```
# /etc/security/limits.d/nginx.conf
nginx soft nofile 800000
nginx hard nofile 800000
nginx soft nproc 800000
nginx hard nproc 800000
```

# 结论

本文中的设置只是示例，不应该未经测试就直接复制到您的生产服务器配置中。还有一些额外的内核设置会影响网络堆栈的性能。总的来说，这些是我在为高性能连接调优内核时使用的最重要的设置。

如果这篇文章中的任何信息不准确，请发表评论，我会更新文章以纠正信息。
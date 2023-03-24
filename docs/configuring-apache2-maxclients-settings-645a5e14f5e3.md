# 配置 Apache2 MaxClients 设置

> 原文：<https://levelup.gitconnected.com/configuring-apache2-maxclients-settings-645a5e14f5e3>

![](img/632ebecbad0afe55f20d6d7599d590bd.png)

[https://images . unsplash . com/photo-1603041385912-d622c 2700422？IX lib = r b-1 . 2 . 1&ixid = mnwxmja 3 fdb 8 mhxwag 90 by 1 wywdlfhx 8 fgvufdb 8 fhx 8&auto = format&fit = crop&w = 1632&q = 80](https://images.unsplash.com/photo-1603041385912-d622c2700422?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1632&q=80)

我会告诉你我是如何解决这个问题的，但可能还有其他方法。在日志中遇到这个问题可能会令人望而生畏`server reached MaxClients setting, consider raising the MaxClients setting`，这会大大降低 web 服务的速度。然而，当查看服务器上的监控统计数据时，我使用的是 cacti。

这是发生这种情况时的配置示例

```
<IfModule prefork.c>
StartServers       8
MinSpareServers    5
MaxSpareServers    10
ServerLimit        1024
MaxClients         768
MaxRequestsPerChild  4000
</IfModule>

<IfModule worker.c>
StartServers         2
MaxClients         150
MinSpareThreads     25
MaxSpareThreads     75 
ThreadsPerChild     25
MaxRequestsPerChild  0
</IfModule>

free -m
             total       used       free     shared    buffers     cached
Mem:           768        352        415          0          0         37
-/+ buffers/cache:        315        452
Swap:            0          0          0

top - 11:03:54 up 41 days, 11:53,  1 user,  load average: 0.05, 0.03, 0.00
Tasks:  35 total,   1 running,  34 sleeping,   0 stopped,   0 zombie
Cpu(s):  0.0%us,  0.0%sy,  0.0%ni, 99.7%id,  0.0%wa,  0.0%hi,  0.0%si,  0.3%st
Mem:    786432k total,   389744k used,   396688k free,        0k buffers
Swap:        0k total,        0k used,        0k free,    38284k cached
```

我运行的是 Apache 2.2，php5 运行在`prefork`模式。从当前的设置来看，这意味着 apache 进程从 8 个进程开始，但是如果有 10 个以上的进程什么都不做；然后减少子进程，使其保持在 5-10 个可用进程之间。

apache 的增加/减少速度是每分钟 1，所以这将退回到空闲可用 apache 进程数量相当少的情况，avg 2。平均值很低，因为你通常只有 5 个可用的进程，所以一旦流量增加，它就会被立即使用，apache 创建新进程的速度会很慢。更糟糕的是，你的申请过程很长，所以需要更长时间才能完成。

![](img/79832e82351007682d95c9d6ca61a0da.png)

如果您在红色峰值之前看到少量绿色，则表示没有足够的进程来接收传入流量。

一种处理和测试的方法是使用类似下面的配置

```
<IfModule prefork.c>
  StartServers       12
  MinSpareServers    12
  MaxSpareServers    12
  MaxClients         12
  MaxRequestsPerChild  300
</IfModule>
```

这将意味着当流量增加/减少时，您不会移动分叉处理的数量，因为您总是希望使用所有的 RAM 并为任何增加做好准备。300 意味着在 300 次请求之后，您将回收任何分支，以防止任何内存泄漏问题。12 将是您可以处理的并行请求的数量，这应该设置为您希望处理的数量。

计算可以运行多少 ram/进程的推荐公式

MaxClients =(总 RAM —操作系统的 RAM—外部程序的 RAM)/(每个 httpd 进程的 RAM)

以下脚本有助于完成所需的设置

```
#!/bin/bash

echo "HostName=`hostname`"

#Formula
#MaxClients . (RAM - size_all_other_processes)/(size_apache_process)
total_httpd_processes_size=`ps -ylC httpd --sort:rss | awk '{ sum += $9 } END { print sum }'`
#echo "total_httpd_processes_size=$total_httpd_processes_size"
total_http_processes_count=`ps -ylC httpd --sort:rss | wc -l`
echo "total_http_processes_count=$total_http_processes_count"
AVG_httpd_process_size=$(expr $total_httpd_processes_size / $total_http_processes_count)
echo "AVG_httpd_process_size=$AVG_httpd_process_size"
total_httpd_process_size_MB=$(expr $AVG_httpd_process_size / 1024)
echo "total_httpd_process_size_MB=$total_httpd_process_size_MB"
total_pttpd_used_size=$(expr $total_httpd_processes_size / 1024)
echo "total_pttpd_used_size=$total_pttpd_used_size"
total_RAM_size=`free -m |grep Mem |awk '{print $2}'`
echo "total_RAM_size=$total_RAM_size"
total_used_size=`free -m |grep Mem |awk '{print $3}'`
echo "total_used_size=$total_used_size"
size_all_other_processes=$(expr $total_used_size - $total_pttpd_used_size)
echo "size_all_other_processes=$size_all_other_processes"
remaining_memory=$(($total_RAM_size - $size_all_other_processes))
echo "remaining_memory=$remaining_memory"
MaxClients=$((($total_RAM_size - $size_all_other_processes) / $total_httpd_process_size_MB))
echo "MaxClients=$MaxClients"
exit
```

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看更多内容请参见[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**将像你这样的开发人员安置在顶级创业公司和科技公司**](https://jobs.levelup.dev/talent/welcome?referral=true)
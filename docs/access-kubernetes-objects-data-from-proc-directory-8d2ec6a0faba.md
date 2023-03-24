# 从/Proc 目录访问 Kubernetes 对象数据

> 原文：<https://levelup.gitconnected.com/access-kubernetes-objects-data-from-proc-directory-8d2ec6a0faba>

## “proc”目录和一些用例的概述

## /proc 目录概述

目录 **/proc** 是一个惊人的概念。它并不真的存在，然而我们可以分析它。它的零长度文件既不是二进制也不是文本，但是我们可以检查和显示它们。这个特殊的目录保存了我们的 Linux 系统的所有细节，比如内核、进程和配置参数。通过理解 **/proc** 目录，我们可以了解 Linux 命令是如何工作的，我们甚至可以执行一些管理任务。

![](img/52f1299384ab657c8255f93cfa97f693.png)

来自:[wizardzines.com](https://wizardzines.com/comics/proc/)

**/proc** 目录被组织在虚拟目录和子目录中，它按照相似的主题对文件进行分组。作为根用户，`**ls /proc**`命令显示如下内容:

```
>> cd /proc
>> ls 

1      19     21220  24840  28408  30323  34754  477   614   87         consoles     kmsg          self
10     190    21254  24908  28466  30324  34755  478   621   88         cpuinfo      kpagecgroup   slabinfo
100    192    21259  24947  285    30325  34756  488   70    89         crypto       kpagecount    softirqs
109    194    21292  24991  28847  30326  34757  490   701   9          devices      kpageflags    stat
11     196    214    25051  28850  30327  34758  493   71    90         diskstats    loadavg       swaps
112    199    216    25081  28881  30328  34759  544   72    91         dma          locks         sys
12     2      21980  25735  28949  32163  34760  547   7204  92         driver       mdstat        sysrq-trigger
125    20     22     25763  28955  32257  34761  548   73    93         execdomains  meminfo       sysvipc
13406  200    22553  25781  28957  32280  34762  559   74    94         fb           misc          thread-self
14     202    22878  25827  29175  32288  34763  560   75    95         filesystems  modules       timer_list
15     205    23     25847  29933  32554  34786  561   76    96         fs           mounts        tty
16     207    23840  25848  29961  32598  34810  567   77    97         interrupts   mtrr          uptime
164    21     23936  25966  3      34153  355    569   78    98         iomem        net           version
165    210    23993  26046  30202  34748  390    570   8     99         ioports      pagetypeinfo  version_signature
166    211    24     26071  30239  34749  4      5788  81    acpi       irq          partitions    vmallocinfo
17     21123  24037  28222  30319  34750  400    588   82    buddyinfo  kallsyms     pressure      vmstat
18     21135  244    28284  30320  34751  474    595   84    bus        kcore        sched_debug   zoneinfo
186    21137  24751  28355  30321  34752  475    6     85    cgroups    key-users    schedstat
188    21208  24800  284    30322  34753  476    606   86    cmdline    keys         scsi
```

更多关于[**【Proc】**](https://www.linux.com/news/discover-possibilities-proc-directory/)

## 从/proc 目录访问 kubernetes 对象数据

假设，我们在一个基于 Kubernetes 集群的 kubeadm 中。我们可以访问运行 kubernetes 组件的控制面板(主节点)。如果我们的 **etcd** (键值存储)作为一个 pod 在**控制平面**节点内运行，我们可以通过利用 **/proc** 目录轻松访问存储在 etcd 上的数据。为此，我们必须登录到**控制平面**节点。然后我们需要找出 etcd 的**进程 ID** 。

找出 **etcd** 的**进程 ID** :

```
#  Find out the etcd process id
controlplane $ ps aux | grep -i etcd

root       25966  2.5  2.2 11214776 46124 ?      Ssl  04:52   0:10 etcd --advertise-client-urls=https://172.30.1.2:2379 root       28957  4.6 15.4 1112260 314108 ?      Ssl  04:53   0:14 kube-apiserver --advertise-address=172.30.1.2 --allow-privileged=true --authorization-mode=Node,RBAC --client-ca-file=/etc/kubernetes/pki/ca.crt --enable-admission-plugins=NodeRestriction --enable-bootstrap-token-auth=true --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt --etcd-certfile=/etc/kubernetes/pki/apiserver-etcd-client.crt --etcd-keyfile=/etc/kubernetes/pki/apiserver-etcd-client.key --etcd-servers=https://127.0.0.1:2379 --kubelet-client-certificate=/etc/kubernetes/pki/apiserver-kubelet-client.crt --kubelet-client-key=/etc/kubernetes/pki/apiserver-kubelet-client.key --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname --proxy-client-cert-file=/etc/kubernetes/pki/front-proxy-client.crt --proxy-client-key-file=/etc/kubernetes/pki/front-proxy-client.key --requestheader-allowed-names=front-proxy-client --requestheader-client-ca-file=/etc/kubernetes/pki/front-proxy-ca.crt --requestheader-extra-headers-prefix=X-Remote-Extra- --requestheader-group-headers=X-Remote-Group --requestheader-username-headers=X-Remote-User --secure-port=6443 --service-account-issuer=https://kubernetes.default.svc.cluster.local --service-account-key-file=/etc/kubernetes/pki/sa.pub --service-account-signing-key-file=/etc/kubernetes/pki/sa.key --service-cluster-ip-range=10.96.0.0/12 --tls-cert-file=/etc/kubernetes/pki/apiserver.crt --tls-private-key-file=/etc/kubernetes/pki/apiserver.key
```

通过执行***【PS aux】****命令，我们提取了 etcd 的**进程 id** 。现在，移动到 **"/proc/25966"** 目录并检查该目录的内容:*

```
*controlplane $ cd /proc/25966
controlplane $ ls -la

...
lrwxrwxrwx   1 root root 0 Dec 14 05:21 exe -> /usr/local/bin/etcd
-r--------   1 root root 0 Dec 14 05:21 environ
dr-x------   2 root root 0 Dec 14 05:13 fd
...*
```

*在“**/proc/25966”**文件夹下有很多文件和文件夹。但目前我们感兴趣的是“ **exe** ”、“ **environ** ”、“ **fd** ”文件。*

***exe** —指向进程二进制文件的符号链接。
**fd** —包含进程的所有文件描述符，显示它正在使用哪些文件或设备。
**环境—** 显示过程的所有环境变量。*

## *访问 kubernetes 的“秘密”数据:*

*现在，让我们在 kubernetes 集群上创建一个**秘密**对象:*

```
*controlplane $ kubectl create secret generic test-secret \
  --from-literal=secret-password=53546235476523645*
```

*然后移动到**"/proc/etcd-process-ID/FD "**目录，列出所有文件:*

```
*controlplane $ cd /proc/25966/fd
controlplane $ ls -la

...
lrwx------ 1 root root 64 Dec 14 05:31 10 -> /var/lib/etcd/member/snap/db  #***
...
lrwx------ 1 root root 64 Dec 14 05:31 14 -> 'socket:[83836]'
lrwx------ 1 root root 64 Dec 14 05:31 15 -> 'socket:[83939]'
lrwx------ 1 root root 64 Dec 14 05:31 16 -> 'socket:[83940]'*
```

***"/proc/etcd-process-ID/FD "**目录下有很多文件。但是我们感兴趣的是一个名为**"/var/lib/etcd/member/snap/db "**的文件，它包含了所有的 etcd 数据。在我们的案例中，文件编号是**“10”**(在您的案例中可能会有所不同)。*

*现在，检查文件编号**“10”**并尝试访问 kubernetes 的秘密:*

```
 *[Secret-key]
controlplane $ cat 10 | strings | grep -A2 secret-password

6{"f:data":{".":{},"f:secret-password":{}},"f:type":{}}B
secret-password    #<---
53546235476523645   #<---
Opaque
--
6{"f:data":{".":{},"f:secret-password":{}},"f:type":{}}B
secret-password     #<---
53546235476523645   #<---
Opaque*
```

*从上图中我们可以看到，我们能够从 **/proc** 目录中获得 Kubernetes **secrets** 数据。*

## *访问 pod“环境”变量:*

*创建一个定义了**环境**变量的新 pod:*

```
*controlplane $  kubectl run pod --image=httpd --env="USERNAME=admin"

controlplane $  kubectl get pod pod  -o wide

NAME   READY   STATUS    RESTARTS   AGE   IP            NODE     
pod    1/1     Running   0          25s   192.168.1.3   node01 #*<--* 
```

*正如我们可以看到的，我们的 pod 运行在 worker-node name **node01** 上。因此，为 pod 运行的流程驻留在“**node 01**worker 节点中。让我们把**宋承宪**带入到**node 01**worker 节点。然后尝试找出运行“ **httpd** ”容器的 pod 的 **process-id** 。*

```
*node01 $  ssh node01

node01 $  ps aux | grep -i httpd  

root       37792  0.2  0.2   5996  4688 ?        Ss   06:09   0:00 httpd -DFOREGROUND
...*
```

*就像我们正在运行的“ **httpd** ”容器内的吊舱。我们已经搜索了运行“ **httpd** ”服务的进程。*

*现在，让我们检查"**/proc/httpd _ process _ id/environ "**文件:*

```
*node01 $ cat /proc/37792/environ | strings | grep -i USERNAME

USERNAME=admin*
```

*从上图中我们可以看到，从 **/proc** 目录中，我们能够访问运行“ **httpd** 容器的 pod 的 **env** 变量。*

> **如果你觉得这篇文章很有帮助，请点击* ***跟随*** *👉******拍手*** *👏* *按钮帮助我写更多这样的文章。
> 谢谢🖤****

## ***👉所有关于 Linux 的文章***

***![Md Shamim](img/b46bdc53005abde6c6cb3e8ff0c200c3.png)

[Md 沙米姆](https://medium.com/@shamimice03?source=post_page-----8d2ec6a0faba--------------------------------)*** 

## ***所有关于 Linux 的文章***

***[View list](https://medium.com/@shamimice03/list/all-articles-on-linux-1339e15e3304?source=post_page-----8d2ec6a0faba--------------------------------)******12 stories******![](img/259cf1a3ab76526a3f714f7cbaffac3d.png)******![](img/985a8b8ce1f1090a033d94ee7ac5f4fd.png)******![](img/d917609dcb8b45812e60ccd8bf2ed277.png)***

## ***👉关于 Kubernetes 的所有文章***

***![Md Shamim](img/b46bdc53005abde6c6cb3e8ff0c200c3.png)

[Md 沙米姆](https://medium.com/@shamimice03?source=post_page-----8d2ec6a0faba--------------------------------)*** 

## ***关于 Kubernetes 的所有文章***

***[View list](https://medium.com/@shamimice03/list/all-articles-on-kubernetes-7ae1a0f96f3b?source=post_page-----8d2ec6a0faba--------------------------------)******24 stories******![](img/f1050aa27a3ef03122558b1ba1de1f58.png)******![](img/f1c4131e92176033bce05392de197205.png)******![](img/27d4385154af67764cf37713dbbdc38e.png)***
# 码头工人的服装| Kubernetes

> 原文：<https://levelup.gitconnected.com/apparmor-for-docker-kubernetes-e82ef023a10c>

## docker 和 kubernetes 的 AppArmor

![](img/3768d8b2b2cbbaca0d384239dbe9217d.png)

阿尔贝托·罗德里格斯·桑塔纳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

AppArmor 是一个 Linux 内核安全模块，允许系统管理员通过每个程序的配置文件来限制程序的能力。配置文件可以允许网络访问、原始套接字访问等功能，以及在匹配路径上读取、写入或执行文件的权限。

在本文中，我们将讨论如何将 AppArmor 配置文件与 docker 容器和 kubernetes pods 一起使用。

如果你是 AppArmor 概念的新手，比如如何使用 AppArmor 和如何创建 AppArmor 档案，那么我强烈建议你看看下面的一些资源:
[AppArmor 综合指南](https://medium.com/information-and-technology/so-what-is-apparmor-64d7ae211ed)
[快速档案语言](https://gitlab.com/apparmor/apparmor/wikis/QuickProfileLanguage)

## 码头工人的服装

默认情况下，docker 容器将使用`docker-default` AppArmor 配置文件运行，除非我们用`security-opt`选项覆盖它。

当我们将 docker runtime 安装到系统中时，它会自动生成一个 AppArmor 配置文件。如果我们在系统上执行`aa-status`，我们会看到`docker-default`已经被加载了。

```
>> aa-status

#----------------------------------------------------------------------------

apparmor module is loaded.
30 profiles are loaded.
30 profiles are in enforce mode.
....
docker-default   # <--***
....
```

## 没有定制的外观轮廓

现在，让我们创建一个名为`nginx-container`的`nginx`容器:

```
>>  docker run -p 80:80 -d --name nginx-container nginx
```

现在，`exec`进入 pod 并尝试使用一些 Linux 实用程序，例如— `ping`和`sh`:

```
# exec into the container
>> docker exec -it nginx-container bash 

# ping
root@b3d4f894386b:~  apt update &&  apt upgrade
root@b3d4f894386b:~  apt-get install iputils-ping
root@b3d4f894386b:~  ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=110 time=0.537 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=110 time=0.427 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=110 time=0.531 ms

#-------------------------------------------------------
# sh
root@b3d4f894386b:~  sh
/ echo "test"
test

#-------------------------------------------------------
# add a new user and change the owner of the etc directory
root@b3d4f894386b:~ useradd test
root@b3d4f894386b:~ chown test:test etc
root@b3d4f894386b:~ ls -la | grep etc
drwxr-xr-x   1 test test 4096 Nov 16 02:23 etc  # etc directory's owner changed to "test" user
```

## 带有定制的外观轮廓

我们可以为`nginx-container`使用以下自定义 AppArmor 配置文件，它将帮助我们阻止`ping`和`sh`以及许多其他功能:

```
#include <tunables/global>
profile docker-nginx flags=(attach_disconnected,mediate_deleted) {
  #include <abstractions/base>
  network inet tcp,
  network inet udp,
  network inet icmp,
  deny network raw,
  deny network packet,
  file,
  umount,
  deny /bin/** wl,
  deny /boot/** wl,
  deny /dev/** wl,
  deny /etc/** wl,
  deny /home/** wl,
  deny /lib/** wl,
  deny /lib64/** wl,
  deny /media/** wl,
  deny /mnt/** wl,
  deny /opt/** wl,
  deny /proc/** wl,
  deny /root/** wl,
  deny /sbin/** wl,
  deny /srv/** wl,
  deny /tmp/** wl,
  deny /sys/** wl,
  deny /usr/** wl,
  audit /** w,
  /var/run/nginx.pid w,
  /usr/sbin/nginx ix,
  deny /bin/dash mrwklx,
  deny /bin/sh mrwklx,
  deny /usr/bin/top mrwklx,

  capability chown,
  capability dac_override,
  capability setuid,
  capability setgid,
  capability net_bind_service,
  deny @{PROC}/* w,   # deny write for all files directly in /proc (not in a subdir)
  # deny write to files not in /proc/<number>/** or /proc/sys/**
  deny @{PROC}/{[^1-9],[^1-9][^0-9],[^1-9s][^0-9y][^0-9s],[^1-9][^0-9][^0-9][^0-9]*}/** w,
  deny @{PROC}/sys/[^k]** w,  # deny /proc/sys except /proc/sys/k* (effectively /proc/sys/kernel)
  deny @{PROC}/sys/kernel/{?,??,[^s][^h][^m]**} w,  # deny everything except shm* in /proc/sys/kernel/
  deny @{PROC}/sysrq-trigger rwklx,
  deny @{PROC}/mem rwklx,
  deny @{PROC}/kmem rwklx,
  deny @{PROC}/kcore rwklx,
  deny mount,
  deny /sys/[^f]*/** wklx,
  deny /sys/f[^s]*/** wklx,
  deny /sys/fs/[^c]*/** wklx,
  deny /sys/fs/c[^g]*/** wklx,
  deny /sys/fs/cg[^r]*/** wklx,
  deny /sys/firmware/** rwklx,
  deny /sys/kernel/security/** rwklx,
} 
```

将上面定义的 AppArmor 配置文件保存到主机系统。我们可以将轮廓保存在`/etc/apparmor.d/docker-nginx`文件中。然后使用`apparmor_parser`命令加载 AppArmor 配置文件，使其可用于容器。

```
# loading AppArmor Profile
>> apparmor_parser /etc/apparmor.d/docker-nginx

# verify
>> aa-status

#----------------------------------------------------------------------------
apparmor module is loaded.
30 profiles are loaded.
30 profiles are in enforce mode.
....
docker-default   
docker-nginx      # <--***
....
```

**卸载:**使用以下命令卸载一个 AppArmor 配置文件:

```
# [PATH TO THE PROFILE]
>> apparmor_parser -R  /etc/apparmor.d/docker-nginx
```

在分离模式下创建`nginx-container`以及自定义 AppArmor 概要文件，这样我们就可以" **exec"** 到运行容器中。

```
>> docker run --security-opt "apparmor=docker-nginx" \
     -p 80:80 -d --name apparmor-nginx nginx
```

现在让我们尝试一些操作来测试 AppArmor 配置文件:

```
>> docker container exec -it apparmor-nginx bash

root@1bafbecdf454:/~ sh
bash: /bin/sh: Permission denied              #<----

root@1bafbecdf454:/~ useradd test
Cannot open audit interface - aborting.       #<----

root@1bafbecdf454:/~ apt update &&  apt upgrade
...
W: chmod 0700 of directory /var/lib/apt/lists/partial failed - 
SetupAPTPartialDirectory (1: Operation not permitted)   #<----
...
```

在上图中，我们可以看到 AppArmor 配置文件产生了差异并阻止了几个操作。

## Kubernetes 的 AppArmor

## 要求

●容器运行时(如— docker、containerd、cri-o 等)必须支持 AppArmor。
● AppArmor 必须安装在将调度 pod 的每个节点上。
● AppArmor 配置文件必须在每个节点上都可用。
●每个集装箱都规定了外观轮廓。

要为带有 pod 的容器指定 AppArmor 配置文件，我们必须以下列方式向 Pod 的元数据添加注释:

```
annotations:
   container.apparmor.security.beta.kubernetes.io/<container_name>: <profile_ref>
```

其中，`<container_name>`是要应用概要文件的容器的名称，`<profile_ref>`指定要应用的概要文件。`profile_ref`可以是以下之一:

> `***runtime/default***` *应用运行时的默认 profile(如* `*docker-default*` *profile)*
> 
> `***localhost/<profile_name>***` *应用主机上加载的概要文件，名称为* `*<profile_name>*`
> 
> `***unconfined***` *表示不会加载任何配置文件*

下面是我们如何在一个特定集装箱的 pod 清单中指定 AppArmor 配置文件的示例。

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-1
  annotations:
    container.apparmor.security.beta.kubernetes.io/<container-name>: localhost/<profile-name>
spec:                                                  
  containers:
  - name: <container-name>   #<----
    image: nginx
```

## 游戏攻略

假设我们有下面的 AppArmor 配置文件，它将从 pod 内部阻塞' **top** '命令。

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  file,
  deny /bin/top mrwklx,  
}
```

为了使 AppArmor 配置文件在所有 worker 节点上都可用，我们可以手动将 **ssh** 放入每个节点，然后使用`apparmor_parser`加载 AppArmor 配置文件。

但是在生产环境中，可能会有一堆工作节点。在这种情况下，我们可以使用下面的脚本将 AppArmor 配置文件加载到节点。

```
NODES=(
    # The SSH-accessible domain names of your nodes
    node01)
for NODE in ${NODES[*]}; do ssh $NODE 'sudo apparmor_parser -q <<EOF
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  file,
  deny /bin/top mrwklx,
}
EOF'
done
```

现在让我们将 AppArmor 配置文件附加到 pod 清单:

```
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:                                     
    container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
spec:
  containers:
  - name: hello
    image: busybox:1.28
    command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
```

然后最后创建 pod 并将' **exec** '放入 pod 中，并尝试执行' **top** 命令:

```
>> kubectl create -f pod.yaml

>> k exec -it hello-apparmor -- sh
----------------------------------------------------------------------
/~ top
sh: top: Permission denied   # It indicates that AppArmor profile operating perfectly
```

## 然后

[](/seccomp-secure-computing-mode-kubernetes-docker-97130516662c) [## Seccomp —安全计算模式| Kubernetes | Docker

### docker 和 kubernetes 的 Seccomp

levelup.gitconnected.com](/seccomp-secure-computing-mode-kubernetes-docker-97130516662c) 

如果你觉得这篇文章有帮助，请**不要忘记**点击**跟随**👉**T5 和**拍手**👏按钮帮我写更多这样的文章。
谢谢🖤**

# 🚀👉**Kubernetes 上的所有文章**

![Md Shamim](img/b46bdc53005abde6c6cb3e8ff0c200c3.png)

[Md 沙米姆](https://medium.com/@shamimice03?source=post_page-----e82ef023a10c--------------------------------)

## 关于 Kubernetes 的所有文章

[View list](https://medium.com/@shamimice03/list/all-articles-on-kubernetes-7ae1a0f96f3b?source=post_page-----e82ef023a10c--------------------------------)24 stories![](img/f1050aa27a3ef03122558b1ba1de1f58.png)![](img/f1c4131e92176033bce05392de197205.png)![](img/27d4385154af67764cf37713dbbdc38e.png)

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)
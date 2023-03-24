# 如何在 Ubuntu 20.04 上使用 KVM 设置桥接网络

> 原文：<https://levelup.gitconnected.com/how-to-setup-bridge-networking-with-kvm-on-ubuntu-20-04-9c560b3e3991>

## 如何让您的虚拟机被您的家庭网络看到？

![](img/049783cc6a32405258fcddef624262ce.png)

图片由 [PIRO4D](https://pixabay.com/users/PIRO4D-2707530/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2324875) 来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2324875)

KVM 提供的默认网络是一个私有网桥，它使用 NAT 来允许来宾虚拟机与外界通信。这适用于许多用例，并且可能是最好的默认设置。但有时我需要虚拟机与主机共享网络。在我过去的许多创建虚拟机的[文章](https://medium.com/better-programming/playing-with-vms-and-kubernetes-26ef93019c22)中，我已经能够摆脱默认网络。但是我正在从事的一个未来项目将需要在主机网络上运行的虚拟机，所以我想在这里记录如何做到这一点。

关于架设公共桥梁的文章很多；这似乎是一个热门话题。但是许多文章都是旧的，没有使用 Ubuntu 最新的网络配置软件，或者他们用 GUI 界面管理网络和 KVM。我将只使用命令行工具，因为我的服务器实际上没有监视器。

这里是我对设置 KVM 默认使用公共网桥的看法。要继续学习，您应该对 Linux、KVM 和现代 Linux 网络有很好的了解。我们将使用 Ubuntu 20.04，它使用`iproute2`包进行命令行访问，使用`netplan`进行配置。Ubuntu 20.04 于 2020 年 4 月发布(因此版本号为 20.04)，但我从年初开始就一直在使用日常版本来熟悉它。

首先，我要安装 KVM，即虚拟机管理器。对于 Ubuntu 20.04，安装非常简单:

```
sudo apt-get install qemu-kvm libvirt-daemon-system \
   libvirt-clients virtinst bridge-utils
```

测试它与`virsh list --all`一起工作。这应该会显示一个空列表，但列表仍然是:

```
rkamradt@artful:~$ virsh list --all
 Id   Name   State
--------------------
```

我们要做的下一件事是替换默认桥接。如上所述，KVM 安装了一个虚拟网桥，所有虚拟机都连接到该网桥。它提供自己的子网和 DHCP 来配置来宾网络，并使用 NAT 来访问外部世界。我们将用运行在主机网络上并使用主机网络上任何外部 DHCP 服务器的公共网桥来取代它。

出于性能原因，建议禁用主机网桥上的`netfilter`。为此，创建一个名为`/etc/sysctl.d/bridge.conf`的文件，并在其中填入以下内容:

```
net.bridge.bridge-nf-call-ip6tables**=**0
net.bridge.bridge-nf-call-iptables**=**0
net.bridge.bridge-nf-call-arptables**=**0
```

然后创建一个名为`/etc/udev/rules.d/99-bridge.rules`的文件，并添加这一行:

```
ACTION**==**"add", SUBSYSTEM**==**"module", KERNEL**==**"br_netfilter", \           RUN+**=**"/sbin/sysctl -p /etc/sysctl.d/bridge.conf"
```

这些都应该在一行上。这将在系统启动时在适当的位置设置标志以禁用网桥上的`netfilter`。重新启动才能生效。

接下来，我们需要禁用 KVM 为自己安装的默认网络。您可以使用`ip`来查看默认网络的样子:

```
rkamradt@beast:~$ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DEFAULT group default qlen 1000
    link/ether d4:be:d9:f3:1e:5f brd ff:ff:ff:ff:ff:ff
6: virbr0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 52:54:00:1d:5b:25 brd ff:ff:ff:ff:ff:ff
7: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc fq_codel master virbr0 state DOWN mode DEFAULT group default qlen 1000
    link/ether 52:54:00:1d:5b:25 brd ff:ff:ff:ff:ff:ff
```

条目`virbr0`和`virbr0-nic`是 KVM 默认安装的。

因为这台主机刚刚安装了 KVM，所以我不必担心现有的虚拟机。如果您有现有的虚拟机，您必须编辑它们以使用新的网络设置。你可以同时拥有公共和私有虚拟机，但是为了避免混淆，我宁愿每台主机只有一种类型的网络。以下是移除默认 KVM 网络的方法:

```
virsh net-destroy default
virsh net-undefine default
```

现在，您可以再次运行`ip`，而`virbr0`和`virbr0-nic`应该会消失。

```
rkamradt@beast:~$ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DEFAULT group default qlen 1000
    link/ether d4:be:d9:f3:1e:5f brd ff:ff:ff:ff:ff:ff
```

如果不是，您可以使用`ip link delete virbr0 type brigde`和`ip link delete virbr0-nic`将其移除。

接下来，我们需要设置一个桥，以便在创建虚拟机时使用。编辑你的`/etc/netplan/00-installer-config.yaml`(备份后)添加一个桥。这是我的编辑后的样子:

```
network:
  ethernets:
    enp0s7:
      dhcp4: false
      dhcp6: false
  bridges:
    br0:
      interfaces: [ enp0s7 ]
      addresses: [192.168.0.104/24]
      gateway4: 192.168.0.1
      mtu: 1500
      nameservers:
        addresses: [8.8.8.8,8.8.4.4]
      parameters:
        stp: true
        forward-delay: 4
      dhcp4: no
      dhcp6: no
  version: 2
```

在我的例子中，`enp0s7`是我的 NIC 的名称，`192.168.0.104`是我的主机的 IP 地址，`192.168.0.1`是我的家庭路由器。我已经设置了我的家庭路由器，通过将 MAC 地址与 IP 地址相关联来为该主机保留`192.168.0.104`。网桥`br0`连接到`enp0s7`接口，即主机上的物理网卡。请注意，`enp0s7`接口没有配置，网桥现在有了以前在`enp0s7`部分指定的网络配置。我不确定`parameters`部分，我直接从一个[示例设置](https://fabianlee.org/2019/04/01/kvm-creating-a-bridged-network-with-netplan-on-ubuntu-bionic/)中复制了它们，以后我可能想玩玩它们。

现在运行`sudo netplan apply`来应用您的新配置。您可以使用`ip`命令来检查它看起来是否正确:

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel master br0 state UP group default qlen 1000
    link/ether 00:1d:72:ac:d9:95 brd ff:ff:ff:ff:ff:ff
13: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether 00:1d:72:ac:d9:95 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.104/24 brd 192.168.0.255 scope global br0
       valid_lft forever preferred_lft forever
    inet6 fe80::21d:72ff:feac:d995/64 scope link 
       valid_lft forever preferred_lft forever
```

注意，`br0`条目现在有了 IP 地址，而`enp0s7`条目现在有了`master br0`来表明它属于网桥。

现在我们可以让 KVM 知道这个桥。创建一个名为`host-bridge.xml`的临时 XML 文件，并插入以下内容:

```
<network>
  <name>host-bridge</name>
  <forward mode="bridge"/>
  <bridge name="br0"/>
</network>
```

使用以下命令使其成为虚拟机的默认网桥:

```
virsh net-define host-bridge.xml
virsh net-start host-bridge
virsh net-autostart host-bridge
```

然后列出网络以确认它已设置为自动启动:

```
rkamradt@artful:~$ virsh net-list --all
 Name          State    Autostart   Persistent
------------------------------------------------
 host-bridge   active   yes         yes
```

现在我们已经配置了一个桥，我们可以创建一个虚拟机。以下是我使用的模板:

一旦`virt-install`程序运行，你会看到安装程序在你的终端上运行。您可以随意安装它，但是当它提示安装额外的软件时，请确保安装 OpenSSH，因为我们将用它来与我们的新虚拟机进行通信。现在在你等待安装的时候喝杯咖啡吧。

有一点不方便的是，它用 DHCP 配置虚拟机的网络，而不是询问你是否想手动配置。对于服务器，最好手动配置一个来自 DHCP 地址池外部的静态 IP 地址。甚至`virsh`工具也不知道新客人的网络。

```
rkamradt@artful:~$ virsh domifaddr node1
 Name       MAC address          Protocol     Address
--------------------------------------------------------------------
```

我能找到地址的唯一方法是在我家的路由器上寻找`node1`。我的家用路由器试图根据 MAC 地址保持 IP 地址相同，但这并不能保证，所以我将其添加到静态 IP 列表中。每个路由器都是不同的，所以你必须弄清楚如何用你自己的路由器做到这一点。将新的 VM 添加到您计划访问它的任何主机上的`/etc/hosts`文件中，这样您就可以通过名称来引用它，而不是记住 IP 地址。

再次使用`ip`命令在主机上查看您的网络:

```
rkamradt@artful:~$ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp0s7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel master br0 state UP mode DEFAULT group default qlen 1000
    link/ether 00:1d:72:ac:d9:95 brd ff:ff:ff:ff:ff:ff
13: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 00:1d:72:ac:d9:95 brd ff:ff:ff:ff:ff:ff
15: vnet0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel master br0 state UNKNOWN mode DEFAULT group default qlen 1000
    link/ether fe:54:00:a3:0d:ee brd ff:ff:ff:ff:ff:ff
```

它将`vnet0`加入了网络。对于你安装的每一个虚拟机，它都会增加一个新的`vnet#`,我不确定为什么，我希望我这样做是对的，但是看起来很有效。

一旦您的 VM 配置好了，您就必须将您的 ssh 凭证传递给它。最简单的方法是使用`ssh-copy-id <username>@<guestname>`如果您的用户名在主机和客户计算机之间相同，则不需要`<username>@`部分。然后你应该可以连接到`ssh <username>@<guestname>`。

一旦进入 guest，您可以使用`ip`命令查看网络:

```
rkamradt@node1:~$ ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 52:54:00:a3:0d:ee brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.166/24 brd 192.168.0.255 scope global enp1s0
       valid_lft forever preferred_lft forever
    inet6 fe80::5054:ff:fea3:dee/64 scope link 
       valid_lft forever preferred_lft forever
```

这次我使用了`ip addr`子命令来查看 IP 地址，而不仅仅是 MAC 地址。请注意，IP 地址与主机在同一个网络中，在这种情况下`192.168.0.0/24`您应该能够在网络中的任何地方访问它。

我绝不是网络专家，但我通常能努力让事情运转起来。希望这将帮助您掌握网络和新一代的命令和配置。如果你只在云提供商的虚拟机上工作，大部分工作都是通过简单的用户界面来完成的。但是，如果你只是像我一样的爱好者，想要在裸机上创建虚拟机来尝试不同的软件，并且可以轻松地随意启动和关闭它们，那么了解幕后发生的事情是很好的。
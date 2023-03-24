# 在几分钟内测试本地 libvirt 虚拟机上的 Windows Ansible 角色。

> 原文：<https://levelup.gitconnected.com/test-windows-ansible-roles-on-local-libvirt-vms-within-minutes-e5dfa031bc4>

## ansi ble | Testing | libvirt | Windows | KVM

## 在几分钟内启动 Windows 虚拟机，测试您的可行角色。

![](img/7ea69655f20529e00b44bbbf3a21945d.png)

杰夫·哈迪在 [Unsplash](https://unsplash.com/s/photos/windows-computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

如果你还没有看过，也可以看看我之前写的关于用 Molecule 测试角色的文章。

[](https://betterprogramming.pub/testing-ansible-roles-locally-with-molecule-on-linux-and-windows-a0903ec92496) [## 在 Linux 和 Windows 上用 Molecule 在本地测试角色

### 加快您的开发周期，满怀信心地发布

better 编程. pub](https://betterprogramming.pub/testing-ansible-roles-locally-with-molecule-on-linux-and-windows-a0903ec92496) 

# 在 15 分钟内为虚拟机准备好映像

这是一个简单的指南，可以让 Windows 虚拟机在几分钟内运行，以测试您的角色。

我发布的 Ansible 角色允许您通过无人值守安装“从头开始”创建 Windows 虚拟机。虽然这是完全可复制的，并且非常有效，但是从 CD 启动，运行安装程序，重新启动系统，直到 Ansible 最终可以在上面运行角色，显然需要花费很长时间(大约 5 分钟)。

因此，本指南分为两个步骤，第一步:从头开始创建虚拟机，为此您需要上述耐心；第二步:保存虚拟机的映像，以便用它来更快地启动未来的测试虚拟机。

[](https://github.com/DrPsychick/ansible-testing) [## GitHub-DrPsychick/ansible-testing:一个 ansi ble 角色，允许为…

### 允许创建 SystemD 实例来测试角色的一个角色

github.com](https://github.com/DrPsychick/ansible-testing) 

# 创建无人值守安装的虚拟机

创建一个角色并从`DrPsychick/ansible-testing`下载示例`libvirt`配置:

```
molecule init role demo.testrole
cd testrole
molecule init scenario libvirtfor f in create destroy molecule requirements vars; do
  curl -o molecule/libvirt/$f.yml https://raw.githubusercontent.com/DrPsychick/ansible-testing/master/docs/molecule/libvirt/$f.yml
done
```

调整`molecule/libvirt`中的配置，定义您想要测试的 Windows 版本。您在此处定义的管理员密码将被保存到映像中。

`vars.yml`

```
virtual_machines:
  - name: "windows2016"
    password: "Ecoo6pev"
    os: "windows"
    isos:
      - name: "WindowsServer2016.iso"
        dev: sda
      - name: "virtio-win.iso"
        dev: sdb
      - name: "scripts-windows2016.iso"
        dev: sdc
    disk_size: 20G
    image_index: 2
    drivers:
      - 'E:\amd64\2k16'
      - 'E:\NetKVM\2k16\amd64'
      - 'E:\Balloon\2k16\amd64'
    mac: "02:00:00:00:13:37"
    vnc_port: 56682
```

如果您更改了虚拟机的名称，不要忘记相应地调整`molecule.yml`:

```
[...]
platforms:
  - name: windows2016
[...]
```

在创建 VM 之前，您需要从公共资源下载适当的 ISO 文件，因为它们不包含在角色中，请参见[https://github . com/drpsycick/ansi ble-testing # test-with-windows-VMs](https://github.com/DrPsychick/ansible-testing#test-with-windows-vms)

现在运行`molecule create -s libvirt`，等待虚拟机启动并运行。您可以通过已配置的`vnc_port`上的 VNC 连接到虚拟机来观察进度。如果您得到一个`sudo: a password is required`错误，运行`sudo echo`然后再试一次。

完成所有检查后，关闭虚拟机:`sudo virsh shutdown windows2016`。

# 保存图像以备后用

现在，创建一个现成图像的 zip 文件。这需要几分钟时间。图像必须具有实例的名称，并且存储在没有路径的 zip 文件中。

```
(cd /var/lib/libvirt/images; zip windows2016-clean.zip windows2016.qcow2)
```

将 zip 文件移动到`iso_dir`，以便`DrPsychick/ansible-testing`角色可以找到它并将其用于新的虚拟机。

```
cd /var/lib/libvirt/images
mv windows2016-clean.zip ../isos/
```

# 配置 ansible-testing 以使用压缩图像

销毁分子场景进行清理:`molecule destroy -s libvirt`，这将删除原来的 VM 磁盘镜像。

现在将`vars.yml`中的定义改为使用压缩图像:

```
virtual_machines:
  - name: "windows2016"
    password: "Ecoo6pev"
    os: "windows"
    disk_image: "windows2016-clean.zip"
    mac: "02:00:00:00:13:37"
    vnc_port: 56682
```

你完了！

当您现在运行`molecule create -s libvirt`时，您会注意到 Windows 实例是如何在不到两分钟的时间内出现的，并且可以通过 WinRM 直接访问。所以创建实例至少比快一倍。

万一您在远程 Linux 机器上运行 Molecule，如果需要，您可以简单地执行 SSH 端口转发来连接到 VNC:

```
ssh -L <vnc_port>:localhost:<vnc_port> user@remote-machine
# now connect with VNC to localhost:<vnc_port>
```

## 参考资料:

[](https://github.com/DrPsychick/ansible-testing) [## GitHub—DrPsychick/ansible-testing:一个允许为…创建 SystemD 实例的 ansi ble 角色

### 一个允许创建 SystemD 实例来测试角色的角色——用于分子…

github.com](https://github.com/DrPsychick/ansible-testing) [](https://molecule.readthedocs.io/en/latest/) [## 易变分子——分子文件

### Molecule 只支持 Ansible 的最新两个主要版本(N/N-1)，也就是说如果最新版本是 2.9.x…

分子. readthedocs.io](https://molecule.readthedocs.io/en/latest/) [](https://drpsychick.org/drpsychick-on-the-web-a9ccfb0df17e) [## 网上心理咨询

### 我所在的所有网站的一个简单的“登陆页”。

drpsychick.org](https://drpsychick.org/drpsychick-on-the-web-a9ccfb0df17e) 

## 谢谢大家！

…为了您的时间和兴趣！

```
**Want to Connect?**If you want to support me, sign up for Medium through my membership link: [*https://drpsychick.org/membership*](https://drpsychick.org/membership) or visit me on GitHub.
```
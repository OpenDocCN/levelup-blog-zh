# 为 Ubuntu 22.04 LTS 版在 LVM 上添加新磁盘

> 原文：<https://levelup.gitconnected.com/add-new-disk-on-lvm-for-ubuntu-22-04-lts-9aabb45636a4>

![](img/95fe04e53aad2ade69fba2ba5bdb34d7.png)

[https://images . unsplash . com/photo-1506452985227-70bc 78385 fb0？IX lib = r b-4 . 0 . 3&ixid = mnwxmja 3 fdb 8 mhxwag 90 by 1 wywdlfhx 8 fgvufdb 8 fhx 8&auto = format&fit = crop&w = 1470&q = 80](https://images.unsplash.com/photo-1506452985227-70bc78385fb0?ixlib=rb-4.0.3&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1470&q=80)

这些说明将帮助您在 Ubuntu 22.04 上向现有的逻辑卷管理(LVM)添加额外的磁盘。LVM 有助于跨多个物理磁盘设备轻松扩展存储。

我准备了一个运行手册，这样你就可以按顺序运行所有的命令，而大多数文章你不能，必须运行额外的步骤。

## 先决条件

*   新磁盘已添加到实例中

## 安装ˌ使成形

1.  找出您分配给 LVM 的磁盘

```
$ sudo pvs
  PV         VG        Fmt  Attr PSize    PFree
  /dev/vda3  ubuntu-vg lvm2 a--    <8.25g    0 
  /dev/vdb   ubuntu-vg lvm2 a--   <50.00g    0 
```

查看更详细的信息

```
$ sudo pvdisplay
  --- Physical volume ---
  PV Name               /dev/vda3
  VG Name               ubuntu-vg
  PV Size               <8.25 GiB / not usable 0   
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              2111
  Free PE               0
  Allocated PE          2111
  PV UUID               96Cnvs-1rYe-xWk8-lB2V-cLSd-ALqk-4ujiPc

  --- Physical volume ---
  PV Name               /dev/vdb
  VG Name               ubuntu-vg
  PV Size               50.00 GiB / not usable 4.00 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              12799
  Free PE               0
  Allocated PE          12799
  PV UUID               Hle3gv-3FNW-jkoe-dRFf-CwWd-pdJx-MWy3Ad
```

2.获取 LVM 的设备路径

```
$ sudo lvdisplay
  --- Logical volume ---
  LV Path                /dev/ubuntu-vg/ubuntu-lv
```

还会有更多的信息但我们只对“吕路径”感兴趣

3.找到添加到实例的新磁盘

```
$ sudo fdisk -l | grep '^Disk /dev/'
Disk /dev/loop0: 63.24 MiB, 66314240 bytes, 129520 sectors
Disk /dev/loop1: 63.23 MiB, 66301952 bytes, 129496 sectors
Disk /dev/loop2: 79.95 MiB, 83832832 bytes, 163736 sectors
Disk /dev/loop3: 102.98 MiB, 107986944 bytes, 210912 sectors
Disk /dev/loop4: 49.62 MiB, 52031488 bytes, 101624 sectors
Disk /dev/vda: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk /dev/vdb: 50 GiB, 53687091200 bytes, 104857600 sectors
Disk /dev/vdc: 500 GiB, 536870912000 bytes, 1048576000 sectors
Disk /dev/mapper/ubuntu--vg-ubuntu--lv: 558.24 GiB, 599403790336 bytes, 1170710528 sectors
```

在本例中，它将是 */dev/vdc* ，因为我向该实例添加了一个 500GB 的磁盘驱动器。

4.在新磁盘驱动器上创建物理卷

```
$ sudo pvcreate /dev/vdc
  Physical volume "/dev/vdc" successfully created.
```

5.扩展现有的卷组以包括这个新的磁盘驱动器

```
$ sudo vgextend ubuntu-vg /dev/vdc
  Volume group "ubuntu-vg" successfully extended
```

6.扩展 LV 大小以包括 100%的新磁盘

```
$ sudo lvm lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
  Size of logical volume ubuntu-vg/ubuntu-lv changed from 58.24 GiB (14910 extents) to <558.24 GiB (142909 extents).
  Logical volume ubuntu-vg/ubuntu-lv successfully resized.
```

7.现在您需要调整文件系统的大小以匹配新的大小

```
$ sudo resize2fs -p /dev/ubuntu-vg/ubuntu-lv
resize2fs 1.46.5 (30-Dec-2021)
Filesystem at /dev/ubuntu-vg/ubuntu-lv is mounted on /; on-line resizing required
old_desc_blocks = 8, new_desc_blocks = 70
The filesystem on /dev/ubuntu-vg/ubuntu-lv is now 146338816 (4k) blocks long.
```

8.现在您可以使用 df -kh 进行验证

```
$ df -kh
Filesystem                         Size  Used Avail Use% Mounted on
tmpfs                              9.9G  1.6M  9.9G   1% /run
/dev/mapper/ubuntu--vg-ubuntu--lv  550G  8.9G  519G   2% /
tmpfs                               50G     0   50G   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
tmpfs                               50G     0   50G   0% /run/qemu
/dev/vda2                          1.7G  247M  1.4G  16% /boot
tmpfs                              9.9G  4.0K  9.9G   1% /run/user/1000
```
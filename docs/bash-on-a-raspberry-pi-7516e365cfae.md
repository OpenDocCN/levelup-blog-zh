# 猛击一个树莓皮

> 原文：<https://levelup.gitconnected.com/bash-on-a-raspberry-pi-7516e365cfae>

![](img/168b6a81b04c474a0c27edd07d6e606d.png)

[Joydeep Pal](https://unsplash.com/@joy19?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

所以你已经在树莓派 Zero 上花了很多钱，[将 Buster Lite](https://www.raspberrypi.org/downloads/raspbian/) 下载到一个微型 SD 卡上，现在你又开始使用 Bourne Again Shell，俗称 [Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) 。让我来帮你吧。

在本文中，我们将介绍您需要了解的关键 Linux、Bash 和 Raspbian 命令。把这个当做记忆慢跑者。它不是每个 Linux/Raspbian 命令的完整列表，也没有介绍所有可能的选项:只是一些重要的选项。

你可以用这篇文章来查找在线手册。例如，如果您想了解更多关于`cat`命令的内容，您可以通过在终端中键入`man cat`来查找。

我们将从用户命令和一些 Bash 键盘技巧开始，然后讨论管理新系统所必需的超级用户命令。在下面的列表中，关键选项用[方括号]括起来。元描述包含在<angle brackets="">中。</angle>

## 文件系统命令

*   `pwd`打印工作目录(又名当前目录)
*   `mkdir <directory>`创建新目录
*   `cd <directory>`改变工作目录——注意:`cd ~`将带你回到你的主目录。`cd ..`将从当前目录转到父目录。而`cd /`会带你去根目录。
*   `rmdir <directory>`删除空目录
*   列出[指定]目录中[所有]文件[具有访问权限和时间戳]
*   `touch <file>`更新文件的时间戳；如果不存在，则创建一个空文件
*   `nano [file]`使用 nano 文本编辑器编辑文件内容(比`vim`文本编辑器更容易使用/学习)
*   `vim [file]`使用 vim 编辑文件内容(vim 功能强大，但比`nano`难学)
*   `cat <file>`打印文件的内容
*   打印文件的前 10 行(或指定数量的行)
*   `tail [-n 5] <file>`打印文件的最后 10 行(或指定数量的行)
*   `rm [-rf] <file>`删除一个文件【可选地递归地降低目录并强制删除】—注意:非常小心使用`-rf`选项:它不能撤销。
*   `mv <source> <destination>`移动(重命名)一个文件或目录
*   `cp <source> <destination>`复制文件
*   `chmod <permission-code> <name-list>`设置文件/目录访问权限(所有权限代码见`man chmod`)
*   `grep <search> <file-list>`在文件中搜索文本——注意:grep 可以在没有参数的情况下通过管道传输(例如`cat /etc/passwd | grep pi`)
*   `find <starting-directory> -name “file.name”`在文件系统中搜索文件
*   `<command> < <input-file> > <output-file>`使用<和>重定向命令的输入和输出
*   `<command> | <command>`将一个命令的输出作为另一个命令的输入
*   `which <command>`显示将要运行的命令的完整目录和文件地址
*   `file <file>`显示关于文件类型的信息

![](img/70a7a50dc8e5e1573e12ea694b00801b.png)

由[艾米·辛普森](https://unsplash.com/@aimssimpson?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 流程管理命令

*   `ps [aux]`报告此系统上正在运行的进程
*   `kill <pid>`使用 ps 命令中的进程标识符终止特定进程
*   `top`运行系统中顶级进程的动态实时视图
*   `htop`运行系统中顶级进程的另一个动态实时视图
*   `<command> &`在一行的末尾使用一个&符号在后台运行一个命令
*   `nohup <command> &`运行后台命令，该命令将在终端关闭后继续
*   `ctrl-c`该 ***键序列*** 取消前台运行的命令，控制返回屏幕
*   `ctrl-z`该 ***按键序列*** 暂停前台运行的命令，并将控制返回屏幕
*   `fg`恢复执行一个暂停的进程；或将后台进程移到前台
*   `bg`在后台恢复执行暂停的进程
*   `jobs` 列出暂停和后台进程【作业编号可与 fg、bg、kill、disown 一起使用】
*   `disown –h %1`从任务列表中取消第一个任务【比如 nohap:防止进程挂起】

![](img/44fdafbe6b381420b55a038f893b00eb.png)

照片由[马西莫·阿达米](https://unsplash.com/@massimo_adami?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 获取有关您的系统的信息

*   `man <command>`获取在线手册，了解有关命令的更多信息(例如`man rmdir`)
*   `date`获取当前日期、时间和时区信息
*   `uname -a`打印关于 Linux 操作系统的关键信息
*   `who`目前谁在使用这个系统？
*   `w`我的用户名是什么，我使用的是哪个终端？
*   `id`查看我真实有效的用户和群组身份
*   `du [optional-directory-name]` —列出当前或指定目录下的磁盘使用情况
*   `df`报告文件系统空间使用情况和可用磁盘空间
*   `mount`报告系统上可用(或已安装)的文件系统
*   `free`报告系统可用的空闲物理内存和交换内存(即空闲内存)
*   `lsusb`列出系统内和连接到系统的 USB 设备
*   `lsmod`列出 Linux 内核中加载的模块
*   `lsof`列出系统上所有打开的文件
*   `cat /proc/cpuinfo`显示 CPU 信息
*   `cat /proc/meminfo`显示内存使用信息
*   显示您正在运行的 Linux 版本
*   `dmesg`显示引导过程的消息日志——对调试有用
*   `uptime`显示系统已经运行了多长时间以及负载情况

![](img/fdffd983180af479a120147a8b120d1a.png)

蒂莫·沃尔茨在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## Bash 变量、历史和其他 Bash 技巧

*   使用一个命令的输出作为另一个命令的参数
*   `env`显示所有的环境变量
*   `export VARNAME=”some value”`设置一个 shell 变量，并使其对进程可用——注意:等号两边没有空格。
*   `echo $VARNAM`打印一个 shell 变量的内容
*   `unset VARNAME`删除一个外壳变量
*   显示输入到 shell 中的命令的历史
*   `up`和`down` ***箭头键*** 允许您快速浏览命令历史
*   `ctrl-r`键入这个 ***键*** ，然后开始键入，Bash 将使用反向历史搜索自动完成
*   `!!`再次运行之前的命令【对 sudo 有用！！，当你第一次忘记数独的时候]
*   `!!:0 <arguments>`使用新参数再次运行之前的命令
*   `<command> !$`运行命令，重用前一个命令的参数
*   `!-2`运行倒数第二个命令
*   `!10`根据历史记录运行第十个命令
*   `bind –p`获取 Bash 键盘快捷键的完整列表

![](img/c925206e3a6dd073d068a6d07b4f731f.png)

照片由[亚历山德拉·尼古莱](https://unsplash.com/@aniculai?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## Raspberry Pi 特定命令

*   `vcgencmd get_mem arm`显示 CPU 内存分配
*   `vcgencmd get_mem gpu`显示 GPU 的内存分配
*   `vcgencmd measure_temp`显示 CPU 温度
*   `echo “$(expr $(</sys/class/thermal/thermal_zone0/temp) / 1000)’C”`显示 GPU 温度
*   `vcgencmd measure_clock arm`以千赫为单位的 CPU 速度——可以报告其他时钟
*   `cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_cur_freq`也显示 CPU 速度
*   `vcgencmd measure_volts core`核心电压；也可以报告其他位置的电压
*   `vcgencmd get_config int`显示系统的配置设置
*   `vcgencmd version`显示固件版本信息

![](img/81b36be1d6e7b6c71ffe2b0f61dcc0fb.png)

照片由[乔丹·马修](https://unsplash.com/@mat_graphik?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 其他有用的命令

*   `passwd`如果您尚未更改密码，请现在更改。默认的 pi 密码使您的系统易受黑客攻击
*   `clear`清除屏幕

## 系统管理命令

*   `sudo <command>`以超级用户(即系统管理员或根用户)的身份运行命令
*   `sudo sh`在当前目录下使用超级用户权限启动外壳(可能会有危险)
*   使用 root 的环境在 root 的主目录中启动一个登录 shell
*   `sudo mount -t <type> <device> <directory>`将设备挂载到文件系统中
*   `sudo umount <device>|<directory>`从文件系统中卸载设备
*   `sudo chown <owner> <file>`更改特定文件的所有者
*   `sudo chgrp <group> <file>`更改特定文件的组
*   `sudo raspi-config`配置您的树莓派

![](img/aa08d9de182aa6c9811c30f6e8c001a1.png)

照片由 [Liliya Lugovaya](https://unsplash.com/@lilymeadow?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 帐户管理命令

*   `sudo useradd -c “John Smith” -m john`为 John 创建一个新的用户帐户
*   `sudo passwd john`为约翰设置密码
*   将 John 添加到可以使用 sudo 的人群中
*   查看 John 在密码文件中的条目
*   测试约翰的新账户是否有效
*   删除约翰的账户

![](img/34fb5bb5d10f6103932473d775237077.png)

由[艾米·辛普森](https://unsplash.com/@aimssimpson?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 软件维护命令

*   `sudo apt-get update`总是先这样做
*   `sudo apt-get full-upgrade`然后是这个——包括依赖项在内的全面升级
*   `sudo apt-get install <package>`安装特定的软件包
*   `sudo apt-get remove <package>`卸载软件但保留配置文件
*   `sudo apt-get purge <package>`彻底卸载软件
*   `apt-cache show <package>`获取关于已安装软件包的信息
*   `sudo apt-get install -f`找到损坏的依赖项并安装它们
*   `dpkg --get-selections`列出所有已安装的软件包

![](img/02ed5c6a89c890173fff66fdf1057463.png)

照片由 [Mona Eendra](https://unsplash.com/@monaeendra?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 互联网和 WiFi 命令

*   `hostname [-f]`报告该系统的互联网主机名[完整]
*   `ifconfig [-av]`报告系统可用的网络接口【完整】
*   `sudo ifup <iface>`启用网络接口—接口通常是 wlan0 或 eth0
*   `sudo ifdown <iface>`禁用网络接口—接口通常是 wlan0 或 eth0
*   `iwconfig`报告系统可用的无线网络接口
*   `iw dev`报告系统可用无线网络接口的另一种方式
*   `iwlist wlan0 scan`扫描无线接入点(有时用于连接 grep“SSID”)
*   `sudo iw dev wlan0 scan`扫描无线接入点的另一种方式
*   `ping <domain-name or internet-address>`检查到指定地址的连接
*   从网上下载文件
*   `ssh <login@address>`建立与另一个系统的安全外壳连接(例如`ssh pi@10.1.1.5`)
*   `netstat -lp`查看您的网络上发生了什么——参见`man netstat`了解众多选项
*   `ip address`查看系统上的活动互联网地址(有关许多选项，请参见:`man ip`)
*   `iptables -h`像防火墙一样，iptables 阻止和允许流量(参见:`man iptables`)

![](img/ecfad4e468d552973afc6fb6c954f197.png)

照片由 [Rutger van Deelen](https://unsplash.com/@rutgervandeelen?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 关机/重启命令

*   现在暂停计算机(首先同步文件系统)
*   `sudo reboot`立即重启电脑

## 结束语

这就是你的覆盆子酱命令盛宴。我希望你感到非常满意。
# 基本的 Linux 命令行程序

> 原文：<https://levelup.gitconnected.com/useful-linux-command-line-programs-weve-to-know-about-8804931927ff>

![](img/20fdd497fe1701593eee1a6155342a6e.png)

由 [Mael BALLAND](https://unsplash.com/@mael_bld?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果我们作为开发人员工作，我们最终必须使用 Linux 命令行。

在本文中，我们将研究一些我们应该知道的使用 Linux 命令行的基本 Linux 程序。

# 卷曲

curl 允许我们向服务器发出 HTTP 请求，然后获得请求。

除了任何 cookies 之外，我们还可以用它来指定请求的头和主体。

# python -m json.tool / jq

python -m json.tool 和 jq 是两个独立的程序，我们可以用它们来美化 json，以便于阅读。

他们都在烟斗后工作。例如，假设`test.json`中有 JSON 文本，我们可以如下运行两者:

```
cat test.json | jq
```

或者:

```
cat test.json | python -m json.tool
```

# 限位开关（Limit Switch）

ls 列出了目录中的文件和目录。

一旦我们运行它，我们就可以获得文件和文件夹的权限，以及它们的更改时间。

# **尾巴**

tail 向我们展示了一个文本文件的结尾。当文本文件用新的内容更新时，如果我们添加了`-f`选项，它将显示最新的内容，并一直显示文件的结尾

# 猫

cat 用于在屏幕上连接和打印文件。我们只需将所需的路径添加到列表的末尾，所有文件的内容就会在屏幕上一个接一个地连接起来。

# 可做文件内的字符串查找

grep 允许我们从另一个命令的输出中搜索模式。

这对于从任何大块文本中搜索数据非常方便。

# 著名图象处理软件

ps 显示我们计算机中正在运行的进程的状态。

日期、进程 ID 和时间都包括在内。

# 包封/包围（动词 envelop 的简写）

env 打印出环境变量。如果环境变量添加不正确，这很有用。

# 顶端

top 显示了系统中当前正在运行的进程所使用的资源数量。

# netstat

netstat 向我们显示了我们系统的网络状态。它向我们显示了所使用的端口以及它们连接的 IP 地址。

此外，我们可以在一个表中看到使用网络连接的进程。

# IP 地址；网络地址

iproute2 包中有`ip address`程序，显示哪些接口连接到哪个网络。

# lsof

`lsof` 包向我们展示了在我们的网络中监听传入数据的项目。

它列出了它正在侦听的端口以及正在侦听给定端口的进程的 ID。

# df

`df`程序向我们展示了我们系统上的空闲磁盘空间。

我们可以看到哪些卷已满，哪些卷未满。

# 杜（姓氏）

与使用`du`的`df`相比，我们可以获得更多关于磁盘容量的信息。

`-h`标志使输出可读，而`-s`显示总大小。

# 身份证明（identification）

id 命令返回当前登录用户的标识。

# chmod

`chmod` 用于改变我们的文件和目录的权限。

这可以帮助我们在遇到权限错误时修复它们。

# dig / nslookup

`dig`和`nslookup`让我们来排除 DNS 错误。我们可以看看是否可以用它将某个 URL 解析为他们的 IP 地址。

只需将我们想要找到的 IP 地址放在每个 URL 之后，并检查输出。

# iptables

`iptables` 用于在 Linux 系统上阻止或允许流量。它类似于网络防火墙。

我们可以用这个包按 IP 地址加入白名单和黑名单。

![](img/fedcefca022d84d048d58ffe6f338b41.png)

[雷鹏](https://unsplash.com/@luipeng?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# sestatus

我们可以用这个命令检查是否启用了 SELinux。然后我们可以检查它是否启用。

# 历史

历史显示了我们最近运行的命令。很容易看到我们已经运行了什么，这样我们就可以再次运行它们。

# 水手

tar 可以用来创建归档文件和解压缩它们。

我们通过运行`tar cvf archive.tar /dirname`在`/dirname`目录下创建一个归档文件来创建一个。

从档案中提取数据，我们运行`tar xvf archive.tar`。

为了查看归档文件，我们运行`tar tvf archive.tar`。

# 结论

我们可以使用许多命令来执行 Linux 命令。

像 curl 和 tar 这样的命令对于发出请求和提取文件非常重要。

我们也可以安装像`jq`这样的包来美化 JSON。

此外，还有更多的工具可以方便地对网络和进程进行故障排除。
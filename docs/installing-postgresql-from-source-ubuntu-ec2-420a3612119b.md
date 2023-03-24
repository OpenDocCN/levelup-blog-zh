# 从源安装 PostgreSQL—Ubuntu EC2

> 原文：<https://levelup.gitconnected.com/installing-postgresql-from-source-ubuntu-ec2-420a3612119b>

![](img/7a486a099ef1d0ddf43f96d827905fb4.png)

我们最近有一个需求，要测试 AWS DB 迁移工具是否适合我们的特定用例，将数据从我们的生产 PostgreSQL 服务器移动到我们的生产 MS SQL 服务器(都托管在 EC2 上)。剧透一下——没有，PostgreSQL 可以用作源，但 MS SQL [不是可用的目标之一。](https://docs.aws.amazon.com/SchemaConversionTool/latest/userguide/CHAP_Source.PostgreSQL.html)

我构建了几个 EC2 实例来测试这一点。从 apt 存储库安装 PostgreSQL 非常简单，但是我想通过从源代码安装 PostgreSQL 的特定版本来尽可能地镜像我们的生产服务器。

我是这样做的——我们在在 AWS 上创建了 EC2 实例后，马上进入*,所以如果你需要这方面的指导，那么这篇博客不适合你！*

## **语境**

*   我使用 PuTTY 从我的 Windows 机器上 SSH 到 AWS 上托管的 Ubuntu 18.04 服务器
*   我将添加一个图形用户界面，因为这将使以后的事情更容易

## **#安装 Gnome**

```
$ sudo apt install tasksel
$ sudo tasksel install ubuntu-desktop
$ sudo apt install xrdp
$ sudo systemctl reboot
$ sudo passwd ubuntu
```

现在你已经安装了标准的 Gnome GUI 并给你的用户分配了一个密码，RDP 进入你的实例来建立基本目录(文档，下载，等等)。).

## **#从源安装的安装依赖项**

```
$ sudo apt install build-essential zlib1g-dev libreadline-dev -y
```

解压和构建 PostgreSQL 所必需的一切。我们要去拿的焦油文件。

## **#从源 URL 下载安装文件到你的下载文件夹**

```
$ wget [https://ftp.postgresql.org/pub/source/v10.6/postgresql-10.6.tar.gz](https://ftp.postgresql.org/pub/source/v10.6/postgresql-10.6.tar.gz)
```

很可能您需要一个不同版本的 PostgreSQL。可以查看[官方 PostgreSQL FTP 服务器](https://www.postgresql.org/ftp/source/)，复制你需要的版本的网址(确保右击. tar.gz 文件和‘复制链接地址’)。wget 是在您指定的 URL 下载文件的命令。

## **#解压 tar 文件**

```
$ tar xvfz postgresql-10.6.tar.gz
```

运行这个程序时，您需要位于下载目录中(假设这是您下载文件的地方)。这将创建一个解压缩版本，供您配置。tar 之后的“xvfz”标志执行以下操作:

*   x:指示 tar 从档案中提取
*   v:详细列出要处理的文件
*   f:表示文件名跟在后面
*   z:通过 gzip 过滤操作(解压缩文件——这是. tar.gz 文件所必需的，而不是。焦油文件)

## **#Configure &安装它(在您保存 tar 文件的文件夹中)**

```
$ cd postgresql-10.6
$ ./configure
$ make
$ sudo make install
```

在这里，您将目录更改为解压缩后的文件，然后运行必要的命令来使用该文件安装 PostgreSQL。

至于从源代码安装 PostgreSQL 瞧！你完了。你可能想测试它的工作原理。为此，我们需要一个 postgres 用户，并访问 psql 命令。您可以在下一篇文章中了解我是如何做到这一点的。
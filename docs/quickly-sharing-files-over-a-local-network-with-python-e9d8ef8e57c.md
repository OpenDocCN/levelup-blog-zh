# 使用 Python 在本地网络上快速共享文件

> 原文：<https://levelup.gitconnected.com/quickly-sharing-files-over-a-local-network-with-python-e9d8ef8e57c>

![](img/1b7ad1c70f6c19a0be6ff08844335429.png)

伊利亚·巴甫洛夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

对于任何不得不在多台计算机上工作，或者与其他人一起从事一个项目的人来说，他们不可避免地不得不在机器之间共享文件。幸运的是，在今天这个时代，有了 Google Drive 或 Dropbox 这样的云服务，人们可以很容易地与任何人分享文件。然而，从这些服务中的一个或甚至一个闪存驱动器上传和下载文件的新版本仍然会变得乏味。

进入 Python 的 *http.server* 模块，它可以让您在任何目录中快速部署本地 web 服务器，当在 web 浏览器上导航到该目录时，它将包含该目录中的所有文件。当服务器运行时，wi-fi 网络上的任何机器都可以下载文件。也就是说，这不是一种安全的文件传输形式，您应该避免托管任何敏感数据，或者在公共网络上使用这种方法。

## 步骤 0-确保安装了 Python 3

显然，我们将需要 Python 来做这件事。通过键入`$ python -V`确保您安装了 python 3 的版本，Windows 用户可能需要键入`$ py -V`。

## 步骤 1 —启动服务器

导航到您希望共享的目录(文件夹)并启动新的终端窗口(Mac/Linux)或命令提示符(windows ),然后键入以下命令:

```
$ python -m http.server## $ py -m http.server ## for windows users
```

如果成功，应该会看到**在::port 8000 上服务 HTTP(**[**HTTP://[::]:8000/**](http://[::]:8000/)**)……**弹出。您现在将文件托管到本地 web 服务器。

## 步骤 2 —获取您的 IP 地址

有几种方法可以获得这些信息，但是最快的方法是从命令行获得。对于 Mac 用户，在新的终端窗口中键入以下内容以获取您的计算机的 IP 地址:

```
$ ifconfig |grep "inet 192"
```

这是第二台机器现在将导航到的地址，其形式应该类似于 *192.168.x.xx。*

对于 Windows 用户，在新的命令提示符窗口中，键入:

```
$ ipconfig 
```

在输出中找到 IPv4 地址(在 wi-fi 部分下)并记下来。同样，它的形式应该是 *192.168.x.xx.*

## 步骤 3 —导航到托管文件

从您希望下载文件的机器上，在网络浏览器(例如 Google Chrome)导航到`http://<Machine 1’s IP Address>:8000`，从这里你可以右键点击并下载托管目录中的单个文件。

恭喜你。现在，您已经成功地通过本地 web 服务器托管和共享了文件。完成后，请务必在原来的终端窗口中按下`Crtl + c`来关闭服务器。
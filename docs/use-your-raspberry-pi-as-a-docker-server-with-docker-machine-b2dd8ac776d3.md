# 使用您的 Raspberry Pi 作为 docker-machine 的 Docker 服务器

> 原文：<https://levelup.gitconnected.com/use-your-raspberry-pi-as-a-docker-server-with-docker-machine-b2dd8ac776d3>

![](img/bbf02c31b2d4883c22481734df490a41.png)

照片由[张秀坤·吕克曼](https://unsplash.com/@exdigy?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果你的电脑速度很慢，或者你无法使用官方的 Docker 桌面安装程序安装 Docker，你可以使用你的 Raspberry Pi(或者任何其他服务器)作为你的专用 Docker 服务器。

最近，我遇到了一个问题，我无法在我的虚拟机上安装 Docker Desktop，该虚拟机运行在我的 [Unraid](https://www.unraid.net/) 服务器上。问题是在锐龙 CPU 上，我使用的操作系统(还)不支持嵌套虚拟化。[嵌套虚拟化](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)意味着你可以在一个虚拟机内部运行一个虚拟机。

![](img/ea0a6e6a243a82a1743f329c2d0dcb9b.png)

还记得古老的迷因吗？

所以我不能使用官方的 Docker for Mac 安装程序来安装 Docker，我必须找到另一种方法。

这个问题的答案是`docker-machine`。

# 什么是 docker-machine？

`docker-machine`是 Docker 的一个 CLI 程序，它允许你像平常一样使用`docker`，但是在服务器上执行命令。如果您有多个运行 docker 的服务器，并且您可以通过使用一个命令`docker-machine env YOUR_ENV`改变 docker 环境来控制它们，那么这是非常有用的。

如果你没有安装`docker-machine`，试着用你的 OSs 包管理器安装或者从 [GitHub](https://github.com/docker/machine/releases) 下载。

在 macOS 上，您可以像这样从 brew 安装它:

```
brew install docker-machine
```

即使你不能安装 Docker 桌面，你也应该可以下载`docker` CLI，你会需要它的。

# 准备我们的服务器

在我们使用`docker-machine`之前，我们需要先在服务器上做一些配置。

我建议创建一个新用户，用于使用`docker-machine`登录。

```
adduser docker-machine
```

接下来，我们需要允许新用户使用`sudo`执行命令，但是用户可以在不输入密码的情况下使用它。这可能是一个安全风险，所以请确保`docker-machine`用户的密码是强密码，并且您的服务器得到了适当的加固。

通过将`visudo`写入您的终端来编辑`sudoers`文件，并在`root`条目后添加一个新行。

```
docker-machine ALL=(ALL) NOPASSWD: ALL
```

保存并退出编辑器。

`docker-machine`通用驱动程序不能识别`raspbian`操作系统，所以我们需要欺骗它，将`/etc/os-release`中的`ID`字段临时改为`debian`。

```
#/etc/os-release
PRETTY_NAME="Raspbian GNU/Linux 10 (buster)"
NAME="Raspbian GNU/Linux"
VERSION_ID="10"
VERSION="10 (buster)"
VERSION_CODENAME=buster
ID=debian
ID_LIKE=debian
HOME_URL="[http://www.raspbian.org/](http://www.raspbian.org/)"
SUPPORT_URL="[http://www.raspbian.org/RaspbianForums](http://www.raspbian.org/RaspbianForums)"
BUG_REPORT_URL="[http://www.raspbian.org/RaspbianBugs](http://www.raspbian.org/RaspbianBugs)"
```

最后，您需要在您的服务器上使用`docker-machine`用户设置公钥认证。我在另一篇文章中记录了如何做到这一点的过程。向下滚动到**“使用公钥认证跳过密码”**部分，并按照步骤进行设置。

现在您的服务器应该准备好使用`docker-machine`安装 docker 了。

# 用 docker-machine 在你的服务器上安装 docker

现在您的机器上已经安装了 docker-machine CLI，我们可以使用它在服务器上安装 docker，只需一个命令。

```
docker-machine create \
  --driver generic \
  --generic-ip-address=192.168.1.13 \
  --generic-ssh-user=docker-machine \
  --generic-ssh-key ~/.ssh/id_rsa \
  rpiRunning pre-create checks...
Creating machine...
(rpi) Importing SSH key...
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with debian...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env rpi
```

就这样，现在你应该已经在你的服务器上安装了 docker！

# 用 docker-machine 改变你的 docker 环境

现在，如果您想在服务器上执行 docker 命令，您只需使用以下命令更改 docker 环境:

```
eval $(docker-machine env rpi)
```

如果您总是想使用服务器环境，请将该行放入您的`.bashrc`或`.zshrc`文件中。

# 开始使用 Docker

用`docker-machine`改变环境后，你可以像平常一样使用`docker`！

```
docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

# 有用的 docker-machine 命令

*   `docker-machine ls` -列出所有环境
*   `docker-machine rm ENV` -删除现有环境
*   `docker-machine ip ENV` -获取服务器的 IP 地址
*   `docker-machine ssh ENV` -打开与服务器的 SSH 连接

# 结论

`docker-machine`是一个很酷的工具，非常容易设置和使用。我发现这种方法非常有趣。目前，设置工作得很好，我没有看到任何缺点。

唯一不同的是，如果我想访问 docker 上运行的服务，我将不得不使用我的服务器的 IP 而不是`localhost`。

*原载于 2021 年 1 月 7 日*[*【https://phiilu.com】*](https://phiilu.com/docker-machine-generic-server-raspberry-pi)*。*
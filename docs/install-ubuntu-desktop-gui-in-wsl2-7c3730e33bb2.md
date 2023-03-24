# 在 WSL2 中安装 Ubuntu 桌面 GUI

> 原文：<https://levelup.gitconnected.com/install-ubuntu-desktop-gui-in-wsl2-7c3730e33bb2>

## 系列:人工智能

## 附有说明和截图的简明指南

![](img/b7c9aaa751cb40326a830f6fdf189b60.png)

图片由[杰克·威瑞克](https://unsplash.com/@weirick)拍摄

> “注意:本文经过了重新编写，以简化流程并包含流程更新。点击查看[的更新文章](https://medium.com/p/71f4b78431a4/)

## 下载 VcXsrv:

1.  访问[官方网站](https://sourceforge.net/projects/vcxsrv/)
2.  点击“下载”

![](img/2dbe2bc384bf06782c18923a5f4b5860.png)

## 安装 VcXsrv:

1.  打开“vcx SRV-64 . 1 . 20 . 8 . 1 . installer . exe”
2.  点击“下一步”
3.  点击“安装”
4.  点击“关闭”

![](img/235ee8fd7851f3a6e1f495cd5bab79e0.png)

## 允许访问 VcXsrv:

1.  检查“专用网络”
2.  单击“允许访问”

![](img/0fae33d376643574a8d693c2be98b249.png)

## 打开 PowerShell:

1.  按下“⊞之窗”
2.  在搜索栏中输入“PowerShell”
3.  右键单击“Windows PowerShell”
4.  单击“以管理员身份运行”

![](img/ebee965807169aeea4c38fc58ab73df7.png)

## 更改执行策略:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
Set-ExecutionPolicy Unrestricted -Force
```

![](img/5c17485da6907e888c66f4841f3823cf.png)

## 打开 WSL2:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
wsl
```

![](img/9fa5919990d9157a4d699620994e5d91.png)

## 安装 Ubuntu 桌面:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
sudo apt --yes install ubuntu-desktop
```

![](img/946ba5f2e53f203fe43776b807a4b58f.png)

## 设置用户名变量:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
username=$([wslvar](#8fc7) USERNAME)
```

![](img/4576f9b46993725c26db4a99b45ce73f.png)

## 创建 Ubuntu 目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
mkdir --parents /mnt/c/users/$username/.ubuntu/
```

![](img/73d6f0c976b247de7997da4d6373554c.png)

## 打开 Ubuntu 目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
cd /mnt/c/users/$username/.ubuntu
```

![](img/53b25d668dbd0109a19c914acc907273.png)

## 获取 Microsoft 公钥:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
sudo apt-key adv --fetch-keys [https://packages.microsoft.com/keys/microsoft.asc](https://packages.microsoft.com/keys/microsoft.asc)
```

![](img/a74bab4ff7db89e40b8a74383b8104a7.png)

## 将 Microsoft 添加到源列表目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
**Ubuntu 20.04:**
sudo sh -c 'echo "deb [arch=amd64] [https://packages.microsoft.com/ubuntu/20.04/prod](https://packages.microsoft.com/ubuntu/18.04/prod) focal main" > /etc/apt/sources.list.d/microsoft-prod.list'**Ubuntu 18.04:**
sudo sh -c 'echo "deb [arch=amd64] [https://packages.microsoft.com/ubuntu/18.04/prod](https://packages.microsoft.com/ubuntu/18.04/prod) bionic main" > /etc/apt/sources.list.d/microsoft-prod.list'
```

![](img/3468ce6b27b792fcb549bf04585c782a.png)

## 更新存储库:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
sudo apt update
```

![](img/34987b8ba66d90389fe394a81da57667.png)

## 下载。Net 5.0 运行时:

1.  访问[官方网站](https://dotnet.microsoft.com/download/dotnet/5.0/runtime)
2.  单击“下载 x64”

![](img/c17f1d26d8f7f1e281f816a94a4f566c.png)

## 安装。Net 5.0 运行时:

1.  单击“windows desktop-runtime-5 . 0 . 5-win-x64 . exe”文件
2.  点击“安装”
3.  点击“下一步”
4.  点击“关闭”

![](img/58d1d1781d73ef2d409bd3941b005977.png)

## 以 Root 用户身份输入 Shell:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
sudo --shell
```

![](img/6c362a8c270e1fdd88d75ed6e58043a2.png)

## 安装 Apt 运输 HTTPS:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
apt install apt-transport-https
```

![](img/71410b35897ce937b951dc59ea87b375.png)

## 下载 GPG 密钥:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
wget --output-document /etc/apt/trusted.gpg.d/wsl-transdebian.gpg [https://arkane-systems.github.io/wsl-transdebian/apt/wsl-transdebian.gpg](https://arkane-systems.github.io/wsl-transdebian/apt/wsl-transdebian.gpg)
```

![](img/4b43b0ab56405e54f3cb7803ba12cd26.png)

## 更改访问权限:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
chmod a+r /etc/apt/trusted.gpg.d/wsl-transdebian.gpg
```

![](img/f0c67c234274429e08c6446a3fbb97e5.png)

## 将 Arkane 系统添加到源列表目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
cat << EOF > /etc/apt/sources.list.d/wsl-transdebian.list
deb https://arkane-systems.github.io/wsl-transdebian/apt/ $(lsb_release -cs) main
deb-src https://arkane-systems.github.io/wsl-transdebian/apt/ $(lsb_release -cs) main
EOF
```

![](img/8a0c03fb2e1ca0326e2a205f620c262d.png)

## 更新存储库:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
apt update
```

![](img/bc24453630359f90f5d571232fafc411.png)

## 以 Root 用户身份退出 Shell:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
exit
```

![](img/ba865adf7f02f55da706be722add1901.png)

## 安装精灵:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
sudo apt install --yes systemd-genie
```

![](img/67cc1fa0d1b45b510671d00f86c47935.png)

## 将精灵添加到 Sudoers 目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
echo "$USER ALL=(ALL) NOPASSWD:/usr/bin/genie" | sudo EDITOR="tee" visudo --file /etc/sudoers.d/$USER
```

![](img/2309cc37f0ca2469ab5d9ec17b060f88.png)

## 创建桌面环境脚本:

1.  从这些指令下面复制代码
2.  将代码粘贴到 PowerShell 中
3.  按“回车”

![](img/8ddf77854ea15f0b1df0ffcaf355c3ef.png)

## 下载 Ubuntu 设计图片:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
wget [https://assets.ubuntu.com/v1/9fbc8a44-circle-of-friends-web.zip](https://assets.ubuntu.com/v1/9fbc8a44-circle-of-friends-web.zip)
```

![](img/5e328fddc3aec4b4e97c5a48f0b42813.png)

## 安装解压缩:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
sudo apt --yes install unzip
```

![](img/050dabce37d470139784ac5a9e9c0c75.png)

## 解压缩 Ubuntu 设计图像:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
unzip 9fbc8a44-circle-of-friends-web.zip
```

![](img/1a3d1be9da611c822bce7114a5a3f01c.png)

## 安装 ImageMagick:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
sudo apt --yes install imagemagick
```

![](img/462203daf5ff0ac05580f13eb52b3b0d.png)

## 创建 Ubuntu 图标:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
convert -resize 64x64 ./circle-of-friends-web/png/cof_orange_hex.png ubuntu.ico
```

![](img/a9325d443e12613a132ea34db81671d6.png)

## 退出 WSL2:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
exit
```

![](img/b9cc5c248491c0602194f8f8078b8dc6.png)

## 关闭 WSL2:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
wsl --shutdown
```

![](img/d9ce4f2cfc4d5fcbcf31a77d6ef251e1.png)

## 创建 VcXsrv 脚本:

1.  从这些指令下面复制代码
2.  将代码粘贴到 PowerShell 中
3.  按“回车”

![](img/f44e5265f79f40fa633a7e6c8da74647.png)

## 创建 Ubuntu 桌面脚本:

1.  从这些指令下面复制代码
2.  将代码粘贴到 PowerShell 中
3.  按“回车”

![](img/89b2ab628cd546ba5046cf0131dd5b16.png)

## 创建快捷图标:

1.  从这些指令下面复制代码
2.  将代码粘贴到 PowerShell 中
3.  按“回车”

![](img/618c10d92d99c4ea8dd92e62e1a7fa94.png)

## 打开 Ubuntu 目录:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”

```
cd $HOME\.ubuntu
```

![](img/2b01f9cf51638ee4d60c071b9b3555b0.png)

## 启动 Ubuntu 桌面:

1.  从下面这些指令中复制命令
2.  将命令粘贴到 PowerShell 中
3.  按“回车”
4.  双击“Ubuntu”快捷方式
5.  等待 180 秒，让 Ubuntu 桌面启动

```
explorer.exe .\
```

![](img/066fde02951d2546f3cde28aaabde04a.png)![](img/5eb9af561af8726d04b6afe4d8f9e331.png)

## 开放终端:

1.  点击左上角的“活动”
2.  在搜索栏中输入“终端”
3.  点击“终端”

![](img/3cd1d8e6dd9d86bc5862417a34fc020e.png)

## 禁用屏幕锁定:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
gsettings set org.gnome.desktop.lockdown disable-lock-screen true
```

![](img/a3594c59d61f63895db9a24010bb7598.png)

## 安装快照存储:

1.  从下面这些指令中复制命令
2.  将命令粘贴到终端
3.  按“回车”

```
sudo snap install snap-store
```

![](img/e14f3af8a76cd94fd8c04321f49c023b.png)

> “希望这篇文章能帮助您获得👯‍♀️🏆👯‍♀️，记得订阅获取更多内容🏅"

## 后续步骤:

这篇文章是一个迷你系列的一部分，帮助读者设置他们开始学习人工智能、机器学习、深度学习和/或数据科学所需的一切。它包括包含复制和粘贴代码的说明和截图的文章，以帮助读者尽快获得结果。它还包括一些文章，包含带有解释和截图的说明，以帮助读者了解正在发生的事情。

```
**Linux:**
01\. [Install Multiple Python Versions](https://medium.com/p/8bd6d301d78c)
02\. [Install the CUDA Driver and Toolkit](https://medium.com/p/3494a4436d6)
03\. [Install the Jupyter Notebook Server](https://medium.com/p/f5bbc07e184a)
04\. [Install Virtual Environments in Jupyter Notebook](https://medium.com/p/c93fd8d07ca0)
05\. [Install the Python Environment for AI](https://medium.com/p/d2937ce641b7)**WSL2:**
01\. [Install Windows Subsystem for Linux 2](https://medium.com/p/e01f92e98cc0)
02\. [Install Multiple Python Versions](https://medium.com/p/ba81f21109d6)
03\. [Install the CUDA Driver and Toolkit](https://medium.com/p/be38703fed5c)
04\. [Install the Jupyter Notebook Server](https://medium.com/p/3ea9bc06a0e5)
05\. [Install Virtual Environments in Jupyter Notebook](https://medium.com/p/d99de1d79fd4)
06\. [Install the Python Environment for AI](https://medium.com/p/6d73735b546)
07\. [Install Ubuntu Desktop GUI (Bonus)](https://medium.com/p/7c3730e33bb2)**Windows 10:**
01\. [Install Multiple Python Versions](https://medium.com/p/15a8685ec99d)
02\. [Install the CUDA Driver and Toolkit](https://medium.com/p/f103ea5eae4b)
03\. [Install the Jupyter Notebook Server](https://medium.com/p/c2ca45793e3b)
04\. [Install Virtual Environments in Jupyter Notebook](https://medium.com/p/a307b6524715)
05\. [Install the Python Environment for AI](https://medium.com/p/604168afbd6e)**MacOS:** 01\. [Install Multiple Python Versions](https://medium.com/p/a58b1966825f)
02\. [Install the Jupyter Notebook Server](https://medium.com/p/7b42d371ac21)
03\. [Install Virtual Environments in Jupyter Notebook](https://medium.com/p/557f23e55f99)
04\. [Install the Python Environment for AI](https://medium.com/p/ed5c93639301)
```
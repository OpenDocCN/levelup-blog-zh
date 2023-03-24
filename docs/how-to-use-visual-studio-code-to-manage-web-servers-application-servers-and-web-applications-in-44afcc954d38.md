# 如何使用 Visual Studio 代码管理 Web 服务器、应用程序服务器和 Web 应用程序

> 原文：<https://levelup.gitconnected.com/how-to-use-visual-studio-code-to-manage-web-servers-application-servers-and-web-applications-in-44afcc954d38>

## Web 开发

## 在 Linode、数字海洋、亚马逊网络服务(AWS)、谷歌云平台和微软 Azure 上

![](img/2cbd2243b9888de09034da20dc099d91.png)

图片来自[大卫·皮斯诺伊](https://unsplash.com/photos/46juD4zY1XA)

## 总结:

大多数云服务提供商都有一个基于 web 的终端应用程序，允许他们的用户远程管理虚拟机。这提供了一种通过安全连接连接到虚拟机的简单方法，不需要密码或 SSH 密钥配置。然而，基于 web 的应用程序有一些限制，使其难以在生产中使用。它不能滚动输出，窗口太小，粘贴的文本格式不正确。

Visual Studio Code (VS Code)是一个流行的文本编辑器，它使得在生产环境中的虚拟机上工作变得更加容易。它可以像普通计算机一样在文本编辑器中创建、打开、编辑和传输虚拟机上的文件。它可以同时在多个终端应用程序上运行命令。它也没有基于 web 的终端应用程序所具有的任何限制。

## 目录:

1.  [获取虚拟机的 IP 地址](#6b43)
2.  [安装 SSH 客户端](#9e76)
3.  [安装 SSH 服务器](#2026)
4.  [生成 SSH 密钥](#b168)
5.  [创建 SSH 配置文件](#273a)
6.  [连接到虚拟机](#9a7b)

## 获取虚拟机的 IP 地址:

`virtual machine`是一个虚拟操作系统，它存储、运行和管理 web 服务器、应用服务器和 web 应用程序。它提供了 Visual Studio 代码用来从远程连接访问虚拟机的 IP 地址。在本节中，我们将登录云服务提供商以检索虚拟机的 IP 地址。

```
**# linode**
1\. log into [linode](https://cloud.linode.com/linodes)
2\. click "linodes" link in navigation panel
3\. click desired linode
4\. find "ip addresses" label
5\. write down ip address**# digital ocean**
1\. log into [digital ocean](https://cloud.digitalocean.com/)
2\. click "droplets" link in navigation panel
3\. click desired droplet
4\. find "ipv4" label
5\. write down ip address**# amazon web services (aws)**
1\. log into [amazon web services](https://console.aws.amazon.com/ec2/)
2\. click "instances" link in navigation panel
3\. click desired instance
4\. find "ipv4 public ip" label
5\. write down ip address**# google cloud platform**
1\. log into [google cloud platform](https://console.cloud.google.com/compute/)
2\. click "vm instances" link in navigation panel
3\. click desired instance
4\. scroll to "network interfaces" section
5\. find "external ip" label
6\. write down ip address**# microsoft azure**
1\. log into [microsoft azure](https://portal.azure.com/)
2\. click "virtual machines" link in navigation panel
3\. click desired virtual machine
4\. find "public ip adddress" label
5\. write down ip address
```

## 安装 SSH 客户端:

`PowerShell`是一种命令行 shell 和面向对象的脚本语言。它使用小命令(cmdlets)在 Windows 操作系统上执行管理任务。在本节中，我们将使用几个 cmdlets 在 Windows 操作系统上安装、启用和启动 SSH 客户端。

```
**# open powershell shell**
1\. press “⊞ windows” key
2\. enter “powershell” into search bar
3\. right-click "windows powershell" search result
4\. click “run as administrator” menu item
5\. paste commands into powershell**# enable copy and paste shortcuts** 1\. click powershell icon in top left corner
2\. click "properties" menu item
3\. check "use ctrl + shift + c / v as copy / paste" checkbox
4\. click "ok" button**# install openssh client** 
add-windowscapability -online -name openssh.client**# start sshd**
start-service sshd**# set ssh to start at boot** set-service -name sshd -startuptype 'automatic'
```

## 安装 SSH 服务器:

是一组程序，允许两台计算机使用安全连接进行远程通信。它使用启动连接的客户端程序和验证并接受连接的服务器程序。在本节中，我们将连接到虚拟机，并在 Linux 操作系统上安装、启用和启动 SSH 服务器。

```
**# change placeholder to user on virtual machine**
$user="placeholder"**# change placeholder to ip address of virtual machine**
$linux_ip_address="placeholder"**# connect to virtual machine with password**
ssh $user@$linux_ip_address**# add ip address to known hosts file** 1\. enter "yes"
2\. enter user password
3\. press "enter" key**# update package information**
sudo apt-get update**# install openssh package**
sudo apt-get install openssh-server**# set ssh to start at boot**
sudo systemctl enable ssh**# start ssh**
sudo systemctl start ssh**# create ssh directory**
sudo mkdir -p ~/.ssh**# disconnect from virtual machine** exit
```

## 生成 SSH 密钥:

`SSH keys`是一种认证方法，用于在 SSH 客户端和 SSH 服务器之间建立加密连接。它包括一对公钥和私钥。SSH 客户端请求连接到 SSH 服务器。SSH 服务器使用公钥加密消息，并用加密的消息响应连接请求。SSH 客户端使用私钥解密加密的消息，并将解密的消息返回给 SSH 服务器。然后，SSH 客户机和 SSH 服务器建立连接。在本节中，我们将生成 SSH 密钥，将公钥传输到远程计算机，将公钥添加到授权密钥列表中，并使用 SSH 密钥连接到虚拟机。

```
**# create .ssh directory**
mkdir $home\.ssh\id_rsa -force**# generate ssh key**
ssh-keygen.exe -t ed25519 -a 100 -o -c "$($user)@$($linux_ip_address)" -f $home\.ssh\id_rsa**# copy ssh key from windows to linux**
scp $home\.ssh\id_rsa.pub ($user)@$($linux_ip_address):~/.ssh/id_rsa.pub**# connect to virtual machine with password**
ssh $user@$linux_ip_address**# add public key to authorized_keys file** sudo cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys**# disconnect from virtual machine** exit**# connect to virtual machine with ssh key**
ssh $user@$linux_ip_address**# disconnect from virtual machine** exit
```

## 创建 SSH 配置文件:

`SSH configuration file`是一个存储用于远程连接特定计算机的设置的文件。它消除了跟踪连接到每台计算机所需的用户名、密码和 IP 地址的需要。在本节中，我们将创建配置文件，并存储连接到虚拟机所需的设置。

```
**# get ssh key path** echo $home\.ssh\id_rsa**# open ssh configuration file** start-process notepad $home\.ssh\config**# create configuration file** 1\. click "yes"**# edit configuration file**
1\. add following configuration to configuration file
2\. change #ip_address_placeholder# to ip address from earlier
3\. change #user_placeholder# to user on virtual machine
4\. change #path_placeholder# to ssh key path from earlier**# save configuration file**
1\. click "file" menu
2\. click "save" menu item
```

## 连接到虚拟机:

已经安装了 SSH 客户机和 SSH 服务器，生成了 SSH 密钥，并且创建了 SSH 配置文件。这允许我们使用安全连接连接到虚拟机，而不需要密码。在本节中，我们将下载并安装 Visual Studio 代码，安装远程开发扩展，连接到虚拟机，并在虚拟机上执行一些常见任务。

```
**# download visual studio code**
1\. open "download" page on [visual studio](https://code.visualstudio.com/download)
2\. click "windows" button**# install visual studio code**
1\. open the "vscodeusersetup" file
2\. click "i accept the agreement" radio button
3\. click "next" button
4\. click "next" button
5\. click "install" button
6\. click "finish" button**# open visual studio code**
1\. press “⊞ windows” key
2\. enter “visual studio code” into search bar
3\. click "visual studio code" search result**# install remote development extension**
1\. press "control + shift + x" keys
2\. enter "remote development extension" into search bar
3\. press "enter" key
4\. click "remote development"
5\. click "install" button**# connect to virtual machine** 1\. click "view" menu
2\. click "command palette" menu item
3\. enter "remote-ssh: connect to host..." into search bar
4\. click "+ add new ssh host..." search result
5\. click "virtual-machine" option**# open terminal on virtual machine**
1\. click "terminal" menu
2\. click "new terminal" menu item
3\. paste command into terminal**# create html directory**
sudo mkdir -p /var/www/html**# open html directory**
1\. press "ctrl + shift + e" keys
2\. click "open folder" button
3\. enter "/var/www/html" into text field
4\. click "ok" button
5\. click "yes, i trust the authors" button**# create new file on virtual machine**
1\. click "file" menu
2\. click "new file" menu item
3\. click "file" menu
4\. click "save" menu item
5\. enter "/var/www/html/index.html" into text field
6\. click "ok" button
```
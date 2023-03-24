# 设置 Raspberry Pi 家庭服务器

> 原文：<https://levelup.gitconnected.com/setting-up-a-raspberry-pi-home-server-ec7e11ee64e0>

![](img/241a5c0560499a6187f40f38ef786e39.png)

如果你想建立一个家庭实验室，Raspberry Pi 电脑是一个很好的选择，因为它噪音低，功耗低，如果你需要新的存储或其他功能，如连接摄像头或显示器，可以很容易地扩展更多模块。作为一个家庭实验室设备，这是一个很好的方式来开始建立一个实验室，并在一个更便宜和更小的外形尺寸上工作。或者，如果你想要挑战，你可以试着尽可能多地跑步，以充分利用这个小设备。

在本文中，我们将介绍如何安装 Rasbian 操作系统，如何配置对新设置设备的远程访问，以及如何开始使用 Ansible 等代码工具来轻松管理设备上运行的内容。

这篇文章中的任何代码片段都可以在 Raspberry Pi 操作系统上运行，但是应该可以在类似 Debian 的 Linux 系统上运行。

# 安装和设置操作系统

要开始将应用程序部署到您的 Raspberry Pi 并使用它，您需要安装一个操作系统。您应该根据您希望使用这台机器的目的来选择这个操作系统；是运行服务的基本机器，还是拥有类似 [Docker](https://www.docker.com/) 的完全虚拟化机器。

一些常见的操作系统包括:[树莓 Pi OS](https://www.raspberrypi.com/software/operating-systems/) 和 [Ubuntu](https://ubuntu.com/download/raspberry-pi) 。对于任何操作系统，你选择尝试和选择一个最小的安装，以保持操作系统的规模小，并使运行的东西更容易在功能不太强大的设备上。您可以在以下链接中找到如何将 Raspberry Pi 操作系统安装到您的 Raspberry Pi 的分步指南:

 [## 操作系统映像- Raspberry Pi

### 许多操作系统可用于 Raspberry Pi，包括 Raspberry Pi OS，我们官方支持的操作系统…

www.raspberrypi.com](https://www.raspberrypi.com/software/operating-systems/) 

当在 Raspberry Pi 上运行操作系统并使用 SD 卡插槽时，您需要注意的一点是，SD 卡内存确实会出现故障，因此要做好准备。由于 Raspberry Pi 循环供电的方式以及 SD 卡的低写入容量，随着时间的推移，SD 卡的内存可能会损坏。

[](https://raspberrypi.stackexchange.com/questions/7978/how-can-i-prevent-my-pis-sd-card-from-getting-corrupted-so-often) [## 怎么才能防止我 Pi 的 SD 卡这么频繁的被损坏？

### 我不打算写关于检查你的硬件和兼容的 SD 卡列表，因为你很可能已经…

raspberrypi.stackexchange.com](https://raspberrypi.stackexchange.com/questions/7978/how-can-i-prevent-my-pis-sd-card-from-getting-corrupted-so-often) 

您可以采取一些缓解措施，例如将 SD 卡挂载切换为只读，或者为您的操作系统使用更稳定的存储，如附加 SSD 或 HDD，如下所示:

[](https://www.amazon.com.au/StarTech-Converter-Raspberry-Development-Boards/dp/B01MQE6P9P) [## 用于 Raspberry Pi 和开发板的 StarTech.com USB 转 SATA 转换器- USB 转 SATA 适配器…

### 增加您的 Raspberry Pi 或其他开发板的数据存储容量，方法是将其直接连接到…

www.amazon.com.au](https://www.amazon.com.au/StarTech-Converter-Raspberry-Development-Boards/dp/B01MQE6P9P) 

# 配置远程访问

对于您的操作系统，我们需要对其进行配置，以便可以通过 SSH 从另一台机器上访问它。默认情况下，您的操作系统将通过 DHCP 自动从您的路由器获取 IP 地址，但我们希望配置一个静态地址，以便我们可以始终连接到同一个地址来访问我们的 Raspberry Pi，它将在重启之间持续存在。

要更新网络接口以使用静态 IP 地址，请编辑`/etc/dhcpcd.conf`文件的内容。只需将`RASPBERRY_PI_IP`和`GATWAY_IP`分别切换为您希望 Raspberry Pi 使用的 IP 地址和您路由器的 IP 地址。

```
interface eth0
static ip_address=RASPBERRY_PI_IP/24
static routers=GATWAY_IP
static domain_name_servers=1.1.1.1
```

现在可以通过静态 IP 地址访问您的 Raspberry Pi，我们需要在 Raspberry Pi 操作系统上启用 SSH，这样我们就可以远程访问它。在使用命令行的 Raspbian OS 上，您可以使用以下命令轻松启用 SSH 服务。

```
# Set the ssh service to start on boot
sudo systemctl enable ssh# Start the ssh service immediately without a reboot
sudo systemctl start ssh
```

有了运行在 raspberry pi 上的 ssh 服务，您现在可以从另一台机器上 SSH 到 raspberry pi。使用另一台机器上的 ssh 客户端运行以下命令，替换您的 raspberry pi 的静态 IP 地址。

```
ssh pi@RASPBERRY_PI_IP
```

系统会提示您输入 Raspberry Pi 的密码，一旦登录，您就可以作为 Pi 用户在 Raspberry Pi 机器上运行命令。对于通过其他工具访问机器来说，这是一种非常有用的模式，并且比必须设置另一组 IO 设备来使用它要方便得多。在下一节中，我们将介绍如何使用一些配置作为代码工具，以简单方便的方式管理 raspberry pi 上的服务和配置。

# 以配置为代码运行服务

您的 Raspberry Pi 现已设置为远程访问，您可以开始在上面运行一些您自己的服务，或者根据您家庭实验室的需要进一步配置它。为了使这个过程更容易，更重要的是，如果您想在另一个 Raspberry Pi 上运行相同的配置步骤，我们可以使用接受配置作为代码的工具，这是一个概念，表明我们可以通过使用简单的配置来定义我们的计算机配置和服务，这些配置编写为代码，可以重复运行，每次都可以获得相同的结果。这样可以对您的计算机进行更加可预测的管理，并允许针对一台或多台主机多次运行相同的配置设置。

在这一节中，我们将提供一个示例，说明如何使用 [ansible](https://www.ansible.com/resources/get-started) 编写简单的“行动手册”,定义您希望在目标主机上运行的顺序任务。对于示例 ansible 剧本，我们将在主机上安装一些包，并在主机上创建另一个用户。

```
# example-playbook.yaml- name: Configure the raspberry pi
  hosts: RASPBERRY_PI_IPtasks:
  - name: "Update Apt Cache"
    apt: 
      update_cache: yes
    tags: installation- name: "Install Common packages"
    apt:
      name: ['runc', 'python-pip', 'docker.io', 'python3-venv', 'docker-compose']
      state: latest
    tags: installation, packages

  - name: "Python Docker"
    pip:
      name: 
        - docker
    tags: python

  - name: "Install Minikube"
    shell: 
      cmd: curl -Lo ~/minikube [https://storage.googleapis.com/minikube/releases/latest/minikube-linux-arm](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-arm) && chmod +x ~/minikube && mkdir -p /usr/local/bin/ && install ~/minikube /usr/local/bin/
    tags: installation, minikube
```

正如您在行动手册中看到的，我们配置了主机的 IP 地址以及我们希望 ansible 使用的登录凭据，例如 ssh 用户名和密码，以及 ansible 在需要对主机执行某些操作时可以用来提升权限的 sudo 密码。Ansible 有许多模块可用于常见的 Linux 系统功能，还有更多模块可用于您可能希望与之交互的其他服务或应用程序，如 AWS、其他操作系统或 Docker 等。

现在，您可以使用下面的脚本针对配置的目标主机运行创建的行动手册。

```
ansible-playbook example-playbook.yaml
```

您将看到每个任务在您的 raspberry pi 上运行时的输出。正如您所看到的，这种配置作为一种代码工具，可以非常容易地在机器上配置和运行一组命令，以产生所需的结果。这将为您节省更多的调试时间，因为每个命令的结果都更容易预测。最坏的情况是，如果一切都坏了，就用一个全新的操作系统重新开始，重新运行你的剧本。

如果你想在你的堆栈上运行更多的服务，看看下面的文章，看看你能运行的 10 个最受欢迎的自托管应用。

[](https://aaron-kt-berry.medium.com/top-10-software-for-your-homelab-in-2021-98137a7de051) [## 2021 年家庭实验室的十大软件

### 你可能有很多理由来运行你的实验室，但是在你的实验室里总会有越来越多的新东西可以尝试…

aaron-kt-berry.medium.com](https://aaron-kt-berry.medium.com/top-10-software-for-your-homelab-in-2021-98137a7de051) 

# 包扎

Raspberry Pi 平台非常易于使用，为您提供了出色的可定制性，无论您想在其上学习或运行什么。现在，您应该对设置 pi 时要考虑的一些好的事项有了更好的了解，您可以如何配置它以便更容易地进行远程访问，以及如何使用配置作为代码来更容易地管理您的 pi 和它上面的软件。我建议一旦你有了你的 raspberry pi 设置，就在机器上设置一些重要的服务，比如备份你需要从 raspberry pi 保存的重要配置和文件，以及设置重要服务或日志的警报。

# 进一步连接

*   如果你正在考虑获得一个中等订阅，你可以通过使用我的[推荐链接](https://aaron-kt-berry.medium.com/membership)来帮助我。
*   查看我在[媒体](https://medium.com/@aaron-kt-berry)上的其他文章，如果你想了解最新消息，请通过[电子邮件](https://aaron-kt-berry.medium.com/subscribe)订阅。
*   如果你想聊天，在推特上联系我，或者在 T2 的 LinkedIn 上联系我，如果你想雇佣我，我在 T4 的 Codementor 上。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)
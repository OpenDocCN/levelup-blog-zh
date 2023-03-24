# Ansible 入门:Windows 10 和 Ubuntu 20.04 工作站的本地自动化

> 原文：<https://levelup.gitconnected.com/getting-started-with-ansible-local-automation-of-windows-10-and-ubuntu-20-04-workstations-ffd03d7dc923>

![](img/8d602a88bd4c687a7355a7d7d8862303.png)

照片由[摄影师](https://unsplash.com/@ffstop?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

你不需要成为一个经验丰富的 DevOps 专业人员，就可以在你的个人工作空间中受益于 [Ansible](https://www.ansible.com/) 和相关的自动化工具。我将展示一个我用来自动化我的个人 Windows 10 和 Ubuntu 20.04 工作站配置的示例设置(类似的方法也适用于 MacOS)。

这种设置的主要目标是自动化软件安装、更新和配置，以快速让我的开发环境在 Ubuntu 和 Windows 上运行，这样在出现稳定性问题或切换到新机器时，可以毫不费力地重新创建系统，用不到一个小时的时间设置每个操作系统，为工作做好准备。将会派上用场的关键工具(除 Ansible 之外)将会是 [Snap](https://snapcraft.io/) (针对 Linux 软件包)、 [Mackup](https://github.com/lra/mackup) (针对 Linux 点文件/配置备份)和 [Chocolatey](https://chocolatey.org/) (针对 Windows 软件包)。

我将在这两个系统上安装 VSCode、git、Node.js (nvm)、Docker、Chromium、Slack 和 Dropbox。

# 配置 Windows 10

首先，我在 cmd 中运行以下脚本来为 Ansible 设置 WinRM:
`powershell.exe -ExecutionPolicy ByPass -File "windows-host-setup.ps1"`

然后，为了在 Windows 上执行我的 Ansible playbook，我需要一个正在运行的 [Ansible control node](https://docs.ansible.com/ansible/latest/network/getting_started/basic_concepts.html#control-node) ，这需要一个 Linux 环境，而让它在同一台机器上运行的最简单的方法是 Windows Subsystem for Linux (WSL)。

## 用于 Linux 的 Windows 子系统

1.  Windows 搜索`Turn Windows features on or off`，检查`Windows Subsystem for Linux`，安装，重启；
2.  从 Windows Store 安装并启动 Ubuntu 20.04
3.  创建带密码的用户；
4.  安装 Ansible: `sudo apt update && sudo apt install -y ansible`
5.  运行 Windows 剧本:`ansible-playbook windows-playbook.yml -i windows-inventory.yml --ask-pass --verbose`

如上所述，我使用 Chocolatey 安装软件包，然后自动更新。最后，我安装 [VSCode 设置同步](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync)来恢复我的 VSCode 设置。

# 配置 Ubuntu 20.04

如果可以的话，我更喜欢 snap 包(它可以自动更新并优雅地回滚)而不是 apt 包。以下是 Ubuntu 20.04 上的作品:

1.  安装 Ansible: `sudo apt update && sudo apt install -y ansible`
2.  运行 Ubuntu playbook: `ansible-playbook ubuntu-playbook.yml -i ubuntu-inventory.yml --ask-become-pass --verbose`

然后我运行 Dropbox，等待它同步，运行`mackup restore`，用`mackup backup`恢复之前备份到 Dropbox 的配置文件。最后，像以前一样安装并运行 VSCode Settings Sync。

# 就是这样！

一些手动步骤将一直保留，但总的来说，这种方法一旦成为工具带的一部分，将节省大量时间。系统配置的更多部分可以自动化，参见[可转换模块集合列表](https://docs.ansible.com/ansible/latest/collections/index.html)获取灵感，并随意派生和扩展我的示例 gist 供您自己使用！:)
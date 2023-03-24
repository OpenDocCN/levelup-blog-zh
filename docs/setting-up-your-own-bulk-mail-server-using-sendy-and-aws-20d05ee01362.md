# 黑客入侵:使用熊智娟和 AWS 建立自己的批量邮件服务器

> 原文：<https://levelup.gitconnected.com/setting-up-your-own-bulk-mail-server-using-sendy-and-aws-20d05ee01362>

![](img/151680966ffe9707c27dc23a0028409f.png)

卡罗尔·郑在 [Unsplash](https://unsplash.com/s/photos/airmail?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

已经有一段时间没写教程了。生活阻碍了我，我所有的项目想法都被搁置了。但是现在，在我的新工作中，我被迫重新评估时事通讯和大宗邮件的发送方式，就在那时我遇到了[熊智娟](https://sendy.co)。它非常棒——一个发送批量邮件的干净的解决方案和界面。我没有被熊智娟雇佣来做这个教程；我真的很喜欢这个产品，希望下面的教程能帮助到其他人。

在 AWS 上有一个非常详细的熊智娟教程，我想在这里强调一下，没有它，我甚至无法开始我的项目——[https://colcol . co . uk/introduction-to-installing-sendy-and-virtual min-on-AWS/](https://colcol.co.uk/introduction-to-installing-sendy-and-virtualmin-on-aws/)我的教程略有不同，因为我已经有一点技术背景，所以我可以稍微缩短步骤。此外，我在 Ubuntu 18.04 上使用灯设置。

你不需要很高的技术背景来完成这个教程，最多你可能只是在设置上比较慢。我还添加了一些我在使用新版 Virtualmin 时遇到的故障排除和问题，以及一些在 EC2 实例上省钱的方法。

需要注意的几点:

1.  你必须使用 Ubuntu **服务器**版本，这样它就没有 GUI 了。节省安装空间。
2.  我在免费层(t2.micro type EC2)上进行了尝试和测试，但它经常崩溃。其他在线教程说这是可能的，但我不知道是否 Virtualmin 升级或熊智娟或操作系统，但最终我不得不转移到 t2.small，它的工作好多了。我留下了一个关于如何自动启动/停止实例的注释，这样您可以节省一些钱。
3.  你需要拥有自己的域名来设置它。
4.  我用亚马逊 SES 代替我自己的 SMTP 服务器来设置它。
5.  我选择 EC2 而不是 raspberry pi 的原因是，如果你的批量邮件服务器托管在 EC2 上，AWS 每个月会给你 62，000 封免费邮件。此外，与 pi，你将负责维护互联网等。

# 那么这个教程是关于什么的呢…

基本上，我的公司花了一大笔钱来发送大宗邮件。这甚至不在 Mailchimp 这样的服务上；所以我无法想象那样的全套服务要花多少钱。因此，我决定寻找另一种解决方案，给我一个类似 Mailchimp 的界面，但自托管，这样可以降低成本。

我发现熊智娟是一个很好的界面选项，因为它们几乎涵盖了除拖放 HTML 电子邮件生成器之外的所有功能(我发现了另一个很好的解决方案，我将在教程中添加)。虽然他们有一次性费用，但这是值得的，因为这意味着软件得到了维护，而且创建者会定期在熊智娟论坛上回答问题。我还在黑色星期五买了熊智娟，他们给了很好的折扣:)所以如果你临近假期，查一下折扣。

本质上，熊智娟安装在 AWS 上的一个 EC2 实例上，与作为服务器控制面板的 T2 虚拟桌面一起。 [AWS SES](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/Welcome.html) 被用作发送邮件的 SMTP 服务器。这很方便，因为你可能知道试图从你自己的邮箱发送大量邮件会导致你的邮件被阻止，因为它看起来像垃圾邮件。因此，使用批量邮件 SMTP 服务器非常重要。

# 开始吧！

好了，我们不要再浪费时间了。让我们进入项目模式，开始使用我们自己的批量邮件服务器。

## 你需要什么

1.  经过验证的 AWS 帐户—验证可能需要一些时间，因此请提前计划。
2.  熊智娟驾照——你在网上购买后，他们会很快发送详细信息。
3.  我自己用的是 Ubuntu 18.04 电脑，因为大部分工作都是通过 SSH 完成的。如果您使用的是 Windows，那么您需要安装 Putty 来 SSH 到 EC2 实例中。
4.  您拥有并有权访问其 DNS 配置的域。

## 主要步骤概述

1.  创建安全组
2.  创建 EC2 实例
3.  为您的密钥对设置配置文件
4.  设置弹性 IP 地址
5.  通过 SSH 连接到 EC2
6.  安装虚拟机
7.  在 Virtualmin 上创建服务器
8.  设置 SSL
9.  安装熊智娟
10.  设置 Amazon SES
11.  验证 SES 上的域
12.  设置 Cron 作业

## 可选建议

1.  备份/恢复您的熊智娟数据库
2.  EC2 实例的自动启动/停止
3.  使用 MJML 创建超棒的 HTML 电子邮件
4.  使用快照在 AWS 上自动备份熊智娟
5.  更新您的熊智娟安装

如你所见，有相当多的步骤，所以调整好你自己的步伐。有时 DNS 配置需要一段时间才能生效，我建议测试服务器一周左右，看看它是否能满足您的需求。

# 创建安全组

1.  登录您的 [AWS 账户](https://aws.amazon.com/console/)。
2.  请确保将您的区域更改为右上角您想要的区域。选择这个的一个好方法是，如果你的大部分电子邮件将从英国发出，那么选择一个基于英国的地区。
3.  在左上方的服务选项卡下(这是我们将用来浏览 AWS 的地方)，找到/选择 **EC2** 。
4.  在左侧菜单中，选择**安全组**。
5.  点击**创建安全组**。
6.  输入以下细节和规则:
    **名称**:熊智娟群发邮件
    **描述** : HTTP，HTTPS，SSH，Virtualmin
    **VPC** :默认
    **规则 1** : HTTP(保留默认值)
    **规则 2** : HTTPS(保留默认值)
    **规则 3** : SSH，选择我的 IP —这将用于 SSH 如果您有多个工作地点，您可以添加所有地点，只要确保您给了它们适当的描述。
    **规则 4** :自定义 TCP，选择我的 IP，端口:10000——这将用于访问 Virtualmin
7.  点击**创建。**
8.  创建后，您将在安全组列表中看到该安全组。在这里，您可以为群组添加名称。悬停在第一列，**名称**，您将看到一个铅笔图标。单击并编辑。

# 创建 EC2 实例

1.  仔细检查你的地区。
2.  导航至 **EC2** 。
3.  从左侧菜单中选择**实例**。
4.  点击**启动实例**。
5.  选择左侧的**仅自由层**。
6.  选择 **Ubuntu Server 18.04 LTS** 。
7.  点击下一个的**。**
8.  对于**实例类型**，在自由层上只有一个合格的实例，那就是 t2.micro，但是这对于熊智娟和 Virtualmin 来说并不适用。我选择了 t2.small，它在过去的一个月里运行得相当平稳。这并不昂贵，而且我已经建议了一种在不使用时停止实例的方法来节省一些钱，因为 AWS 是按小时计费的。
9.  点击**下一步:配置实例细节**。
10.  确保选择**防止意外终止**，因为如果终止实例，您将丢失所有数据。有一些方法可以设置数据不被删除，但是恢复数据超出了本教程的范围。
11.  点击**下一步:添加存储**。
12.  输入 30GB 作为磁盘大小。这是空闲层上允许的最大值。在这里你也可以选择终止时不删除，但是正如我所说的，恢复它超出了本教程的范围。其余的可以保留为默认值。
13.  点击**下一步:添加标签**。
14.  不需要添加任何标签。
15.  点击**下一步:配置安全组**。
16.  选择**选择一个现有的安全组**。
17.  并选择我们在上一节中创建的安全组。在本教程中，它被称为**熊智娟批量邮件**。
18.  点击**查看并启动**。
19.  确保您的所有设置都是正确的。
20.  点击**启动**。
21.  如果您以前没有使用过 AWS，将会要求您创建一个新的密钥对。
22.  输入名称并下载文件。你只能做这一次，所以请确保你有它保存在某个安全的地方。
23.  启动 EC2 后，您可以输入一个实例名，以便于区分。比如**熊智娟服务器**。

## 设置登录 EC2 的密钥文件

1.  将下载的密钥文件移动到您的。ssh 文件夹。
2.  需要更改该文件的权限，否则 AWS 会在尝试使用该密钥文件登录时给出错误—[https://docs . AWS . Amazon . com/AWS C2/latest/user guide/troubleshooting instances connecting . html # troubleshoot-unprotected-key](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html#troubleshoot-unprotected-key)
3.  打开终端并导航到您的。ssh 文件夹。
4.  输入以下内容:`chmod 400 <mykey>.pem`

# 为您的密钥对设置配置文件

通常，当通过 ssh 登录到 EC2 实例时，还需要传递 pem 文件，这使得 SSH 命令有点麻烦。config 设置有助于将密钥文件指向 Amazon，这样您就不需要每次都输入密钥文件。

如果您不希望设置配置文件，这就是您的 ssh 命令通常的样子，如果您不希望创建配置设置，

```
ssh -i /path/<mykey>.pem ubuntu@ec2-198-51-100-1.compute-1.amazonaws.com
```

1.  打开终端并导航到您的。ssh 文件夹。
2.  如果你没有配置文件，那么`touch config`
3.  然后用`nano config`编辑配置文件
4.  根据您安装的映像，您的实例的用户名会发生变化。在这里找到完整列表—[https://docs . AWS . Amazon . com/AWS C2/latest/user guide/connection-prereqs . html # connection-prereqs-get-info-about-instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/connection-prereqs.html#connection-prereqs-get-info-about-instance) 为了这个教程，我们安装了一个 Ubuntu 镜像；所以用户名是 ubuntu。
5.  在配置文件中追加以下内容:

```
Host *amazonaws.com
IdentityFile ~/.ssh/<mykey>.pem
User ubuntu
```

6.Ctrl+O(保存更改)，然后 Ctrl+X 关闭编辑器。

# 设置弹性 IP 地址

AWS 上的一个[弹性 IP](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html) 对于您的域指向 EC2 服务器来说是一种非常方便的方式。只要与 EC2 相关联，它们就可以在 AWS 免费层下免费使用。

1.  在 AWS 上的 EC2 仪表板上，选择 **Elastic IPs** 。
2.  点击**分配弹性 IP 地址**。
3.  选择亚马逊的 IPv4 地址池。
4.  点击**分配**。
5.  现在点击新分配的 IP 地址，并从**动作**菜单或从分配 IP 地址时弹出的成功消息中选择**关联 IP 地址**。
6.  选择**实例**。
7.  为熊智娟选择 EC2 实例。
8.  点击**关联**。
9.  回到你的 [EC2 实例](https://console.aws.amazon.com/ec2/)，点击实例并查看下面的描述标签。确保公共 IP 与我们刚刚创建的弹性 IP 相同。

## 将弹性 IP 添加到您的域的 DNS 记录中

现在，我们需要将您的域或子域与该 IP 地址相关联。这是您将用来访问熊智娟的域。我建议将它创建为你网站的一个子域，类似于`sendy.<mydomain>.com`，因为你的主域是你理想的托管网站的地方(为此你可能需要时事通讯)。这将是您的熊智娟安装的 URL。

每个域主机都有自己的平台，但本质上，您需要登录到您的域主机控制面板，并导航到 DNS 设置/记录或高级 DNS 设置/记录。

一旦你在正确的地方，你需要添加一个**一个**记录。如果您希望主机名是一个子域，如`sendy.<mydomain>.com` 所示，主机名将是 **sendy** ，或者如果您使用根域，主机名将留空。

> 注意:这需要几个小时来关联，所以如果你很快完成了这个教程，如果你的熊智娟网站仍然无法加载，不要惊慌。睡一觉:)

# 通过 SSH 连接到 EC2

1.  在 AWS 控制台上，导航到您的 [EC2 实例](https://console.aws.amazon.com/ec2/)(如果需要，通过服务菜单)。
2.  在 EC2 实例列表中，单击您希望安装熊智娟的实例；我们在本教程中创建的。
3.  在下面的描述选项卡中，找到公共 DNS 并复制它。
4.  打开你的终端。
5.  输入`ssh <my-EC2-public-DNS>`
6.  接受指纹信息，您就登录了。

## 更新操作系统

1.  一旦您登录到 EC2，让我们借此机会在开始安装其余软件之前更新操作系统。
2.  `sudo apt-get update`
3.  `sudo apt-get upgrade`
4.  `sudo apt-get full-upgrade`
5.  `sudo reboot`
6.  重新启动时，您将与 EC2 断开连接。

# 安装虚拟机

为了让 Virtualmin 启动并运行，我们必须首先对 EC2 服务器的主机名进行一些编辑。

## 编辑主机名

1.  通过 ssh 登录您的 EC2。
2.  登录后，输入以下内容:`sudo nano /etc/hostname`。
3.  这里将有一个单词代表服务器的主机名—将其改为 **sendy** 。
4.  `sudo reboot`
5.  重新登录。
6.  提示中应该会更新您的服务器名称。
7.  现在，我们需要更新主机文件— `sudo nano /etc/hosts`。
8.  增加以下`127.0.1.1 sendy.<mydomain>.com`。这是假设熊智娟正在使用子域 **sendy** ，如果没有，那么简单地添加`<mydomain>.com`。
9.  `sudo reboot` —在对任何配置进行更改后重启总是好的。

## 安装虚拟桌面

1.  通过 ssh 重新登录到您的 EC2。
2.  首先我们需要下载安装文件— `wget http://software.virtualmin.com/gpl/scripts/install.sh`。
3.  运行安装文件— `sh ./install.sh --minimal`。我们将使用最小化的设置，因为熊智娟不需要 Virtualmin 附带的额外文件。尽管如此，如果你要在同一个地方托管网站，你可能需要完全安装 Virtualmin。这方面的细节超出了本教程的范围。
4.  输入 **y** 继续。
5.  您可能会被要求输入 FQDN，请输入您的熊智娟网址。在本教程的情况下，将是`sendy.<mydomain>.com`。
6.  安装分 3 个阶段进行，需要一些时间。
7.  安装完成后，更改 Virtualmin 的 root 密码— `sudo /usr/share/webmin/changepass.pl /etc/webmin/ root <mypassword>`

> 注意:我在设置熊智娟时试验了很多版本的 Ubuntu 和设置，我注意到在 Ubuntu 16.04 上，安装 Virtualmin 时有一个语言环境问题，下面是解决方案——[https://askubuntu . com/questions/162391/how-do-I-fix-my-locale-issue](https://askubuntu.com/questions/162391/how-do-i-fix-my-locale-issue)

## Virtualmin 安装后向导

1.  更改密码后，打开浏览器，输入您的熊智娟网址，如下所示— `sendy.<mydomain>.com:10000`
2.  使用用户名 **root** 登录，使用您在上一节中刚刚创建的密码登录。
3.  登录 Virtualmin 后，您将看到安装后向导。
4.  在第一个**介绍**页面点击**下一个**。
5.  因为我们使用 Virtualmin 来简单地监控熊智娟，所以为**预加载 Virtualmin 库选择 No？**和**是运行电子邮件域名查找服务器？**。
    点击**下一步。**
6.  对于**病毒扫描**选择否。点击**下一步。**
7.  对于**垃圾邮件过滤**选择否。点击**下一步。**
8.  在**数据库服务器**中，对 **MySQL** 选择是，对 **PostgreSQL** 选择否。点击**下一步。**
9.  为 MySQL 设置一个 root 密码。这应该不同于您为 Virtualmin 设置的 root 密码。点击下一个的**。**
10.  对于 **MySQL 数据库大小**，我选择了**大**，因为我有数百封电子邮件，但我想为它的增长留出空间；大杯对熊智娟来说应该足够了。这个真的要看你了。点击下一个的**。**
11.  你的**主域名服务器**是熊智娟网址— `sendy.<mydomain>.com`点击**下一步**。
12.  选择**仅存储散列密码**。点击下一个的**。**
13.  在**全部完成**页面上，只需点击**下一步**。
14.  现在 Virtualmin 将被刷新。点击**重新检查并刷新配置**。
15.  将会出现一串安装信息，一旦所有步骤完成，您应该会看到一条系统就绪信息。

# 在 Virtualmin 上创建服务器

1.  在`sendy.<mydomain>.com:10000`登录虚拟桌面。
2.  确保你在 Virtualmin 选项卡上(左侧菜单，右上方，有两个选项:Webmin 和 Virtualmin)。
3.  点击**创建虚拟服务器**。
4.  输入以下详细信息:
    **域名:** `sendy.<mydomain>.com`
    **管理密码:**与您在本教程中设置的其他密码不同的密码。
    **管理用户名:**自动(这将使你的用户名成为你的熊智娟网址的子域。在这种情况下，`sendy`。
5.  在**启用功能**下，我检查了以下内容，
    - DNS 域已启用？
    - Apache 网站启用？
    - MySQL 数据库启用？
    - Webmin 登录已启用？
    - Apache SSL 网站已启用？
6.  点击**创建服务器**。
7.  将会出现各种设置信息。一旦创建了服务器，您将在左侧菜单的顶部看到它。
8.  当您点击它时，您将会看到它的摘要，例如我们在步骤 4 中选择了**自动**的用户名。

# 设置 SSL

1.  点击 Virtualmin 左侧菜单上的**服务器配置**。
2.  点击 **SSL 证书**。注意:如果您没有看到 SSL 证书选项，那么有可能您还没有启用它。点击**编辑虚拟服务器**并选择**启用功能。**检查 **Apache SSL 网站是否已启用？**。
3.  点击顶部菜单上的**让我们加密**。
4.  在**为**申请证书下，您可以手动输入域名(如果您有不同于默认域名的域名),或者将其保留为默认域名。
5.  点击**请求证书**。
    *注意:如果您在尝试请求证书时收到 ACME 错误，您将需要在 EC2 实例上安装一个工具。通过 ssh 登录您的 EC2 和* `*sudo apt-get install certbot*` *，然后是* `*sudo certbot register*` *。输入你的邮箱地址，重启 EC2 实例* `*sudo reboot*` *。参考:*https://www.cloudmin.com/node/67390T42
6.  到**让我们在你的虚拟桌面`sendy.<mydomain>.com:10000`上加密**。
7.  当你点击**请求证书**时，这次应该不会有错误。
8.  点击顶部菜单中的**当前证书**。
9.  点击**复制到虚拟桌面**。
10.  注销当前虚拟主页。
11.  打开一个新的浏览器窗口，导航到你的虚拟桌面`sendy.<mydomain>.com:10000`。
12.  这一次连接将是安全的。

# 安装熊智娟

## 配置 MySQL 数据库

1.  SSH 到您的 EC2 实例。
2.  输入`mysql_secure_installation`。
3.  这将询问您一系列问题以保护您的 SQL server。
4.  配置由您决定；以下是我跟随的设置:
    **验证密码插件** : n
    **Root 密码**:你在 Virtualmin 的后安装 wixard 中设置的那个。
    **移除匿名用户** : y
    **不允许远程 root 登录** : y
    **移除测试数据库并访问** : y
    **重新加载权限表** : y
5.  熊智娟需要编辑 SQL 配置以使其正常运行。参考:[https://sendy.co/forum/discussion/7752/#Item_6](https://sendy.co/forum/discussion/7752/#Item_6)
6.  在你的终端中输入下面的
    到 EC2 实例`sudo nano /etc/mysql/mysql.conf.d/mysql.cnf`。
7.  在`[mysqld]`部分的末尾，有一个 SQL 模式列表。确保`ONLY_FULL_GROUP_BY`被移除。
8.  编辑后的列表可能如下:
    `sql_mode = "STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION"`
9.  重启 MySQL 服务
    `sudo service mysqld restart`

## 建立熊智娟

1.  使用您在购买许可证时收到的电子邮件中的链接下载熊智娟。让这些文件现在就在你的计算机上，因为我们需要对配置进行一些编辑。
2.  下载时，导航至您的虚拟桌面。
3.  点击左侧菜单上的**编辑数据库**。
4.  点击顶部菜单中的**密码**。
5.  将鼠标悬停在钥匙符号上以获取您的用户名和密码。记下它们。
6.  一旦熊智娟下载到你的本地电脑，解压。
7.  打开文件`includes/config.php`。
8.  更新所需详细信息:
    **安装 URL** : `sendy.<mydomain>.com`
    **主机名** : `localhost`
    **用户名**:您从 Virtualmin
    **密码**:您从 Virtualmin
    **DB 名称** : `sendy`
9.  将 sendy 文件复制到 EC2 中有点复杂，有很多方法可以做到，我确信比我的方法更好。所以，如果你把这个项目作为一次学习经历，就在这里做实验吧。我很乐意听到不同于我概述的方法的建议:
    -我使用 Filezilla 复制文件
    -当 VirtualMin 安装后，它会创建一个名为 **sendy** (假设您遵循了我的教程中的命名约定)
    -要使用 Filezilla，您必须作为这个 **sendy** 用户登录到 EC2。为此，您需要授予该用户 SSH 访问权限。
    -一旦你给它 SSH 访问权限，你就可以使用 Filezilla 登录并复制必要的文件。
    步骤详述如下。
10.  以通常的方式 SSH 到 EC2 实例中。
11.  首先，我们必须得到私钥的公钥部分(即**)。pem** 文件)我们从 AWS 下载的。输入`cat .ssh/authorized_keys`。复制从`ssh-rsa`开始的输出。暂时将它保存在一些文本编辑器中。记得以后扔掉。
12.  现在我们必须切换到 EC2 实例上的 **sendy** 用户。输入`sudo su sendy`
13.  您将看到您的提示更改为 **sendy**
14.  为该用户创建 SSH 目录:
    `mkdir .ssh`
15.  更改 SSH 文件夹的权限。这在处理钥匙时很重要:
    `chmod 700 .ssh`
16.  创建一个`authorized_keys`文件:
    `touch .ssh/authorized_keys`
17.  更改文件的权限:
    `chmod 600 .ssh/authorized_keys`
18.  让我们添加从 EC2 主用户复制的公钥。
    `nano .ssh/authorized_keys`
19.  将密钥(不是您的`.pem`密钥，而是我们之前复制的公钥)复制到文件中。`Ctrl+O`，`Ctrl+X`保存文件。
    *注:这些步骤从这里改编而来:*[*https://docs . AWS . Amazon . com/AWS C2/latest/user guide/managing-users . html*](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/managing-users.html)
20.  登录到 Filezilla 上的 EC2 实例。详细信息将与您的 SSH 登录相同，但这一次不是使用`ubuntu`作为 Filezilla 上的用户名，而是输入`sendy`。
21.  将文件从本地计算机(左窗格)复制到 EC2 上的目录`/home/sendy/public_html/`(右窗格)。
    *注意:确保隐藏文件* `*.htaccess*` *也被复制。*
22.  如果复制时 EC2 实例上的用户和组所有者发生了变化，可以通过再次登录(或在 Filezilla 上)将它们更改为`sendy`:
    `sudo chown -R sendy:sendy /home/sendy/public_html`
23.  安装一些熊智娟需要的库:
    `sudo apt-get install libcurl4-openssl-dev php7.2-curl`
24.  `sudo reboot`
25.  一旦 EC2 重新启动，导航到您的熊智娟 URL — `sendy.<mydomain>.com`。
26.  如果你看到一个 500 的错误，请按照这个链接来纠正它—[https://sendy.co/troubleshooting#500-error](https://sendy.co/troubleshooting#500-error)
    *注意:你可以使用 Virtualmin 的文件管理器来编辑文本文件。*
27.  `sudo reboot`
28.  导航到熊智娟— `sendy.<mydomain>.com`
29.  您现在应该会看到一个安装页面。在服务器兼容性列表中是 mod_rewrite 没有启用，没关系。在 AWS 上安装时，这通常是不启用的。
30.  填写所有细节。登录详细信息将用于管理员登录。AWS 详细信息现在可以留空。我们将在下一节对此进行补充。
31.  填写完详细信息后，点击**立即安装。**
32.  您现在应该看到登录页面，您可以在其中输入您在之前的安装页面上设置的电子邮件和密码。

# 设置 Amazon SES

1.  登录您的 [AWS 控制台](https://aws.amazon.com/)。
2.  使用**服务**菜单导航至 **IAM** 。
3.  确保您的区域设置在右上方。
4.  点击右侧菜单中的**用户**。
5.  点击**添加用户**。
6.  输入以下详细信息:
    **用户名** : sendy
    **访问类型**:程序化访问
7.  点击**下一步:权限**。
8.  选择**附加现有策略**。
9.  在搜索中输入`sesf`。选择 **AmazonSESFullAccess** 。
10.  在搜索中键入`snsf`。选择**amazonsfunllaccess**。
11.  点击**下一步:标签**。这一节可以跳过。
12.  点击**下一步:复习。查看您的设置。**
13.  点击**创建用户**。
14.  将凭据复制到一个临时文本文件中，以便轻松进入熊智娟。或者，并排打开熊智娟，直接复制。
    *注意:请确保在复制完凭证后，删除包含凭证的临时文本文件。*
15.  转到您的熊智娟— `sendy.<mydomain>.com`，在右上角的下拉菜单中，选择**设置**。
16.  在**Amazon Web Services Credentials**下添加您的 AWS 凭证，并在 **Amazon SES region** 下选择您的 AWS 区域。
17.  点击**保存**。

# 验证 SES 上的域

当你第一次开始使用 AWS SES 时，它会将你置于一个沙盒环境中，在那里你可以发送有限的电子邮件，并且只能从经过验证的电子邮件或域中发送。这是为了鼓励您在向客户发送简讯之前测试您的批量邮件服务器。利用这段时间彻底测试和学习熊智娟是如何工作的，因为你的错误在这里不会有任何影响。这也是检查熊智娟在 AWS 上是否设置正确，以及 EC2 实例类型是否是您所需要的理想类型的好时机。在我的例子中，我从 t2.micro(免费层)开始，但是熊智娟经常崩溃，所以我最终不得不升级。

> 注意:当您进行了足够多的测试，并准备增加对 SES 的限制，并脱离沙盒环境时，您需要向 AWS 提出请求。在此查找详细信息—[https://docs . AWS . Amazon . com/ses/latest/developer guide/manage-sending-quotas-request-increase . html](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/manage-sending-quotas-request-increase.html)

1.  登录您的 [AWS 控制台](https://aws.amazon.com/)并导航至 **SES** 。
2.  点击左侧菜单上的**域**。
3.  点击**验证新域**。
4.  输入您的域名。该域名应与您托管熊智娟的域名— `<mydomain>.com`相匹配，不含`sendy`子域名部分。
5.  检查**生成 DKIM 设置**。
6.  打开域的控制面板，您可以在其中设置 DNS 记录。正如我们在上一节中设置弹性 IP 时所做的那样。
7.  需要添加两类记录:**域验证记录** (TXT)和 **DKIM 记录集** (CNAME)。将它们添加到您的 DNS 设置中。
    *注意:如果您的域已经有关联的邮件，不要添加* ***邮件接收记录*** *。*
8.  检查与您的 AWS 帐户相关的电子邮件，很快您就会收到正在验证的域的确认。

这是重要的一步，因为它减少了你的简讯进入客户垃圾邮件的机会。现在你可以开始玩熊智娟了，只要电子邮件只发送到你的域名或从你的域名发出。

如果你想试着发送给你的其他电子邮件进行测试，比如私人邮件，你必须点击左边菜单上的**电子邮件地址**来验证个人邮件。

> 注意:AWS 提供了一个很棒的邮箱模拟器来测试熊智娟上的电子邮件行为—[https://docs . AWS . Amazon . com/ses/latest/developer guide/Mailbox-Simulator . html](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/mailbox-simulator.html)

## 从域更新您的邮件(可选)

1.  验证您的域后，在 SES 控制台的域列表中单击它。
2.  在这里，您可以看到您的域的设置，例如我们在上一节中添加的记录。
3.  一个额外的功能是使用来自域的邮件。如果你试着从熊智娟发一封电子邮件(比如发给你自己)，你可以在邮件标题中看到，这封邮件是**mail-by**<AWS 服务器名称>。你可以通过观察你可能收到的其他简讯来尝试一下。有些人显示一个个性化的域名，有些人只显示原始的服务器名称。比如 AWS 简讯来自`bounce.amazon.com`。`bounce`是一个用于时事通讯的常见子域，但这真的取决于你。
4.  回到 AWS 控制台，打开来自域的**邮件部分。并点击**添加来自域的邮件**。**
5.  确保来自域的**邮件不同于您的熊智娟子域，并且没有被您域的任何其他部分使用。有各种各样的选择，像`bounce.<mydomain>.com`或`mail.<mydomain>.com`或`info.<mydomain>.com`。**
6.  您必须在您的域的控制面板上设置两种类型的 DNS 设置: **MX 记录设置** (MX)和 **SPF 记录设置** (TXT)。
    *注意:不要担心一个子域的 MX 记录不会与你的主域的电子邮件冲突。*
7.  一旦 AWS 验证了该域，您应该会收到一封电子邮件。

# 设置 Cron 作业

熊智娟建议您添加四个 cron 作业:

*   导入 CSV
*   计划活动
*   激活自动回复
*   更新方案

尽管 cron 作业不是必需的，但是最好添加它们，以防服务器发生问题，熊智娟可以继续它正在执行的任何任务。

下面是熊智娟关于克隆工作细节的摘录，

```
To import large CSVs more reliably and have Sendy handle server timeouts, add a cron job with the following command.Time Interval
*/1 * * * * 
Command
php /home/sendy/public_html/import-csv.php > /dev/null 2>&1
(Note that adding cron jobs vary from hosts to hosts, most offer a UI to add a cron job easily. Check your hosting control panel or consult your host if unsure.).The above cron job runs every 1 minute. You can set it at 5 minutes (eg. */5) or any interval you want. The shorter the interval, the faster your CSV will start to import. Once added, wait for cron job to start running. If your cron job is functioning correctly, the blue informational message will disappear and future CSV imports will be done via cron.
________________________________________________
To schedule campaigns or to make sending more reliable, add a cron job with the following command.Time Interval
*/5 * * * * 
Command
php /home/sendy/public_html/scheduled.php > /dev/null 2>&1
This command needs to be run every 5 minutes in order to check the database for any scheduled campaigns to send.
(Note that adding cron jobs vary from hosts to hosts, most offer a UI to add a cron job easily. Check your hosting control panel or consult your host if unsure.).Once added, wait around 5 minutes. If your cron job is functioning correctly, you'll see the scheduling options instead of this modal window when you click on "Schedule this campaign?".
_____________________________________________
To activate autoresponders, add a cron job with the following command.Time Interval
*/1 * * * * 
Command
php /home/sendy/public_html/autoresponders.php > /dev/null 2>&1
This command needs to be run every minute in order to check the database for any autoresponder emails to send.
(Note that adding cron jobs vary from hosts to hosts, most offer a UI to add a cron job easily. Check your hosting control panel or consult your host if unsure.).Once added, wait one minute. If your cron job is functioning correctly, you'll see the autoresponder options instead of this modal window when you click on the "Autoresponders" button.
____________________________________________________
Add a cron job that runs every 15 minutes with the following command:Time Interval
*/15 * * * * 
Command
php /home/sendy/public_html/update-segments.php > /dev/null 2>&1
(Note that adding cron jobs vary from hosts to hosts, most offer a UI to add a cron job easily. Check your hosting control panel or consult your host if unsure.).Once added, wait for the cron job to start running in 15 minutes. If your cron job is functioning correctly, the blue informational message will disappear and your segmentation results will be updated automatically every 15 minutes by the cron job.
```

## 在 Virtualmin 上添加 cron 作业

1.  导航到您的虚拟桌面。
2.  在左上角选择 **Webmin** 。
3.  在**系统**下，点击**预定 Cron 作业**。
4.  点击顶部的**创建一个新的计划 cron 作业**。
5.  输入以下**作业详细信息** :
    **作为**执行 cron 作业:root
    **Active？**:是
    **命令**:在这里输入你正在添加的 cron 任务的命令。例如，对于导入 CSV，输入`php /home/sendy/public_html/import-csv.php > /dev/null 2>&1`。你可以通过[查看你的熊智娟](https://sendy.co/forum/discussion/4800/where-are-the-cron-job-setup-instructions/p1)或者我在上面列出的命令。
    *注意:如果您的熊智娟安装没有安装在* `*sendy*` *上，php 文件的路径可能会有所不同。* 其余可以留空或者可以添加一个**描述**。
6.  对于**何时执行**，这真的取决于你，熊智娟会给你最佳执行时间的指导方针。例如，对于导入 CSV，他们建议每 1 分钟一次。参见上面列出的 cron 作业中的**下的**时间间隔。
7.  对于**执行**的日期范围，最好选择**在任意日期**运行。
8.  点击**创建。**
9.  对其余的 cron 作业也这样做。

# 可选建议

以下是您可以在熊智娟中使用或整合的一些选项。

## 备份/恢复您的熊智娟数据库

在你真正开始使用熊智娟之前，这是一个好主意。您可能需要将您的熊智娟转移到另一个服务器或移动到一个更大的数据库等。

**倒车**

1.  导航至您的虚拟桌面— `sendy.<mydomain>.com:10000`。
2.  点击左侧菜单上的**编辑数据库**。
3.  点击**管理你数据库旁边的**，应该命名为`sendy`。
4.  在底部，点击**备份数据库。**
5.  在**备份到文件**部分，选择**在浏览器中下载**。
6.  在底部，点击**立即备份**。
7.  将下载 sql 文件。

最终，我想自动化这个过程，但没有机会这样做。希望能得到一些建议。

**恢复**

1.  导航至您的虚拟桌面— `sendy.<mydomain>.com:10000`。
2.  点击左侧菜单中的**编辑数据库**。
3.  点击**管理你数据库旁边的**，应该命名为`sendy`。
4.  点击底部的**执行 SQL** 。
5.  点击**从文件**运行 SQL。
6.  从上传的文件中选择**，假设您已经从上一节下载了备份。**
7.  附上你的`.sql`文件。通常被称为`backup.sql`。
8.  点击**执行**。

## EC2 实例的自动启动/停止

对于使用 EC2 实例的付费版本的人来说，这是一个很好的选择。AWS 按小时收费，因此 EC2 离线的任何时间都是省钱的。唯一的缺点是，当您的实例关闭时，熊智娟不能发送任何预定的活动，也不会从您的简讯中收集点击数据。

就我而言，这些电子邮件只针对一个地区的人，所以让它在半夜离线是有意义的。

我不打算在这里重写这些步骤，因为 AWS 已经在他们的文章中详细介绍了每个步骤—[https://AWS . Amazon . com/premium support/knowledge-center/start-stop-lambda-cloud watch/](https://aws.amazon.com/premiumsupport/knowledge-center/start-stop-lambda-cloudwatch/)

## 使用 MJML 创建超棒的 HTML 电子邮件

不幸的是，熊智娟不提供所见即所得编辑器，所以如果你想写一些 HTML 简讯，你需要找到另一种方式来做。在选定 MJML 之前，我至少尝试了十种。我对此感到非常惊讶。它非常容易使用，文档中有足够的教程和例子让你自己制作。

MJML 代码简洁明了，如果你使用的是他们支持的编辑器，你可以很容易地将所有内容转换成 HTML 并复制到熊智娟的模板中。我个人最喜欢的是 [Visual Studio Code](https://code.visualstudio.com/) ，它有一个 MJML 插件，你可以在编码时实时查看时事通讯的样子，还可以转换成 HTML，等等。

## 使用快照在 AWS 上自动备份熊智娟

随着你的熊智娟数据库的增长，开始备份你的服务器是个好主意，主要是你的注册用户数据库。也避免了重新设置服务器。

AWS 已经有了一个自动执行这些备份的教程—[https://AWS . Amazon . com/premium support/knowledge-center/EBS-snapshot-data-life cycle-manager/](https://aws.amazon.com/premiumsupport/knowledge-center/ebs-snapshot-data-lifecycle-manager/)

有许多方法可以备份您的数据，使用快照可能会变得昂贵。所以，在你继续做这件事之前，探索一下你的选择。

## 更新您的熊智娟安装

几个月前我写了这个帖子，现在是时候让熊智娟做一些更新了。

> 注意:在本教程中，我还对熊智娟在 EC2 上的首次安装方式做了新的更新。最初，我有一个稍微麻烦的方法，首先将文件复制到 EC2 上的 Ubuntu 主目录，然后 SSH 'ing 并将它们复制到 public_html 目录。因此，对于使用这种旧方法的读者，我已经将其更新为直接 SSH 到 EC2 上的 sendy 用户。您可以向上滚动到“设置熊智娟”部分，查看该部分的更新。下一节假设您可以通过 SSH 进入 sendy 用户。

更新熊智娟非常简单。

1.  您可能会在熊智娟页面的底部看到一条消息，说明有可用的更新。单击该按钮，系统会提示您在本地机器上下载新的熊智娟。
2.  你还会看到熊智娟关于如何更新你的系统的一些说明。查看这些说明很重要，因为每次更新都可能需要您做一些特别的事情。下面的步骤假设你已经看过了。
3.  一般来说，一旦下载了新的 sendy 文件并在本地机器上解压缩，就会要求您从 EC2 实例中复制`/includes/config.php`文件。这很重要。sendy 下载中的新配置文件不会保存您的数据库配置。所以复制，把旧的`config.php`复制到这个新下载的安装文件中。你可以直接通过文件管理器下的 VirtualMin 页面来实现。
4.  如果你已经在`/locale/`下载了额外的语言文件，那么把它们从 EC2 复制到你的本地机器上。
5.  如果您对`.htaccess`文件进行了更改，那么也下载该文件。
6.  用下载的文件替换新版本— `config.php`、`/locale/`、`.htaccess`
7.  使用 EC2 通常的 SSH 细节登录到 Filezilla 上的 EC2 实例，但是将用户名改为`sendy`。
    *注意:对于在 2020 年 5 月 12 日之前使用本教程的读者，请向上滚动到设置熊智娟，查看如何使用* `*sendy*` *用户登录到您的 EC2 实例。*
8.  将新下载的 sendy 安装文件复制并替换到`/home/sendy/public_html/`的 EC2 实例目录中。
9.  刷新您的熊智娟页面，您应该会看到新的变化(新版本可用的消息消失了)。如果你看不到这些变化，你的电脑可能已经缓存了这个页面。

哇，那是一个很长的教程。会有一些错误，我提前道歉。我已经读了好几遍了，但是它太长了！请指出任何不符合的地方。

你可以在下面找到更多的黑客系列。本系列旨在用免费工具或最低成本快速构建一些东西，而不是购买专有软件。看看他们！

## [黑客入侵:谷歌工作表&客户关系管理](https://medium.com/all-technology-feeds/hacking-it-google-sheets-crm-59e413bee303)

## [破解它:从谷歌表单生成 PDFs】](https://medium.com/@nem25/hacking-it-generate-pdfs-from-google-forms-3ca4fcc5a0aa)

> 希望这个指南是有用的，如果有任何意见/编辑/问题/疑问，请提出来:)欢迎所有的反馈。也可以通过 Twitter 随意提问—[https://twitter.com/neha_m25](https://twitter.com/neha_m25)
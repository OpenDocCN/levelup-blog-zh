# 如何在云中备份 MySQL 数据库

> 原文：<https://levelup.gitconnected.com/how-to-backup-a-mysql-database-in-the-cloud-1192fc4629f2>

## WEB 开发:

## 包含逐步说明的完整解决方案

![](img/e19992cec8134bde23b98dd16444db8e.png)

图片由 Geran de Klerk 提供

## 总结:

阅读本文的主要好处是，您将学习如何使用 mysqldump 来备份和恢复您的数据库。这包括执行一次性备份、创建远程备份服务、传输备份以及从备份中恢复数据库。这将在发生硬件或软件故障、数据损坏和人为事件(如恶意攻击或数据删除)时保护您的数据库。但是，这不能保护您的数据库免受由不适当的访问权限、弱密码和未加密数据引起的破坏。

我们将使用 Linode 作为我们的云服务提供商，因为他们的产品易于使用、价格合理、可扩展，并且他们的客户服务非常出色。我们将使用 Nginx 作为我们的网络服务器，因为他们的产品是免费的，处理高网站流量，并为世界上 10 万个顶级网站中的 60%提供支持。

**推广:**这个[推荐链接](https://linode.gvw92c.net/P0BmGX)为 Linode 提供 100 美元的信用

```
**# How to Put a High Performance Website on the Internet:** 01\. [How to Host a Website in the Cloud](https://medium.com/p/915acad9c15f)
02\. [How to Protect a Website From Threats](https://medium.com/p/78ce938c7bd5/)
03\. [How to Optimize a Website to Load in 1 Second](https://medium.com/p/3d67f11018ec)
04\. [How to Add a Database to a Website](https://medium.com/p/8d36d1e77316)
05\. [How to Protect a Database from Threats](https://medium.com/p/7cbc15c714ae)
06\. [How to Backup a Database in the Cloud](https://medium.com/p/1192fc4629f2)
07\. [How to Scale a Website to Handle High Traffic](https://medium.com/p/382a8d960f77)
08\. [How to Add a Node.js Application to a Website](https://medium.com/p/57218f9dfaaa)
09\. How to Add a Python Application to a Website
10\. How to Add a Payment Method to a Website
```

## 目录:

1.  [创建虚拟机](#86b8)
2.  [安装 Mysqldump 实用程序](#3656)
3.  [启用简单防火墙](#ece8)
4.  [创建用户账户](#cf40)
5.  [禁用 Root 登录](#e6d6)
6.  [禁用密码认证](#5f58)
7.  [创建 MySQL 用户](#9ab6)
8.  [创建备份时间表](#73dd)
9.  [创建备份文件](#28d1)
10.  [打开 SSH 端口](#9162)
11.  [传输备份文件](#fc24)
12.  [用备份文件恢复数据库](#48fb)

## 创建虚拟机:

虚拟机是一个模拟的计算机系统，拥有自己的 CPU、内存、存储和网络接口，位于我们云服务提供商的物理硬件上。这使我们可以安装我们的网络服务器，托管我们的网站，并根据需要每月花费很少的费用升级我们的硬件。在本节中，我们将使用最便宜的月度计划创建包含我们网站的虚拟机。

```
**# create virtual machine** 1\. create an account on [linode](https://www.linode.com/lp/free-credit-short/)
2\. click "create" button
3\. click "linode" menu item**# select linux distribution**
1\. click image dropdown menu in "choose a distribution" section
2\. click "ubuntu 20.04 LTS" menu item**# select datacenter to store virtual machine**
1\. click region dropdown menu in "region" section
2\. select same region as your virtual machines from before**# select monthly plan**
1\. click "shared cpu" tab in "plan" section
2\. click "nanode 1gb" radio button**# finish virtual machine**
1\. scroll down to "linode label" section
2\. enter "database-backup" into "linode label" text field
3\. enter password into "root password" text field
4\. click "create linode" button
5\. wait until linoide finishes
```

## 安装 Mysqldump 实用程序:

Mysqldump 实用程序是备份 MySQL 数据库的程序。这允许我们生成一个 MySQL 文件，其中包含我们需要在数据库中重新创建结构和数据的 SQL 语句。在本节中，我们将通过安装 MySQL-Client 包来安装 Mysqldump 实用程序。

```
**# open console as root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "database-backup" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter "root" into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# update package information**
sudo apt-get update**# install mysql-client**
sudo apt-get install --yes mysql-client
```

## 启用简单防火墙:

简单防火墙是一个程序，它根据 IP 地址和端口信息阻止传入和传出的网络流量，从而保护您的计算机免受攻击。这允许我们将网络流量限制在防火墙规则允许的 IP 地址和端口。在本节中，我们将通过执行一个命令来阻止所有传入的网络流量。

```
**# enable firewall**
sudo ufw enable
```

## 创建用户帐户:

用户帐户是可以访问文件和执行命令的实体。这使得攻击者更难侵入我们的网络服务器，因为他们需要先破解我们的用户名，然后才能破解我们的密码。在本节中，我们将创建一个具有管理权限的用户帐户。

```
**# change "placeholder" to unique username** username="placeholder"**# create new user on virtual machine** sudo adduser $username**# choose password for new user**
1\. enter password for user account
2\. press "return"
3\. re-enter password
4\. press "return"
5\. press "return" to enter the default value
6\. enter "y" to confirm information
7\. press "return"**# add admin group to sudo file**
sudo groupadd admin**# grant administrative permissions to new user**
sudo usermod -aG admin $username**# switch to new user**
sudo su $username
```

## 禁用 Root 登录:

Root 用户是一个用户帐户，可以无限制地访问每个基于 Linux 的操作系统预装的整个系统。这为攻击者提供了他们入侵我们的网络服务器所需的一半信息。在本节中，我们将通过更改 SSH 配置文件中的设置来防止 root 用户访问我们的虚拟机。

```
**# disable root login**
sudo sed "s|PermitRootLogin yes|PermitRootLogin no|g" -i /etc/ssh/sshd_config**# restart ssh**
sudo service ssh restart
```

## 禁用密码验证:

密码是一种身份验证方法，当密码太弱或被许多不同的帐户重复使用时，它会成为一种威胁。这使得攻击者有可能通过反复试验来破解密码。在本节中，我们将通过更改 SSH 配置中的设置来防止用户使用密码身份验证访问我们的虚拟机。

```
**# disable password authentication** sudo sed "s|PasswordAuthentication yes|PasswordAuthentication no|" -i /etc/ssh/sshd_config**# restart ssh**
sudo service ssh restart
```

## 创建 MySQL 用户:

MySQL 用户是一个可能会破坏我们整个数据库的用户帐户。这通常发生在新的数据库管理员出于方便将“all privileges”选项授予他们的 MySQL 用户时。在本节中，我们将通过为 MySQL 用户创建备份数据库所需的绝对最小权限来防止这种情况。

```
**# open console as root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "database" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter "root" into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# download backup mysql file**
sudo curl -o /etc/mysql/backup_user.sql [https://gist.githubusercontent.com/david-littlefield/77cd5ab402d16dc54fb2328d7c3e80aa/raw](https://gist.githubusercontent.com/david-littlefield/77cd5ab402d16dc54fb2328d7c3e80aa/raw)**# store mysql non-root user**
user="backup"**# change "placeholder" to strong password**
password="placeholder"**# add user to mysql file** sudo sed "s|#user_placeholder#|$user|g" -i /etc/mysql/backup_user.sql**# add password to mysql file** sudo sed "s|#password_placeholder#|$password|g" -i /etc/mysql/backup_user.sql**# change "placeholder" to mysql root user password** root_password="placeholder"**# open mysql**
sudo mysql -u root -p$root_password**# run crud mysql file**
source /etc/mysql/backup_user.sql**# exit mysql**
exit**# remove crud mysql file**
sudo rm /etc/mysql/backup_user.sql
```

## 创建备份计划:

Crontab 是一个在特定日期和时间自动执行一组命令的程序。这使我们能够创建数据库的例行备份，该备份以预定的时间间隔重复出现。在本节中，我们将通过创建一个 crontab 文件每天备份一次数据库。

```
**# get ip address of "database" linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "database" linode
3\. find ip address under "ip addresses" label
4\. write down ip address**# open console as non-root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "database-backup" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# download bash file that backs up mysql**
sudo curl -o /etc/mysql/mysqldump.sh [https://gist.githubusercontent.com/david-littlefield/05c95818cabbd2cd29e0e5780f6a7a44/raw](https://gist.githubusercontent.com/david-littlefield/05c95818cabbd2cd29e0e5780f6a7a44/raw)**# store mysql non-root user from earlier**
user="backup"**# change "placeholder" to mysql non-root user password**
password="placeholder"**# change "placeholder" to ip address of "database" linode** host="placeholder"**# store database name** database="website"**# store mysql port number**
port="5000"**# add user to mysqldump configuration file** sudo sed "s|#user_placeholder#|$user|g" -i /etc/mysql/mysqldump.cnf**# add password to mysqldump configuration file**
sudo sed "s|#password_placeholder#|$password|g" -i /etc/mysql/mysqldump.cnf**# add host to bash file**
sudo sed "s|#host_placeholder#|$host|g" -i /etc/mysql/mysqldump.sh**# add database to bash file**
sudo sed "s|#database_placeholder#|$database|g" -i /etc/mysql/mysqldump.sh**# add port to bash file**
sudo sed "s|#port_placeholder#|$port|g" -i /etc/mysql/mysqldump.sh**# only allow root user to access bash file**
sudo chmod 600 /etc/mysql/mysqldump.sh**# schedule backup to run one-time per day**
sudo echo -e "1 \* \* \* \* exec /bin/bash /etc/mysql/mysqldump.sh" > ~/mysql**# move mysql crontab file to "cron.d" directory**
sudo mv ~/mysql /etc/cron.d/mysql
```

## 创建备份文件:

备份是我们数据库的副本，保护我们免受数据丢失。这使我们能够在发生硬件或软件故障、数据损坏以及人为事件(如恶意攻击或数据删除)时恢复数据。在本节中，我们将通过创建 web 服务器、准备 web 服务器和创建例行备份服务来实现这一点。

```
**# create backup file one-time**
sudo bash /etc/mysql/mysqldump.sh**# verify backup file exists**
sudo ls /etc/mysql/backup/$(date +%Y)/$(date +%m)/$(date +%d)/
```

## 打开 SSH 端口:

SSH 端口是 SSH 协议用来在我们的链路节点之间建立安全连接的端口。这个端口被我们的“数据库”节点上的防火墙关闭了。在本节中，我们将通过向防火墙添加防火墙规则并启用密码身份验证来打开 SSH 端口。

```
**# get ip address of "database" linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "database-backup" linode
3\. find ip address under "ip addresses" label
4\. write down ip address**# open console as non-root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "database" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# change "placeholder" to ip address of "database-backup" linode** ip_address="placeholder"**# enable ssh port from ip address of "database-backup" linode**
sudo ufw allow from $ip_address to any port 22**# enable password authentication** 
sudo sed "s|PasswordAuthentication no|PasswordAuthentication yes|" -i /etc/ssh/sshd_config**create ".ssh" directory**
sudo mkdir -p ~/.ssh/
```

## 传输备份文件:

“数据库”节点不允许根用户远程访问。这使得攻击者更难访问我们的数据库，因为他们需要事先侵入我们的云服务提供商的数据中心。然而，这阻止了我们从“数据库备份”节点远程恢复数据库，因为 mysqldump 实用程序需要 root 访问权限。在本节中，我们将在我们的 linodes 之间建立一个安全连接，禁用密码验证，并传输我们的备份文件。

```
**# open console as non-root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "database-backup" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**create ".ssh" directory**
sudo mkdir -p ~/.ssh/**# create ssh key**
sudo ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa_database**# create ssh key without a passphrase**
1\. press "return" to use no passphrase
2\. press "return" to confirm**# set restricted file permissions**
sudo chmod 600 ~/.ssh/id_rsa_database.pub**# change "placeholder" to non-root user on "database" linode** user="placeholder"**# add ip address to ssh configuration file**
sudo echo -e "host database\n    hostname $host\n    identityfile ~/.ssh/id_rsa_database\n" >> ~/config**# move ssh config file to ".ssh" directory** 
sudo mv ~/config ~/.ssh/config**# copy ".ssh" directory to "root" directory**
sudo cp -r ~/.ssh/ /root/.ssh/**# add ssh key to "authorized_keys" file**
sudo cat ~/.ssh/id_rsa_database.pub | ssh $user@database "cat >> ~/.ssh/authorized_keys"**# connect to "database" linode**
sudo ssh $user@database**# disconnect from "database" linode**
exit**# disable password authentication** sudo sed "s|PasswordAuthentication yes|PasswordAuthentication no|" -i /etc/ssh/sshd_config**# transfer backup mysql file to "database" linode**
sudo scp -i ~/.ssh/id_rsa_database /etc/mysql/backup/$(date +%Y)/$(date +%m)/$(date +%d)/backup.sql $user@$host:~/backup.sql
```

## 用备份文件还原数据库:

MySQL 程序能够从我们的备份文件中复制数据库中的结构和数据。这允许我们从头开始重新创建我们的数据库或覆盖我们现有的损坏的数据库。在本节中，我们将删除现有的数据库，重新创建它，并删除我们的备份文件。

```
**# open console as non-root user on "database" linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "database" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# open mysql**
sudo mysql -u root -p$root_password**# delete "website" database**
drop database website;**# create "website" database**
create database website;**# select "website" database**
use website;**# confirm data deleted**
select * from records;**# exit mysql**
exit**# store database name**
database="website"**# restore database from backup**
sudo mysql -u root -p$root_password $database < ~/backup.sql**# open mysql**
sudo mysql -u root -p$root_password**# select "website" database**
use website;**# confirm data restored**
select * from records;**# exit mysql**
exit**# remove backup mysql file**
sudo rm ~/backup.sql
```

## 后续步骤:

以下文章包含一个宝石本身，但一起宝藏。

```
**# How to Put a High Performance Website on the Internet:** 01\. [How to Host a Website in the Cloud](https://medium.com/p/915acad9c15f)
02\. [How to Protect a Website From Threats](https://medium.com/p/78ce938c7bd5/)
03\. [How to Optimize a Website to Load in 1 Second](https://medium.com/p/3d67f11018ec)
04\. [How to Add a Database to a Website](https://medium.com/p/8d36d1e77316)
05\. [How to Protect a Database from Threats](https://medium.com/p/7cbc15c714ae)
06\. [How to Backup a Database in the Cloud](https://medium.com/p/1192fc4629f2)
07\. [How to Scale a Website to Handle High Traffic](https://medium.com/p/382a8d960f77)
08\. [How to Add a Node.js Application to a Website](https://medium.com/p/57218f9dfaaa)
09\. How to Add a Python Application to a Website
10\. How to Add a Payment Method to a Website
```
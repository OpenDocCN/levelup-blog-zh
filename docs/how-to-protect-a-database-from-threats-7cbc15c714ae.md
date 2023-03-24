# 如何保护数据库免受威胁

> 原文：<https://levelup.gitconnected.com/how-to-protect-a-database-from-threats-7cbc15c714ae>

## WEB 开发:

## 包含逐步说明的完整解决方案

![](img/85e7b22c0456426525fbd95def35f9a6.png)

图片由[eth 消息](https://unsplash.com/photos/eNxYF6cexYU)

## 总结:

阅读本文的主要好处是，您将了解如何使用端到端加密来保护您的数据库。这包括应用程序级加密、SSL 加密数据库连接、远程访问和用户权限限制，以及许多强化数据库的安全相关选项。这将阻止大多数可以通过您的网站和数据库阻止的攻击。然而，这不能防止由糟糕的代码、弱密码和第三方插件引起的攻击。

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

1.  [创建用户账户](#f029)
2.  [禁用 Root 登录](#e6d6)
3.  [禁用密码认证](#5f58)
4.  [执行 MySQL 安全安装](#e0d0)
5.  [更改 MySQL 凭证的存储位置](#1904)
6.  [插入 MySQL 占位符](#4e53)
7.  [加密 MySQL 数据库连接](#30dd)
8.  [加密 MySQL 数据](#763d)
9.  [要求 MySQL Root 用户使用密码](#53ad)
10.  [限制](#c360) [MySQL](#53ad) [非 Root 用户权限](#c360)
11.  [限制 MySQL 失败的密码尝试](#6fe6)
12.  [更改 MySQL 默认端口](#75a7)
13.  [限制 MySQL 远程访问](#7974)
14.  [更改 MySQL 文件权限](#d555)
15.  [删除 MySQL 历史文件](#001d)
16.  [启用 MySQL 日志文件](#7e69)
17.  [加密 MySQL 二进制日志](#ef62)
18.  [禁用 MySQL 日志 Raw](#3fa4)
19.  [禁用 MySQL 符号链接](#07be)
20.  [禁用 MySQL 本地文件读取](#23c3)
21.  [禁用 MySQL 显示数据库](#7a82)
22.  [禁用 MySQL 代理用户](#7233)
23.  [限制 MySQL 加载目录](#48b0)

## 创建用户帐户:

用户帐户是可以访问文件和执行命令的实体。这使得攻击者更难侵入我们的网络服务器，因为他们需要先破解我们的用户名，然后才能破解我们的密码。在本节中，我们将创建一个具有管理权限的用户帐户。

```
**# open console as root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "database" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter "root" into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# change "placeholder" to unique username** username="placeholder"**# create new user on virtual machine** sudo adduser $username**# choose password for new user**
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

root 用户是一个用户帐户，可以无限制地访问每个基于 Linux 的操作系统预装的整个系统。这为攻击者提供了他们入侵我们的网络服务器所需的一半信息。在本节中，我们将通过更改 SSH 配置文件中的设置来防止 root 用户访问我们的虚拟机。

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

## 执行 MySQL 安全安装:

MySQL 安全安装是一个 shell 脚本，它实现了一些建议的安全设置。这允许我们启用密码验证、删除匿名用户、禁止远程 root 登录以及删除测试数据库。在本节中，我们将通过运行 shell 脚本来实现这一点。

```
**# run mysql security installation** sudo mysql_secure_installation utility**# require medium password strength**
1\. enter "yes" to enable password validation
2\. press "enter" key
3\. enter "1" to require medium strength password
4\. enter password
5\. re-enter password
6\. enter "yes" to continue with chosen password
7\. press "return" key**# harden mysql security**
1\. enter "yes" to remove anonymous users
2\. press "enter" key
3\. enter "yes" to disallow root login remotely
4\. press "enter" key
5\. enter "yes" to remove test database
6\. press "enter" key
7\. enter "yes" to reload privilege tables
8\. press "enter" key
```

## 更改 MySQL 凭证存储位置:

我们用来连接 MySQL 数据库的敏感信息目前存储在我们网站目录的源代码中。由于某些 web 服务器和人为错误，这可能会将我们的敏感信息暴露在互联网上。在本节中，我们将通过将我们的敏感信息存储在网站目录之外的受保护文件中来防止这种情况。

```
**# get ip address of "database" linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "database" linode
3\. find ip address under "ip addresses" label
4\. write down ip address**# open console as non-root user** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "website-1" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# download credentials php file**
sudo curl -o /etc/mysql/credentials.php [https://gist.githubusercontent.com/david-littlefield/141c97722ba5bd07cd87c8cdadcc3db6/raw](https://gist.githubusercontent.com/david-littlefield/141c97722ba5bd07cd87c8cdadcc3db6/raw)**# store database name**
database="website"**# change "placeholder" to ip address of "database" linode**
host="placeholder"**# store default port**
port="3306"**# change "placeholder" to existing mysql non-root user**
user="placeholder"**# store mysql non-root password**
password="StrongPassword1234!"**# add database to credentials php file**
sudo sed "s|#database_placeholder#|$database|g" -i /etc/mysql/credentials.php**# add host to credentials php file**
sudo sed "s|#host_placeholder#|$host|g" -i /etc/mysql/credentials.php**# add port to credentials php file**
sudo sed "s|#port_placeholder#|$port|g" -i /etc/mysql/credentials.php**# add user to credentials php file**
sudo sed "s|#user_placeholder#|$user|g" -i /etc/mysql/credentials.php**# add password to credentials php file**
sudo sed "s|#password_placeholder#|$password|g" -i /etc/mysql/credentials.php
```

```
**# replace existing configuration php file**
sudo curl -o /var/www/html/includes/configuration.php [https://gist.githubusercontent.com/david-littlefield/1a530aac25445c7ffd3fae6bbb517dfb/raw](https://gist.githubusercontent.com/david-littlefield/1a530aac25445c7ffd3fae6bbb517dfb/raw)
```

## 插入 MySQL 占位符:

占位符指的是临时填充我们的安全相关选项的配置文件空间的文本。这使我们能够轻松地用适当的选项替换占位符。在本节中，我们将通过在配置文件中添加占位符来实现这一点。

```
**# store placeholders**
placeholders=$(curl [https://gist.githubusercontent.com/david-littlefield/643a2e52b410d51f3eb621e73051e6b6/raw](https://gist.githubusercontent.com/david-littlefield/643a2e52b410d51f3eb621e73051e6b6/raw))**# add placeholders to configuration file**
sudo sed "s|\[mysqld\]|$placeholders|g" -i /etc/mysql/mysql.conf.d/mysqld.cnf
```

## 加密 MySQL 数据库连接:

默认情况下，数据库连接不加密。这使得能够访问同一网络的攻击者能够检查我们的网站和数据库之间发送和接收的数据。在本节中，我们将通过向我们的网站和数据库配置文件添加几个选项来防止这种情况。

```
**# open console as root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "database" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter "root" into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# download mysql file**
sudo curl -o /etc/mysql/ssl.sql [https://gist.githubusercontent.com/david-littlefield/eefbaac2cdc56d4ed5e6a73731182d9c/raw](https://gist.githubusercontent.com/david-littlefield/eefbaac2cdc56d4ed5e6a73731182d9c/raw)**# change "placeholder" to existing mysql non-root user**
user="placeholder"**# store mysql non-root password**
password="StrongPassword1234!"**# add user to ssl mysql file**
sudo sed "s|#user_placeholder#|$user|g" -i /etc/mysql/ssl.sql**# add password to ssl mysql file**
sudo sed "s|#password_placeholder#|$password|g" -i /etc/mysql/ssl.sql**# open mysql**
sudo mysql -u root -p$root_password**# run ssl mysql file**
source /etc/mysql/ssl.sql**# confirm ssl status not in use**
\s**# exit mysql**
exit**# remove ssl mysql file**
sudo rm /etc/mysql/ssl.sql
```

```
**# create ssl certificate and key files**
sudo mysql_ssl_rsa_setup --uid=mysql**# store ssl encryption**
ssl_encryption=$(curl [https://gist.githubusercontent.com/david-littlefield/5cdf54e868b8e9fadc9e98806738fa9e/raw)](https://gist.githubusercontent.com/david-littlefield/5cdf54e868b8e9fadc9e98806738fa9e/raw))**# add ssl encryption to mysql configuration file** sudo sed "s|#ssl_encryption_placeholder#|$ssl_encryption|g" -i /etc/mysql/mysql.conf.d/mysqld.cnf**# restart mysql**
sudo systemctl restart mysql
```

```
**# open console as non-root user** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "website-1" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# store pdo options**
pdo_options=$(curl [https://gist.githubusercontent.com/david-littlefield/31c756ed62f435ebdef71ac542a93436/raw](https://gist.githubusercontent.com/david-littlefield/31c756ed62f435ebdef71ac542a93436/raw))**# add ssl pdo options to configuration php file**
sudo sed "s|#pdo_options_placeholder#|$pdo_options|g" -i /var/www/html/includes/configuration.php**# change "placeholder" to mysql non-root user** user="placeholder"**# store mysql non-root password** password="StrongPassword1234!"**# change "placeholder" to ip address of "database" linode**
host="placeholder"**# fail to connect to mysql without ssl encryption**
sudo mysql -u $user -p$password -h $host –ssl-mode=disabled**# connect to mysql with ssl encryption**
sudo mysql -u $user -p$password -h $host**# confirm ssl status cipher in use**
\s**# exit mysql**
exit
```

## 加密 MySQL 数据:

应用程序级加密是一个数据安全过程，它对应用程序中的数据进行加密。这提供了针对风险的保护，例如物理磁盘访问、数据库管理员、数据泄漏以及应用程序不同部分之间的数据传输。在本节中，我们将通过向我们的凭证文件添加一个加密密钥，以及向处理数据的 php 文件添加加密函数来实现这一点。

*   它允许我们只加密需要保护的数据

```
**# open console as non-root user** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "website-1" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# store encryption key**
key=$(openssl rand -hex 24)**# add encryption key to php file**
sudo sed "s|#key_placeholder#|$key|g" -i /etc/mysql/credentials.php
```

```
**# encrypt the "location" text**
sudo sed "s|\$_POST\[\"location_input\"\]|Encryption::encrypt(\$_POST\[\"location_input\"\])|g" -i /var/www/html/process.php**# encrypt the "description" text**
sudo sed "s|\$_POST\[\"description_input\"\]|Encryption::encrypt(\$_POST\[\"description_input\"\])|g" -i /var/www/html/process.php**# restart nginx**
sudo systemctl restart nginx
```

```
**# decrypt the "location" text in "form_provider" php file**
sudo sed "s|\$record -> get_location()|Encryption::decrypt(\$record -> get_location())|g" -i /var/www/html/includes/classes/Form_Provider.php**# decrypt the "description" text in "form_provider" php file**
sudo sed "s|\$record -> get_description()|Encryption::decrypt(\$record -> get_description())|g" -i /var/www/html/includes/classes/Form_Provider.php**# restart nginx**
sudo systemctl restart nginx
```

```
**# decrypt the "location" text in "grid_item" php file**
sudo sed "s|\$this -> record -> get_location()|Encryption::decrypt(\$this -> record -> get_location())|g" -i /var/www/html/includes/classes/Grid_Item.php**# decrypt the "description" text in "grid_item" php file**
sudo sed "s|\$this -> record -> get_description()|Encryption::decrypt(\$this -> record -> get_description())|g" -i /var/www/html/includes/classes/Grid_Item.php**# restart nginx**
sudo systemctl restart nginx
```

```
**# open console as root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "database" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter "root" into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# open mysql**
sudo mysql**# select database**
use website**# check encrypted data**
select * from records order by id desc limit 1;**# exit mysql**
exit
```

## 要求 MySQL Root 用户使用密码:

Root 用户是 MySQL 自带的一个拥有无限特权的用户帐户，登录我们的数据库不需要密码。这使得攻击者可以毫不费力地访问我们的数据库。在本节中，我们将通过在 MySQL 中运行几个带有几条语句的查询来防止这种情况。

```
**# open console as root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "database" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter "root" into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# download root password mysql file**
sudo curl -o /etc/mysql/root_password.sql [https://gist.githubusercontent.com/david-littlefield/ddcc2aba08e9acd1153e4230be563a84/raw](https://gist.githubusercontent.com/david-littlefield/ddcc2aba08e9acd1153e4230be563a84/raw)**# change "placeholder" to strong password**
root_password="placeholder"**# add root password to mysql file**
sudo sed "s|#password_placeholder#|$root_password|g" -i /etc/mysql/root_password.sql**# open mysql**
sudo mysql**# run mysql file**
source /etc/mysql/root_password.sql**# exit mysql**
exit**# remove root password mysql file**
sudo rm /etc/mysql/root_password.sql
```

## 限制 MySQL 非根用户权限:

C.R.U.D .用户是一个用户帐户，具有创建、读取、更新和删除记录的有限能力，可以访问单个数据库。这减少了用户帐户受损造成的损失。在本节中，我们将通过运行 MySQL 文件来实现这一点，该文件撤销了非 root 用户的 root 权限，只授予了四个基本权限。

```
**# download crud mysql file**
sudo curl -o /etc/mysql/crud.sql [https://gist.githubusercontent.com/david-littlefield/bcbb3f2761c470bf016e7dd7b0e92f9b/raw](https://gist.githubusercontent.com/david-littlefield/bcbb3f2761c470bf016e7dd7b0e92f9b/raw)**# store database name**
database="website"**# change "placeholder" to existing mysql non-root user** user="placeholder"**# add database to crud mysql file** sudo sed "s|#database_placeholder#|$database|g" -i /etc/mysql/crud.sql**# add user to mysql file** sudo sed "s|#user_placeholder#|$user|g" -i /etc/mysql/crud.sql**# open mysql**
sudo mysql -u root -p$root_password**# run crud mysql file**
source /etc/mysql/crud.sql**# exit mysql**
exit**# remove crud mysql file**
sudo rm /etc/mysql/crud.sql
```

## 限制 MySQL 失败的密码尝试次数:

FAILED_LOGIN_ATTEMPTS 和 PASSWORD_LOCK_TIME 选项是定义在帐户被锁定之前可以对帐户进行的连续不正确密码尝试的次数以及帐户将保持锁定的天数的设置。这可以防止攻击者对我们的数据库进行暴力攻击。在本节中，我们将通过用 MySQL 中的几条语句运行一个查询来实现这一点。

```
**# download failed password mysql file**
sudo curl -o /etc/mysql/failed_password.sql [https://gist.githubusercontent.com/david-littlefield/d75f4f97983e4bfc889ae2fa08acbf26/raw](https://gist.githubusercontent.com/david-littlefield/d75f4f97983e4bfc889ae2fa08acbf26/raw)**# change "placeholder" to desired number of login attempts**
login_attempts="placeholder"**# change "placeholder" to number of days to lock account** lock_time="placeholder"**# add user to mysql file** sudo sed "s|#user_placeholder#|$user|g" -i /etc/mysql/failed_password.sql**# add login attempts to mysql file**
sudo sed "s|#login_attempts_placeholder#|$login_attempts|g" -i /etc/mysql/failed_password.sql**# add lock time to mysql file**
sudo sed "s|#lock_time_placeholder#|$lock_time|g" -i /etc/mysql/failed_password.sql**# open mysql**
sudo mysql -u root -p$root_password**# run crud mysql file**
source /etc/mysql/failed_password.sql**# exit mysql**
exit**# remove crud mysql file**
sudo rm /etc/mysql/failed_password.sql
```

## 更改 MySQL 默认端口:

默认端口是 MySQL 用来处理传入和传出网络请求的端口号。这是攻击者用来攻击我们数据库的第一个端口号。在本节中，我们将通过在配置文件中添加一个选项以及在防火墙中添加一个新规则来防止这种情况。

```
**# download port configuration file**
sudo curl -o /etc/mysql/port.cnf [https://gist.githubusercontent.com/david-littlefield/9e0d6961462cd5e0ecfba19caa1ca70e/raw](https://gist.githubusercontent.com/david-littlefield/9e0d6961462cd5e0ecfba19caa1ca70e/raw)**# store port number** port_number="5000"**# add port to firewall rules**
sudo ufw allow $port_number**# add port to configuration file**
sudo sed "s|#port_placeholder#|$port_number|g" -i /etc/mysql/port.cnf**# store port**
port=$(cat /etc/mysql/port.cnf)**# add port to mysql configuration file**
sudo sed "s|#port_placeholder#|$port|g" -i /etc/mysql/mysql.conf.d/mysqld.cnf**# remove port configuration file**
sudo rm /etc/mysql/port.cnf**# restart mysql**
sudo systemctl restart mysql
```

```
**# open console as non-root user** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "website-1" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# store port number** port_number="5000"**# add port to credentials file**
sudo sed "s|3306|$port_number|g" -i /etc/mysql/credentials.php
```

## 限制 MySQL 远程访问:

远程访问是一项服务，通过公开我们的数据库，允许我们的 web 服务器从不同的地理位置连接到我们的数据库。这为攻击者提供了访问我们数据库的机会。在本节中，我们将通过为 web 服务器的 IP 地址创建防火墙规则并阻止所有其他 IP 地址来防止这种情况。

```
**# get ip address of "website-1" linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "website-1" linode
3\. find ip address under "ip addresses" label
4\. write down ip address**# open console as non-root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "database" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# switch to root user**
su root**# delete previous firewall rule** sudo ufw delete allow mysql**# change "placeholder" to ip address of "website-1" linode**
ip_address="placeholder"**# store port**
port="5000"**# create firewall rule for ip address in text file**
ufw allow from $ip_address to any port $port**# switch back to non-root user**
exit
```

## 更改 MySQL 文件权限:

文件和目录权限是一项 Linux 功能，它允许我们控制谁可以读取、写入和执行系统中的文件。这可以为攻击者提供对受限文件的访问权限，攻击者可以在其中修改或删除其内容。在本节中，我们将通过确保为所有 MySQL 文件和目录设置适当的权限来防止这种情况。

```
**# set mysql "etc" directory permissions** sudo find /etc/mysql/ -type d -exec chmod 755 {} \;**# set mysql "etc" file permissions**
sudo find /etc/mysql/ -type f -exec chmod 644 {} \;**# set mysql "var" directory permissions**
sudo find /var/lib/mysql/ -type d -exec chmod 755 {} \;**# set mysql "var" file permissions**
sudo find /var/lib/mysql/ -type f -exec chmod 644 {} \;
```

## 删除 MySQL 历史文件:

历史文件是一个记录在 MySQL 中交互执行的语句的文件。这可能使攻击者能够访问关于数据库名称、用户、密码和权限的信息。在本节中，我们将通过忽略写入其中的数据来防止这种情况。

```
**# remove history file**
sudo rm ~/.mysql_history**# create symbolic link that ignores data written to history file**
sudo ln -s /dev/null ~/.mysql_history
```

## 启用 MySQL 日志文件:

日志文件是一个记录 MySQL 中发生的事件和操作的信息的文件。这使我们能够监控性能、诊断和调试应用程序，并调查可能导致恶意活动的事件。在本节中，我们将通过向配置文件添加多个选项来跟踪四种不同类型的日志。

*   它交换重要信息，以实现性能的小幅下降

```
**# store logs** logs=$(curl [https://gist.githubusercontent.com/david-littlefield/760a861de62792e6eb8b0ce7d01cc010/raw](https://gist.githubusercontent.com/david-littlefield/760a861de62792e6eb8b0ce7d01cc010/raw)[)](/))**# add logs to configuration file** sudo sed "s|#logs_placeholder#|$logs|g" -i /etc/mysql/mysql.conf.d/mysqld.cnf**# restart mysql**
sudo systemctl restart mysql
```

## 加密 MySQL 二进制日志:

二进制日志是一个未加密的文件，其中包含对我们的数据库所做的所有更改，因此可以对其进行备份和复制。这为攻击者提供了足够的信息来重新创建我们的数据库。在本节中，我们将通过在配置文件中添加一个选项来防止这种情况。

```
**# store binary log** 
binary_log=$(curl [https://gist.githubusercontent.com/david-littlefield/d27563a343d80c36d857bb8c4d9e7d39/raw)](https://gist.githubusercontent.com/david-littlefield/d27563a343d80c36d857bb8c4d9e7d39/raw))**# add binary log to configuration file**
sudo sed "s|#binary_log_placeholder#|$binary_log|g" -i /etc/mysql/mysql.conf.d/mysqld.cnf**# restart mysql**
sudo systemctl restart mysql
```

## 禁用 MySQL 原始日志:

Log Raw 选项是一种在 SQL 语句中以纯文本形式写入密码的设置，这些语句记录在我们的常规、慢速和二进制日志中。这使攻击者能够访问我们日志文件中的密码。在本节中，我们将通过在配置文件中添加一个选项来防止这种情况。

```
**# store log raw** log_raw=$(curl [https://gist.githubusercontent.com/david-littlefield/402b19e7e73c4a8eb7c92459229de47f/raw)](https://gist.githubusercontent.com/david-littlefield/402b19e7e73c4a8eb7c92459229de47f/raw))**# add log raw to configuration file**
sudo sed "s|#log_raw_placeholder#|$log_raw|g" -i /etc/mysql/mysql.conf.d/mysqld.cnf**# restart mysql**
sudo systemctl restart mysql
```

## 禁用 MySQL 符号链接:

符号链接在 MySQL 中用于将数据库存储在默认位置之外的不同目录中。这是一个漏洞，因为任何对我们的数据目录具有写权限的人都可以删除我们系统中的文件。在本节中，我们将通过在配置文件中添加一个选项来防止这种情况。

```
symbolic_links=$(curl [https://gist.githubusercontent.com/david-littlefield/bc635e36c03aa44de1053e9e85297795/raw](https://gist.githubusercontent.com/david-littlefield/bc635e36c03aa44de1053e9e85297795/raw))sudo sed "s|#symbolic_links_placeholder#|$symbolic_links|g" -i /etc/mysql/mysql.conf.d/mysqld.cnf**# restart mysql**
sudo systemctl restart mysql
```

## 禁用 MySQL 本地文件读取:

LOAD DATA INFILE 语句是一条 SQL 语句，允许用户读取系统上的文件。这使得攻击者能够在不拥有适当文件权限的情况下访问包含敏感信息的文件。在本节中，我们将通过在配置文件中添加一个选项来防止这种情况。

```
**# store local infile**
local_infile=$(curl [https://gist.githubusercontent.com/david-littlefield/810a43a6a0d0cf363478374d2c75bffa/raw](https://gist.githubusercontent.com/david-littlefield/810a43a6a0d0cf363478374d2c75bffa/raw))**# add local infile to configuration file**
sudo sed "s|#local_infile_placeholder#|$local_infile|g" -i /etc/mysql/mysql.conf.d/mysqld.cnf**# restart mysql**
sudo systemctl restart mysql
```

## 禁用 MySQL 显示数据库:

SHOW DATABASES 语句是一个 SQL 语句，它允许每个用户在 MySQL 中看到我们的数据库的名称。这为攻击者提供了攻击我们的数据库所需的部分信息。在本节中，我们将通过在配置文件中添加一个选项来防止这种情况。

```
**# store show database** show_databases=$(curl [https://gist.githubusercontent.com/david-littlefield/ee208865d0d7ee7bb3b84e9b5a927eae/raw)](https://gist.githubusercontent.com/david-littlefield/ee208865d0d7ee7bb3b84e9b5a927eae/raw))**# add show databases to configuration file** sudo sed "s|#show_databases_placeholder#|$show_databases|g" -i /etc/mysql/mysql.conf.d/mysqld.cnf**# restart mysql**
sudo systemctl restart mysql
```

## 禁用 MySQL 代理用户:

代理用户是一个临时用户帐户，它模拟实际用户帐户的权限，允许外部用户访问我们的数据库，而无需创建新帐户。这使得攻击者有可能以特权用户的身份未经授权访问我们的数据库。在本节中，我们将通过在配置文件中添加几个选项来防止这种情况。

```
**# store proxy users**
proxy_users=$(curl [https://gist.githubusercontent.com/david-littlefield/f997c6826b5ab166b61db4137caeacac/raw)](https://gist.githubusercontent.com/david-littlefield/f997c6826b5ab166b61db4137caeacac/raw))**# add proxy users to configuration file**
sudo sed "s|#proxy_users_placeholder#|$proxy_users|g" -i /etc/mysql/mysql.conf.d/mysqld.cnf**# restart mysql**
sudo systemctl restart mysql
```

## 限制 MySQL 加载目录:

LOAD_DATA 语句是一个 SQL 语句，它将数据从一个文件加载到数据库的一个表中。这使得攻击者能够从管理我们数据库的用户可以访问的任何文件中加载敏感数据。在本节中，我们将通过在配置文件中添加一个选项来防止这种情况。

*   它只允许特权用户从指定目录加载文件

```
**# create load directory**
sudo mkdir -p /etc/mysql/load/**# store load directory** load_directory=$(curl [https://gist.githubusercontent.com/david-littlefield/3feac17b71c5f19565b932afade2a933/raw](https://gist.githubusercontent.com/david-littlefield/3feac17b71c5f19565b932afade2a933/raw))**add load directory to mysql configuration file** sudo sed "s|#load_directory_placeholder#|$load_directory|g" -i /etc/mysql/mysql.conf.d/mysqld.cnf**# restart mysql**
sudo systemctl restart mysql
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
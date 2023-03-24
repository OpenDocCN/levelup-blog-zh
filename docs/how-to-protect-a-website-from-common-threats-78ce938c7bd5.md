# 如何保护网站免受威胁

> 原文：<https://levelup.gitconnected.com/how-to-protect-a-website-from-common-threats-78ce938c7bd5>

## WEB 开发:

## 包含逐步说明的完整解决方案

![](img/8b2b15e0325d73f34301b798d925205d.png)

图片由 [Jornada Produtora](https://unsplash.com/photos/q2Zk10NBnsA)

## 总结:

阅读本文的主要好处是，您将学习如何使用商业级加密，防止敏感信息泄露，以及保护您的客户、网站和 web 服务器免受最常见的网络攻击。这将阻止 web 服务器可以阻止的所有类型的攻击。然而，这不能防止由我们自己编写的糟糕代码、弱密码和第三方插件引起的攻击。

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

1.  [创建用户账户](#70dd)
2.  [SSH 进入虚拟机(Linux)](#0559)
3.  [SSH 进入虚拟机(Windows)](#99ae)
4.  [SSH 进入虚拟机(Mac)](#8ad7)
5.  [安装 SSL 证书](#f5a6)
6.  [更新 SSL 证书](#cd28)
7.  [禁用 Root 登录](#f10b)
8.  [禁用密码认证](#3036)
9.  [防止分布式拒绝服务攻击](#9de5)
10.  [防止版本泄露漏洞](#3b13)
11.  [防止目录列表漏洞](#dbb9)
12.  [防止跨站脚本攻击](#be71)
13.  [防止点击劫持攻击](#7e86)
14.  [防止内容嗅探攻击](#e6e4)
15.  [防止图片热链接攻击](#abb4)

## 创建用户帐户:

用户帐户是可以访问文件和执行命令的实体。这使得攻击者更难侵入我们的网络服务器，因为他们需要先破解我们的用户名，然后才能破解我们的密码。在本节中，我们将创建一个具有管理权限的用户帐户。

```
**# locate ip address to website**
1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "website-1" linode
3\. find ip address under "ip addresses" label
4\. write down ip address**# open console as root user** 1\. click "launch lish console" link
2\. press "return" key
3\. enter "root" into "login" prompt
4\. enter your password into "password" prompt
5\. press "return" key
6\. paste commands into console**# change "placeholder" to unique username** username="placeholder"**# create new user on virtual machine** sudo adduser $username**# choose password for new user**
1\. enter password for user account
2\. press "return"
3\. re-enter password
4\. press "return"
5\. press "return" to enter the default value
6\. enter "y" to confirm information
7\. press "return"**# add admin group to sudo file**
sudo groupadd admin**# grant administrative permissions to new user**
sudo usermod -aG admin $username**# switch to new user**
sudo su $username**# create ssh directory**
sudo mkdir -p ~/.ssh**# change "placeholder" to unique username from earlier** username="placeholder"**# assign ownership of .ssh directory to new user** sudo chown -R $username: /home/$username/.ssh**# assign ownership of nginx directory to new user** sudo chown $username: /etc/nginx/nginx.conf**# restart ssh**
sudo service ssh restart**# switch back to root user**
exit**# exit console** exit
```

## SSH 到虚拟机(Linux):

SSH 协议是一种鉴定方法，用于在两台电脑之间远程建立安全连接。这将允许您从您的计算机连接到我们的虚拟机，而无需输入密码。在本节中，我们将创建我们的 SSH 密钥，将我们的私钥存储在您的计算机上，并将我们的公钥存储在我们的虚拟机上。

```
**# open terminal** 1\. click “activities” in top-left corner
2\. enter “terminal” into search bar
3\. click “terminal”
4\. paste commands into terminal**# install openssh in terminal**
sudo apt-get install openssh-server**# enable ssh in terminal** sudo systemctl enable ssh**# start ssh in terminal**
sudo systemctl start ssh**# create ssh directory in terminal** sudo mkdir -p ~/.ssh**# create ssh key in terminal**
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa_linode**# create ssh key without a passphrase in terminal**
1\. press "return" to use no passphrase
2\. press "return" to confirm**# change "placeholder" to ip address from earlier in terminal** ip_address="placeholder"**# change "placeholder" to unique username from earlier** username="placeholder"**# add ip address to ssh configuration file in terminal**
echo -e "\nhost website\n    hostname $ip_address\n    identityfile ~/.ssh/id_rsa_linode\n" >> ~/.ssh/config**# add ssh key to "authorized_keys" list in terminal**
sudo cat ~/.ssh/id_rsa_linode.pub | ssh $username@website "cat >> ~/.ssh/authorized_keys"**# add virtual machine to "known hosts" list** 1\. enter "yes" to continue
2\. press "return" key
3\. enter password for virtual machine
4\. press "return" key**# connect to virtual machine** ssh $username@website**# change text size in bash configuration file**
sudo echo "stty cols 177 rows 21" >> ~/.bashrc**# reload bash configuration file**
source ~/.bashrc
```

## SSH 进入虚拟机(Windows):

SSH 协议是一种鉴定方法，用于在两台电脑之间远程建立安全连接。这将允许您从您的计算机连接到我们的虚拟机，而无需输入密码。在本节中，我们将创建我们的 SSH 密钥，将我们的私钥存储在您的计算机上，并将我们的公钥存储在我们的虚拟机上。

```
**# open powershell**
1\. press “⊞ windows” key
2\. enter “powershell” into search bar
3\. click “run as administrator” option
4\. paste commands into powershell**# create putty directory in powershell** mkdir $home\putty -force**# download putty in powershell** invoke-webrequest -uri [https://the.earth.li/~sgtatham/putty/latest/w64/putty.exe](https://the.earth.li/~sgtatham/putty/latest/w64/putty.exe) -outfile $home\putty\putty.exe**# download puttygen in powershell** invoke-webrequest -uri [https://the.earth.li/~sgtatham/putty/latest/w64/puttygen.exe](https://the.earth.li/~sgtatham/putty/latest/w64/puttygen.exe) -outfile $home\putty\puttygen.exe**# download pscp in powershell**
invoke-webrequest -uri [https://the.earth.li/~sgtatham/putty/latest/w64/pscp.exe](https://the.earth.li/~sgtatham/putty/latest/w64/pscp.exe) -outfile $home\putty\pscp.exe**# open puttygen in powershell** invoke-item $home\putty\puttygen.exe**# create public key in putty**
1\. click "generate" button
2\. move mouse around in empty space under progress bar
3\. click "save public key" button
4\. navigate to desktop directory
5\. enter "public_key.txt" into "file name" text field
6\. click "save" button**# create private key in putty** 1\. click "save private key" button
2\. click "yes" to save without a passphrase
3\. enter "private_key.ppk" into "file name" text field
4\. click "save" button**# move public key to putty directory in powershell** move-item -path $home\desktop\public_key.txt -destination $home\putty\public_key.txt**# move private key to putty directory in powershell** move-item -path $home\desktop\private_key.ppk -destination $home\putty\private_key.ppk**# change "placeholder" to ip address from earlier in powershell** $ip_address="placeholder"**# change "placeholder" to unique username from earlier in powershell** $username="placeholder"**# change "placeholder" to password from earlier in powershell**
$password="placeholder"**# copy public key to virtual machine in powershell**
& $home\putty\pscp.exe -pw $password $home\putty\public_key.txt $username@$ip_address`:/home/$username/.ssh**# open putty in powershell**
invoke-item $home\putty\putty.exe**# load private key in putty**
1\. double-click "ssh" subcategory in "connection" category
2\. click "auth" subcategory in "ssh" subcategory
3\. click "browse" button
4\. navigate to "putty" directory (c:\users\username\putty\)
5\. click "private_key.ppk" file
6\. click "open" button**# save settings in putty** 1\. click "data" subcategory in "connection" category
2\. enter username from earlier into "auto-login username" text field
3\. click "session" category
4\. enter ip address from earlier into "host name" text field
5\. enter "website" into "saved session" text field
6\. click "save" button**# connect to virtual machine in putty** 1\. click "open" button
2\. click "accept" button
3\. enter password from earlier
4\. press "return" key**# install dos2unix in putty**
sudo apt-get install --yes dos2unix**# convert text file format to unix in putty** sudo dos2unix ~/.ssh/public_key.txt**# add ssh key to "authorized_keys" list in putty**
sudo ssh-keygen -i -f ~/.ssh/public_key.txt >> ~/.ssh/authorized_keys**# exit putty**
exit**# reopen putty**
1\. press “⊞ windows” key
2\. enter “putty.exe” into search bar
3\. click "putty.exe" result**# connect to virtual machine in putty** 1\. click "website" in "saved sessions" section
2\. click "load button
3\. click "open" button"**# change text size in bash configuration file**
sudo echo "stty cols 177 rows 21" >> ~/.bashrc**# reload bash configuration file**
source ~/.bashrc
```

## SSH 到虚拟机(Mac):

SSH 协议是一种鉴定方法，用于在两台电脑之间远程建立安全连接。这将允许您从您的计算机连接到我们的虚拟机，而无需输入密码。在本节中，我们将创建我们的 SSH 密钥，将我们的私钥存储在您的计算机上，并将我们的公钥存储在我们的虚拟机上。

```
**# open terminal** 1\. press “command ⌘ + spacebar” keys
2\. enter “terminal” into search bar
3\. press “return” key
4\. paste commands into terminal**# create ssh key in terminal**
ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa_linode**# create ssh key without a passphrase in terminal**
1\. press "return" to use no passphrase
2\. press "return" to confirm**# change "placeholder" to ip address from earlier in terminal** ip_address="placeholder"**# change "placeholder" to unique username from earlier** username="placeholder"**# add ip address to ssh configuration file in terminal**
echo -e "\nhost website\n    hostname $ip_address\n    identityfile ~/.ssh/id_rsa_linode\n" >> ~/.ssh/config**# add ssh key to "authorized_keys" list in terminal**
sudo cat ~/.ssh/id_rsa_linode.pub | ssh $username@website "cat >> ~/.ssh/authorized_keys"**# add virtual machine to "known hosts" list** 1\. enter "yes" into terminal
2\. press "return" key
3\. enter password for virtual machine
4\. press "return" key**# connect to virtual machine in terminal**
ssh $username@website**# change text size in bash configuration file**
sudo echo "stty cols 177 rows 21" >> ~/.bashrc**# reload bash configuration file**
source ~/.bashrc
```

## 安装 SSL 证书:

SSL 协议是一种身份验证方法，用于通过 web 浏览器在我们的 web 服务器和我们的用户之间建立安全连接。这将允许我们的网站显示锁图标和 HTTPS 计划与您的域名。在本节中，我们将创建 SSL 证书，下载配置文件，并定制 web 服务器。

```
**# reopen website-1 linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "website-1" linode**# open console as new user** 1\. click "launch lish console" link
2\. press "return" key
3\. enter unique username from earlier into "login" prompt
4\. enter your password into "password" prompt
5\. press "return" key
6\. paste commands into console**# change "placeholder.com" to your domain name**
domain_name="placeholder.com"**# change "placeholder" to email address** email="placeholder"**# install certbot**
sudo apt-get install --yes certbot python3-certbot-nginx**# create ssl certificate**
sudo certbot certonly --webroot -w /var/www/html -d $domain_name -d [www.$domain_name](http://www.$domain_name) --non-interactive --agree-tos --email $email**# download nginx configuration file**
sudo curl -o /etc/nginx/nginx.conf [https://gist.githubusercontent.com/david-littlefield/59ddd7b03c22c759ebb95b1fcf923b32/raw](https://gist.githubusercontent.com/david-littlefield/59ddd7b03c22c759ebb95b1fcf923b32/raw)**# store number of worker connections** worker_connections=$(ulimit -n)**# add number of worker connections to configuration file**
sudo sed "s|#worker_connections_placeholder#|$worker_connections|g" -i /etc/nginx/nginx.conf**# add domain name to configuration file** sudo sudo sed "s|#domain_name_placeholder#|$domain_name|g" -i /etc/nginx/nginx.conf**# store php-fpm socket** php_fpm_socket=$(sudo find / -name *[0-9][\-]fpm.sock)**# add "php-fpm socket" path to configuration file**
sudo sed "s|#php_fpm_socket_placeholder#|$php_fpm_socket|g" -i /etc/nginx/nginx.conf**# create dh parameters** sudo openssl dhparam -out /etc/nginx/dhparam.pem 2048**# restart nginx**
sudo systemctl restart nginx**# evaluate ssl certificate strength with ssl server test** 1\. open [ssl server test](https://www.ssllabs.com/ssltest/index.html)
2\. enter your domain name into "hostname" text field
3\. click "submit" button
4\. review "overall rating" score
```

## 续订 SSL 证书:

Lets Encrypt 是一个证书颁发机构，提供所有主流 web 浏览器都信任的免费 SSL 证书，有效期为 90 天。这将允许我们的网站被看到，而不会被网页浏览器的警告所阻止。在本节中，我们将通过下载我们的续订脚本并安排它每月运行一次来自动续订我们的 SSL 证书。

```
**# download ssl renewal bash file**
sudo curl -o /etc/nginx/letsencrypt_ssl_renewal.sh [https://gist.githubusercontent.com/david-littlefield/e9120ee22b33d5f99a0ece9274ff3434/raw](https://gist.githubusercontent.com/david-littlefield/e9120ee22b33d5f99a0ece9274ff3434/raw)
```

```
**# download certbot cron file**
sudo curl -o ~/certbot [https://gist.githubusercontent.com/david-littlefield/6114ba5e160b1bd3dca38fdd2101e991/raw](https://gist.githubusercontent.com/david-littlefield/6114ba5e160b1bd3dca38fdd2101e991/raw)**# move certbot cron file to cron.d directory** sudo mv ~/certbot /etc/cron.d/certbot
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

## 防止分布式拒绝服务攻击:

分布式拒绝服务攻击试图使我们网站上的传入网络流量过载，以降低我们网站服务器的速度或使其崩溃。这可以通过限制一个 ip 地址在给定时间内可以向我们的 web 服务器发出的请求数量来防止。在本节中，我们将在配置文件的“http”块中添加“limit_req_zone”指令，在“location”块中添加“limit_req”指令。

```
**# store distributed denial-of-service 1** ddos_1=$(curl [https://gist.githubusercontent.com/david-littlefield/66396a7328549591b03c0b779e6ddd3c/raw)](https://gist.githubusercontent.com/david-littlefield/66396a7328549591b03c0b779e6ddd3c/raw))**# add "limit_req_zone" directive to configuration file** sudo sed "s|#distributed_denial_of_service_1_placeholder#|$ddos_1|g" -i /etc/nginx/nginx.conf
```

```
**# store distributed denial-of-service 2** ddos_2=$(curl [https://gist.githubusercontent.com/david-littlefield/8318ea096d2906c4cf0b4a26235ae51d/raw)](https://gist.githubusercontent.com/david-littlefield/8318ea096d2906c4cf0b4a26235ae51d/raw))**# add "limit_req" directive to configuration file** sudo sed "s|#distributed_denial_of_service_2_placeholder#|$ddos_2|g" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
```

## 防止版本泄露漏洞:

版本泄露漏洞是一种敏感信息泄漏，可用于识别我们 web 服务器当前版本中的漏洞。这可以通过在错误页面和 HTTP 头中隐藏版本号和操作系统来避免。在本节中，我们将在配置文件的“http”块中添加“server_tokens”指令。

```
**# store version disclosure** version_disclosure=$(curl [https://gist.githubusercontent.com/david-littlefield/bcd43f1554118b4081c9146fe37f7612/raw)](https://gist.githubusercontent.com/david-littlefield/bcd43f1554118b4081c9146fe37f7612/raw))**# add "server_tokens" directive to configuration file** sudo sed "s|#version_disclosure_placeholder#|$version_disclosure|g" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
```

## 防止目录列表漏洞:

目录列表漏洞是一种敏感信息泄漏，它会泄露我们的 web 服务器上没有索引文件的目录的内容。这可以通过禁用显示没有索引文件的目录内容的功能来防止。在本节中，我们将在配置文件的“http”块中添加“autoindex”指令。

```
**# store directory listing** directory_listing=$(curl [https://gist.githubusercontent.com/david-littlefield/84cd612cf08c74a04158568053f9ee16/raw)](https://gist.githubusercontent.com/david-littlefield/84cd612cf08c74a04158568053f9ee16/raw))**# add "autoindex" directive to configuration file** sudo sed "s|#directory_listing_placeholder#|$directory_listing|g" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
```

## 防止跨站点脚本攻击:

跨站点脚本攻击是试图将恶意脚本注入我们的网站，从我们的用户那里提取敏感信息。这可以通过在我们的 HTTP 响应头中添加一个头来防止，当检测到攻击时，它会阻止我们的网站加载。在本节中，我们将在配置文件的“server”块中添加“add_header”指令。

```
**# store cross-site scripting** cross_site_scripting=$(curl [https://gist.githubusercontent.com/david-littlefield/51654dec3034fbfe651abc375a0f70a2/raw)](https://gist.githubusercontent.com/david-littlefield/51654dec3034fbfe651abc375a0f70a2/raw))**# add "add_header" directive to configuration file** sudo sed "s|#cross_site_scripting_placeholder#|$cross_site_scripting|g" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
```

## 防止点击劫持攻击:

点击劫持攻击试图将我们的网站嵌入到恶意网站中，该恶意网站使用交互式覆盖来提取我们用户的敏感信息。这可以通过向我们的 HTTP 响应头添加一个头来防止，该头只允许我们的网站从来自我们的协议、域和端口的 HTTP 请求中加载。在本节中，我们将在配置文件的“server”块中添加“add_header”指令。

```
**# store clickjacking** clickjacking=$(curl [https://gist.githubusercontent.com/david-littlefield/b8a267f4463072fc34454ee0a93ae73d/raw)](https://gist.githubusercontent.com/david-littlefield/b8a267f4463072fc34454ee0a93ae73d/raw))**# add "add_header" directive to configuration file** sudo sed "s|#clickjacking_placeholder#|$clickjacking|g" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
```

## 防止内容嗅探攻击:

内容嗅探攻击试图欺骗 web 浏览器执行伪装成不同文件类型的脚本。这可以通过在我们的 HTTP 响应头中添加一个头来防止，这个头可以阻止 web 浏览器执行任何不属于“content-type”头中包含的文件类型的文件。在本节中，我们将在配置文件的“server”块中添加“add_header”指令。

```
**# store content sniffing** content_sniffing=$(curl [https://gist.githubusercontent.com/david-littlefield/7c54f4c4f81a4be7ca4d2fc634a8a6d9/raw)](https://gist.githubusercontent.com/david-littlefield/7c54f4c4f81a4be7ca4d2fc634a8a6d9/raw))**# add "add_header" directive to configuration file** sudo sed "s|#content_sniffing_placeholder#|$content_sniffing|g" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
```

## 防止热链接攻击:

热链接攻击是试图将我们网站上的文件嵌入到另一个网站上，以使用我们的带宽而不是他们的带宽。这可以通过添加一个 HTTP 头来防止，该头只允许来自我们的域和子域的 HTTP 请求。在本节中，我们将在配置文件的“location”块中添加“valid_referers”指令。

```
**# store hotlinking** hotlinking=$(curl [https://gist.githubusercontent.com/david-littlefield/63d6ed79801a6c23d7554a89d88d2aa4/raw)](https://gist.githubusercontent.com/david-littlefield/63d6ed79801a6c23d7554a89d88d2aa4/raw))**# add "location" block to configuration file** sudo sed "s|#hotlinking_placeholder#|$hotlinking|g;" -i /etc/nginx/nginx.conf**# change "placeholder.com" to your domain name from earlier**
domain_name="placeholder.com"**# add domain name to configuration file** sudo sudo sed "s|#domain_name_placeholder#|$domain_name|g" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
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
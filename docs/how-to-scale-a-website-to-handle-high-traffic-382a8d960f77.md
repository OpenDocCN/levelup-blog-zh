# 如何扩展网站以处理高流量

> 原文：<https://levelup.gitconnected.com/how-to-scale-a-website-to-handle-high-traffic-382a8d960f77>

## WEB 开发:

## 包含逐步说明的完整解决方案

![](img/b69585ba1fb036bb841d47a859a825c9.png)

图片由 [shawnanggg](https://unsplash.com/photos/h3zMSayQRls)

## 总结:

阅读本文的主要好处是，您将了解如何使用负载平衡器、多个 web 服务器和压力测试来扩展您的网站，以处理至少 10k 个并发连接。这将显著提高网站的响应时间、每秒请求数和并发用户数。然而，这将需要一个带有专用 CPU 的 web 服务器来托管您的负载平衡器，以及 11 个带有共享 CPU 的 web 服务器来存储您的网站。

*   **结果:**这将产生 100 毫秒的平均响应时间<
*   [节点平衡器](https://www.linode.com/products/nodebalancers/)对于有< 10k 并发连接的站点更便宜

我们将使用 Linode 作为我们的云服务提供商，因为他们的产品易于使用、价格合理、可扩展，并且他们的客户服务非常出色。我们将使用 Nginx 作为我们的网络服务器，因为他们的产品是免费的，处理高网站流量，并为世界上 10 万个顶级网站中的 60%提供支持。

*   **推广:**这个[推荐链接](https://linode.gvw92c.net/P0BmGX)为 Linode 提供 100 美元的信用

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

1.  [创建负载平衡器](#45ca)
2.  [更新 DNS 记录(非 CDN)](#d408)
3.  [更新 DNS 记录(CDN)](#627d)
4.  [配置负载均衡器](#40a4)
5.  [创建用户账户](#fe1a)
6.  [禁用 Root 登录](#dc5a)
7.  [禁用密码认证](#70e7)
8.  [优化操作系统](#cdc5)
9.  [修改第一个 Web 服务器](#4484)
10.  [将第一台 Web 服务器添加到负载平衡器](#bfa7)
11.  [对负载平衡器进行压力测试](#58f0)
12.  [创建第二台网络服务器](#f7c7)
13.  [将第二台 Web 服务器添加到负载平衡器](#6601)
14.  [将第二台网络服务器添加到数据库](#728b)
15.  [测试负载平衡器(Linux)](#2e8f)
16.  [测试负载平衡器(Windows)](#5943)
17.  [测试负载均衡器(Mac)](#923a)
18.  [对负载平衡器进行压力测试](#2f44)
19.  [创建其他网络服务器](#7a49)
20.  [修改网络服务器](#6c10)

## 创建负载平衡器:

负载平衡器是一个程序，它将从您的域名传入的网络流量分配到包含我们网站的众多进行中的 web 服务器之一。在本节中，我们将通过创建一个具有四个专用 CPU 的节点来创建包含负载平衡器的虚拟机。

```
**# create virtual machine**
1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "create" button
3\. click "linode" menu item**# select linux distribution**
1\. click image dropdown menu in "choose a distribution" section
2\. click "ubuntu 20.04 LTS" menu item**# select datacenter to store virtual machine**
1\. click region dropdown menu in "region" section
2\. select same region as your virtual machine from before**# select monthly plan**
1\. click "dedicated cpu" tab in "plan" section
2\. click "dedicated 8gb" radio button**# finish virtual machine**
1\. scroll down to "linode label" section
2\. enter "load-balancer" into "linode label" text field
3\. enter password into "root password" text field
4\. scroll down to "add-ons" section
5\. check "private ip" checkbox
6\. click "create linode" button
```

## 更新 DNS 记录(非 CDN):

DNS 记录当前将传入的网络流量从您的域名重定向到包含我们网站的虚拟机。在本节中，我们将通过更改 a/aaaa 记录中与我们的域名和 www 子域关联的 ip 地址，将相同的传入网络流量重定向到包含我们的负载平衡器的虚拟机。

```
**# get ip address of load balancer**
1\. click "linodes" option in side panel
2\. write down ip address of load balancer in "ip address" column**# redirect website traffic from domain to load balancer** 1\. open "domains" page on [linode](https://cloud.linode.com/domains)
2\. click your domain name card
3\. scroll down to "a/aaaa record" section
4\. click "..." link for your domain name
5\. click "edit" menu item
6\. enter ip address from previous step in "ip address" text field
7\. click "save" button**# redirect website traffic from subdomain to load balancer** 1\. click "..." link for www subdomain
2\. click "edit" menu item
3\. enter ip address from previous step in "ip address" text field
4\. click "save" button**# check status of dns record**
1\. open "dns check" page on [dnschecker](https://dnschecker.org/)
2\. enter domain with www subdomain into "example.com" text field
3\. click "search" button
4\. verify ip address of load balancer 
5\. wait up to 72 hours for changes to take effect worldwide
```

## 更新 DNS 记录(CDN):

DNS 记录当前将传入的网络流量从您的域名重定向到包含我们网站的虚拟机。在本节中，我们将通过更改 a/aaaa 记录中与我们的域名和 www 子域关联的 ip 地址，将相同的传入网络流量重定向到包含我们的负载平衡器的虚拟机。

```
**# get ip address of load balancer**
1\. click "linodes" option in side panel
2\. write down ip address of load balancer in "ip address" column**# redirect website traffic from domain to load balancer**
1\. open "dashboard" page on [cloudflare](https://dash.cloudflare.com/)
2\. click your domain name card
3\. click "dns" option in side panel
4\. click "edit" link for your domain
5\. enter ip address from previous step in "ipv4 address" text field
6\. click "save" button**# redirect website traffic from subdomain to load balancer** 4\. click "edit" link for your subdomain
5\. enter ip address from previous step in "ipv4 address" text field
6\. click "save" button
```

## 配置负载平衡器:

Nginx web 服务器充当 TCP 负载平衡器，可以将连接直接传递到我们的 web 服务器。这使我们能够重用我们的 web 服务器，在以前的文章中，我们保护它免受威胁，并优化它在一秒钟内加载。在本节中，我们将安装 Nginx，下载我们的配置文件，并指定打开文件和工作连接的最大数量。

*   它可以充当 HTTP 负载平衡器，但是需要做更多的工作。

```
**# open console as root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "load-balancer" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter "root" into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# update package information** sudo apt-get update**# install nginx** sudo apt-get install --yes nginx**# download the nginx configuration file**
sudo curl -o /etc/nginx/nginx.conf [https://gist.githubusercontent.com/david-littlefield/b4aa97d8c872361d26cd0ca8de85a58c/raw](https://gist.githubusercontent.com/david-littlefield/b4aa97d8c872361d26cd0ca8de85a58c/raw)**# store maximum number of open files**
open_files=500000**# add maximum number of open files to configuration file** sudo sed "s|#open_files_placeholder#|$open_files|g" -i /etc/nginx/nginx.conf**# store number of worker connections** worker_connections=100000**# add number of worker connections to configuration file**
sudo sed "s|#worker_connections_placeholder#|$worker_connections|" -i /etc/nginx/nginx.conf**# close console**
exit
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
sudo usermod -aG admin $username
```

## 禁用 Root 登录:

root 用户是一个用户帐户，可以无限制地访问每个基于 Linux 的操作系统预装的整个系统。这为攻击者提供了他们入侵我们的网络服务器所需的一半信息。在本节中，我们将通过更改 SSH 配置文件中的设置来防止 root 用户访问我们的虚拟机。

```
**# disable root login in ssh configuration file**
sudo sed "s|PermitRootLogin yes|PermitRootLogin no|g" -i /etc/ssh/sshd_config**# restart ssh**
sudo service ssh restart
```

## 禁用密码验证:

密码是一种身份验证方法，当密码太弱或被许多不同的帐户重复使用时，它会成为一种威胁。这使得攻击者有可能通过反复试验来破解密码。在本节中，我们将通过更改 SSH 配置中的设置来防止用户使用密码身份验证访问我们的虚拟机。

```
**# disable password authentication** sudo sed "s|PasswordAuthentication yes|PasswordAuthentication no|" -i /etc/ssh/sshd_config**# restart ssh**
sudo service ssh restart
```

## 优化操作系统:

操作系统经过优化，通过对 web 服务器和最终用户使用相同的默认值，为每个人提供最佳的整体性能。在本节中，我们将通过提高打开文件和最大连接限制以及使用更有效的 tcp 拥塞控制算法来优化我们的操作系统，使其成为高性能的 web 服务器。

```
**# store sysctl configurations**
sysctl=$(curl [https://gist.githubusercontent.com/david-littlefield/a61f9104882de9575f66a841c45060f6/raw](https://gist.githubusercontent.com/david-littlefield/a61f9104882de9575f66a841c45060f6/raw))**# add sysctl configurations to sysctl configuration file** sudo echo -e $sysctl >> /etc/sysctl.conf**# reload sysctl configurations**
sudo sysctl -p
```

```
**# store open file limits**
limits=$(curl [https://gist.githubusercontent.com/david-littlefield/02a78bab0f37c0f759218759db77a43b/raw](https://gist.githubusercontent.com/david-littlefield/02a78bab0f37c0f759218759db77a43b/raw))**# add open file limits to limits configuration file** sudo echo -e $limits >> /etc/security/limits.conf
```

```
**# store service configurations** ulimit=$(curl [https://gist.githubusercontent.com/david-littlefield/01cbf5baae49bccb691830205eb7d1d4/raw](https://gist.githubusercontent.com/david-littlefield/01cbf5baae49bccb691830205eb7d1d4/raw))**# add service configurations to nginx service file**
sudo sed "s|\[Service\]|$ulimit|g" -i /lib/systemd/system/nginx.service**# reload** **systemd configuration**
sudo systemctl daemon-reload**# restart nginx**
sudo systemctl restart nginx**# close console**
exit
```

## 修改第一台 Web 服务器:

第一个 web 服务器是指包含我们的网站的虚拟机，我们将其托管在云中，免受威胁，并在之前的文章中进行了优化，可以在一秒钟内加载。在本节中，我们将通过禁用速率限制、创建并记下它的私有 ip 地址，并重新启动它以使更改生效，来准备我们的第一个 web 服务器。

```
**# open console as non-root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "website-1" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# disable rate limit in console**
sudo sed "s|limit_req|# limit_req|g" -i /etc/nginx/nginx.conf**# close console**
exit**# create private ip address**
1\. click "network" tab
2\. click "add ip address" button
3\. click "private" radio button
4\. click "allocate" button**# write down private ip address of website-1** 1\. scroll down to "ip addresses" section
2\. write down "ipv4 – private" ip address**# restart "website-1" linode**
1\. click "reboot" button
2\. click "reboot linode" button
```

## 将第一台 Web 服务器添加到负载平衡器:

负载平衡器将传入的网络流量从您的域名传递到我们的配置文件中列出的 web 服务器。在本节中，我们将通过将私有 ip 地址添加到配置文件中的 web 服务器列表中，将第一个 web 服务器添加到我们的负载平衡器中。

```
**# open console as non-root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "load-balancer" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter "root" into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# change placeholder to private ip address of website-1** website_1="placeholder"**# insecure web server**
insecure_web_server="server $website_1:80;\n        #insecure_web_server_placeholder#"**# add insecure web server to configuration file** sudo sed "s|#insecure_web_server_placeholder#|$insecure_web_server|g" -i /etc/nginx/nginx.conf**# store secure web server**
secure_web_server="server $website_1:443;\n        #secure_web_server_placeholder#"**# add secure web server to configuration file**
sudo sed "s|#secure_web_server_placeholder#|$secure_web_server|g" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
```

## 在负载平衡器上执行压力测试:

Loader 是一种可扩展性测试服务，它使用数千个并发连接对网站进行压力测试。这是一个免费的工具，我们可以在我们的网站上使用高达 10k 并发连接。在本节中，我们将通过增加每个测试中的并发连接数来对负载平衡器执行压力测试，直到负载平衡器崩溃。

```
**# create a loader account**
1\. create an account on [loader](https://loader.io/register/signup?plan=1)
2\. open "target hosts" page on [loader](https://loader.io/targets)
3\. click "new host" button
4\. enter your domain with www subdomain into "domain" text field
5\. click "next: verify" button
6\. copy the verification token**# reopen console as non-root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "website-1" linode
3\. click "launch lish console" link
4\. paste commands into console**# change placeholder to the verification token** verification_token="placeholder"**# create verification token file**
sudo echo -e "$verification_token" > ~/$verification_token.txt**# move verification token file to website directory**
sudo mv ~/$verification_token.txt /var/www/html/$verification_token.txt**# complete loader.io verification**
1\. click "verify" button
2\. click "new test" button**# create load balancer test**
1\. enter "concurrent connections" into "name" text field
2\. click "test type" dropdown menu
3\. click "clients per second" menu item
4\. enter "1000" into "clients" text field
5\. enter "20" into "duration" text field
6\. click "min" menu item
7\. click "sec" menu item
8\. click "protocol" dropdown menu
9\. click "https" menu item**# test load balancer**
1\. click "run test" button
2\. review average, minimum, and maximum response time
3\. review timeout, 400/500, and network errors**# retest load balancer with double concurrent connections**
1\. click "edit test" icon
2\. enter "2000" into "clients" text field
3\. click "run test" button
4\. review average, minimum, and maximum response time
5\. review timeout, 400/500, and network errors**# retest load balancer with double concurrent connections**
1\. click "edit test" icon
2\. enter "4000" into "clients" text field
3\. click "run test" button
4\. review average, minimum, and maximum response time
5\. review timeout, 400/500, and network errors**# retest load balancer with double concurrent connections**
1\. click "edit test" icon
2\. enter "8000" into "clients" text field
3\. click "run test" button
4\. review average, minimum, and maximum response time
5\. review timeout, 400/500, and network errors
```

## 创建第二台 Web 服务器:

第二台 web 服务器是我们的第一台 web 服务器的克隆，它允许我们的负载平衡器在两台 web 服务器之间分配传入的网络流量。这使得我们的负载平衡器能够处理更高的流量负载，同时降低每个 web 服务器的负载。在本节中，我们将通过克隆我们的第一个 web 服务器，创建并记下它的私有 ip 地址，然后重新启动它以使更改生效，来创建我们的第二个 web 服务器。

```
**# create "website-2" linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "create" button
3\. click "linode" menu item
4\. click "clone linode" tab
5\. click "website-1" card**# select datacenter to store virtual machine**
1\. click region dropdown menu in "region" section
2\. select same region from before**# select monthly plan**
1\. click "shared cpu" tab in "plan" section
2\. click "nanode 1gb" radio button**# finish virtual machine**
1\. scroll down to "linode label" section
2\. enter "website-2" into "linode label" text field
3\. enter password into "root password" text field
4\. click "create linode" button
5\. wait until linoide finishes
6\. click "power on" link
7\. click "power on linode" button**# create private ip address**
1\. click "network" tab
2\. click "add ip address" button
3\. click "private" radio button
4\. click "allocate" button**# write down private ip address of website-2** 1\. scroll down to "ip addresses" section
2\. write down "ipv4 – private" ip address**# restart "website-2" linode**
1\. click "reboot" button
2\. click "reboot linode" button**# open console as non-root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "website-2" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# change web server number**
sudo sed "s|add_header website 1|add_header website 2|g" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx**# close console**
exit
```

## 将第二台 Web 服务器添加到负载平衡器:

负载平衡器将传入的网络流量从您的域名传递到我们的配置文件中列出的 web 服务器。在本节中，我们将通过将私有 ip 地址添加到配置文件中的 web 服务器列表中，将第二台 web 服务器添加到负载平衡器中。

*   它将被手动添加到我们的不安全和安全的 web 服务器列表中。

```
**# open console as non-root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "load-balancer" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# open nginx configuration file**
sudo vim /etc/nginx/nginx.conf**# add private ip address of website-2 to insecure web servers**
1\. press "↓" key to scroll down one-time
2\. scroll down to "#insecure_web_server_placeholder#" text
3\. press "i" key to enter "insert" mode
4\. press "return" key to add new line
5\. press "↑" key to scroll up to new line
6\. press " " key to add whitespace one-time
7\. add seven more whitespaces to match format
8\. enter ip address using format: "server ip_address:80;"**# add private ip address of website-2 to secure web servers**
1\. press "↓" key to scroll down one-time
2\. scroll down to "#secure_web_server_placeholder#" text
4\. press "return" key to add new line
5\. press "↑" key to scroll up to new line
6\. press " " key to add whitespace one-time
7\. add seven more whitespaces to match format
8\. enter ip address using format: "server ip_address:443;"**# save changes to configuration file**
1\. press "esc" key
2\. enter ":wq"
3\. press "return" key**# restart nginx**
sudo systemctl restart nginx
```

## 将第二台 Web 服务器添加到数据库中:

数据库受防火墙保护，防火墙会阻止防火墙规则未明确允许的所有传入网络。这可以防止攻击者试图从他们的远程计算机连接到我们的数据库。在本节中，我们将通过创建允许其 ip 地址的防火墙规则，使我们的第二台 web 服务器能够连接到我们的数据库。

```
**# open console as non-root user** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "website-2" linode
3\. find ip address under "ip addresses" label
4\. write down ip address**# open console as non-root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "database" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# switch to root user**
su root**# change "placeholder" to ip address of "website-2" linode**
ip_address="placeholder"**# store port**
port="5000"**# create firewall rule for ip address in text file**
ufw allow from $ip_address to any port $port**# switch back to non-root user**
exit**# close console**
exit
```

## 测试负载平衡器(Linux):

默认情况下，负载平衡器设计为使用循环算法，在我们的 web 服务器之间按顺序平均分配传入的网络流量。在本节中，我们将使用 curl 命令行工具来检查 HTTP 头，以验证我们的负载平衡器是否正常工作。

```
**# open terminal**
1\. click “activities” in top-left corner
2\. enter “terminal” into search bar
3\. click “terminal” icon**# change placeholder to your domain name** domain_name="placeholder.com"**# request headers from website** curl -IL $domain_name**# check http headers**
1\. request http headers from website several times
2\. ensure "website" header switches between 1 and 2
```

## 测试负载平衡器(Windows):

默认情况下，负载平衡器设计为使用循环算法，在我们的 web 服务器之间按顺序平均分配传入的网络流量。在本节中，我们将使用 curl 命令行工具来检查 HTTP 头，以验证我们的负载平衡器是否正常工作。

```
**# open powershell**
1\. press “⊞ windows”
2\. enter “powershell” into search bar
3\. click “run as administrator” menu item**# change placeholder to your domain name** $domain_name="placeholder.com"**# request headers from website** curl -method head -usebasicparsing $domain_name**# check http headers**
1\. request http headers from website several times
2\. ensure "website" header switches between 1 and 2
```

## 测试负载平衡器(Mac):

默认情况下，负载平衡器设计为使用循环算法，在我们的 web 服务器之间按顺序平均分配传入的网络流量。在本节中，我们将使用 curl 命令行工具来检查 HTTP 头，以验证我们的负载平衡器是否正常工作。

```
**# open terminal**
1\. press “command ⌘ + spacebar” keys
2\. enter “terminal” into search bar
3\. press “return” key**# change placeholder to your domain name** domain_name="placeholder.com"**# request headers from website** curl -IL $domain_name**# check http headers**
1\. request http headers from website several times
2\. ensure "website" header switches between 1 and 2
```

## 在负载平衡器上执行压力测试:

并发连接数表示在任何给定时间我们网站上可以发出的并行请求的数量。它应该随着我们添加到负载平衡器的每个 web 服务器而增加。在本节中，我们将在我们的负载平衡器上重复相同的压力测试，以确认我们的响应时间更快，错误更少。

```
**# retest load balancer with 1k concurrent connections**
1\. open "tests" page on [loader](https://loader.io/tests)
2\. click "concurrent connections" link
3\. click "edit test" icon
4\. enter "1000" into "clients" text field
5\. click "run test" button
6\. review average, minimum, and maximum response time
7\. review timeout, 400/500, and network errors**# retest load balancer with double concurrent connections**
1\. click "edit test" icon
2\. enter "2000" into "clients" text field
3\. click "run test" button
4\. review average, minimum, and maximum response time
5\. review timeout, 400/500, and network errors**# retest load balancer with double concurrent connections**
1\. click "edit test" icon
2\. enter "4000" into "clients" text field
3\. click "run test" button
4\. review average, minimum, and maximum response time
5\. review timeout, 400/500, and network errors**# retest load balancer with double concurrent connections**
1\. click "edit test" icon
2\. enter "8000" into "clients" text field
3\. click "run test" button
4\. review average, minimum, and maximum response time
5\. review timeout, 400/500, and network errors**# retest load balancer with 10k concurrent connections**
1\. click "edit test" icon
2\. enter "10000" into "clients" text field
3\. click "run test" button
4\. review average, minimum, and maximum response time
5\. review timeout, 400/500, and network errors
```

## 创建其他 Web 服务器:

负载平衡器可以在数百个 web 服务器之间分配传入的网络流量，以处理多达数百万个并发连接。在本节中，我们将继续通过重复最近几节中的步骤来扩展我们的网站，以便向我们的负载平衡器添加额外的 web 服务器。

```
**# add web servers to load balancer**
1\. repeat "create the second web server" section
2\. repeat "modify the second web server" section
3\. increment web server number in http headers
4\. repeat "add the second web server to the load balancer" section
5\. repeat "test the load balancer" section
6\. repeat "evaluate the load balancer performance" section
7\. repeat these steps until desired performance is achieved
```

## 修改 Web 服务器:

速率限制是一项安全功能，它限制了一个 ip 地址在给定时间内可以向我们的每个 web 服务器发出的请求数量。它可以帮助减缓暴力密码猜测攻击，并防止分布式拒绝服务攻击。它在前面的步骤中被禁用，以便在我们的负载平衡器上执行压力测试。在本节中，我们将在我们的负载平衡器中列出的每个 web 服务器上启用速率限制。

```
**# open console as non-root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "website-1" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# enable rate limit**
sudo sed "s|# limit_req|limit_req|g" -i /etc/nginx/nginx.conf**# close console**
exit**# enable rate limit on all web servers**
1\. repeat same steps for each web server listed in load balancer
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
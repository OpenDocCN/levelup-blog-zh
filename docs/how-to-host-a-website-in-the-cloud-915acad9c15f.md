# 如何在云中托管网站

> 原文：<https://levelup.gitconnected.com/how-to-host-a-website-in-the-cloud-915acad9c15f>

## WEB 开发:

## 包含逐步说明的完整解决方案

![](img/e62805ecfb075ed4df37b5c56f16aee9.png)

图片由[森迪·纪伯伦](https://unsplash.com/photos/oalS6SkZc_s)

## 总结:

阅读这篇文章的主要好处是，你将学会如何使用共享主机服务，这些服务比 Wix、GoDaddy 和 Wordpress 等网站建设服务提供更快的速度、更好的控制和更便宜的计划。这将让您可以选择自定义网站上可以自定义的所有内容。然而，这将要求你管理自己的网络服务器，并使用代码构建和更新自己的网站。

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

1.  [更改名称服务器](#366a)
2.  [创建虚拟机](#c65b)
3.  [编辑 DNS 记录](#c736)
4.  [安装网络服务器](#dce2)
5.  [安装网站](#d0c2)

## 更改名称服务器:

域名注册商是租赁您的域名，并将来自您的域名的传入网络流量重定向到您帐户中指定的名称服务器的企业。在本节中，我们将把这些名称服务器更改为我们的云服务提供商拥有的服务器，以便将传入的网络流量路由到存储我们网站的 web 服务器。

*   我使用 GoDaddy，但其他域名注册商的步骤类似。

```
**# open dns settings of your domain registrar**
1\. open "products" page on [GoDaddy](https://account.godaddy.com/products)
2\. scroll to "all products and services" section
3\. click "dns" in "domains" section4\. scroll to "nameservers" section
5\. click "change" button
6\. click "enter my own nameservers" link**# enter nameservers** 1\. enter "ns1.linode.com" into "nameserver 1" text field
2\. enter "ns2.linode.com" into "nameserver 2" text field
3\. click "add nameserver" link
4\. enter "ns3.linode.com" into "nameserver 3" text field
5\. click "add nameserver" link
6\. enter "ns4.linode.com" into "nameserver 3" text field
7\. click "add nameserver" link
8\. enter "ns5.linode.com" into "nameserver 3" text field**# save nameservers**
1\. click "save" button
2\. check "yes, i consent to update nameservers" checkbox
3\. click "continue" button
4\. enter verification code
5\. click "verify code" button
```

## 创建虚拟机:

虚拟机是一个模拟的计算机系统，拥有自己的 CPU、内存、存储和网络接口，位于我们云服务提供商的物理硬件上。这使我们可以安装我们的网络服务器，托管我们的网站，并根据需要每月花费很少的费用升级我们的硬件。在本节中，我们将使用最便宜的月度计划创建包含我们网站的虚拟机。

```
**# create virtual machine** 1\. create an account on [linode](https://www.linode.com/lp/free-credit-short/)
2\. click "create" button
3\. click "linode" menu item**# select linux distribution**
1\. click image dropdown menu in "choose a distribution" section
2\. click "ubuntu 20.04 LTS" menu item**# select datacenter with good performance** 1\. open "speed test" page on [linode](https://www.linode.com/speed-test/)
2\. click region closest to your customers
3\. click "start speed test" button
4\. review download speed, upload speed, and ping score
5\. repeat for regions closest to your customers
6\. remember region with high download and upload speed and low ping**# select datacenter to store virtual machine**
1\. click region dropdown menu in "region" section
2\. select region with good performance from before**# select monthly plan**
1\. click "shared cpu" tab in "plan" section
2\. click "nanode 1gb" radio button**# finish virtual machine**
1\. scroll down to "linode label" section
2\. enter "load-balancer" into "linode label" text field
3\. enter password into "root password" text field
4\. click "create linode" button**# copy ip address** 1\. copy ip address under "ip addresses" label
```

## 编辑 DNS 记录:

DNS 记录是包含您的域名信息的文本文件，我们的云服务提供商使用该文件将传入的网络流量从他们的名称服务器路由到包含我们网站的 web 服务器。在本节中，我们将为您的域名和包含我们虚拟机 ip 地址的“www”子域创建“A”记录。

```
**# open dns settings** 1\. click "domains" option in side panel
2\. click "create domain" button
2\. click "primary" radio button
3\. enter domain into "domain" text field
4\. enter regular email into "soa email address" text field
5\. click "create domain" button**# create "a" record** 1\. click "add an a/aaaa record" button
2\. enter "@" into "hostname" text field 
3\. paste ip address into "ip address" text field
4\. click "ttl" dropdown menu
5\. click "30 seconds" menu item
6\. click "save" button**# create "a" record** 1\. click "add an a/aaaa record" button
2\. enter "www" into "hostname" text field 
3\. paste ip address into "ip address" text field
4\. click "ttl" dropdown menu
5\. click "30 seconds" menu item
6\. click "save" button
```

## 安装 Web 服务器:

网络服务器是一个接收和响应用户访问我们网站的请求的程序。这将允许我们存储、处理和向用户分发我们的网站内容。在本节中，我们将安装 Nginx，下载我们的配置文件，并定制我们的 web 服务器。

```
**# open console**
1\. click "linodes" option in side panel
2\. click "website-1" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter "root" into "login" prompt
6\. enter your password into "password" prompt
5\. paste commands into console**# add php repository to package manager** sudo add-apt-repository --yes ppa:ondrej/php**# update package information** sudo apt-get update**# install php and php extensions** sudo apt-get install --yes php7.2-fpm php7.2-cli php7.2-mcrypt php7.2-gd php7.2-imap php-memcached php7.2-mbstring php7.2-xml php7.2-curl php7.2-bcmath php7.2-xdebug**# set php-fpm to automatically start with server** sudo systemctl enable php7.2-fpm.service**# install nginx** sudo apt-get install --yes nginx**# download nginx configuration file**
sudo curl -o /etc/nginx/nginx.conf [https://gist.githubusercontent.com/david-littlefield/c02e64e0dc6a5df84de2f821c0914895/raw](https://gist.githubusercontent.com/david-littlefield/c02e64e0dc6a5df84de2f821c0914895/raw)**# store number of worker connections** worker_connections=$(ulimit -n)**# update number of worker connections**
sudo sed "s|#worker_connections#|$worker_connections|" -i /etc/nginx/nginx.conf**# change "placeholder.com" to your domain name**
domain_name="placeholder.com"**# add domain name to configuration file** sudo sudo sed "s|#domain_name#|$domain_name|g" -i /etc/nginx/nginx.conf**# store php-fpm socket** php_fpm_socket=$(sudo find / -name *[0-9][\-]fpm.sock)**# update php-fpm socket**
sudo sed "s|#php_fpm_socket#|$php_fpm_socket|" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
```

## 安装网站:

该网站是一个包含 php、css、文本和图像的模板，不使用数据库查询、JavaScript 函数或可能会降低速度的第三方模块。这使我们能够记录我们的基准性能，并将其与我们的优化性能进行比较。在这一节中，我们将通过从 GitHub 存储库中克隆我们的网站来将其安装在我们的虚拟机上。

*   它只使用 bootstrap 为我们的网站提供设计模板。

```
**# open html directory** cd /var/www**# remove existing html directory**
sudo rm -rf /var/www/html**# clone the website repository**
sudo git clone [https://github.com/david-littlefield/website.git](https://github.com/david-littlefield/website.git) /var/www/html**# assign ownership of html directory to www-data user** sudo chown -R www-data: /var/www/html**# restart nginx**
sudo systemctl restart nginx**# measure performance with gtmetrix** 1\. open [gtmetrix](https://gtmetrix.com/)
2\. enter your domain name into "enter url to analyze" text field
3\. click "test your site" button
4\. scroll to "page details" section
5\. review "fully loaded time" metric
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
# 如何向网站添加 Node.js 应用程序

> 原文：<https://levelup.gitconnected.com/how-to-add-a-node-js-application-to-a-website-57218f9dfaaa>

## WEB 开发

## 包含逐步说明的完整解决方案

![](img/f7c7fb69e8281a067c199b1569603cb5.png)

图片由[亚历山大·安德鲁斯](https://unsplash.com/photos/soLEw77Napo)

## 总结:

`web application`非常简单，易于实现。这是一个可以远程访问的常规程序。它需要一个虚拟机、一个 web 服务器、一个应用服务器、一个 Node.js 应用程序和一个流程管理器。这将确保应用程序可以远程使用，可以被许多用户同时访问，并且总是在后台运行。

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

1.  [创建虚拟机](#add8)
2.  [创建子域(非 CDN)](#d633)
3.  [创建子域(CDN)](#e9a3)
4.  [准备应用服务器](#ef51)
5.  [准备申请](#c2d8)
6.  [启动流程管理器](#bb66)
7.  测试应用程序

## 创建虚拟机:

`virtual machine`是一个虚拟操作系统，它存储、运行和管理其他需求。它提供了网站将用来从远程连接访问应用程序的 IP 地址。在本节中，我们将使用最便宜的月度计划来创建虚拟机。

```
**# create virtual machine** 1\. create an account on [linode](https://www.linode.com/lp/free-credit-short/)
2\. click "create" button
3\. click "linode" menu item**# select linux distribution**
1\. click image dropdown menu in "choose a distribution" section
2\. click "ubuntu 20.04 LTS" menu item**# select datacenter to store virtual machine**
1\. click region dropdown menu in "region" section
2\. select same region as your virtual machines from before**# select monthly plan**
1\. click "shared cpu" tab in "plan" section
2\. click "nanode 1gb" radio button**# complete virtual machine**
1\. scroll down to "linode label" section
2\. enter "application" into "linode label" text field
3\. enter password into "root password" text field
4\. click "create linode" button
```

## 创建子域(非 CDN):

通过连接到虚拟机的 IP 地址和端口号，可以访问虚拟机。IP 地址是标识虚拟机的唯一地址。它包含一组由句点分隔的四个数字。端口号是由 IP 地址组合而成的一个数字。它代表防火墙授权传输数据的指定区域。然而，子域使得连接过程容易得多。

`subdomain`是一个单词或字符组合，它被添加到你的域名的开头。它将来自子域的传入网络流量重定向到存储在域名服务器上“a/aaaa”记录中的 IP 地址。在本节中，我们将使用上一节中虚拟机的 IP 地址创建子域。

*   此步骤适用于不由 CDN 管理的网站。

```
**# get ip address of "application" linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "application" linode
3\. find ip address under "ip addresses" label
4\. write down ip address**# send incoming network traffic to "application" linode** 1\. open "domains" page on [linode](https://cloud.linode.com/domains)
2\. click your domain name
3\. click "add an a/aaaa record" button
4\. enter "app" into "hostname" text field 
5\. enter ip address from earlier into "ip address" text field
6\. click "ttl" dropdown menu
7\. click "30 seconds" menu item
8\. click "save" button
```

## 创建子域(CDN):

通过连接到虚拟机的 IP 地址和端口号，可以访问虚拟机。IP 地址是标识虚拟机的唯一地址。它包含一组由句点分隔的四个数字。端口号是与 IP 地址组合在一起的一个数字。它代表防火墙授权传输数据的指定区域。然而，子域使得连接过程容易得多。

`subdomain`是一个单词或字符组合，它被添加到你的域名的开头。它将来自子域的传入网络流量重定向到存储在域名服务器上“a/aaaa”记录中的 IP 地址。在本节中，我们将使用上一节中虚拟机的 IP 地址创建子域。

*   此步骤适用于由 CDN 管理的网站。

```
**# get ip address of "application" linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "application" linode
3\. find ip address under "ip addresses" label
4\. write down ip address**# send incoming network traffic to "application" linode**
1\. open "dashboard" page on [cloudflare](https://dash.cloudflare.com/)
2\. click your domain name card
3\. click "dns" option in side panel
4\. click "add record" button
5\. enter "app" into "name" text field
6\. enter ip address from earlier into "ipv4 address" text field
7\. click "save" button
```

## 准备 Web 服务器:

`web server` (Nginx)是一个能够接收、处理和响应虚拟机上的网络连接的程序。它可以处理负载平衡、权限处理、请求路由、端口分配、SSL/TLS 加密和静态文件缓存。但是，我们将只使用它将传入的网络流量转发到应用服务器。在本节中，我们将安装、启用和配置 web 服务器。

```
**# update package information** sudo apt-get update**# install nginx dependencies**
sudo apt-get install --yes curl gnupg2 ca-certificates lsb-release ubuntu-keyring**# install nginx** sudo apt-get install --yes nginx**# start nginx at startup** 
sudo systemctl enable nginx**# download nginx configuration file**
sudo curl -o /etc/nginx/nginx.conf [https://gist.githubusercontent.com/david-littlefield/bc3245f917140a0f270cfef1c4cdf150/raw](https://gist.githubusercontent.com/david-littlefield/bc3245f917140a0f270cfef1c4cdf150/raw)**# store subdomain**
subdomain="app"**# change "placeholder.com" to your domain name** 
domain_name="placeholder.com"**# store number of worker connections** worker_connections=$(ulimit -n)**# store port number**
port_number=5000**# add subdomain to configuration file**
sudo sed "s|#subdomain_placeholder#|$subdomain|g" -i /etc/nginx/nginx.conf**# add domain to configuration file**
sudo sed "s|#domain_name_placeholder#|$domain_name|g" -i /etc/nginx/nginx.conf**# add number of worker connections to configuration file**
sudo sed "s|#worker_connections_placeholder#|$worker_connections|" -i /etc/nginx/nginx.conf**# add port number to configuration file**
sudo sed "s|#port_number_placeholder#|$port_number|" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
```

## 准备应用程序服务器:

`application server` (Express)是一个能够接收、处理和响应虚拟机上的传入网络连接的程序。它实现了与 web 服务器相同的目的，但是 web 服务器允许我们向应用程序添加 SSL/TLS 加密。应用服务器可以直接与 Node.js 应用程序交互。它还可以处理请求路由、中间件和响应处理。在本节中，我们将安装 Node.js 依赖项和应用服务器。

```
**# download node.js**
curl -fsSL [https://deb.nodesource.com/setup_16.x](https://deb.nodesource.com/setup_16.x) | sudo -E bash**# install node.js**
sudo apt-get install --yes nodejs**# install build essential**
sudo apt-get install --yes build-essential**# install express**
sudo npm install express
```

## 准备应用程序:

`application` (Node.js)是一个常规程序，它使用木偶库在 Unsplash 网站上搜索图像。木偶库是一个 Node.js 包，它在 Chrome web 浏览器中执行自动任务。这允许我们使用用户指定的关键字执行搜索，按照最近的日期对搜索结果进行排序，并解析搜索结果中图像的 url。图像的 url 由应用程序以数组的形式返回给应用服务器。应用服务器在 HTTP 响应中将数组发送到网站。在本节中，我们将从 Github 资源库下载并安装应用程序。

```
**# install git** sudo apt-get install --yes git**# install puppeteer dependencies** sudo apt-get install --yes gconf-service libasound2 libatk1.0-0 libc6 libcairo2 libcups2 libdbus-1-3 libexpat1 libfontconfig1 libgcc1 libgconf-2-4 libgdk-pixbuf2.0-0 libglib2.0-0 libgtk-3-0 libnspr4 libpango-1.0-0 libpangocairo-1.0-0 libstdc++6 libx11-6 libx11-xcb1 libxcb1 libxcomposite1 libxcursor1 libxdamage1 libxext6 libxfixes3 libxi6 libxrandr2 libxrender1 libxss1 libxtst6 ca-certificates fonts-liberation libappindicator1 libnss3 lsb-release xdg-utils wget libgbm-dev**# install puppeteer** sudo npm install puppeteer**# open "www" directory**
cd /var/www/**# download "application" repository** sudo git clone [https://github.com/david-littlefield/application](https://github.com/david-littlefield/application) app**# open "app" directory**
cd app
```

## 启动流程管理器:

`process manager` (PM2)是一个确保应用程序总是在后台运行的程序。它启动应用程序，在零停机时间内更新应用程序，并在应用程序崩溃、应用程序服务器重启或虚拟机重启时自动重启应用程序。它还可以处理日志记录、监控和集群。在本节中，我们将安装、启用和启动进程管理器。

```
**# install process manager**
sudo npm install --global pm2**# set pm2 to start at startup** sudo pm2 startup ubuntu**# start application in background** pm2 start server.js
```

## 测试应用程序:

应用程序(Node.js)正在运行，可用于远程连接。这使得网站能够通过向我们附加到子域的路由发送一个带有用户指定的关键字的 HTTP 请求来搜索图像。HTTP 请求被转发到包含该应用程序的虚拟机的 IP 地址。HTTP 请求由 web 服务器(Nginx)接收，转发到应用服务器(Express)，并由应用程序执行。搜索结果通过 web 服务器传递，并在 HTTP 响应中返回到网站。最后，网站接收、处理并显示 HTTP 响应。

```
**#  test application**
1\. open web browser
2\. visit your domain name
3\. click "search" link
4\. enter "beach background" into "keywords" text field
5\. click "search" button
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
10\. How to Add a Payment Method to a Websites
```
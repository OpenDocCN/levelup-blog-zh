# 如何向网站添加数据库

> 原文：<https://levelup.gitconnected.com/how-to-add-a-database-to-a-website-8d36d1e77316>

## WEB 开发

## 包含逐步说明的完整解决方案

![](img/40015fd7f3287e5619e3097e2f8147be.png)

图片由 [Ruslan Zaplatin](https://unsplash.com/photos/VvWTybOHVT4) 提供

## 总结:

阅读本文的主要好处是，您将学习如何在 web 服务器上托管数据库，将您的网站连接到数据库，以及执行用于管理持久数据的四个基本操作。这将允许您的网站存储、组织、访问和过滤可以跟踪的关于您的客户、产品和业务的各种类型的数据。然而，这将要求您知道您想要跟踪什么数据，并配置表、查询、编程逻辑和接口来跟踪它。

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

1.  [创建虚拟机](#1cfb)
2.  [安装 MySQL 数据库](#6664)
3.  [准备 MySQL 数据库](#799f)
4.  [更换静态网站](#33ab)
5.  [将网站连接到数据库](#01c1)
6.  [选择数据库中的记录](#d610)
7.  [将记录插入数据库](#a2ce)
8.  [更新数据库中的一条记录](#3f40)
9.  [从数据库中删除一条记录](#be85)
10.  [查看数据库流程](#b3e0)

## 创建虚拟机:

虚拟机是一个模拟的计算机系统，拥有自己的 CPU、内存、存储和网络接口，位于我们云服务提供商的物理硬件上。这使我们可以安装我们的 web 服务器，托管我们的数据库，并根据需要每月花费很少的成本升级我们的硬件。在本节中，我们将使用最便宜的月度计划创建包含数据库的虚拟机。

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
2\. enter "database-1" into "linode label" text field
3\. enter password into "root password" text field
4\. click "create linode" button
5\. wait until linoide finishes
6\. click "power on" link
7\. click "power on linode" button**# create private ip address**
1\. click "network" tab
2\. click "add ip address" button
3\. click "private" radio button
4\. click "allocate" button**# write down private ip address of database** 1\. scroll down to "ip addresses" section
2\. write down "ipv4 – private" ip address**# restart "database-1" linode**
1\. click "reboot" button
2\. click "reboot linode" button
```

## 安装 MySQL 数据库:

MySQL 数据库是一个关系数据库管理系统，它将数据组织到使用访问控制保护的表中。这使我们能够控制哪些用户可以创建、请求、更新或删除存储在数据库中的数据。在本节中，我们将安装 MySQL，配置我们的基本安全设置，并启用远程连接。

```
**# open console as root user on linode** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "database" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter "root" into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# update package information** sudo apt-get update**# install mysql** sudo apt-get install --yes mysql-server**# enable firewall**
sudo ufw enable**# allow mysql to bypass firewall** sudo ufw allow mysql**# open mysql directory** cd /etc/mysql/mysql.conf.d**# allow mysql to receive connections from any ip address**
sudo sed "s|127.0.0.1|0.0.0.0|g" -i mysqld.cnf**# set mysql to run at startup** sudo systemctl enable mysql**# restart mysql** sudo systemctl restart mysql
```

## 准备 MySQL 数据库:

MySQL 客户端是一个命令行程序，提供前端接口来管理我们的数据库。这允许我们通过交互地执行 SQL 语句，或者通过从文本文件执行 SQL 语句来操作数据库中的数据。在本节中，我们将下载并执行我们的文本文件来创建我们的数据库、表格和数据，我们将把它们加载到我们的网站中。

*   密码必须包含大小写混合的字母、数字和符号。

```
**# download mysql file** curl -o /etc/mysql/database.sql [https://gist.githubusercontent.com/david-littlefield/74266b53347d2605fac6f53a27277f4c/raw](https://gist.githubusercontent.com/david-littlefield/74266b53347d2605fac6f53a27277f4c/raw)**# store database name** database="website"**# store table name**
table="records"**# change "placeholder" to unique username** user="placeholder"**# change "placeholder" to desired password** password="placeholder"**# add database to mysql file** sudo sed "s|#database_placeholder#|$database|g" -i /etc/mysql/database.sql**# add table to mysql file**
sudo sed "s|#table_placeholder#|$table|g" -i /etc/mysql/database.sql**# add user to mysql file** sudo sed "s|#user_placeholder#|$user|g" -i /etc/mysql/database.sql**# add password to mysql file** sudo sed "s|#password_placeholder#|$password|g" -i /etc/mysql/database.sql**# open mysql as root user**
sudo mysql -u root -p**# run mysql file**
source /etc/mysql/database.sql**# exit mysql**
exit
```

## 替换静态网站:

静态网站是向所有用户显示相同内容的网站。它为 web 浏览器提供使用硬编码文本预先构建的 HTML 文件，以及存储在我们的 web 服务器上的 CSS 和 JavaScript 文件。这种类型的网站最适合以内容为特色、不经常更新并且提供非交互式或非个性化内容的网站。

动态网站是一个向所有用户展示独特内容的网站。它为 web 浏览器提供使用服务器端语言实时生成的 HTML 文件，以及存储在我们的 web 服务器上的 CSS 和 JavaScript 文件。这种类型的网站最适合管理内容、经常更新并提供交互式或个性化内容的网站。

我们托管、保护和优化的网站是一个静态网站，它帮助我们以最小的复杂度单独、渐进地学习每一个主题。它还允许我们在静态和动态网站之间进行性能比较。在这一节中，我们将通过简单地加载一个不同的 php 文件来把我们的静态网站变成一个动态网站。

```
**# open console as non-root user** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "website-1" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# switch from static website to dynamic website** sudo sed "s|index static.php|index dynamic.php|g" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
```

## 将网站连接到数据库:

配置文件是一个 php 文件，包含我们的网站需要连接到我们的数据库和执行查询的 MySQL 凭证。它使用我们的凭证来建立连接、存储连接并指定如何处理错误。在本节中，我们将通过向配置文件添加数据库名称、ip 地址、用户和密码来存储我们的凭证。

*   它使用一种在网络浏览器中看不到的服务器端语言

```
**# change "placeholder" to database name from earlier** database="placeholder"**# change "placeholder" to ip address of database** host="placeholder"**# change "placeholder" to database username from earlier** user="placeholder"**# change "placeholder" to database password from earlier** password="placeholder"**# add database to configuration file**
sudo sed "s|#database_placeholder#|$database|g" -i /var/www/html/includes/configuration.php**# add host to configuration file** sudo sed "s|#host_placeholder#|$host|g" -i /var/www/html/includes/configuration.php**# add username to configuration file**
sudo sed "s|#user_placeholder#|$username|g" -i /var/www/html/includes/configuration.php**# add password to configuration file**
sudo sed "s|#password_placeholder#|$password|g" -i /var/www/html/includes/configuration.php
```

## 选择数据库中的记录:

SELECT 语句是一个 SQL 语句，它从数据库中的一个或多个表中检索记录。这允许我们通过与其他 SQL 语句结合来请求一个、一些或所有记录。在本节中，我们将请求所有的记录，用这些记录中的数据生成 HTML，并将 HTML 作为内容呈现在我们的网站上。

```
**# load website without cache**
1\. open chrome web browser
2\. click "..."  menu
3\. click "more tools" menu item
4\. click "developer tools" menu item
5\. click "network" tab
6\. check "disable cache" checkbox
7\. enter your domain name into address bar
8\. press "return" key
```

## 将记录插入数据库:

INSERT 语句是一个 SQL 语句，它在数据库的一个表中插入一条或多条记录。这允许我们通过为表中的一个、一些或所有列提供数据来添加记录。在本节中，我们将解析网站上文本字段中的数据，使用解析的数据创建更多数据，下载相关图像，并将数据添加到数据库中。

```
**# add image to "recent" page**
1\. click "upload" tab**# enter unsplash url into "unsplash url" text field** https://unsplash.com/photos/UoqAR2pOxMo**# enter location into "location" text field**
Da Nang, Vietnam**# enter description into "description" text field**
The Non Nuoc beach is located at the foot of the Marble Mountains and extends over 5 km. This beach has calm waves and crystal clear blue water all year round. You can also eat locally caught fresh fish at one of the restaurants. It is also an ideal place for sports such as surfing, windsurfing, volleyball, etc.**# create record**
1\. click "upload" button
```

## 更新数据库中的记录:

UPDATE 语句是一条 SQL 语句，用于更新数据库中一个表中的一条或多条记录。这允许我们通过将它与其他 SQL 语句相结合来更改一个、一些或所有记录。在本节中，我们将解析来自网站上文本字段的数据，解析来自当前记录对象的更多数据，并更改数据库中的数据。

```
**# open "edit" page**
1\. click "Da Nang, Vietnam" image**# remove description**
1\. select description from "description" text field
2\. press "delete" key**# enter description into "description" text field**
Son Tra peninsula is located about 8 km from the city center and has many beautiful beaches such as But beach, Tien Sa beach, Nam beach, Rang beach, Bac beach and Con beach. These beaches are all very beautiful at the foot of mountains with jungle and clear blue sea. Apart from relaxing on the beach and swimming, you can also go into the jungle, visit pagodas, ride a scooter around the peninsula and snorkel.**# update record** 1\. click "save" button
```

## 从数据库中删除记录:

DELETE 语句是一个 SQL 语句，它从数据库的一个表中删除一条或多条记录。这允许我们通过与其他 SQL 语句结合来删除一个、一些或所有记录。在这一节中，我们将从 URL 中的查询参数解析记录 id，删除相关的图像，并从数据库中删除带有记录 id 的数据。

```
**# open "edit" page**
1\. click "Da Nang, Vietnam" image**# delete record** 1\. click "delete" button**# measure performance with gtmetrix** 1\. open [gtmetrix](https://gtmetrix.com/)
2\. enter your domain name into "enter url to analyze" text field
3\. click "test your site" button
4\. scroll to "page details" section
5\. review "fully loaded time" metric
```

## 查看数据库流程:

该数据库是由我们的网站与存储在多个 php 文件中的不同类链操纵。这个过程可以在存储在我们 web 服务器上的 php 文件中找到。在这一节中，我们将定制并查看 php 文件，该文件演示了这个过程的一个统一版本。

```
**# add database to demo file** sudo sed "s|#database_placeholder#|$database|g" -i /var/www/html/demo.php**# add host to demo file** sudo sed "s|#host_placeholder#|$host|g" -i /var/www/html/demo.php**# add username to demo file** sudo sed "s|#username_placeholder#|$username|g" -i /var/www/html/demo.php**# add password to demo file** sudo sed "s|#password_placeholder#|$password|g" -i /var/www/html/demo.php**# change "placeholder.com" to your domain name** domain_name="placeholder.com"**# view url to demo php file**
echo $domain_name/demo.php**# open demo php file in web browser**
1\. copy url to demo php file
2\. paste url into address bar
3\. press "enter" key
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
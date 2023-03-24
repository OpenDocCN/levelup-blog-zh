# 如何优化一个网站在 1 秒钟内加载

> 原文：<https://levelup.gitconnected.com/how-to-optimize-a-website-to-load-in-1-second-3d67f11018ec>

## WEB 开发:

## 包含逐步说明的完整解决方案

![](img/65d6cc592e9b1a9d4b28d6ace33d5ad5.png)

图片由内森·范·埃格蒙德拍摄

## 总结:

阅读本文的主要好处是，您将学习如何使用成熟的优化技术来减少文件大小，提高文件传输速度，并尽可能快地重用保存的文件来加载您的网站。这将积极影响你的搜索引擎排名，跳出率和转换率。然而，这将要求您的网站和数据库查询中的代码进行优化，以获得最佳结果。

*   **结果:**这将减少 50%的加载时间到 1 秒或更少。
*   **注意:**page speed 和 HTTP/3 模块使得加载时间变得更糟。

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

1.  [下载 Brotli 模块](#6549)
2.  [从源代码重新安装 Nginx】](#074b)
3.  [准备网络服务器](#d6f0)
4.  [启用 HTTP/2 协议](#b7ca)
5.  [启用 Gzip 压缩](#f156)
6.  [启用 Brotli 压缩](#9840)
7.  [转换图像格式](#1296)
8.  [移除未使用的 CSS](#9ac0)
9.  [缩小 PHP 和 CSS 文件](#326c)
10.  [调整图像大小](#b113)
11.  [启用 FastCGI 缓存](#9954)
12.  [启用网络浏览器缓存](#9db4)
13.  [将图像缓存存储在 RAM 上](#662b)
14.  [将 FastCGI 缓存存储在 RAM 上](#407a)
15.  [优化操作系统](#530f)
16.  [启用 CDN](#9f25)

## 下载 Brotli 模块:

Brotli 模块是一个扩展，只能通过从源代码安装来添加到 Nginx 中。这使得我们可以比 gzip 压缩减少 30%的文件大小，从而更快地加载我们的网站。在本节中，我们将下载模块并存储所需的安装参数。

```
**# open console as non-root user** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "website-1" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# open user directory** cd ~**# download brotli**
git clone [https://github.com/google/ngx_brotli](https://github.com/google/ngx_brotli) --recursive**# move brotli to src directory**
sudo mv ngx_brotli /usr/local/src**# store brotli configuration**
brotli="--add-dynamic-module=/usr/local/src/ngx_brotli"
```

## 从源代码重新安装 Nginx:

Nginx web 服务器必须从源代码构建，才能使用第三方扩展。在本节中，我们将删除 Nginx，安装依赖项，下载源代码，编译源代码，并从源代码重新安装 Nginx。这也需要我们重新安装 Nginx，创建我们的符号链接，移动我们的共享对象文件，并创建我们的 systemd 服务文件。

```
**# backup nginx configuration** sudo cp /etc/nginx/nginx.conf ~/nginx.conf**# uninstall nginx**
sudo apt-get remove --yes nginx nginx-common**# store nginx configuration**
default=$(curl [https://gist.githubusercontent.com/david-littlefield/f411e9bcebc30fdbc6795214e1ea3382/raw)](https://gist.githubusercontent.com/david-littlefield/f411e9bcebc30fdbc6795214e1ea3382/raw))
```

```
**# update packages in package manager** sudo apt-get update**# install checkinstall**
sudo apt-get install --yes checkinstall**# install nginx dependencies**
sudo apt-get install --yes build-essential libpcre3 libpcre3-dev zlib1g zlib1g-dev openssl libssl-dev libgd-dev libxml2-dev libxslt1-dev**# open user directory**
cd ~**# download nginx**
sudo wget -O- https://nginx.org/download/nginx-1.18.0.tar.gz | tar -xz**# move nginx to src directory** sudo mv nginx-1.18.0 /usr/local/src**# open nginx directory**
cd /usr/local/src/nginx-1.18.0**# configure nginx** sudo ./configure $default $brotli**# prepare nginx**
sudo make**# install nginx**
sudo checkinstall**# create nginx documentation**
1\. type "y" key to create documentation
2\. press "return" key to confirm documentation
3\. press "return" key to use default values**# create symlink to modules directory**
sudo ln -s /usr/lib/nginx/modules /etc/nginx/modules**# copy shared object files to modules directory** sudo cp /usr/local/src/nginx-1.18.0/objs/*.so /usr/lib/nginx/modules**# store nginx systemd configuration** systemd=$(curl [https://gist.githubusercontent.com/david-littlefield/897e1d9e79afc8ad36c62028101b30d8/raw)](https://gist.githubusercontent.com/david-littlefield/897e1d9e79afc8ad36c62028101b30d8/raw))
```

```
**# create nginx systemd file** sudo echo -e $systemd > ~/nginx.service**# move nginx systemd file to system directory** sudo mv ~/nginx.service /etc/systemd/system/nginx.service**# restart systemd**
sudo systemctl daemon-reload**# set nginx to run nginx on startup** sudo systemctl enable nginx**# start nginx** sudo systemctl start nginx**# replace nginx configuration** sudo cp ~/nginx.conf /etc/nginx/nginx.conf**# restart nginx** sudo systemctl restart nginx
```

## 准备 Web 服务器(可选):

网络服务器是一个接收和响应用户访问我们网站的请求的程序。这将允许我们存储、处理和向用户分发我们的网站内容。它需要一个域名、DNS 记录、虚拟机、web 服务器和网站，我们在第一篇文章中已经介绍过了。在本节中，我们将安装 SSL 包，创建 SSL 证书，下载配置文件，并定制 web 服务器。

*   **重要提示:**如果你已经完成了前两篇文章，请跳过这一部分。

```
**# open console as non-root user** 1\. open "linodes" page on [linode](https://cloud.linode.com/linodes)
2\. click "website-1" linode
3\. click "launch lish console" link
4\. press "return" key
5\. enter your unique username into "login" prompt
6\. enter your password into "password" prompt
7\. press "return" key
8\. paste commands into console**# change "placeholder.com" to your domain name**
domain_name="placeholder.com"**# change "placeholder" to email address** email="placeholder"**# install certbot**
sudo apt-get install --yes certbot python3-certbot-nginx**# create ssl certificate**
sudo certbot certonly --webroot -w /var/www/html -d $domain_name -d [www.$domain_name](http://www.$domain_name) --non-interactive --agree-tos --email $email**# download the nginx configuration file**
curl -o /etc/nginx/nginx.conf [https://gist.githubusercontent.com/david-littlefield/cdc9d66935235091b03954f221c35e94/raw](https://gist.githubusercontent.com/david-littlefield/cdc9d66935235091b03954f221c35e94/raw)**# add domain name to configuration file** sudo sudo sed "s|#domain_name#|$domain_name|g" -i /etc/nginx/nginx.conf**# store number of worker connections** worker_connections=$(ulimit -n)**# update number of worker connections**
sudo sed "s|#worker_connections#|$worker_connections|" -i /etc/nginx/nginx.conf**# store php-fpm socket** php_fpm_socket=$(sudo find / -name *[0-9][\-]fpm.sock)**# update php-fpm socket**
sudo sed "s|#php_fpm_socket#|$php_fpm_socket|" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx**# measure performance with gtmetrix** 1\. open [gtmetrix](https://gtmetrix.com/)
2\. enter your domain name into "enter url to analyze" text field
3\. click "test your site" button
4\. scroll to "page details" section
5\. review "fully loaded time" metric
```

## 启用 HTTP/2 协议:

HTTP/2 协议是 HTTP/1 的扩展，支持使用单个连接并行执行多个 HTTP 请求。通过并行加载我们的文件，这样可以更快地加载我们的网站。在本节中，我们将在配置文件的“server”块中的“listen”指令中添加“http2”参数，在“location”块中添加“http2_push”指令。

```
**# adds http2 to configuration file**
sudo sed "s|listen 443 ssl|listen 443 ssl http2|g" -i /etc/nginx/nginx.conf**# stores http2** http2=$(curl [https://gist.githubusercontent.com/david-littlefield/9e1a964dd47759e728393b4fb1b754dc/raw)](https://gist.githubusercontent.com/david-littlefield/9e1a964dd47759e728393b4fb1b754dc/raw))**# add http2 to configuration file** sudo sed "s|#http2_placeholder#|$http2|" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
```

## 启用 Gzip 压缩:

Gzip 算法是一种标准的压缩算法，浏览器使用它来传输加载我们的网站所需的文件。这将我们的文件大小减少了 70%，从而加快了我们网站的加载速度。在本节中，我们将把 Gzip 指令添加到配置文件中的“http”块。

```
**# store gzip compression**
gzip_compression=$(curl [https://gist.githubusercontent.com/david-littlefield/b4608d2f6e5aeefcd9909e09e4e2043b/raw)](https://gist.githubusercontent.com/david-littlefield/b4608d2f6e5aeefcd9909e09e4e2043b/raw))**# add gzip compression to configuration file** sudo sed "s|#gzip_placeholder#|$gzip_compression|" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
```

## 启用 Brotli 压缩:

Brotli 算法是 Google 开发的一种较新的压缩算法。这比 gzip 压缩减少了 30%的文件大小，从而加快了我们网站的加载速度。在本节中，我们将把 Brotli 指令添加到配置文件中的“main”和“http”块中。

```
**# stores brotli module** brotli_module=$(curl [https://gist.githubusercontent.com/david-littlefield/38f1868b8696218fdd83db3b3a27d644/raw)](https://gist.githubusercontent.com/david-littlefield/38f1868b8696218fdd83db3b3a27d644/raw))**# add brotli module to configuration file** sudo sed "s|#brotli_1_placeholder#|$brotli_module|" -i /etc/nginx/nginx.conf
```

```
**# store brotli compression**
brotli_compression=$(curl [https://gist.githubusercontent.com/david-littlefield/e1039558c301cc25efe02226ce18773c/raw)](https://gist.githubusercontent.com/david-littlefield/e1039558c301cc25efe02226ce18773c/raw))**# add brotli compression to configuration file** sudo sed "s|#brotli_2_placeholder#|$brotli_compression|" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
```

## 转换图像格式:

Webp 图像是一种图像格式，可产生与 jpeg、png 和 gif 图像相同的可感知图像质量，但需要更少的存储空间。通过将我们的文件大小减少 25–35 %,这将加快我们网站的加载速度。在这一节中，我们将安装 webp 包，将 jpeg 图像转换为 webp 图像，并更改 php 文件中图像的文件格式。

```
**# install webp**
sudo apt-get install --yes webp**# convert jpeg image to webp image** sudo cwebp -q 80 /var/www/html/assets/images/1.jpeg -o /var/www/html/assets/images/1.webp**# convert jpeg images in images directory to webp images** for jpeg_file_path in /var/www/html/assets/images/*.jpeg; do webp_file_path=$(echo $jpeg_file_path | sed "s|\.jpeg|\.webp|"); sudo cwebp -q 80 $jpeg_file_path -o $webp_file_path; done**# change file format of images in html file** sudo sed "s|\.jpeg|\.webp|g" -i /var/www/html/index.php**# change file format of images in configuration file** sudo sed "s|\.jpeg|\.webp|g" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
```

## 移除未使用的 CSS:

css 移除过程是从大型模块(如 Bootstrap)中移除不必要的 CSS 的过程。这样可以通过减小 css 文件的大小来加快网站的加载速度。在本节中，我们将备份我们的 css 文件，安装 css 删除包，并从我们的 css 文件中删除不必要的 css。

```
**# open html directory**
cd /var/www/html**# create backup directory**
sudo mkdir -p assets/backup/**# copy css files to backup directory** sudo cp assets/css/bootstrap.css assets/css/style.css assets/backup/**# download node.js**
sudo curl -fsSL [https://deb.nodesource.com/setup_16.x](https://deb.nodesource.com/setup_16.x) | sudo -E bash**# install node.js**
sudo apt-get install --yes nodejs**# install purgecss**
sudo npm install -g purgecss**# remove unused css**
sudo purgecss --css assets/css/bootstrap.css assets/css/style.css --content index.php --output assets/css/**# restart nginx**
sudo systemctl restart nginx
```

## 缩小 PHP 和 CSS 文件:

缩小过程是从我们网站的源代码中删除不必要的文本的过程。这通过减少 60%的源代码来加快我们网站的加载速度。在这一节中，我们将备份我们的 php 文件，安装缩小包，并缩小我们的 css 和 html 文件。

```
**# open html directory**
cd /var/www/html**# copy php file to backup directory** sudo cp index.php assets/backup/**# install html-minifier**
sudo npm install -g html-minifier**# minify index html file**
sudo html-minifier --collapse-whitespace --remove-comments --remove-optional-tags --remove-redundant-attributes --remove-script-type-attributes --remove-tag-whitespace --use-short-doctype --minify-css true --minify-js true --output index.php index.php**# install postcss and cssnano**
sudo npm install -g cssnano postcss-cli**# minify bootstrap css file**
sudo postcss --use cssnano --no-map --output assets/css/bootstrap.css assets/css/bootstrap.css**# minify style css file**
sudo postcss --use cssnano --no-map --output assets/css/style.css assets/css/style.css**# restart nginx**
sudo systemctl restart nginx
```

## 调整图像大小:

图像过滤器模块是一个扩展，它可以调整我们网站上请求的图像的大小，而不需要额外的图像副本。这样可以通过减小图片大小和缓存图片来加快网站的加载速度。在本节中，我们将在配置文件的“http”块中添加图像过滤器指令、“location”块和“server”块。

```
**# store image filter module** image_filter_module=$(curl [https://gist.githubusercontent.com/david-littlefield/18ef3e08f1851e7587d6bcd5d9c9dfe1/raw)](https://gist.githubusercontent.com/david-littlefield/18ef3e08f1851e7587d6bcd5d9c9dfe1/raw))**# add "load_module" directive to configuration file** sudo sed "s|#image_filter_1_placeholder#|$image_filter_module|" -i /etc/nginx/nginx.conf
```

```
**# store image filter cache**
image_filter_cache=$(curl [https://gist.githubusercontent.com/david-littlefield/5b665a8e0d292acf65cc2bc320b72052/raw)](https://gist.githubusercontent.com/david-littlefield/5b665a8e0d292acf65cc2bc320b72052/raw))**# add "proxy_cache_path" directive to configuration file** sudo sed "s|#image_filter_2_placeholder#|$image_filter_cache|" -i /etc/nginx/nginx.conf
```

```
**# store image filter proxy**
image_filter_proxy=$(curl [https://gist.githubusercontent.com/david-littlefield/19b89c9adbd9b2e4ae3aa3319787d0a9/raw)](https://gist.githubusercontent.com/david-littlefield/19b89c9adbd9b2e4ae3aa3319787d0a9/raw))**# add image filter proxy directives to configuration file** sudo sed "s|#image_filter_3_placeholder#|$image_filter_proxy|" -i /etc/nginx/nginx.conf
```

```
**# store image filter resize**
image_filter_resize=$(curl [https://gist.githubusercontent.com/david-littlefield/e656d74b1fbdfc5faf837e0eff789cbc/raw)](https://gist.githubusercontent.com/david-littlefield/e656d74b1fbdfc5faf837e0eff789cbc/raw))**# add "server" block to configuration file** sudo sed "s|#image_filter_4_placeholder#|$image_filter_resize|" -i /etc/nginx/nginx.conf
```

```
**# enable srcset attribute in html file** sudo sed "s|srcset_placeholder|srcset|g" -i /var/www/html/index.php**# change image path format** sudo sed "s| \/assets\/images\/| \/media\/555\/|g" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
```

## 启用 FastCGI 缓存:

FastCGI 缓存是一种 web 服务器功能，它存储我们网站的内容，这些内容是由我们的 web 服务器使用我们数据库中的数据创建的。这通过从我们的网络服务器加载存储的版本而不是重新创建它来更快地加载我们的网站。在本节中，我们将把 FastCGI 指令添加到配置文件中的 http、server 和 location 块中。

*   我们将在下一篇文章中把这个数据库添加到我们的网站上。

```
**# store fastcgi cache 1** fastcgi_cache_1=$(curl [https://gist.githubusercontent.com/david-littlefield/85506f99b5a1bed143792bfef4158cea/raw)](https://gist.githubusercontent.com/david-littlefield/85506f99b5a1bed143792bfef4158cea/raw))**# add fastcgi cache 1 to configuration file** sudo sed "s|#fastcgi_cache_1_placeholder#|$fastcgi_cache_1|" -i /etc/nginx/nginx.conf
```

```
**# store fastcgi cache 2** fastcgi_cache_2=$(curl [https://gist.githubusercontent.com/david-littlefield/55d2e1a3b96a2c86820d336b29f81e04/raw)](https://gist.githubusercontent.com/david-littlefield/55d2e1a3b96a2c86820d336b29f81e04/raw))**# add fastcgi cache 2 to configuration file** sudo sed "s|#fastcgi_cache_2_placeholder#|$fastcgi_cache_2|" -i /etc/nginx/nginx.conf
```

```
**# store fastcgi cache 3**
fastcgi_cache_3=$(curl [https://gist.githubusercontent.com/david-littlefield/5cebcbe0413fcb8e1fc5a19f606851b4/raw)](https://gist.githubusercontent.com/david-littlefield/5cebcbe0413fcb8e1fc5a19f606851b4/raw))**# add fastcgi cache 3 to configuration file** sudo sed "s|#fastcgi_cache_3_placeholder#|$fastcgi_cache_3|" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx
```

## 启用 Web 浏览器缓存:

网络浏览器缓存是一种网络浏览器功能，可将我们网站的内容存储在用户的计算机上。通过从他们的电脑上加载存储的版本，而不是从我们的服务器上请求，这样可以更快地加载我们的网站。在本节中，我们将在配置文件的“http”块中添加“add_header”指令和“expires”指令。

```
**# store browser cache**
browser_cache=$(curl [https://gist.githubusercontent.com/david-littlefield/8afceefa904790cbfbfdd3cf40cd9b3c/raw)](https://gist.githubusercontent.com/david-littlefield/8afceefa904790cbfbfdd3cf40cd9b3c/raw))**# add browser cache to configuration file** sudo sed "s|#browser_cache_placeholder#|$browser_cache|" -i /etc/nginx/nginx.conf**# remove webp from browser cache**
sudo sed "s|gif\|webp|gif|g" -i /etc/nginx/nginx.conf**# restart nginx**
sudo systemctl restart nginx**# test browser cache**
1\. open [browser cache test](https://www.giftofspeed.com/cache-checker/)
2\. enter your domain name into "enter your url" text field
3\. click "check" button
4\. check cached files
```

## 将调整后的图像缓存存储在 RAM 中:

默认情况下，调整大小后的图像缓存存储在我们的磁盘驱动器上。这将从我们的内存加载比从我们的固态硬盘更快。在本节中，我们将创建缓存目录，将目录挂载到 RAM 上，并将目录添加到挂载文件系统的系统配置文件中。

```
**# create resize_cache directory**
sudo mkdir -p /var/www/resize_cache**# mount** **resize_cache** **directory to ram**
sudo mount -t tmpfs -o size=256M tmpfs /var/www/resize_cache**# switch to root user** su root**# re-mount resize_cache directory to ram on startup**
sudo echo -e "tmpfs /var/www/resize_cache tmpfs defaults, size=256M 0 0\n" >> /etc/fstab**# switch back to non-root user**
exit**# restart nginx**
sudo systemctl restart nginx
```

## 将 FastCGI 缓存存储在 RAM 中:

默认情况下，FastCGI 缓存存储在我们的磁盘驱动器上。这将从我们的内存加载比从我们的固态硬盘更快。在本节中，我们将创建缓存目录，将目录挂载到 RAM 上，并将目录添加到挂载文件系统的系统配置文件中。

```
**# create** **fastcgi_cache** **directory**
sudo mkdir -p /var/www/fastcgi_cache**# mount fastcgi_cache directory to ram**
sudo mount -t tmpfs -o size=256M tmpfs /var/www/fastcgi_cache**# switch to root user** su root**# re-mount** **fastcgi_cache** **directory to ram on startup**
sudo echo -e "tmpfs /var/www/fastcgi_cache tmpfs defaults, size=256M 0 0\n" >> /etc/fstab**# switch back to non-root user**
exit**# restart nginx**
sudo systemctl restart nginx**# measure performance with gtmetrix** 1\. open [gtmetrix](https://gtmetrix.com/)
2\. enter your domain name into "enter url to analyze" text field
3\. click "test your site" button
4\. scroll to "page details" section
5\. review "fully loaded time" metric
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
sudo sed "s|\[Service\]|$ulimit|g" -i /etc/systemd/system/nginx.service**# reload** **systemd configuration**
sudo systemctl daemon-reload**# restart nginx**
sudo systemctl restart nginx
```

## 启用 CDN:

内容交付网络是一组互连的 web 服务器，它们位于世界上不同的地理位置。这使得我们的网站可以从离用户最近的网络服务器上加载一份副本，从而加快加载速度。在本节中，我们将把我们的名称服务器更改为 Cloudflare 拥有的名称服务器，以便我们可以使用它们的网络。

*   Cloudflare 可能需要 24 小时才能完全缓存我们的网站

```
**# add domain name to cloudflare**
1\. create an account on [cloudflare](https://dash.cloudflare.com/sign-up)
2\. enter your domain name into "enter your site" text field
3\. click "add site" button
4\. click "free $0" card
5\. click "continue" button
6\. click "continue" button**# open dns settings of your domain registrar**
1\. open "products" page on [GoDaddy](https://account.godaddy.com/products)
2\. scroll to "all products and services" section
3\. click "dns" in "domains" section4\. scroll to "nameservers" section
5\. click "change" button
6\. click "enter my own nameservers" link**# replace nameservers** 1\. remove "ns1.linode.com" from "nameserver 1" text field
2\. enter "elmo.ns.cloudflare.com" into "nameserver 1" text field
3\. remove "ns2.linode.com" from "nameserver 2" text field
4\. enter "magali.ns.cloudflare.com" into "nameserver 2" text field
5\. click "trash can" icon next to "nameserver 3" text field
6\. click "trash can" icon next to "nameserver 4" text field
7\. click "trash can" icon next to "nameserver 5" text field**# save nameservers**
1\. click "save" button
2\. check "yes, i consent to update nameservers" checkbox
3\. click "continue" button
4\. enter verification code
5\. click "verify code" button**# complete cloudflare setup**
1\. reopen "change your nameservers" page on cloudflare
2\. click "done, check nameservers" button**# enable minification and brotli optimizations** click "speed" option in side panel
click "optimization" option in side panel
check all checkboxes in "auto minify" section
toggle switch on in "brotli" section**# enable http/3 optimization**
click "network" option in side panel
toggle switch on in "http/3 (with quic)" section**# set cache expiration with http headers** click "caching" option in side panel
click "configuration" option in side panel
click "browser cache ttl" dropdown menu
click "respect existing headers" menu item**# measure performance with gtmetrix** 1\. open [gtmetrix](https://gtmetrix.com/)
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
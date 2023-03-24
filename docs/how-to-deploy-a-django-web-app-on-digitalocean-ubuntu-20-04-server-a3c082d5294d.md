# 如何在 DigitalOcean Ubuntu 20.04 服务器上部署 Django Web App

> 原文：<https://levelup.gitconnected.com/how-to-deploy-a-django-web-app-on-digitalocean-ubuntu-20-04-server-a3c082d5294d>

在本文中，我们将通过最终将我们的 web 应用程序部署到数字海洋水滴来结束我们的 Django 系列。如果您一直在跟进，您应该已经为成功的部署做好了准备。我们将使用一台数字海洋 ubuntu 20.04 服务器，我们购买它时只花了 5 美元。

尽管这对于一个小规模的网络应用程序来说已经足够了，但是请记住，如果你真的增加了你的网络流量，你将需要升级 droplet 的容量。

如果你还没有跟上，只是来学习部署你的 Django 应用程序，看看这个设置教程，它涵盖了从头开始设置你的 droplet，安装 python3 开发环境和 Django 启动项目需要做的事情。

[](https://skolo-online.medium.com/getting-started-with-python-django-web-development-4e073e64c4a0) [## Python Django Web 开发入门

### Python Django 是最常用的 web 开发平台之一。它提供了一种简化的 web 方法…

skolo-online.medium.com](https://skolo-online.medium.com/getting-started-with-python-django-web-development-4e073e64c4a0) 

# 你的 Django 应用程序准备好部署了吗？

在你部署之前，你需要一个域名，你可以从任何一个 domains.co.za 获得。然后，您需要登录到客户端区域，并调整您的 DNS 设置。您必须编辑域的 A 记录以指向您的 droplet。如果你不知道如何做到这一点，你的域名托管服务的客户支持应该可以帮助你。

要遵循的步骤是:

1.  花 5 美元从数字海洋购买一个[水滴。](https://m.do.co/c/7d9a2c75356d)
2.  构建您的 Django 应用程序(您可以按照本系列的其余部分获得更多指导)
3.  购买一个域名用于您的网站
4.  编辑域名的 DNS 记录，指向您从 digitalocean 购买的 droplet
5.  回来并遵循教程的其余部分

# 将 Django web 应用程序部署到 digitalocean ubuntu 20.04 服务器的高级步骤

![](img/9653e12d5e4e3b975f1f0d8ad2545db5.png)

如何部署 Django webapp

*   编辑设置文件以添加新主机
*   测试 gunicorn 是否安装正确
*   创建 gunicorn systemd 服务文件
*   配置 Nginx 为您的 web 应用服务
*   为 SSL 证书安装 PythonCertbot，并为您的网站安装 SSL 证书

## 编辑 Django 设置文件

找到设置文件，它应该在您的 Django 项目目录中，并添加到您的域允许的主机中。查找 ALLOWED_HOSTS 并添加到现有主机:

```
ALLOWED_HOSTS = [
........
'yourdomain.com',
'www.yourdomain.com',
.........
]
```

## 测试 Gunicorn 安装正确

为了测试这一点，您需要允许 gunicorn 运行您的应用程序。运行以下命令:

```
gunicorn --bind 0.0.0.0:8000 yourproject.wsgi
```

然后为您的应用测试 IP 地址位置，端口 8000。

如果没有安装 Gunicorn，运行(在您的虚拟环境中):

```
pip install gunicorn
```

完成后，停用您的虚拟环境。

```
deactivate
```

## 创建 Gunicorn systemd 文件

基本上，systemd 文件是一个可以自动运行应用程序的系统文件。因此，你不用每次都用 gunicorn 运行上面的命令并查看你的应用程序，而是让 systemd 文件管理它。

即使服务器停机维护，如果停电，您也不必担心重启应用程序。systemd 文件会解决这个问题——如果您想了解更多细节，请阅读数字海洋的这篇文章:

[](https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files) [## 了解 Systemd 单位和单位文件|数字海洋

### Linux 发行版越来越多地采用或计划采用 systemd init 系统。这套强大的…

www.digitalocean.com](https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files) 

首先为 gunicorn 创建一个 systemd 套接字文件。

```
sudo nano /etc/systemd/system/gunicorn.socket
```

该文件的内容应该如下所示:

```
[Unit]
Description=gunicorn socket

[Socket]
ListenStream=/run/gunicorn.sock

[Install]
WantedBy=sockets.target
```

接下来，我们使用以下命令为 gunicorn 创建一个 systemd 服务文件:

```
sudo nano /etc/systemd/system/gunicorn.service
```

将此粘贴到文件中，我用粗体突出显示了需要更改的部分。用您的 droplet 用户名替换您的用户名。并确保项目根目录(manage.py 文件所在的位置)和 projectenv 文件夹的路径-toprojectdir 是正确的。这些都很重要。

```
[Unit]
Description=gunicorn daemon
Requires=gunicorn.socket
After=network.target

[Service]
User=**yourusername**
Group=www-data
WorkingDirectory=/home/**yourusername/path-to-your-projectdir**
ExecStart=/home/**yourusername**/**path-to-your-  projectdi**r/**yourprojectenv**/bin/gunicorn \
          --access-logfile - \
          --workers 3 \
          --bind unix:/run/gunicorn.sock \
          **yourproject**.wsgi:application

[Install]
WantedBy=multi-user.target
```

完成并检查文件后，可以运行以下命令来启动 systemd 文件。

```
sudo systemctl start gunicorn.socket
sudo systemctl enable gunicorn.socket
```

您可以使用以下命令检查 gunicorn 插座的状态:

```
sudo systemctl status gunicorn.socket
```

检查 systemd 状态:

```
sudo systemctl status gunicorn
```

每次对 python 文件进行更改时，如果想要添加或更新任何项目文件，则需要重新启动 systemd 文件:

```
sudo systemctl daemon-reload
sudo systemctl restart gunicorn
```

## 在 digitalocean ubuntu 20.04 上配置 Nginx 来服务您的 Django Webapp

如果您尚未安装 nginx:

```
sudo apt update
sudo apt install nginx
```

为您的项目创建新的服务器块

```
sudo nano /etc/nginx/sites-available/**yourproject**
```

在文件中，粘贴以下内容:

```
server {
    listen 80;
    listen [::]:80; server_name **yourdomain.com www.yourdomain.com;**

    location = /favicon.ico { access_log off; log_not_found off; }
    location /static/ {
        root /home/**yourusername/path-to-youprojectdir**;
    }

    location / {
        include proxy_params;
        proxy_pass http://unix:/run/gunicorn.sock;
    }
}
```

关闭文件。使用以下命令启用它:

```
sudo ln -s /etc/nginx/sites-available/**yourproject** /etc/nginx/sites-enabled
```

检查 nginx 配置，确保没有错误:

```
sudo nginx -t
```

重启 nginx:

```
sudo systemctl restart nginx
```

如果您激活了防火墙，则允许 ngix 完全访问:

```
sudo ufw allow 'Nginx Full'
```

## 从 Letsencrypt 为您的网站安装免费 SSL 证书的 Python Certbot

这部分是可选的，你可以购买一个 SSL 或者用 certbot 安装一个免费的。运行命令进行安装:

```
sudo apt-get update
sudo apt-get install python-certbot-nginx
```

然后你可以用一个命令为你的网站安装一个 SSL 证书，非常简单😃

```
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

你的网站应该使用安全 SSL 启动并运行。

# youtube 上的完整视频教程

你可以在 youtube 这里继续关注，我们最终部署了我们一直在建设的求职网站。教程从一些其他东西开始，如果你想直接跳到部署阶段，使用视频描述中的时间戳。
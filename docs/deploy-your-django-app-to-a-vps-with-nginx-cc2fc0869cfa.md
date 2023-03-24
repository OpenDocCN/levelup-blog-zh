# 使用 Nginx 将 Django 应用程序部署到 VPS

> 原文：<https://levelup.gitconnected.com/deploy-your-django-app-to-a-vps-with-nginx-cc2fc0869cfa>

毫不费力地向世界展示你的 Django 应用

![](img/e3813dab08940441df95321c81894525.png)

安德拉兹·拉济奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 我们将涵盖的内容

在本教程中，我将指导你在 VPS 上部署 Django 应用程序，并通过 Nginx 向世界开放。此外，我们还将讨论使用 GitHub Actions，通过一种称为连续交付(CD)的技术来实现自动化部署。

没有进一步的原因，让我们直接进入行动！

# **将您的 Django 应用程序移至您的 VPS**

为了部署您的 Django 应用程序，我们首先需要在我们的 VPS 本地安装它。如果你的项目托管在 GitHub 上，我们可以通过运行 VPS 轻松下载

```
**$** git clone https://github.com/***username***/***repository_name***.git
```

其中`username`是您的 GitHub 用户名，`repository_name`是您在 GitHub 上的库名。如果您的项目不在任何在线 git 存储库中，我们可以通过在本地运行上传到我们的 VPS

```
**$** scp -r ***localPath***/* ***user***@***vpsIP***:***remotePath***
```

它会用用户`user`将你机器上`localPath`的内容复制到`vpsIP`的`remotePath`。在我们的例子中，`localPath`应该是本地 Django 应用程序的路径，而`remotePath`是 vps 上您希望保存应用程序的路径。

# 安装 Nginx

一旦你的 Django 应用程序在你的 VPS 中，是时候设置 Nginx 向世界开放你的应用程序了。如果您已经安装了 Nginx，请跳到下一步。要在 vps 上安装 nginx，只需运行

```
**$** sudo apt update
**$** sudo apt install nginx
```

一旦安装了 Nginx，我们需要调整防火墙对 Nginx 的外部访问权限。由于`ufw`负责防火墙，运行

```
**$** sudo ufw allow 'Nginx HTTP'
```

这将打开端口 80，允许正常的未加密流量。要仅允许 TLS/SSL 加密流量，用`Nginx HTTPS`或`Nginx Full`替换`Nginx HTTP`以允许两者。

# 配置 Nginx

现在已经安装了 Nginx，我们可以继续添加 Django 应用程序配置文件

```
**$** sudo nano /etc/nginx/sites-available/**djangoapp**
```

其中`djangoapp`应该是你的项目名，这样以后你就可以记住这个文件的用途了。现在在文本编辑器上写下以下内容:

```
server {
    server_name **domainorip.com**;

    access_log off;

    location /static/ {
        alias **remotePath**/static;
    }

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header X-Forwarded-Host $server_name;
        proxy_set_header X-Real-IP $remote_addr;
        add_header P3P 'CP="ALL DSP COR PSAa PSDa OUR NOR ONL UNI COM NAV"';
    }
}
```

其中,`domainorip.com`应该是您的 VPS ip 地址或指向相同 ip 地址的域,`remotePath`应该是您 VPS 上 Django 应用程序的路径。这个配置文件将把所有到`domainorip`的流量重定向到 [http://127.0.0.1:8000，](http://127.0.0.1:8000,)，这应该看起来很熟悉，因为这是你的 Django 应用在 VPS 中启动的地方。如果你的 Django 应用提供静态文件，In 将从`**remotePath**/static`开始提供静态文件。

我们现在已经通知 Nginx 这个配置是可用的，但是还没有启用。为此，我们必须在刚刚创建的配置文件和`/etc/nginx/sites-enabled`之间创建一个符号链接，这可以用

```
**$** cd /etc/nginx/sites-enabled
**$** sudo ln -s ../sites-available/**djangoapp**
```

其中`djangoapp`应该是我们刚刚为 Django 应用程序创建的配置文件的名称。要确保配置文件中没有错误，请运行

```
**$ nginx -t** nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

如果你收到这两条信息，那么你就可以开始了。我们现在需要重启 nginx 来发布它的新变化:

```
**$** sudo service nginx restart
```

# 运行你的 Django 应用

剩下要做的就是运行我们的 Django 应用程序。在我们这样做之前，我们应该在我们的 VPS 上安装所有需要的依赖项，运行所有剩下的数据库迁移，并收集我们的静态文件。如果您在 Django 应用程序的`requirements.txt`中列出了您的依赖项，请导航到您的项目目录并运行

```
**$** pip3 install -r requirements.txt
```

或者简单地逐个安装您的依赖项。使用以下命令照常运行所有数据库迁移

```
**$** python3 manage.py makemigrations
**$** python3 manage.py migrate
```

要收集我们的静态文件，请编辑您的静态文件设置:

```
STATIC_ROOT = '**remotePath**/static/'

STATIC_URL = '/static/'
```

其中`remotePath`也是 VPS 上 Django 应用程序的路径。我们现在需要跑

```
**$** python manage.py collectstatic
```

一旦所有这些都完成了，我们终于可以跑了

```
**$** python3 manage.py runserver
```

开始我们的 Django 应用程序！如果您现在在本地浏览器上导航到`domainorip.com`，您应该会看到您的应用程序！

但是有一个问题，一旦你关闭当前启动服务器的会话，你的 Django 应用程序就会关闭。如果我们希望 Django 应用程序永远运行，我们必须将其设置为服务。要了解关于服务和守护进程的更多信息，请阅读下面这篇文章，在这篇文章中，我详细介绍了如何创建和维护服务。

[](https://betterprogramming.pub/unleashing-your-daemons-creating-services-on-ubuntu-731cd933e02e) [## 释放你的守护进程:在 Ubuntu 上创建服务

### 这不是黑魔法，但也够接近了

better 编程. pub](https://betterprogramming.pub/unleashing-your-daemons-creating-services-on-ubuntu-731cd933e02e) 

# 启用 HTTPS

在此步骤之前，请确保您已经注册了域名。但是首先，什么是 HTTPS，你为什么想要使用它？

HTTPS 不同于 HTTP，它的流量是通过加密来保护的，而不是通过明文传输。为了启用 HTTPS，我们需要一个 SSL 证书，感谢 certbot，我们可以免费获得它！

要安装 certbot，请运行

```
**$** sudo add-apt-repository ppa:certbot/certbot
**$** sudo apt-get update
**$** sudo apt install python-certbot-nginx
```

安装 certbot 后，我们将请求它为我们的域名生成一个 SSL 证书:

```
**$** sudo certbot --nginx -d domain.com -d [www.domain.com](http://www.example.com/)
```

这将引发一系列问题。在它们被回答之后，certbot 将会回答包含您的`domain.com`的 Nginx 配置，因此它可以支持 HTTPS。我们现在需要告诉 ufw 为 Nginx 启用 HTTPS:

```
**$** sudo ufw allow 'Nginx HTTPS'
```

# 最后的想法

恭喜你！您已经正式部署了您的 Django 应用程序，向全世界开放了它，并确保了它的流量！让我告诉你，这不是一个小壮举。

但是最有可能的是，您的应用程序之旅不会就此停止。随着 bug 的出现和新功能的加入，你的应用程序将会被修改并需要重新部署，这看起来是一个乏味的过程。但是让我告诉你这句话是多么的错误！

由于持续集成(CI)和持续交付(CD)等敏捷技术，编码和部署您的变更变得前所未有的简单！查看以下文章，了解如何使用 CD 和 GitHub 操作自动部署您的更改！

[](/make-deployment-easy-with-continuous-delivery-and-github-action-f5dde92468a1) [## 通过持续交付和 GitHub 操作简化部署

### 了解 CD 如何为您节省数百个小时

levelup.gitconnected.com](/make-deployment-easy-with-continuous-delivery-and-github-action-f5dde92468a1) 

一如既往，如果需要，请随时联系我。希望这篇教程对你有帮助。

部署愉快！

谢谢你
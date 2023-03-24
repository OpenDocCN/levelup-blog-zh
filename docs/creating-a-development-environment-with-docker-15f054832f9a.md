# 用 Docker 创建开发环境

> 原文：<https://levelup.gitconnected.com/creating-a-development-environment-with-docker-15f054832f9a>

![](img/9837112fc33f1d39060e3ff37e66f212.png)

为了创建这个开发环境，我将使用 docker-compose，以便用一个命令管理整个环境。这种环境可以在本地或远程运行，因为只需要一个 shell 连接。

将创建一个 docker-compose.yaml 文件来定义应用程序的所有系统依赖项。根据系统架构，这个文件可以用于在生产中部署，但是如果我们在生产中使用 Kubernetes 或 Aws ECS，通常这是不可能的。

在这个例子中，将使用一个简单的 Symfony 应用程序，它使用 MySql 和 Elasticseach。

[在 Ubuntu 中安装 docker](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-16-04)

# 使用 docker-compose

很有可能一个项目需要一些外部依赖，如 Mysql、MariaDB、Redis、Memcached 或 Elasticsearch。所有这些依赖关系应该在开发期间(可能在 CI/CD 期间)以简单的方式可用。

Docker-compose us 是一个 Docker 工具，允许用一个命令来构建、运行和停止几个容器。所需的配置在一个文件中，其中定义了要使用的映像、暴露的端口、环境变量…

一个例子:

```
#docker-compose -f docker/dev/docker-compose.yml up
version: '3'
services:
  blog:
    build: .
    ports:
        - 81:80
    volumes:
        - ../../:/var/www
        - ../uploads:/media/uploads
    command: /usr/sbin/apache2ctl -D FOREGROUND
    environment:
      - "DATABASE_PASSWORD=12345"
      - “DATABASE_USER=mysql_user"
      - "DATABASE_HOST=mysql"
      - "DATABASE_DB_NAME=site"
  mysql:
      image: mysql
      ports:
          - 6603:3306
      environment:
        MYSQL_ROOT_PASSWORD: "12345"
      volumes:
          - ../data:/var/lib/mysql
```

在这个文件中，有两个服务被定义为“博客”和“mysql”。Docker-compose 将在每个正在运行的容器中的/etc/hosts 中添加所需的行，以便它们可以在用作主机的服务名下互相查看。这意味着从 blog 容器 ping mysql run 将向 mysql 容器发送一个 ping。这意味着当在生产环境中运行博客时，我们可以定义一个额外的名为 mysql 的主机，指向生产 mysql 服务器，而无需更改代码。

在博客服务中，Docker 将使用位于同一目录中的 Docker 文件构建图像。

端口 80 也将暴露给映射到主机端口 81 的“外部世界”。

# 文件持久性、Docker 卷

源代码和上传将存储在两个卷中，这两个卷是安装在容器内的主机目录。这意味着在主机上更改的文件将同时在容器中更新。

当容器被销毁时，在容器文件中所做的更改将会丢失(一般来说，这是因为有一个命令可以使用在运行的容器中所做的更改来创建新的映像)。例如，在上传目录的情况下，如果它没有映射到外部卷，如果我们停止容器，所有更改都将丢失。

MySql 也是如此。如果数据目录未映射到卷，则如果容器映像被更新或被删除，所有数据都将丢失。

# 在 Docker 中运行命令

当运行命令结束时，Docker 容器“死亡”。Docker 设计为每个容器运行一个命令。当这个命令完成时，容器被停止。

在博客服务的例子中，命令块定义了将在容器中运行的命令，启动 Apache 服务器，并使其在前台监听端口 80 的请求。因此，如果 Apache 停止，容器也会停止。

# 定义环境

在这个块中，变量可以以一种不需要改变代码的方式来定义，以便将应用程序移植到另一个服务器或环境。例如，数据库连接参数是在代码之外定义的，所以如果 blog 容器映像将被部署在不同的服务器上，更改环境变量就足够了。

# 数据库

如前所述，当容器映像更新或容器死亡时，容器中的更改会丢失。如果数据库正在该容器上运行，则数据目录必须映射到卷，否则所有数据都会丢失。

用 Docker 还是不用 Docker 来运行数据库是很有主见的。根据项目类型，它可能是一个问题，也可能不是。

对于开发环境来说，这非常实用，因为我们可以运行任何运行在 linux 上的数据库，并在 Windos、MacOs、Linux 上开发我们的应用程序……只需很少的更改。

在生产环境中也是可能的，但取决于使用情形。Docker“迫使”我们制作无状态的应用，这些应用运行在随时可能死亡和旋转的容器中。这使得我们的应用程序很容易扩展，但是在数据库的情况下就不那么容易扩展或缩小了。

在我的生产环境中，我更喜欢使用更传统的数据库方法，并在外部机器上设置它。

# 运行开发环境

一旦 docker-compose.yaml 文件准备好了，就该测试它了。

运转

```
docker-compose up
```

在文件所在的同一个目录中，或者在另一个路径中

```
docker-compose -f /path/to/docker-compose.yml up
```

码头将被 docker 进程“锁定”,并将显示所有集装箱的日志。要在不锁定终端的情况下运行环境，需要在命令末尾添加-d 选项。在这种情况下，要检查日志，我们需要运行以下命令:

```
docker-compose -f /path/to/docker-compose.yml
```

或者

```
docker-compose -f /path/to/docker-compose.yml -f
```

跟踪日志并查看实时更新。

因为我们可以选择到 docker-compose 文件的路径，所以我们可以准备好不同的环境并启动我们需要的东西。

要停止环境，我们可以停止它或停止并删除它

```
docker-compose -f /path/to/docker-compose.yml stop
docker-compose -f /path/to/docker-compose.yml down
```

要查看正在运行的命令:

```
docker ps
```

得到这样一个列表:

```
69b34ec43ed1 mysql “docker-entrypoint.s…" 2 seconds ago Up 3 seconds 0.0.0.0:6603->3306/tcp mysql-container
ee022eb58b03 dev_blog “/usr/sbin/apache2ct…" 2 seconds ago Up 2 seconds 0.0.0.0:81->80/tcp blog_app
```

要在容器中运行终端来执行某些任务，或者调试它，我们可以运行:

```
docker exec -ti ee022eb58b03 bash
```

控制台提示将改变，命令将在容器内运行。要返回主机终端，我们可以键入“exit”并按 enter 键。

要访问该应用程序，我们可以在浏览器中打开 [http://localhost:81](http://localhost:81/) 。

为了访问数据库，我们可以连接到 localhost，但是使用端口 6603(在 docker-compose 文件中映射的端口)和定义的凭证。

# Dockerfile，我们的应用程序依赖于操作系统级别

Docker 文件告诉 Docker 如何构建一个映像，该映像稍后将用于运行运行我们的应用程序的容器。

要获得更多关于 Dockerfile 语法的信息，请点击这里。

```
FROM ubuntu:xenialENV DEBIAN_FRONTEND noninteractive
# php7.1 Repo
RUN apt-get update -y && apt-get install -y software-properties-common python3-software-properties && \
    LC_ALL=C.UTF-8 add-apt-repository -y ppa:ondrej/php && \
    apt-get update -y#Timezone at container level.
ENV TZ=Europe/Madrid
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone# System update and packets update
RUN apt-get install -y \
    git \
    imagemagick \
    apache2 \
    jpegoptim \
    php7.1 \
    acl \
    libapache2-mod-php7.1 \
    php7.1-curl \
    php7.1-xml \
    php7.1-mbstring \
    nodejs \
    unzip \
    npm \
    php7.1-gd \
    php7.1-intl \
    php7.1-xsl \
    php7.1-mysql# Apache and php config file
COPY apache/site.conf /etc/apache2/sites-available/site.conf
COPY php/php.ini /etc/php/7.1/apache2/php.ini# Enable apache site config file and disable default one
RUN a2ensite site.conf && \
    a2dissite 000-default.conf# Enable apache needed modules
RUN a2enmod php7.1 && \
    a2enmod rewrite && \
    a2enmod actions && \
    a2enmod deflate && \
    a2enmod expires && \
    a2enmod headers && \
    a2enmod actions && \
    a2enmod proxy# Install npm needed packages 
RUN npm install -g less && \
    npm install uglify-js -g && \
    npm install uglifycss -g# Creating symfony directorie
RUN mkdir -p /var/www/app/cache && \
    mkdir -p /var/www/app/logs && \
    mkdir -p /media/tmp && \
    mkdir -p /media/uploads# Symlink to nodejs for symfony
RUN ln -s /usr/bin/nodejs /usr/bin/node# Directory permisions 
RUN chmod -R 777 /media/tmp && \
    chmod -R 777 /media/uploads
RUN chown -R www-data:www-data /media/tmp && \
    chown -R www-data:www-data /media/uploads
```

这个 Dockerfile 用 Apache 和 Php 7.1 构建了一个基于 Ubuntu 的映像，还有一些由 app 使用的模块，Nodejs 和 assetic 用来管理 css 和 js 的另外两个模块。

在构建过程中，Apache 和 Php 配置文件被复制到映像中。体积也可以用于测试不同的值。在这种情况下，一旦改变，容器必须重新启动，但我们节省了构建映像的时间。

# 结论

使用 Docker 和 Docker-compose 创建可以移植到其他开发者并且不耦合到底层操作系统的环境是很容易的。

维护 Dockerfile 文件的不同版本(如果有的话),我们确保应用程序可以在任何环境下正常运行，而无需处理每个环境的依赖性。

这是[本帖](https://carlos-compains.medium.com/creando-un-entorno-de-desarrollo-con-docker-a56790af6271)的英文版。
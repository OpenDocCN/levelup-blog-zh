# 使用 Docker 和 Compose 构建和部署 Django 应用程序

> 原文：<https://levelup.gitconnected.com/build-and-deploy-a-django-application-using-docker-and-compose-9bf0d8dc5ebb>

![](img/d2b7d3dd59e4a9126979e359ff4c464d.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/cloud-computing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

本教程将讨论 Docker，然后在 Django 和 postgres 数据库中构建一个简单的任务管理系统，并将其部署到 Ubuntu 16 服务器中的 Docker。

# 什么是 Docker

Docker 是一个软件容器化平台，其中不同的应用程序以及它们的库和依赖项运行在不同的容器中。集装箱运输有以下好处:

*它降低了总体拥有成本。
*由于容器重量轻，因此易于维护和扩展系统。
*集装箱化带来了隔离，因此最大限度地减少了过度依赖，因为流程在不同的集装箱中运行。
* RAM 容器的正确使用只占用运行其操作所需的空间。
*易于交付代码——Docker 为从开发到生产的应用程序提供了一个标准化的环境，因此开发人员不必担心这种兼容性
*更快的集成——这意味着只需几个命令，您就可以在同一容器或不同容器中交付不同的应用程序实例。例如，您可以在不同的容器中有两个不同的 Django 应用程序，运行在不同版本的 Python 中。

# Docker 撰写

Docker Compose 是一个用于定义和运行多容器 Docker 应用程序的工具。使用用于配置应用程序服务的 YAML 文件，这是可能的。然后，只需从配置中发出一个命令，就可以创建和启动这些服务。

# Docker 图像和容器

docker 映像包含应用程序所需的软件组件的指令，容器是映像的运行实例。

## 基本 Docker 命令

下面是你应该掌握的基本 Docker 命令。

***docker PS**–我们使用这个命令列出所有正在运行的容器。
***docker PS a**–我们使用这个命令列出所有的容器(包括那些没有运行的)。
***docker images**-该命令用于列出所有图像。
***docker run[image-id]**–该命令将运行指定的图像。
***Docker pull[image]**–该命令将从 Docker 注册表中提取一个图像。例如，要获取 ubuntu 图像，我们将运行:

```
docker pull ubuntu
```

# 入门指南

在我们开始项目之前，我们需要确保我们的服务器包是最新的。更新系统软件包:

```
apt-get update
```

下一步将是安装 Docker。您可以通过发出以下命令来做到这一点。

```
apt install docker.io
```

**安装 docker-compose**

```
apt install docker-compose
```

要检查是否安装了 Docker 和 Docker Compose，请发出以下命令:

```
docker -v
Docker version 18.06.1-ce, build e68fc7a
docker-compose -v
docker-compose version 1.8.0, build unknown
```

现在我们已经安装了 Docker Compose，可以准备应用程序进行部署了。
创建一个新目录，djangoapps

```
mkdir djangoapps
```

在根目录下创建一个 requirements.txt 文件。

```
touch requirements.txt
```

然后，我们将在 Django 项目中使用的所有包添加到这个文件中:

```
#requirements.txt
Django==2.1.5
djangorestframework==3.9.1
gunicorn==19.9.0
psycopg2==2.7.7
```

**创建 Docker 文件**
Docker 文件包含构建映像的指令。在项目的根目录中创建 Dockefile。

```
cd djangoapps
touch Dockerfile
```

典型的 Dockefile 将包含以下实体:

* base image
* maintainer
*关于项目将如何存储在容器中的命令指令
*在容器内部执行的默认指令

将以下内容添加到 Docker 文件中。

```
FROM python:3
RUN mkdir /project
WORKDIR /project
COPY requirements.txt /project/
RUN pip install -r requirements.txt
COPY . /project/
```

我们在下面解释上面的命令:

* FROM python:3 —提取 Python 3 映像
* RUN mkdir/project—创建名为 project
* WORKDIR /project 的目录—将 project 设置为复制和运行命令的工作目录。
* COPY requirements . txt/project/—将需求文件复制到项目目录下
*运行 pip install -r requirements.txt —安装需求文件中的包
* COPY。/project/ —将当前目录中的内容复制到 Docker 映像中的目标项目目录

Docker 最后的要求是**docker-compose.yml**文件。这个文件包含应用程序所需的不同服务、它们的卷、如何链接它们以及它们公开的端口。例如，在我们的例子中，我们有 main 和 Postgres 服务。

创建一个名为**docker-compose.yml**的文件，并将以下内容添加到该文件中。

```
 version: ‘2’
 services:
 web:
 build: .
 command: gunicorn docker_django.wsgi:application — bind 0.0.0.0:8000
 volumes:
 — .:/project
 ports:
 — 8001:8001”
 depends_on:
 — db
 db:
 image: postgres
```

完成 Docker 配置后，我们可以开始 Django 项目了。
我们将首先安装 Django。

Django 项目将位于根目录中。如图所示创建

```
Django-admin startproject docker_django .
```

创建一个名为“任务”的新应用程序。

```
Django-admin.py startapp tasks
```

将 tasks 和 rest 框架添加到 settings.py 文件中已安装应用程序的列表中。

```
#docker-django settings.py# Application definition
INSTALLED_APPS = [
 …………..
 tasks’, # add here

 ]
```

编辑已配置的 SQLite 数据库，并使用 Postgres 数据库。

```
DATABASES = {
 ‘default’: {
 ‘ENGINE’: ‘django.db.backends.postgresql_psycopg2’,
 ‘NAME’: ‘postgres’,
 ‘USER’: ‘postgres’,
 ‘PASSWORD’: ‘postgres’,
 ‘HOST’: ‘db’,
 ‘PORT’: ‘5432

 }
 }
```

在 tasks/models.py 中，我们首先创建任务模型来存储任务细节。

```
from django.db import models
 import datetime
 import uuid
 from django.utils import timezone
 now = timezone.now# Create your models here.
class Task(models.Model):
 id = models.UUIDField(primary_key=True, default=uuid.uuid4)
 name = models.CharField(max_length=30, null=False, blank=False)
 description = models.CharField(max_length=30, null=False, blank=False)
 date = models.DateTimeField(default=timezone.now)

 class Meta:
 app_label = ‘tasks’
```

我们的应用程序现在可以部署了。转到根文件夹，发出 Docker build 命令来构建我们的映像。

```
docker-compose build
```

该命令将构建两个映像:

然后在后台启动服务。

```
docker-compose up -d
```

然后迁移数据库模型:

```
docker-compose run main python manage.py migrate
docker-compose run main Python manage.py makemigrations
```

创建超级用户

```
docker-compose main Python manage.py createsuperuser
```

## Bash 交互式 shell

或者，您可以使用 exec 命令在映像中运行 bash。首先通过发出。

```
docker container ps
 CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
 f2b050f94771 djangoapps_main “python manage.py ru…” 4 minutes ago Up 4 minutes 0.0.0.0:8001->8001/tcp djangoapps_main_1
 dc7661187cc5 postgres “docker-entrypoint.s…” 6 minutes ago Up 6 minutes 5432/tcp djangoapps_db_1
```

然后，您可以通过运行**exec**命令来执行容器中的命令。

```
docker exec -it [container_name] /bin/bash
```

在我们的案例中，它将是:

```
root@villehub:~/djangoapps# docker exec -i -t f2b050f94771 /bin/bash
 root@f2b050f94771:/project#
```

正如您在上面看到的，目录变成了

```
root@f2b050f94771:/project#
```

您还可以打开一个 shell 环境，在这里您可以执行命令，比如与数据库交互。

```
root@f2b050f94771:/project# python manage.py shell
 Python 3.7.2 (default, Dec 23 2020, 02:31:57)
 [GCC 6.3.0 20170516] on linux
 Type help”, copyright”, credits” or license” for more information.
 (InteractiveConsole)
 >>>
```

例如，要在数据库中添加任务，我们将执行以下操作:

```
>>> from tasks.models import Task
>>> task1 = Task(name=”Task1",description=”Perform task 1")
>>> task1.save()
>>> task2 = Task(name=”Task2",description=”Perform task 2")
>>> task1.save()
>>> task3 = Task(name=”Task3",description=”Perform task 3")
>>> task1.save()
```

检索所有任务

```
>>> all = Task.objects.all()
>>> all
<QuerySet [<Task: Task object (1914b174–031e-430c-b33c-43f9ce08161d)>, <Task: Task object (3510833a- bfcb-447a-8bc2–3c3bfa814559)>, <Task: Task object (6c01815a-1fe4–4381-af51–63637ab93d5b)>]>
```

# 结论

在本教程中，您了解了成功构建一个可靠的 Django 系统并将其部署到 Docker Compose 的必要条件。现在，您可以在端口 8000 导航到您的服务器的 IP 地址，您的 Django 应用程序应该已经启动并运行了。
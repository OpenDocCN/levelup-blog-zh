# 在 10 秒内设置 PostgreSQL

> 原文：<https://levelup.gitconnected.com/setup-postgresql-in-10-seconds-c22f7a1369cf>

上个月，我写了一篇关于如何在 Docker 中设置 PostgreSQL+pg admin+PostGraphile 的文章，有一种更简单的方法。

![](img/7adf8ce88febacc3398ce84f1155dab1.png)

为了启动我在前面的故事中提到的三个容器，我们将使用 docker-compose。

Compose 是一个定义和运行多容器 Docker 应用程序的工具。使用 Compose，您可以使用 YAML 文件来配置应用程序的服务。然后，只需一个命令，您就可以从您的配置中创建并启动所有服务

使用 Compose 基本上是一个三步的过程:

1.  用一个`Dockerfile`来定义你的应用环境，这样它可以在任何地方被复制，在这种情况下，我们将使用来自 [dockerhub](https://hub.docker.com/) 的图像。
2.  在`docker-compose.yml`中定义组成你的应用的服务，这样它们可以在一个隔离的环境中一起运行。
3.  运行`docker-compose up`，Compose 会启动并运行你的整个应用程序。

## 让我们开始黑吧:

*   创建一个名为`docker-compose.yml`的文件
*   在您喜欢的文本编辑器中打开该文件，然后粘贴以下代码:

在 docker-compose 中，我们正在定义我们的服务，它们具有我在上一篇文章中所写的相同配置。

*   创建一个名为`.env`的文件。该文件将包含您对容器的配置。应该是这样的:

这就是所有配置的服务！

## 来运行服务

```
# Run containers for all services in docker-compose.yml
$ docker-compose up

# Run containers as daemon (in background)
$ docker-compose up -d
```

## 重新初始化数据库

如果您通过修改`/db/init`中的文件对数据库模式进行更改，您将需要重新初始化数据库以查看这些更改。这意味着您需要删除 Docker 卷、数据库 Docker 映像并重建它。

```
# Stop running containers
$ docker-compose down# List Docker volumes
$ docker volume ls# Delete volume
$ docker volume rm <your_repository_name>_db# Delete database image to force rebuild
$ docker rmi db# Run containers (will automatically rebuild the image)
$ docker-compose up
```

## 关闭容器

```
# Shut down containers
$ docker-compose down
```

也就是说，用 pgAdmin 和 PostgreSQL 运行和部署 PostgreSQL 数据库的方法更简单了。

如果你想克隆这个库或者做一些修改，所有的代码都在我的 Github 中。
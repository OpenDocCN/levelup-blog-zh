# 不会毁了你一整天的开发环境。

> 原文：<https://levelup.gitconnected.com/a-dev-environment-that-wont-ruin-your-entire-day-cad4e268eaa3>

![](img/4eb676cd0d27393cc6d1d26de5f4f20b.png)

如果你在这里，你就会知道建立一个本地开发环境是多么的痛苦。我已经尝试了许多解决方案，但是没有一个比我在这里提出的方法更快或更容易。

## 我们将开展的工作:

docker/docker-compose
一些前端框架(React、Angular、Vue 等)
一些后端服务(Golang、Python、Node 等)
一些数据库(postgres、mysql 等)

# 启动你的引擎

从运行您的服务开始。下面是一个给出上下文的例子。

## 前端[示例]

```
$ cd some-frontend
$ npm start 
> serving localhost:3000
```

## 后端[示例]

```
$ cd some-backend
$ go run server.go
> serving localhost:8080
```

## 数据库(带 docker)[示例]

```
$ cat docker-compose.yaml
version: '3.8'
services:
  backend-db:image: postgres:14
    restart: always
    ports:
      - '5432:5432'
    environment:
      POSTGRES_USER: guest
      POSTGRES_PASSWORD: guest
      POSTGRES_DB: postgres
    volumes:
      - backend-db-data:/var/lib/postgresql/data
volumes:
  backend-db-data:
    driver: local$ docker-compose up -d
> backend-db *running*
```

## 问题是

这就是我们的问题所在:前端和后端在不同的域上。(就您的浏览器而言，localhost:3000 和 localhost:8080 是不同的域)如果您想调用不同的域，您必须处理 CORS 或相同站点配置。

此外，当您有多个后端服务时会发生什么？你如何选择不同的路径？你真的想深入一些 nginx 配置吗？呀。不用了，谢谢。

## 解决方案

有一个鲜为人知的 docker 容器叫做[小代理](https://github.com/nhumrich/small-prox)。这是我经常使用的反向代理！(充分披露，我认识作者，我对项目有贡献)

Small-prox 非常易于使用，并且支持基于主机和基于路径的路由。您还可以在容器中的服务中路由到本地端口。它也完全是用 Python 写的。

## 更新你的 docker-compose.yaml

```
version: '3.8'
services:
  smallprox:
    image: nhumrich/small-prox
    ports:
      - "80:80"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      DEBUG: "False"
      LOCAL_PORTS: "mysite.localhost=3000,mysite.localhost/api=8080"
... 
  backend-db: *# Local database instance
...* 
```

您可能需要重新启动 docker-compose:

```
$ docker-compose up -d
> backend-db *running
 * smallprox  *running*
```

然后导航到[http://mysite.localhost/](http://mysite.localhost/)，瞧！你可以开始工作了！

## 结论

我希望你能从这种模式中得到一些用处。当然还有其他方法可以做到这一点，但在我看来，没有一种方法是如此优雅。

当你准备好的时候，Small-prox 可以做更多的事情，包括 SSL 和连接你的其他容器到子域。也许我会做一个跟进，如果有人想要的话。[让我知道](https://twitter.com/alairock)！

当你这样做时，给回购一颗星和一个追随者！
# 如何在没有第三方库的情况下为 Golang 和 Docker 设置代码实时重载

> 原文：<https://levelup.gitconnected.com/how-to-live-reload-code-for-golang-and-docker-without-third-parties-ee90721ef641>

![](img/c46d2bd9f71a99ef1edc24d8814a05c8.png)

> 所描述的方法可以在 [Github](https://github.com/spalt08/go-live-reload) 上作为生产就绪的样板文件获得。

在谷歌搜索了几个小时的 Go 实时重装解决方案后，这似乎不是一个小问题。

有一堆实用工具，比如空气[或新鲜](https://github.com/cosmtrek/air)或杜松子酒可以提供这些。但是谁愿意使用第三方库来完成简单的原子任务并使项目过载呢？

我们来看看[奥列格·列别杰夫的文章](https://medium.com/@olebedev/live-code-reloading-for-golang-web-projects-in-19-lines-8b2e8777b1ea)。这是一种优雅且可配置的实时重载方法。但是如果你是 Makefiles 中的一个哑元(比如像我)，那么实现和定制它是相当困难的。

这是一个简单的分步教程，介绍如何实时重载(或热重载)Golang 代码。

## 文件监控

使用 [fswatch](https://github.com/emcrisostomo/fswatch) 实用程序，您可以处理文件更改并执行 bash 命令。它是跨平台的，甚至支持 Windows。

```
$ fswatch -or --event=Updated ./my-project | xargs -n1 -I{} <cmd>
```

只有当你保存任何源文件时，我们才会重建我们的 Go 项目。需要标志`-or`来接收单个事件并监视子目录。用户还可以使用`-e`和`-i`标志来排除和包含某些文件和目录。[这里的](https://stackoverflow.com/questions/34713278/fswatch-to-watch-only-a-certain-file-extension/37237522#37237522)就是一个例子。

## 生成文件

Makefiles 允许您描述和执行一组不同的任务。它有严格的语法和规则。

让我们在项目根目录下创建一个名为 **Makefile** 的文件。

当复制粘贴下面的代码时，你可能需要用制表符替换两个前导空格。

生成文件

## 运行 Go 并实时重新加载

编辑 Makefile 并安装 fswatch 后，您可以执行 make 任务:

```
$ make serve
```

这个解决方案可以用于任何编程语言。

## Docker 呢？

我们来做一个 Docker 的生产开发环境。

使用 Docker 的主要优点是你不需要在你的电脑上安装 Go 或者处理$GOPATH 或者项目文件的位置(如果你使用的是 macOS 的话这是相当不舒服的)。

你可以[在官网下载](https://www.docker.com/products/docker-desktop) Docker。

让我们创建一个名为 **Dockerfile** 的文件:

Dockerfile 文件

然后，创建一个名为 **docker-compose.yml** 的文件:

*复制粘贴下面的代码时，您可能需要用制表符替换两个前导空格。*

docker-compose.yml

现在我们有两个 docker 容器:`production`和`development`。开发人员开始执行任务，并实时重新加载我们的 Go 代码。

您可以在任何云实例或服务器上使用适当的容器部署一个**生产版本**。只需使用 docker-compose up 命令:

```
$ docker-compose up -d production*// For monitoring production logs use:*
$ docker-compose logs --tail=100 -f production*// For bash access to production container use:*
$ docker-compose exec production sh
```

对于**开发**过程，只需使用适当的容器:

```
$ docker-compose up development
```

源代码可以在 [Github](https://github.com/spalt08/go-live-reload) 上作为生产就绪的样板文件获得。
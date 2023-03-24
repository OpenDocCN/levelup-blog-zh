# 又一个 TODO 应用！使用 Redis 和 Go(第 1 部分)

> 原文：<https://levelup.gitconnected.com/yet-another-todo-app-with-redis-and-go-part-1-7a1134f5752e>

## 通过使用 Go 构建 CLI 应用程序来学习 Redis

![](img/470552d9bed7c224ecc0f07f9ecdd3b2.png)

格伦·卡斯滕斯-彼得斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

[Redis](https://redis.io/) 是一个多功能的键值存储，支持多种[丰富的数据结构](https://redis.io/commands/)，如`List`、`Set`、`Sorted Set`、`Hash`等。——这意味着价值观可以不仅仅是简单的`string`。这些数据结构结合在一起，可以用于构建强大的应用程序，包括缓存(Redis 的基础)、实时处理、分布式工作队列、排行榜、会话存储等。

这个*两部分的博客系列*将借助一个简单而实用的例子讲述一些 Redis 数据结构:*又一个* **TODO 应用**！😉而不是构建一个基于 web 的前端界面(老实说，这真的不是我喜欢的！)我选择保持简单，提供一个命令行界面(CLI)。它内置于`[Go](https://golang.org/)`中，带有流行的用于 CLI 应用的`[cobra](https://github.com/spf13/cobra)`库，并使用`Redis`作为后端存储。

通过阅读本系列，您将了解到:

*   通用的`Redis`数据结构——这个应用程序利用了`HASH`和`SET`
*   使用`[go-redis](https://github.com/go-redis/redis)`客户端与 Redis 交互
*   如何使用`cobra`库(它也支持 CLI 工具，如`docker`、`kubectl`等)。)
*   你可以试用有几个不同选项的`todo`应用程序:`Docker`或 [Azure Redis 缓存](https://docs.microsoft.com/azure/azure-cache-for-redis/?WT.mc_id=medium-blog-abhishgu)

为了简单起见，我把它分成了两部分。第一部分(这个博客)将提供一个应用程序，安装和设置(包括`Azure Redis Cache`)的快速概述，并尝试它。第二部分将深入研究代码和实现细节

> *代码为* [*可在 Github*](https://github.com/abhirockzz/redis-todo-cli-app) 上获得

# `todo`应用概述

下面是您可以使用`todo` CLI 应用程序做些什么的快速概述。使用`todo --help`很容易解决这个问题:输出是不言自明的:

```
Yet another TODO app. Uses Go and RedisUsage:
  todo [command]Available Commands:
  create      create a todo with description
  delete      delete a todo
  help        Help about any command
  list        list all todos
  update      update todo description, status or bothFlags:
  -h, --help      help for todo
  -v, --version   version for todoUse "todo [command] --help" for more information about a command.
```

可以`create`、`list`、`update`和`delete` todos(好老的 CRUD！)

*   要`create` a `todo`，只需输入`description` -详情请使用`todo create --help`
*   要`list`所有待办事项，只需使用`todo list`。也可以通过`status`(已完成或未完成)进行过滤——详情请使用`todo list --help`

以下是来自`todo list`的输出:

```
+----+--------------------------------+-------------+
| ID |          DESCRIPTION           |   STATUS    |
+----+--------------------------------+-------------+
|  3 | push redis todo cli app code   | pending     |
|    | to github                      |             |
|  2 | write tutorial for redis todo  | in-progress |
|    | cli app                        |             |
|  1 | implement todo cli app         | completed   |
+----+--------------------------------+-------------+
```

*   您可以通过指定 todo `id`来`update`一个`todo`的`description`、`status`(或两者都有)——详细信息，请使用`todo update --help`
*   对于`delete`待办事项，只需使用`todo delete`和它的`id`即可——详情请使用`todo delete --help`

# `todo`应用程序安装

> *我假设你已经安装了*`[*Go*](https://golang.org/)`

*从克隆回购开始:*

```
*git clone https://github.com/abhirockzz/redis-todo-cli-app
cd redis-todo-cli-app*
```

*构建应用程序并设置`PATH`变量*

```
*go build -o todo
export PATH=$(pwd):$PATH*
```

*要确认，使用`todo --help`*

*在使用该应用程序之前，设置一个 Redis 实例*

# *Redis 设置*

*`todo`存储在`Redis`中。获得`Redis`实例的最快方法之一是提取它的 Docker 映像并开始使用它:*

```
*docker pull redis
docker run -d --name todo_redis -p 6379:6379 redis*
```

*现在你应该有 Redis Docker 容器在后台运行，并且应该可以通过端口`6379`访问*

> **要删除容器(不是图像)，只需使用* `*docker rm -f todo_redis*`*

*如果你使用基于 Docker 的设置，你可以跳过剩下的部分，直接进入下一个(最后的)部分，因为这个应用程序使用`localhost:6379`作为默认的 Redis 主机*

*如果您已经想覆盖它，只需设置`REDIS_HOST`环境变量，例如*

```
*export REDIS_HOST=myredis:6379*
```

*如果您的 Redis 实例受密码保护，只需将`REDIS_PASSWORD_REQUIRED`环境变量设置为`true`并使用`REDIS_PASSWORD`环境变量来指定密码，例如*

```
*export REDIS_HOST=my-secure-redis:6379
export REDIS_PASSWORD_REQUIRED=true
export REDIS_PASSWORD=foobarredis*
```

## *Azure Redis 缓存*

*[Azure Cache for Redis 是云中的托管产品](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview?WT.mc_id=medium-blog-abhishgu)。它提供对托管在 Azure 中的安全专用 Redis 缓存的访问，Azure 内外的任何应用程序都可以访问该缓存。*

*出于这篇博客的目的，我们将设置一个带有`Basic`层的 Azure Redis 缓存，这是一个非常适合开发/测试和非关键工作负载的单节点缓存。请注意，您还可以从`Standard`和`Premium`层中选择[提供不同的特性，包括持久性、集群、地理复制等](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview?WT.mc_id=medium-blog-abhishgu#feature-comparison)。*

*您可以使用 Azure CLI 通过`[az redis create](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest&WT.mc_id=medium-blog-abhishgu#az-redis-create)`命令快速设置 Azure Redis 缓存实例，例如*

```
*az redis create --location westus2 --name todo-redis --resource-group todo-app-group --sku Basic --vm-size c0*
```

> **结帐* [*“为 Redis 创建 Azure 缓存”*](https://docs.microsoft.com/azure/azure-cache-for-redis/scripts/create-cache?WT.mc_id=medium-blog-abhishgu) *获取分步指南**

*完成后，您需要获取连接到 Azure Redis 缓存实例所需的信息，即主机、端口和访问密钥。这也可以使用 CLI 来完成，例如*

```
*//host and (SSL) port
az redis show --name todo-redis --resource-group todo-app-group --query [hostName,sslPort] --output tsv//primary access key
az redis list-keys --name todo-redis --resource-group todo-app-group --query [primaryKey] --output tsv*
```

> **Checkout* [*“获取 Redis 的 Azure 缓存的主机名、端口和密钥”*](https://docs.microsoft.com/azure/azure-cache-for-redis/scripts/cache-keys-ports?WT.mc_id=medium-blog-abhishgu) *获取分步指南**

*好了，现在您可以用刚刚提取的信息设置所需的环境变量，例如*

```
*//todo-redis is the name of the cache
export REDIS_HOST=todo-redis.redis.cache.windows.net:6380
export REDIS_PASSWORD_REQUIRED=true
export REDIS_PASSWORD=[primary access key]
export REDIS_SSL_REQUIRED=true*
```

> **应用程序使用 TLS 1.2 通过 SSL 连接到 Redis—*[*这是推荐的方法*](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-remove-tls-10-11?WT.mc_id=medium-blog-abhishgu)*

*你应该已经准备好试用这个应用程序了*

# *一些 todos！*

*首先创建一个`todo`*

```
*todo create --description "write the todo cli app"*
```

*您应该会看到这个响应*

```
*created todo! use 'todo list' to get your todos*
```

*多创造一些*

```
*todo create --description "write the tutorial for redis cli app using go"
todo create --description "push cli app code to github"
todo create --description "get some sleep"*
```

*用`todo list`列出你所有的`todo`。您应该看到:*

```
*+----+--------------------------------+---------+
| ID |          DESCRIPTION           | STATUS  |
+----+--------------------------------+---------+
|  3 | push cli app code to github    | pending |
|  2 | write the tutorial for redis   | pending |
|    | cli app using go               |         |
|  1 | write the todo cli app         | pending |
|  4 | get some sleep                 | pending |
+----+--------------------------------+---------+*
```

*更新几个`todo`年代*

```
*todo update --id 1 --status completed
todo update --id 2 --status in-progress
todo update --id 4 --description "get some sleep NOW"*
```

*列出`todo`应该会给你更新的信息:*

```
*+----+--------------------------------+-------------+
| ID |          DESCRIPTION           |   STATUS    |
+----+--------------------------------+-------------+
|  3 | push cli app code to github    | pending     |
|  2 | write the tutorial for redis   | in-progress |
|    | cli app using go               |             |
|  1 | write the todo cli app         | completed   |
|  4 | get some sleep NOW             | pending     |
+----+--------------------------------+-------------+*
```

*仅列出`pending` `todo` s:*

```
*todo list --status pending*
```

*您应该得到:*

```
*+----+-----------------------------+---------+
| ID |         DESCRIPTION         | STATUS  |
+----+-----------------------------+---------+
|  3 | push cli app code to github | pending |
|  4 | get some sleep NOW          | pending |
+----+-----------------------------+---------+*
```

*先删了`todo` #4(谁需要睡觉！？)*

```
*todo delete --id 4*
```

*不再需要睡眠！(根据`todo list`*

```
*+----+--------------------------------+-------------+
| ID |          DESCRIPTION           |   STATUS    |
+----+--------------------------------+-------------+
|  3 | push cli app code to github    | pending     |
|  2 | write the tutorial for redis   | in-progress |
|    | cli app using go               |             |
|  1 | write the todo cli app         | completed   |
+----+--------------------------------+-------------+*
```

## *不要忘记检查 Redis*

*让我们通过查看 Redis 数据结构来确认一下。为此，您可以使用`[redis-cli](https://redis.io/topics/rediscli)`。如果你使用 Azure Redis 缓存，我会推荐使用一个非常方便的基于 web 的 Redis 控制台*

*让我们检查已经创建的数据结构。我们使用`todo`来命名数据结构(实现细节在下一篇博文中)，所以我们可以使用`[SCAN](https://redis.io/commands/scan)`来使用这个模式*

```
*SCAN 0 COUNT 1000 MATCH todo**
```

> **出于性能考虑* `[*KEYS*](https://redis.io/commands/keys)` *命令被禁止**

*您将看到以下输出:*

```
*1) "0"
2) 1) "todos-id-set"
   2) "todo:1"
   3) "todo:2"
   4) "todo:3"
   5) "todoid"*
```

*`todos-id-set`和`todoid`分别对应`SET`和`STRING`(计数器)数据类型。`todo:1`、`todo:2`和`todo:3`是包含`todo`细节的`HASH`数据类型。让我们看看`todo:1`(即`todo`与`id` 1)*

```
*HGETALL todo:1*
```

*您应该会得到以下响应:*

```
*1) "status"
2) "pending"
3) "desc"
4) "write the todo cli app"*
```

> **作为练习，确认其他* `*todo*` *s，同时查看* `*SET*` *和* `*STRING*` *计数器数据类型**

*好吧！这篇博文到此结束。请继续关注第二部分，我们将探索代码并了解事情是如何工作的。*
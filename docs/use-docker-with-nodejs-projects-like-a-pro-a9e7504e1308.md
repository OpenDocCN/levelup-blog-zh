# 像专业人士一样使用 Docker 和 NodeJS 项目！

> 原文：<https://levelup.gitconnected.com/use-docker-with-nodejs-projects-like-a-pro-a9e7504e1308>

## 在开发和生产中利用 Docker 的力量

![](img/f2e819d3303457da491ba3c219b6f167.png)

由 [Avel Chuklanov](https://unsplash.com/es/@chuklanov?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/digital?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

Docker 是一项非常强大的技术，可以在许多方面让我们的生活变得更加轻松。

今天我们将对 NodeJS(Express)应用程序进行 Dockerize。我们将看到如何使用 docker 进行本地开发，这样您就不需要担心每个项目的 nodejs 版本。

我们将假设已经有一个 NodeJS 应用程序启动并运行。如果您想了解我们是如何构建 express 应用程序的，请阅读下面的文章。

[](https://www.mohammadfaisal.dev/blog/create-express-typescript-boilerplate) [## 创建-快递-打字稿-样板文章|穆罕默德·费萨尔

### ExpressJS 是 NodeJS 应用程序最流行的框架。它非常通用，没有任何限制。所以你…

www.mohammadfaisal.dev](https://www.mohammadfaisal.dev/blog/create-express-typescript-boilerplate) 

## 在我们开始之前…

我用 ExpressJS 和 Typescript 创建了一个[专业样板。本文是这个系列的一部分。你可以在下面找到所有的文章。](https://github.com/Mohammad-Faisal/professional-express-sequelize-docker-boilerplate)

```
***** [**Creating a ExpressJS + Typescript Boilerplate**](https://javascript.plainenglish.io/create-an-express-boilerplate-with-typescript-810eb6c29196) ***** [**How to setup Linter and Formatter for NodeJS**](https://javascript.plainenglish.io/how-to-set-up-linter-formatter-for-node-js-d6b34c0c8be5) ***** [**How to handle multiple environments in NodeJS**](https://blog.devgenius.io/how-to-handle-multiple-environments-in-nodejs-7391d2db2abe) ***** [**Error Handling in NodeJS**](https://javascript.plainenglish.io/error-handling-in-node-js-like-a-pro-ed210baa0600) ***** [**Request Validation in NodeJS**](https://medium.com/p/c69f2494cf18) ***** [**Using Docker Professionally with NodeJS**](/use-docker-with-nodejs-projects-like-a-pro-a9e7504e1308) ***** [**Using Docker for Local Development in NodeJS**](https://javascript.plainenglish.io/node-js-database-with-docker-for-local-development-285212c5162f) ***** [**Logging in NodeJS**](https://javascript.plainenglish.io/node-js-logging-for-professionals-6be07c003e7f) ***** [**Kubernetes with NodeJS**](/kubernetes-deployment-with-nodejs-made-easy-eaeec32b62e3)
```

我们开始吧！

# 获取 NodeJS 样板文件

首先，克隆 [express 样板库](https://github.com/Mohammad-Faisal/express-typescript-skeleton)，在这里我们有一个已经设置了 Typescript、EsLint 和 Prettier 的 express 应用程序。

我们将在此基础上集成 Docker。

```
git clone [https://github.com/Mohammad-Faisal/express-typescript-skeleton.git](https://github.com/Mohammad-Faisal/express-typescript-skeleton.git)
```

# 为什么是 Docker？

我很高兴你问了。Docker 可以抽象出大多数与环境相关的错误，因为 Docker 可以确保您的应用程序在您的机器和世界上任何其他机器上都是相同的。

假设您的机器安装了 NodeJS 14。但是您得到了一个使用 NodeJS 12 的应用程序。那你是做什么的？安装 Node 的两个版本？是的，使用 NVM 等工具可以做到这一点。

但是如果你把你的应用程序 dockerize，你不需要任何额外的负担在你的机器上。您可以在 docker 容器中运行任何版本的 NodeJS。

我希望你能了解它的好处。让我们直接进入代码吧！

# 安装 Docker

如果您没有安装 Docker，请转到[下面的链接](https://docs.docker.com/engine/install/)并遵循安装步骤。

然后在项目的根目录下创建 Dockerfile。

```
touch Dockerfile
```

# 定义环境

让我们打开`Dockerfile`并开始为 Docker 构建配置。

我们需要做的第一件事是定义环境。对于我们的应用程序，我们将使用 NodeJS 16。你可以在 [Docker Hub](https://hub.docker.com/_/node) 上看到可用版本

```
FROM node:16
```

但是大多数时候，我们并不需要整个 NodeJS 环境。我们可以使用轻量级的替代方案。

```
FROM node:lts-alpine
```

通常，alpine 版本是轻量级的，但它为您的应用程序提供了足够的功能。

# 创建工作目录

docker 映像将有一个单独的目录结构。所以我们需要在图像中的某个地方保存我们的应用程序代码。

让我们为我们的应用程序创建一个目录。

```
WORKDIR /usr/src/app
```

# 安装依赖项

我们在顶部定义的映像已经安装了 NodeJS 和 NPM，所以我们不必担心这个问题。接下来我们需要将我们的`package.json`文件复制到我们刚刚在上面定义的工作目录中。

```
# we are using a wildcard to ensure that both package.json and 
# package-lock.json file into our work directoryCOPY package*.json ./
```

然后安装依赖项。我们可以利用 Docker 的 RUN 命令。

```
RUN npm install
```

如果你想为生产构建代码，我们可以使用下面的命令。[关于 npm ci](https://blog.npmjs.org/post/171556855892/introducing-npm-ci-for-faster-more-reliable) 命令的解释可以在这里找到。

```
RUN npm ci --only=production
```

# 复制应用程序代码

您可能想知道为什么我们没有在前面的步骤中将应用程序代码复制到工作目录中。这是因为 Docker 为每一层创建了一个中间图像。并且为每个命令创建一个层。

由于我们在前面的步骤中不需要应用程序代码，这将是一种空间浪费，如果我们在前面的步骤中复制整个代码，这个过程的效率会更低。

如果你有兴趣了解更多。[去这里](https://bitjudo.com/blog/2014/03/13/building-efficient-dockerfiles-node-dot-js/)

我们现在就去拿密码。

```
COPY . .
```

现在让我们公开内部应用程序端口。因为默认情况下，出于安全原因，Docker 映像不会暴露任何端口。

```
EXPOSE 3000
```

# 运行应用程序

现在我们有了工作目录中的所有代码。现在让我们构建我们的应用程序。

```
RUN npm build
```

现在我们将定义运行服务器的命令。

当我们在 dist 文件夹(在 tsconfig.json 文件中定义)中构建包时，我们必须运行构建的映像。

```
CMD [ "node", "dist/index.js" ]
```

请注意，我们没有使用`npm start`来运行应用程序，因为使用 Node 运行会提高效率。其次，它会导致退出信号(如 SIGTERM 和 SIGINT)被 Node.js 进程接收，而不是被 npm 接收。

# 最终文档文件

现在，您的 docker 文件应该如下所示:

Dockerfile 文件

# 创建一个 dockerignore 文件

我们可以通过避免将一些资源复制到应用程序中来进一步优化 Docker 映像。创建一个`.dockerignore`文件，并将下面的代码粘贴到那里

dockerignore

# 建立我们的形象

让我们通过运行以下命令来构建我们的映像。

```
docker build . -t express-typescript-docker
```

注意命令中的`.`。这不是偶然的。此外，我们通过传递`-t`选项来标记我们的应用程序

您可以通过运行以下命令来查看构建的映像。

```
docker images
```

这会给我们这样的输出

```
REPOSITORY                TAG      IMAGE ID     CREATED    SIZE
express-typescript-docker latest  11b3fd2ab6d4 29 seconds  262MB
```

# 运行我们的图像

让我们通过运行以下命令在容器中运行我们的图像。

```
docker run -p 3000:3000 -d express-typescript-docker:latest
```

注意`-p`选项，它将暴露的 docker 端口映射到我们的本地机器端口。在我们的 docker 文件中，我们公开了端口 3000。我们将同一个端口映射到我们的机器上。

如果我们想在本地机器的端口 4000 上运行我们的应用程序，我们可以这样做。`-p 4000:3000`

`You can also pass environment variables into the container by passing `-e "NODE_ENV=production"` into the command.`

# 查看运行中的容器。

运行以下命令。

```
 docker ps
```

这将为您提供以下输出

```
CONTAINER ID   IMAGE  COMMAND  CREATED STATUS PORTS   NAMES

9be24fc778bb   
express-typescript-docker:latest   
"docker-entrypoint.s…"   
8 seconds ago   
Up 7 seconds   
0.0.0.0:3000->3000/tcp, 
8080/tcp   
eager_bardeen
```

# 测试端点

现在进入你的浏览器或者像 Postman 这样的 HTTP 客户端，点击下面的 URL。

```
[http://localhost:3000](http://localhost:3000)
```

看看结果

```
{ "message": "Hello World!" }
```

此外，您可以通过运行以下命令来查看容器内部的日志。从我们之前运行的命令`docker ps`中获取容器 id。

```
docker logs container_idConnected successfully on port 3000
{ message: 'Hello World'}
```

# 停下集装箱。

您可以通过运行以下命令来停止正在运行的容器。

```
docker stop container_id
```

并检查`docker ps`以查看该命令是否成功。

# 使用 Docker 的开发环境

以上配置可用于生产。但是在开发过程中，每当我们更改代码时，我们都不能构建映像。我们需要一些开发设置来即时反映我们的代码更改，以解决这个问题。

为此，我们可以创建一个单独的`Dockerfile.dev`并添加以下配置

让我们在根目录下创建一个`docker-compose.yml`文件来实现这一点。并在那里添加以下配置

注意我们指向`Dockerfile.dev`来告诉 docker-compose 需要使用的文件。

它将创建一个名为 express-typescript-docker 的图像，并在开发模式下运行它。

我们可以运行以下命令来查看它的运行情况:

```
docker-compose up
```

现在，您可以尝试更改任何代码并点击保存。您的代码将自动更新！

您可以通过按 CTRL+C 或运行以下命令来停止容器。

```
docker-compose down
```

# 在生产中使用 Docker 排版

我们在生产中也可以使用`**docker-compose**` 。为此，让我们创建一个单独的`**Dockerfile.prod**`

Dockerfile.prod

注意，这里我们不像以前那样需要`EXPOSE`和`CMD`命令，因为 docker-compose 会处理这些。

现在让我们创建一个`docker-compose.prod.yml`文件。

`docker-compose.prod.yml`

这将从`Dockerfile.prod`获取配置，并从那里运行命令节点 dist/index.js。其余配置将取自默认的`docker-compose.yml`文件。

要在生产中启动容器，让我们运行以下命令。

```
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up -d
```

注意`-f`标志，它告诉`docker-compose`哪些文件用于配置。

您可以通过运行以下命令来验证容器是否正在运行。

```
docker ps
```

或者点击 [http://localhost:3000](http://localhost:3000/)

# 丰富

键入所有这些命令可能会很耗时，所以让我们在项目的根目录下创建一个 Makefile 文件来简化我们的工作！

```
up:
    docker-compose up -dup-prod:
    docker-compose -f docker-compose.yml -f docker-compose.prod.ymldown:
    docker-compose down
```

现在我们可以运行`make up`或`make up-prod`来运行容器。

你可以在这里找到这篇文章的所有代码。

## 更进一步

我们可以利用 docker-compose 的强大功能，在本地开发工作流中使用数据库，从而将这个设置提升到下一个级别。更多信息请见下面的文章。

[](https://javascript.plainenglish.io/node-js-database-with-docker-for-local-development-285212c5162f) [## Node.js +带有 Docker 的数据库，用于本地开发

### 各种数据库的一站式解决方案。

javascript.plainenglish.io](https://javascript.plainenglish.io/node-js-database-with-docker-for-local-development-285212c5162f) 

## 视频版本:

[https://www.youtube.com/watch?v=9yCk2R3injE&t = 362s](https://www.youtube.com/watch?v=9yCk2R3injE&t=362s)
[https://www.youtube.com/watch?v=nawJwaPW1yI&t = 14s](https://www.youtube.com/watch?v=nawJwaPW1yI&t=14s)

```
**Want to Connect?**You can reach out to me via [**LinkedIN**](https://www.linkedin.com/in/56faisal/) or my [**Personal Website**](https://www.mohammadfaisal.dev/blog)
```

# 资源

*   [Docker Nodejs 最佳实践](https://github.com/nodejs/docker-node/blob/main/docs/BestPractices.md)
*   [建立 Docker 的官方节点文档](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/)

## 阅读更多 NodeJS 文章

[](https://javascript.plainenglish.io/error-handling-in-node-js-like-a-pro-ed210baa0600) [## 像专家一样处理 Node.js 中的错误

### 你需要知道的一切。

javascript.plainenglish.io](https://javascript.plainenglish.io/error-handling-in-node-js-like-a-pro-ed210baa0600) [](https://javascript.plainenglish.io/node-js-logging-for-professionals-6be07c003e7f) [## 专业人员的 Node.js 日志记录

### 发挥温斯顿和摩根的全部潜力

javascript.plainenglish.io](https://javascript.plainenglish.io/node-js-logging-for-professionals-6be07c003e7f) [](https://blog.devgenius.io/how-to-handle-multiple-environments-in-nodejs-7391d2db2abe) [## 如何在 NodeJS 中处理多种环境

### 如何建立一个专业的节点工程

blog.devgenius.io](https://blog.devgenius.io/how-to-handle-multiple-environments-in-nodejs-7391d2db2abe) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份惊艳的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)
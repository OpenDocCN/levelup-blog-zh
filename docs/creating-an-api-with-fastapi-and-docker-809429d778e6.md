# 用 FastAPI 和 Docker 创建 API

> 原文：<https://levelup.gitconnected.com/creating-an-api-with-fastapi-and-docker-809429d778e6>

![](img/7306588af9dfc7b7f4e559b2a49ff9ba.png)

> 本教程基于大约 2020 年我们在 [LifeMetrics](http://lifemetrics.io) 建立的东西

部署一个生产就绪的 API 服务器通常是很麻烦的，但也不尽然。大约只需要 15 分钟！

FastAPI 是一个令人惊叹的 python 框架，它使编写 API 变得简单，但也非常快！我们将把它构建在 docker 容器中，以实现提供者之间最大的可移植性，以及在 Kubernetes 部署中的潜在用途。

# 设置项目

我们将使用[诗歌](https://github.com/python-poetry/poetry)来处理我们所有的依赖关系(这让人想起 Node.js 世界中的 npm)。对于开发，我们将使用推荐的安装诗歌的方式:

```
curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python
```

**注意:**在这一点上，你可能需要在你的道路上添加诗歌:

```
export PATH=$PATH:$HOME/.poetry/bin
```

接下来，我们将初始化我们的项目目录。这里我们制作了一个名为“orangutan”的 API，让我们像这样设置我们的目录:

```
**orangutan**
    - **orangutan**
        - server.py
```

让我们有一个非常基本的服务器(学习如何制作一个更复杂/完整的 API 查看[官方 FastAPI 教程](https://fastapi.tiangolo.com/tutorial/first-steps/)):

```
"""An API about orangutans"""
from fastapi import FastAPIapp = FastAPI()[@app](http://twitter.com/app).get("/")
async def root():
    return { "message": "The orangutans are three extant species of great apes native to Indonesia and Malaysia." }
```

我们需要实际安装我们的 fastapi 依赖项以及任何附加的依赖项。让我们设置诗歌:

```
poetry init
```

在这个初始化过程中，会要求您添加依赖项，但是您也可以在之后使用:

```
poetry add package_name
```

我们需要确保添加我们的 fastapi 依赖项，让我们也向我们的 Orangutan API 添加一些公共包:

```
poetry add fastapi
poetry add requests
poetry add pymongo
poetry add -D black --allow-prereleases
poetry add -D pytest
poetry add -D flake8
```

**注意:**`-D`标志将一个依赖项标记为 dev-dependency，我们可以安全地将其从生产部署中删除。添加 black 时需要使用`--allow-prereleases`标志，因为从技术上来说，它并没有发布，只需要将它添加到 black 依赖项中。

我们的文件夹结构现在应该是这样的:

```
**orangutan**
    - **orangutan**
        - server.py
    - pyproject.toml
    - poetry.lock
```

# 设置 Docker

我们将把我们的 API 打包到 docker 容器中用于生产。确保您已经在本地机器上安装了 docker。然后，我们可以从在我们的基本 orangutan 目录中添加 Dockerfile 开始:

```
FROM tiangolo/uvicorn-gunicorn-fastapi:python3.7# set path to our python api file
ENV MODULE_NAME="orangutan.server"# copy contents of project into docker
COPY ./ /app# install poetry
RUN pip install poetry# disable virtualenv for peotry
RUN poetry config virtualenvs.create false# install dependencies
RUN poetry install
```

我们可以使用伟大的[uvicon-guni corn-fastapi-docker](https://github.com/tiangolo/uvicorn-gunicorn-fastapi-docker)docker 镜像，因为它通过合理的缺省值为我们做了很多工作。

>*这个映像使用 gunicorn，它在 uvicon 的基础上增加了负载平衡，但是如果我们将我们的容器作为负载平衡 kubernetes 部署的一部分来部署，我们可以使用一个只包含 uvicon 的 docker 映像，并让我们的容器编排复制来管理我们的负载平衡，这在生产环境中是很好的。感谢指出这一点* [更改 docker 容器的 ENV 以反映其名称](https://medium.com/u/f1a1aed26c53#variable_name)。

让我们试着从我们的基本目录开始构建我们的映像吧！

```
docker build -t orangutan ./
```

这应该已经成功完成了，接下来让我们在本地机器上启动容器以确保它能够工作:

```
docker run -d --name orangutan0 -p 80:80 orangutan
```

`-d`标志将我们的容器作为守护进程启动，`-name`标志命名我们的容器，`-p`标志发布一个端口供 docker 之外的人访问。我们现在应该有一个运行在本地机器上的容器`orangutan0`，我们可以通过 [http://127.0.0.1/](http://127.0.0.1/) 🥳访问它

# 部署到服务器

我们将使用数字 Ocean droplet 来部署我们的 API，但是您可以将它部署在任何可以运行 docker 容器的地方！这些是步骤:

1.  将我们的 docker 图像推送到 Docker Hub
2.  创建一个液滴
3.  ssh 进入水滴
4.  将我们的图像下载到我们的 droplet 上
5.  发布我们的猩猩 API🚀

因此，让我们创建一个存储库，并将我们的映像推送到 Docker Hub 上(Docker 映像的默认托管存储库，您必须拥有一个帐户并登录)。

```
docker tag orangutan:latest orangutan:latest
docker push dockerhub_username/orangutan:latest
```

**注意:**这将创建一个公共回购。我们很可能不希望这种情况出现在生产中，但是你可以在 https://hub.docker.com/repositories 改变这种情况。

现在我们的图像已经公开了，让我们旋转我们的数字海洋水滴(这只是一个 vps)。我们将使用 rancherOS，因为它是专门为托管容器而设计的，并且非常轻量级。我们并不真的需要一个完整的操作系统，因为我们只是在运行我们的容器。

一旦你创造了一个牧场主的小滴。让我们用 ssh 登录 rancher 用户(您需要提供一个与 digitalocean 相关的 ssh 密钥来创建 droplet)。

```
ssh rancher@your_droplet_ip
```

在那里，我们需要执行几个命令来拉取和启动我们的映像:

```
docker logindocker pull dockerhub_username/orangutandocker run -d --name orangutan0 -p 80:80 dockerhub_username/orangutan
```

现在让我们尝试访问我们的水滴的 ip！🚀我们已经部署了我们的 api！

现在我们的工作流程看起来像这样:

1.  在我们的本地机器上开发
2.  建立并推广我们新改变的码头工人形象
3.  将最新版本放到我们的 droplet 上并重新发布

对于真实的生产环境，我们还需要设置一些东西:

1.  负载平衡器后的冗余液滴(或使用负载平衡器的 kubernetes)
2.  HTTPS 支持
3.  将私人秘密注入 ENV
4.  当我们发布新版本时，自动重新部署我们的容器

我打算在以后的教程中讨论这些，但是我们已经从 fastAPI 中转移了技术栈，所以我不打算写更多的内容。
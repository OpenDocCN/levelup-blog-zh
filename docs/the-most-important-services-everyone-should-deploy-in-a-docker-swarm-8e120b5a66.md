# 每个人都应该在 Docker 群中部署的 4 项重要服务

> 原文：<https://levelup.gitconnected.com/the-most-important-services-everyone-should-deploy-in-a-docker-swarm-8e120b5a66>

## 你需要这些服务！

## 学习如何用四个你会喜欢的重要服务来增强你的 Docker 群。

![](img/a5661c77236c0547027876760ef4d276.png)

在我的上一篇文章中，我展示了如何在大约 15 分钟内建立一个 Docker 群。记住:每当你阅读“码头工人群体”时，我们都在谈论“码头工人群体模式”

在本文中，我将展示并解释每个人都应该在 Docker Swarm 中使用的四个服务: **traefik** 、 **Portainer** 、 **Docker-Registry** 、 **FTP** 。

# 特拉菲克

> *让网络变得乏味
> 云原生网络堆栈刚刚开始工作。*

任何 Docker 群中的一个重要服务是 traefik。我用这个为每个 docker 服务分配域/子域。一个简单的 docker-compose.yml 可以在我的 FTP [这里](https://ftp.f1nalboss.de/data/docker-compose.traefik.yml)找到。要使用该文件，有必要通过执行以下命令来更新 docker 群组的标签:

```
$> export NODE_ID=$(docker info -f '{{.Swarm.NodeID}}')
$> docker node update --label-add traefik-public.traefik-public-certificates=true $NODE_ID
```

这两个命令假设您希望在 manager 节点上安装 traefik！要了解什么是管理器节点[看看 docker 文档](https://docs.docker.com/engine/swarm/how-swarm-mode-works/nodes/)。

另一个**非常**重要的步骤是在文件夹中创建一个名为`acme.json`的文件，docker-compose 将存储在该文件夹中，并执行以下操作:

```
$> chmod 600 acme.json
```

此外，您还必须定义一个`TRAEFIK_USERNAME`、`TRAEFIK_HASHED_PASSWORD`、`TRAEFIK_DOMAIN`和`TRAEFIK_SSLEMAIL`:

```
$> export TRAEFIK_USERNAME=admin
$> export TRAEFIK_HASHED_PASSWORD=$(openssl passwd -apr1 testpassword)
$> export TRAEFIK_DOMAIN=dashboard.YOUR_DOMAIN.tld
$> export TRAEFIK_SSLEMAIL=your_email@address.de
```

在部署服务之前必须执行的最后一个命令是创建将跨所有容器使用的`traefik-public`网络:

```
$> docker network create --driver=overlay traefik-public
```

完成后，就可以将 traefik 部署到您的 docker 群中了:

```
$> docker stack deploy -c docker-compose.traefik.yml
```

发射`https://dashboard.YOUR_DOMAIN.tld`测试一下就行了。该域将使用 SSL 证书，如果您按照上述说明操作，您可以使用***admin/test password***登录。

# 便携式集装箱

> Portainer 是一个强大的基于 GUI 的容器即服务解决方案，可以帮助组织轻松安全地管理和部署云原生应用程序。

我的 docker-compose.yml for Portainer 并不特别。需要的话可以在这里[找到*。通常我的和你在谷歌上搜索 Portainer 服务找到的没有什么不同。*](https://ftp.f1nalboss.de/data/docker-compose.portainer.yml)

*如果你用我的，你必须给你的经理加一个标签！ **这非常重要**，因为 Portainer 需要连接到 docker 插座。您可以使用以下命令添加标签:*

```
*$> export NODE_ID=$(docker info -f '{{.Swarm.NodeID}}')
$> docker node update --label-add portainer.portainer-data=true $NODE_ID*
```

*此外，声明将要使用的`PORTAINER_DOMAIN`。*

```
*$> export PORTAINER_DOMAIN=portainer.$PRIMARY_DOMAIN*
```

*完成此操作后，您可以部署 Portainer:*

```
*$> docker stack deploy -c docker-compose.portainer.yml portainer*
```

***计时提示:**确保在 Portainer 准备好之后立即登录并创建您的凭证，否则它会自动关闭以保证安全。如果您没有及时创建凭据，它会自动关闭，您可以使用以下命令强制它重新启动:*

```
*$> docker service update portainer_portainer --force*
```

# *Docker 注册表*

> *Registry 是一个无状态、高度可伸缩的服务器端应用程序，它存储并允许您分发 Docker 映像。注册中心是开源的，受 Apache 许可。*

*如果你不想把你的代码/数据上传到公共注册中心，docker 注册中心在 swarm 环境中是很重要的。如果你没有自己的私有注册表，那么每次你用你的应用程序代码扩展一个官方图像时，你必须把结果图像上传到 docker-hub。虽然这是伟大的，如果你只是想扩展功能的一般方式，这是不可取的，如果你复制你的网站与你的封闭源代码到图像。*

*假设您有一个修改过的 Nginx 图像，其中 HTML 文件夹被复制到图像中:*

```
*FROM nginx
COPY html /usr/share/nginx/html*
```

*在许多情况下，`html`文件夹会包含你不想让别人使用的网站。如果你有一个私人注册表，你可以建立图像并上传，但如果你没有，你必须使用公共注册表。*

*这就是为什么我认为每个群体都需要访问私有注册表。这就是为什么我创建了一个。通常你可以用一个简单的`docker run`命令建立一个注册表，但是我想有一个可以从任何地方访问的注册表。因此，我创建了一个可以通过用户名和密码访问的注册表。*

*我的个人`docker-compose.yml`可以在这里下载[。它将在我的 Traefik 环境中运行，并创建一个新的注册表，我可以从我的群中的每个节点使用它。要部署服务，您必须声明一些在部署时使用的环境变量:`REGISTRY_USERNAME`、`REGISTRY_HASHED_PASSWORD`和`REGISTRY_HOST`:](https://ftp.f1nalboss.de/data/docker-compose.registry.yml)*

```
*$> export REGISTRY_USERNAME=reg_adm
$> export REGISTRY_HASHED_PASSWORD=$(openssl passwd -apr1 regsupersecret)
$> export REGISTRY_HOST=reg.YOUR_DOMAIN.tld*
```

*使用这些变量，您可以部署服务:*

```
*docker stack deploy -c docker-compose.registry.yml registry*
```

*要使用注册表，您还需要做两件事。第一件事是生活质量的特点，以方便地改变注册域，而不影响每一个容器使用的图像从私人注册。**将 DOCKER_REGISTRY 添加到。为** `**root**` **或您正在使用的任何用户配置文件，以便知道是否应该推送/下载 docker-compose 文件**。第二项任务非常重要。你必须在你的群的每个节点上执行`docker login`，这样每个节点都被允许拉取图像。*

*现在你可以开始在 docker-compose.yml 中使用你的私有注册表了。如果你创建了一个`DOCKER_REGISTRY`环境变量，你可以像这样在 docker-compose.yml 中使用它:*

```
*myapp:
    image: ${DOCKER_REGISTRY}/simple-app
    build:
      context: ./
      dockerfile: Dockerfile*
```

*如果您想要部署包含上述部分的服务，您必须构建并推送您的映像以正确部署它:*

```
*$> docker-compose build
$> docker-compose push
$> docker stack deploy -c docker-compose.yml www*
```

*如果您只构建和部署它，那么您的群中的其他节点无法获取映像，因此服务无法部署。*

# *文件传送协议*

*另一个重要的服务是 FTP 服务器，用于保存你在使用 swarm 时创建的任何应用程序或服务中想要使用的文件。*

*我决定使用“pure-ftp ”,因为这是我在搜索运行在 docker 环境中的 ftp 服务器时找到的。因为我想保持简单，所以我创建了一个没有任何`traefik`配置的 FTP 服务器。**但是**我做了一个简单的技巧，把 FTP 服务器和一个网站放在 docker-compose.yml 中。我这样做是因为我想有可能通过`https`从服务器下载我用`ftp`上传的文件。*

*经过配置，我想出了[这个 docker-compose.yml](https://ftp.f1nalboss.de/data/docker-compose.web.yml) 。在该文件中，您可以看到我为 web 服务使用了一个定制的 docker 文件，其中包含:*

```
*FROM nginx
COPY html /usr/share/nginx/html*
```

*我描述的技巧只是在`docker-compose.yml`内定义的体积。如您所见，我在两个服务中都定义了数据。在`web-service`中，我在 Nginx HTML 文件夹中有一个额外的文件夹，这样我就可以从网上访问它。在`ftp-service`中，我将数据定义为用户上传数据的地方。*

*在部署这个服务之前，您必须声明环境变量:`FTP_USERNAME`、`FTP_PASSWORD`、`FTP_DOMAIN_FOR_CERT`、`FTP_ORG_FOR_CERT`、`FTP_COUNTRYCODE_FOR_CERT`和`WEBSERVICE_DOMAIN`:*

```
*$> export WEBSERVICE_DOMAIN=www.MYDOMAIN.tld
$> export FTP_USERNAME=SUPERUSER
$> export FTP_PASSWORD=clearTextPW
$> export FTP_DOMAIN_FOR_CERT=$WEBSERVICE_DOMAIN
$> export FTP_ORG_FOR_CERT=mybusiness
$> export FTP_COUNTRYCODE_FOR_CERT=DE*
```

*此外，你必须给你的群的任何节点添加一个标签。为此，使用`docker node ls`找出每个节点的 ID 并执行:*

```
*$> docker node update --label-add www.ftp-data=true ID_OF_NODE_TO_USE*
```

*完成后，您可以安全地部署启用 FTP 的网站*

```
*docker stack deploy -c docker-compose.web.yml webandftp*
```

*现在可以用一个 [FTP 客户端](https://filezilla-project.org)连接到您的`WEBSERVICE_DOMAIN`并上传一个文件(`test.txt`)，然后可以通过这个 URL: `WEBSERVICE_DOMAIN/data/test.txt`访问它*

*整个 FTP docker 服务可以从我的 GitHub 下载:*

*[](https://github.com/paulscode-de/ftp-server) [## GitHub-pauls code-de/FTP-server:FTP 服务器(pureftp)集成在 traefik 的网站上，您可以在这里…

### ftp 服务器(pureftp)集成到 traefik 的一个网站，您可以从 ftp 访问文件

github.com](https://github.com/paulscode-de/ftp-server) 

**很重要的一点是，只有该域也有一条到您的经理节点的 A 记录，您才能连接到您的** `**WEBSERVICE_DOMAIN**` **！**

# 5.结束语

我希望这篇文章对你有所帮助，并且可以使用我提供的文件在你自己的 Docker Swarm 中设置这些服务。

依我拙见，这四个服务应该存在于每一个 Docker Swarm 环境中，因为它们是强制性的(或者与类似功能的服务交换)。* 

*这篇博客到此结束。我很想听听你的想法和想法🤗请把它们记在下面👇👇👇此外，如果你有问题，不要犹豫，问我！*

**✍️写的**

*保罗·克努特*

*👨🏻‍💻🤓🏋️‍🏸🎾🚀*

*丈夫，两个孩子的父亲，极客，终身学习者，技术爱好者软件工程师*

**一个稍微不同的版本最初发表在*[*https://*www . paulsblog . dev*/services-you-want-to-have-in-A-swarm-environment*](https://www.paulsblog.dev/services-you-want-to-have-in-a-swarm-environment)*

**问好🙌开:**

*[*Twitter*](https://www.twitter.com/paulknulst) *，*[*LinkedIn*](https://www.linkedin.com/in/paulknulst/)*，*[*GitHub*](https://github.com/paulknulst)*，* [*个人网站*](https://www.paulsbog.dev)*
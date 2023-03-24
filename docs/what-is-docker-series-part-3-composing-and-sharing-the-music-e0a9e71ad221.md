# 什么是 Docker 系列:第 3 部分-创作和分享音乐

> 原文：<https://levelup.gitconnected.com/what-is-docker-series-part-3-composing-and-sharing-the-music-e0a9e71ad221>

## 既然我们能创造，我们就能创作并最终分享。

![](img/c4f7bf62a9621139b523d9509c0238f0.png)

我们已经讨论了 Docker 在开发周期中的位置。我们已经讨论了 Docker 如何运行以及 Docker 运行什么。我们甚至已经启动并运行了一个 Ubuntu 实例。现在我们已经有了一些基本的东西，我们可以开始创建多个容器实例了。

# Docker 撰写

![](img/7356fba7c8dde51ec261efcb56b7125a.png)

Samuel Sianipar 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Docker Compose 是另一个命令，它让开发人员有机会在任何给定的时间构建/运行多个 Docker 容器。这允许开发人员编排运行应用程序所需的所有服务(数据库、后端、前端、消息服务器等),并使用一个命令运行它们。对于开发人员来说，这是一个强大的工具，因为在运行组合之间对图像的任何更改都会自动构建图像的新版本，并将其集成到服务设置中。

您只需定义该文件一次，然后继续使用相同的命令，而不是记住需要首先启动哪些服务:

`docker-compose up .`

> 假设`docker-compose.yaml`文件在`.`的目录中。

运行多个 Docker 容器最简单的方法是使用一个`docker-compose.yaml`文件。一个`docker-compose.yaml`文件的例子是:

```
version: '2.0'
services:
  web:
    build: .
    ports:
    - "5000:5000"
    volumes:
    - .:/code
    - logvolume01:/var/log
    links:
    - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

这里发生了很多事情。让我们慢慢打开这个文件。

`version`是要使用的 docker-compose 类型。最常见的是 2.0，但撰写本文时的最新版本是 3.8。大部分还是用 2.0。

`services`是要构建的服务类型。您可以在这里定义要使用的图像以及这些图像需要哪些属性。服务下的值是添加到`Dockerfiles`中描述的设置之上的值。

*   `build`是在构建时应用的设置。可以指定构建一个`Dockerfile`路径
*   `ports`是在主机上打开的端口，以`{host:container}`的格式连接到 Docker 容器。
*   `volumes`用于写入持久数据。可以添加容器的一次性卷，而无需顶级密钥。跨容器使用相同的卷应该会将名称添加到顶级卷密钥中(如上面的示例所示)。
*   `links`用于链接彼此之间的服务——带有 WordPress 实例的数据库——但这是一个遗留特性，应该改为通过网络链接容器。
*   `redis`是另一个使用`redis`映像的服务(如果在主机上找不到，则从 Docker Hub 获取)。也是用`link`在`web`服务中定义的服务。

> 有关所有可用选项的参考，请查看 Docker 网站上的[合成文件文档](https://docs.docker.com/compose/compose-file/)。

你可以启动多个服务，使用[网络](https://docs.docker.com/compose/compose-file/#networks)连接它们，并通过一个命令`docker-compose up .`开始部署完整的应用程序。要停止，只需运行`docker-compose down`，它将停止文件中包含的所有正在运行的容器。

这是将 Docker 容器组合在一起的开始。关于这个主题的更广泛的想法，请查看我在 AWS EC2 实例上的 Docker 容器中做的关于 [WordPress 的教程。看一下这篇文章，你也可以拥有自己的 WordPress，在 AWS 上运行 Nginx 的反向代理。它也适用于免费层！](https://medium.com/@srepollock/wordpress-in-a-docker-container-on-an-aws-ec2-instance-acb9b2243040)

# 码头枢纽

![](img/23813231fe516a4209ca0b8fcd99824a.png)

照片由 [Kira auf der Heide](https://unsplash.com/@kadh?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Docker Hub 是 Docker 的包注册表。用户可以随时将他们创建的图像上传到存储库，并将其更新为新版本供其他用户获取。

看看注册表中您可能想要使用的实例，或者您可能想要扩展和构建的实例。有如此多的图片提供了无限的潜力，开始做一些伟大的事情的最好方法就是开始实验。

![](img/3444a17ed194f9d5171a7f43cc660e7d.png)

照片由 [Riz Mooney](https://unsplash.com/@rizmooney?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

另外，现在您已经看到了 Docker 的功能，您已经有机会运行一些自己的测试，并且您已经想出了一些您可能想要共享的图像；在那里，你可以通过 Docker Hub 传递你的知识。其他人可能正面临着和你一样的挑战。为什么不分享知识，让其他人继续他们的热情呢？

# 最后

Docker 是一个不可思议的平台。这是一种创建程序的方法，这些程序每次启动时都能以相同的方式在多个平台上运行。

Docker 解决了“它在我的机器上工作”的问题，因为它每次都使用相同的资源为客户端运行产品。它可以让您使用集群组织工具(如 [Swarm](https://docs.docker.com/engine/swarm/) 或 [Kubernetes](https://kubernetes.io/) )来扩展您的应用程序(稍后将讨论)，并为开发人员提供工具来将问题隔离到单个环境，而不是跨环境的所有问题。

这是一个小系列，讨论我从 Docker 学到的东西。我在这里和那里有更多的花絮要添加，我将继续写关于 Docker 的文章，并在我进行的过程中把它们添加到列表中。

我希望你能从这些文章中获得一些知识。我真的希望你渴望更多，并继续学习下去。请随时联系我，讨论我发表的任何文章。如果能听到一些关于你从阅读中学到了什么的反馈，那就太好了。

# 什么是 Docker 系列:

*   [第一部](/what-is-docker-series-part-1-1e419c28f951)
*   [第二部分](https://medium.com/@srepollock/what-is-docker-series-part-2-images-and-dockerfiles-141c15eaca0f)

感谢您抽出时间阅读。祝一切顺利，最重要的是，保持安全——斯潘塞
# 了解容器的好处

> 原文：<https://levelup.gitconnected.com/understanding-the-benefits-of-containers-7236318815f8>

# 容器介绍

一旦您阅读了这一部分，您将能够识别传统软件开发中出现的问题，定义容器并描述其特征，列出容器的优点和挑战，并说出流行的容器供应商的名称。

![](img/626ba4b2ffde687f67a0d294ece773e6.png)

Cloud-native 是最新的应用程序开发方法，用于构建可扩展的、动态的、混合的云友好软件。容器是这种方法的重要组成部分。例如，想想海运集装箱如何彻底改变了货物运输:运输公司用各种货物装满标准尺寸的集装箱，而不是用任何合适的物品装满集装箱。这使得在世界范围内运输货物变得更加容易，也使得跟踪库存变得更加容易。此外，集装箱减少了浪费的空间和时间，提高了运输效率。

![](img/d06f84756d75869ea25e96b3cc082b2b.png)

物流学家根据集装箱的大小和需要到达的时间来决定可以运输集装箱的包装选择，如船舶、飞机、火车和卡车。与此相对应的数字技术是容器技术。此外，容器解决了使软件可移植的问题，因此应用程序可以在多个平台上运行。

![](img/9c13ea64c845b6fc75ee2bed12a67f9c.png)

容器是一个标准的软件单元，它封装了应用程序代码、运行时、系统工具、系统库以及程序员高效构建、发布和运行应用程序所必需的设置。运营和底层基础设施问题不再是阻碍因素。您可以快速地将应用程序从笔记本电脑移动到测试环境，从试运行环境移动到生产环境，并且始终知道您的应用程序将正常工作。容器可以很小，只有几十兆字节，开发人员几乎可以立即启动容器化的应用程序，这些功能是当今开发和部署解决方案标准的基础。

![](img/bebcfad52ac9aa1e4ea21e71a1f91800.png)

在传统计算环境中，开发人员无法隔离应用程序，也无法在物理服务器上为应用程序分配或指定特定的存储和内存资源。因此，服务器经常利用不足或过度利用，导致利用率低和投资回报低。传统部署需要全面的资源调配和昂贵的维护成本。物理服务器的限制可能会在高峰工作负载期间限制应用程序的性能。应用程序不能跨多个环境和操作系统移植。实现硬件弹性通常非常耗时、复杂且昂贵。因此，传统的内部 IT 环境具有有限的可扩展性。最后，当使用传统设置将软件分发到多个平台和资源时，自动化是具有挑战性的。容器帮助您克服这些挑战。

![](img/2bf6a3df37bb42117af9cc056f3b2bea.png)

容器引擎虚拟化操作系统，并负责运行容器。独立于平台的容器为运行应用程序提供了轻量级、快速、隔离、可移植和安全的环境，并且通常比虚拟机需要更少的内存空间。容器内的二进制、库和其他实体使应用程序能够运行，而一个设备可以托管多个容器；容器帮助程序员快速地将代码部署到应用程序中。

![](img/71fb9980b53783cc1d88d3346b7e3918.png)

容器可以部署在任何云、桌面或虚拟化基础设施上。它们独立于操作系统，可以在 Windows、Linux 或 Mac OS 上运行。容器也是独立于语言平台的——您可以使用任何编程语言。

![](img/9312bf577c07ab9bcef6b10f1608163f.png)

容器让您可以使用自动化快速创建应用程序，降低部署时间和成本，提高资源利用率(内存和 CPU)，以及跨不同环境移植。它们还支持微服务等下一代应用。

容器有几个优点，但也有一些缺点。首先，当服务器的操作系统受到恶意活动的影响时，服务器的安全性就会受到威胁。当管理数千个容器时，开发人员可能会变得不知所措。最后，将单一的遗留应用程序转换为微服务是一个复杂的过程，开发人员可能很难针对特定的场景调整容器的大小。

让我们来看看一些最流行的容器平台。Docker 是当今最流行的容器平台。Podman 是一个无守护进程的容器引擎，提供了比 Docker 更高的安全性，但它没有被广泛使用。对于数据密集型应用程序和操作，开发人员通常更喜欢 LXC。在运行的物理机器上提供最高级别的隔离。

![](img/8a186a5be88ca9b6a39e6e81919000e8.png)

在本节中，您了解了容器的兴起，以及组织如何使用它们来克服与隔离、利用率、供应、性能等相关的挑战。容器是软件的标准单元，它封装了构建、发布和运行应用程序所需的一切。容器是运行在操作系统之上的应用程序。它们独立于平台，可缩短部署时间，提高利用率，实现流程自动化，并支持下一代应用(微服务)。不幸的是，这些好处是有代价的:管理、遗留项目迁移和规模调整可能是巨大的挑战。主要的集装箱供应商包括码头工人、波德曼、LXC 和流浪者。

# Docker 简介

阅读本节后，您将能够:定义什么是 Docker 容器，描述 Docker 如何使用虚拟化技术来创建容器，列出容器的优点及其潜在的缺点，并确定克服使用容器的挑战的策略。

![](img/accc881ea54dda5c375123731cff1be8.png)

Docker 是一个开放的平台，可以像容器一样开发、运输和运行应用程序。Docker 因其简单的架构、巨大的可伸缩性以及在多个平台、环境和位置上的可移植性而受到开发人员的欢迎。

![](img/7bc5df90fad5cc4d35fcbbe25efb9870.png)

Docker 将应用程序与底层硬件、操作系统和容器运行时隔离开来。

![](img/56a43394ac13ff909889fdd795d39167.png)

Docker 是用 Go 编程语言编写的，它使用 Linux 内核特性来交付其功能。Docker 还使用名称空间来提供一个隔离的工作空间，称为容器。Docker 为每个容器创建了一组名称空间，每个方面运行在一个单独的名称空间中。

Docker 启发了补充工具的开发，如 Docker CLI、Docker Compose 和 Prometheus，以及各种存储插件。它还导致了诸如 Docker Swarm 或 Kubernetes 等编排技术的产生，以及使用微服务和无服务器的开发方法。

Docker 提供了以下好处:隔离和一致的环境导致稳定的应用程序部署。部署在几秒钟内完成，因为 Docker 映像很小，可重复使用，可以跨多个项目使用。Docker 的自动化功能有助于消除错误，简化维护周期。Docker 支持敏捷和 CI/CD DevOps 实践。Docker 简单的版本控制加速了测试、回滚和重新部署。Docker 有助于对应用程序进行分段，以便于刷新、清理和修复。开发人员协作以更快地解决问题，并在需要时扩展容器。图像可以跨平台移植，为开发人员和 IT 团队提供了更大的灵活性。最终，这将减少现场停机时间，加快错误解决速度，这对所有相关人员来说都是双赢。

Docker 不太适合具有这些品质的应用程序:它们需要高性能或安全性，它们围绕单一架构构建，它们使用丰富的 GUI 功能，或者它们执行标准的桌面或有限的功能。

在本节中，您了解了 Docker 是一个开放平台，用于开发、运输和运行作为容器的应用程序。Docker 通过为每个人提供项目开发环境的本地副本，加快了跨多个环境的部署过程，并使代码协同工作变得更加容易。此外，一旦您在 Docker 容器中创建了应用程序，您就可以将该容器部署到另一个位置——尽管这可能不是所有项目都需要的。最后，由于 Docker 容器不依赖于任何特定的底层基础设施或虚拟化软件，它们可以在任何有 Docker 实例运行的地方使用。

# 构建和运行容器映像

阅读完本节之后，您可以构建您的容器映像。您还将知道如何使用现有图像启动容器。您将学习如何使用 Docker 的一些最基本的命令。

![](img/3b861308964a686f9e17a18ee7bcb8b2.png)

此图显示了运行容器的开发过程。创建和运行容器的步骤如下:创建 Dockerfile，这是一个文本文件，包含创建图像时将执行的所有命令。接下来，使用这个文件创建一个映像，一个容器的指令集。并使用该映像创建一个运行容器，它是该映像的一个运行实例。

![](img/e67f984cd53be2a6a3e052f97e4b36aa.png)

```
**## Steps to create and run containers:
1.** Create a Dockerfile
**2.** Use the Dockerfile to create a container image
**3.** Use the container image to create a running container
```

使用 Dockerfile 文件创建一个运行容器。这个示例 Dockerfile 文件包含来自和 CMD 的命令。从定义基础图像。CMD 打印出“Hello World！”在终端上。

```
FROM alpine
CMD ["echo", "Hello, world!!"]
```

Docker 构建命令使用构建命令、标记、存储库、版本和当前目录构建映像。输出消息包括:向 Docker 守护进程发送构建上下文“成功构建”和“成功标记 my-app:v1”

![](img/31c4e2ebdc447e45ac7e9b05f6995e60.png)

要验证映像是否已创建，请运行“docker images”命令。输出显示了存储库(my-app)、标签(v1)、图像 ID、创建日期和图像大小。

![](img/1802bf142b2fb06564aa2e5383e3e82e.png)

要运行应用程序的实例，请使用带有容器图像的名称和标记的 run 命令。应用程序打印出“你好，世界！!"

![](img/bafa6a180e7d99a5a145b5bd8024a1ef.png)

运行 docker ps -a 命令，该命令显示您创建的容器的详细信息。

![](img/3cc41405efe403ea4248107efe168e7b.png)

build 命令从 Dockerfile 文件创建容器。images 命令列出了所有图像、它们的标签和存储库以及它们的大小。run 命令从映像创建并运行一个容器。push 命令将图像存储在已配置的注册表中，pull 命令从已配置的注册表中检索图像。

![](img/1e80a986e260f3dad6d6023368deec9d.png)

在本节中，您了解了 build 命令与 Dockerfile 一起用于构建映像，run 命令与映像一起用于创建运行容器，一些最常见的 Dockerfile 命令是 build、images、run、pull 和 push。

# Docker 对象

阅读完这一节后，您将知道如何识别和命名 Docker 对象，列出 Docker 文件中使用的命令，描述容器映像的命名格式，并解释 Docker 如何使用网络、存储和插件。

![](img/67af5742b07c871e9318a683660152b7.png)

Docker 是一个管理软件容器中应用程序的构建、发布和运行的工具。它由 Dockerfile、images、container、network、storage volumes 等对象以及 plugins 和 add-ons 等其他实体组成。

![](img/70060f6b37de30b317302eb209dc6afc.png)

Dockerfile 是一个文本文件，包含创建图像所需的说明。您可以从控制台或终端使用任何编辑器创建 docker 文件。这里有几个基本的教育，你可以把它们写进你的档案里。

## 从

Dockerfile 必须始终以定义基本映像的 FROM 指令开头。最常见的基础映像是操作系统或特定语言，如 Go 或 Node.js。

## 奔跑

RUN 命令执行命令。

## 煤矿管理局

CMD 指令设置执行的默认命令。因此，Dockerfile 应该只有一个 CMD 指令。如果一个 docker 文件有几个 CMD 指令，那么只有最后一个会生效。

## 让我们了解一下 Docker 图像。

Docker 映像是一个只读模板，包含创建 Docker 容器的说明。Dockerfile 文件提供了构建映像的指令。每个 Docker 指令在图像中创建新层。当您更改 Dockerfile 文件并重建图像时，仅重建已更改的层。图像包含只读图层，这在发送和接收图像时可以节省磁盘空间和网络带宽。当实例化这个映像时，您会得到一个运行的容器。此时，可写容器层被置于只读层之上。需要可写层，因为容器作为图像不是不可变的。

![](img/a2c183c6d93ee24997f1382d568cca30.png)

## 让我们来学习图像是如何命名的。

命名映像时，名称由三部分组成:主机名、存储库和标记。

```
hostname/repository:tag
```

**主机名:**主机名标识映像注册表。

**存储库:**存储库是一组相关的容器映像。

**标签:**标签提供了关于图像的特定版本或变体的信息。

考虑图像名称**docker.io/ubuntu:18.04**:

```
docker.io/ubuntu:18.04
```

**主机名:**主机名 **docker.io** 指的是 Docker Hub 注册表。使用 **Docker CLI** 时，可以排除 docker.io 主机名。

**存储库:**存储库名称 ubuntu 表示一个 Ubuntu 映像。

**标签:**最后，标签，这里显示为 18.04，代表已安装的 Ubuntu 版本。

## 让我们了解一下 Docker 容器。

Docker 容器是图像的一个实例。您可以使用 Docker API 或 CLI 来创建、启动、停止或删除映像。您可以连接到多个网络，并将存储附加到容器。您也可以基于其当前状态创建新图像。Docker 使容器彼此之间以及与主机之间保持良好的隔离。

使用 Docker 时，网络有助于隔离容器的通信。当容器停止运行时，数据不会持久化。但是您可以使用卷和绑定装载来持久化数据，即使在容器停止之后。诸如存储插件之类的插件提供了连接到外部存储平台的能力。

在本节中，您了解了 docker 包含几个对象，包括 docker 文件、映像、容器、网络、存储卷和其他对象，如插件和附件。基本的 Docker 指令是 FROM、RUN 和 CMD。docker 容器是映像的一个实例——一个正在运行的进程及其文件系统，可以独立于其他容器或主机系统启动和停止。要命名图像，请使用以下格式: <hostname>: <repository>: <tag>。Docker 使用网络来隔离容器通信。Docker 使用卷和绑定挂载来持久化数据，即使在容器停止运行之后。此外，插件(如存储插件)提供了连接外部存储平台的能力。</tag></repository></hostname>

# 码头建筑

阅读完本文后，您将能够识别 Docker 架构的组件，解释这些组件的特性，并描述如何使用 Docker 创建容器。

![](img/5c9df9f6d8992f29bdcdbaa6b5790a69.png)

Docker 平台使您能够将应用程序作为容器来管理，并提供完整的应用程序环境。Docker 组件包括 Docker 客户端、Docker 守护进程和 Docker Hub。以下是 Docker 工作原理的高级概述:

首先，您将通过 Docker 客户机使用 Docker 命令行接口或 REST API 向您的 Docker 主机服务器发送指令。接下来，主机包含守护程序，称为 dockerd。守护程序监听 Docker API 请求或命令，如“docker run ”,并处理这些命令。然后，它根据这些指令构建、运行和分发容器。最后，它将容器图像存储在注册表中。

Docker 主机管理图像、容器、名称空间、网络、存储插件和附加组件。

![](img/6121bf2d4f9e7952c634deca54fd0524.png)

Docker 客户机允许您与 Docker 主机通信，主机可以是本地的，也可以是远程的。例如，您可以在同一个系统上运行 Docker 客户机和守护进程，或者连接到一个远程守护进程。守护进程还可以与其他守护进程对话，以管理 Docker 容器的服务。

![](img/38e9c2fdc3f362a9499a436818f2908c.png)

Docker 在存储库中存储和分发图像。存储库可以是公共的，比如 Docker Hub，任何人都可以访问，也可以是私有的。出于安全原因，企业通常选择私有存储库。和位置由第三方提供者托管，比如 IBM Cloud Container Registry，或者自托管在私有数据中心或云上。

## 让我们了解一下如何将图像移入注册表。

首先，开发人员使用自动化或构建管道将图像构建并推送到 Docker 存储的注册表中。然后，系统可以提取这些图像。

![](img/b39ae4922cfacb3dfc36efa5560546de.png)

让我们更详细地研究一下这个过程。这里是 Docker 架构的可视化表示，它由客户端组成；Docker 主机，包括 Docker 守护进程；以及具有其现有存储图像的注册表。当用户请求 Docker 从注册表上的当前图像创建图像时，容器化过程开始。首先，您可以使用现有的基础映像或 docker 文件。Dockerfile 是一个文本文档，包含创建图像所需的所有命令。要创建容器映像，发出 build 命令，创建一个带有名称的容器映像。创建映像后，可以使用 push 命令将其存储在注册表中。主机首先在本地检查映像是否已经可用，然后发出带有映像名称的 run 命令来创建容器。如果映像在主机中不可用，Docker 客户端将连接到注册表并将映像拉至主机。然后，守护程序使用该映像创建一个正在运行的容器。

![](img/fd158fc410a00325b292398c3be26c09.png)

在本节中，您了解了:Docker 体系结构由客户机、主机和注册表组成。客户机使用命令和 REST APIs 与主机进行交互。Docker 主机包括守护进程，通常称为 dockerd。Docker 主机还管理映像、容器、名称空间、网络、存储、插件和附加组件，容器化是用于构建、推送和运行映像以创建运行容器的过程。

## 在这一点上，你知道:

*   容器是一个标准化的软件单元，包括构建、运输和运行应用程序所需的一切。
*   容器降低了部署时间和成本，提高了利用率，自动化了流程并支持下一代应用程序。主要的集装箱供应商包括码头工人、波德曼、LXC 和流浪者。
*   Docker 是一个开发、发布和运行应用程序的开放平台。
*   对于基于整体架构的应用程序或需要高性能或安全性的应用程序，Docker 容器可能不是最佳选择。
*   Docker 架构由 Docker 客户端组成，Docker 客户端是在您的计算机上运行的程序，使您能够运行容器；Docker 主机，它运行在您的服务器上，允许您部署那些容器；和一个容器注册表，其中存储了图像以便可以重用。
*   Docker 主机包含 Docker 守护进程和各种各样的对象，包括图像、容器、网络、存储卷和其他东西，如插件和附加组件。
*   Docker 使用网络来隔离容器。
*   Docker 使用卷和绑定挂载来帮助您持久化数据，即使在容器停止运行之后。
*   像存储插件这样的插件允许您连接到外部存储平台。

## 备忘单:Docker CLI

```
**curl**
localhost Pings the application.**docker build** Builds an image from a Dockerfile.**docker build . -t** Builds the image and tags the image id.**docker CLI** Start the Docker command line interface.**docker container rm** Removes a container.**docker images** Lists the images.**docker ps** Lists the containers.**docker ps -a** Lists the containers that ran and exited successfully.**docker pull** Pulls the latest image or repository from a registry.**docker push** Pushes an image or a repository to a registry.**docker run** Runs a command in a new container.**docker run -p** Runs the container by publishing the ports.**docker stop** Stops one or more running containers.**docker stop $(docker ps -q)** Stops all running containers.**docker tag** Creates a tag for a target image that refers to a source image.**docker –version** Displays the version of the Docker CLI.**exit**
Closes the terminal session.**export MY_NAMESPACE** Exports a namespace as an environment variable.**git clone** Clones the git repository that contains the artifacts needed.**ls**
Lists the contents of this directory to see the artifacts.
```

## 词汇表:容器基础知识

```
**Agile** is an iterative approach to project management and software development that helps teams deliver value to their customers faster and with fewer issues.**Client-server architecture** is a distributed application structure that partitions tasks or workloads between the providers of a resource or service, called servers, and service requesters, called clients.**A container** powered by the containerization engine, is a standard unit of software that encapsulates the application code, runtime, system tools, system libraries, and settings necessary for programmers to efficiently build, ship and run applications.**Container Registry Used** for the storage and distribution of named container images. While many features can be built on top of a registry, its most basic functions are to store images and retrieve them.**CI/CD pipelines** A continuous integration and continuous deployment (CI/CD) pipeline is a series of steps that must be performed in order to deliver a new version of software. CI/CD pipelines are a practice focused on improving software delivery throughout the software development life cycle via automation.**Cloud native** A cloud-native application is a program that is designed for a cloud computing architecture. These applications are run and hosted in the cloud and are designed to capitalize on the inherent characteristics of a cloud computing software delivery model.**Daemon-less** A container runtime that does not run any specific program (daemon) to create objects, such as images, containers, networks, and volumes.**DevOps** is a set of practices, tools, and a cultural philosophy that automate and integrate the processes between software development and IT teams.**Docker** An open container platform for developing, shipping and running applications in containers.
A Dockerfile is a text document that contains all the commands you would normally execute manually in order to build a Docker image. Docker can build images automatically by reading the instructions from a Dockerfile.**Docker client** is the primary way that many Docker users interact with Docker. When you use commands such as docker run, the client sends these commands to dockerd, which carries them out. The docker command uses the Docker API. The Docker client can communicate with more than one daemon.**Docker Command Line Interface (CLI)** The Docker client provides a command line interface (CLI) that allows you to issue build, run, and stop application commands to a Docker daemon.
Docker daemon (dockerd) creates and manages Docker objects, such as images, containers, networks, and volumes.**Docker Hub** is the world's easiest way to create, manage, and deliver your team's container applications.**Docker localhost** Docker provides a host network which lets containers share your host’s networking stack. This approach means that a localhost in a container resolves to the physical host, instead of the container itself.**Docker remote host** A remote Docker host is a machine, inside or outside our local network which is running a Docker Engine and has ports exposed for querying the Engine API.**Docker networks** help isolate container communications.
Docker plugins such as a storage plugin, provides the ability to connect external storage platforms.**Docker storage** uses volumes and bind mounts to persist data even after a running container is stopped.**LXC** LinuX Containers is a OS-level virtualization technology that allows creation and running of multiple isolated Linux virtual environments (VE) on a single control host.**Image** An immutable file that contains the source code, libraries, and dependencies that are necessary for an application to run. Images are templates or blueprints for a container.**Immutability** Images are read-only; if you change an image, you create a new image.**Microservices** are a cloud-native architectural approach in which a single application contains many loosely coupled and independently deployable smaller components or services.**Namespace** A Linux namespace is a Linux kernel feature that isolates and virtualizes system resources. Processes which are restricted to a namespace can only interact with resources or processes that are part of the same namespace. Namespaces are an important part of Docker’s isolation model. Namespaces exist for each type of resource, including networking, storage, processes, hostname control and others.**Operating System Virtualization** OS-level virtualization is an operating system paradigm in which the kernel allows the existence of multiple isolated user space instances, called containers, zones, virtual private servers, partitions, virtual environments, virtual kernels, or jails.**Private Registry** Restricts access to images so that only authorized users can view and use them.**REST API** A REST API (also known as RESTful API) is an application programming interface (API or web API) that conforms to the constraints of REST architectural style and allows for interaction with RESTful web services.**Registry** is a hosted service containing repositories of images which responds to the Registry API.**Repository** is a set of Docker images. A repository can be shared by pushing it to a registry server. The different images in the repository can be labelled using tags.**Server Virtualization** is the process of dividing a physical server into multiple unique and isolated virtual servers by means of a software application. Each virtual server can run its own operating systems independently.**Serverless** is a cloud-native development model that allows developers to build and run applications without having to manage servers.**Tag** is a label applied to a Docker image in a repository. Tags are how various images in a repository are distinguished from each other.
```

由于利用 Docker 容器的新功能和公司的爆炸式增长，我们可以有把握地说，这确实是当今最热门的云计算话题之一。尽管 Docker 相对较新，仍然缺乏一些基本功能，但很难否认它的潜力和致力于在开发过程中使用 Docker 的公司名单，包括亚马逊网络服务、谷歌、IBM、微软 Azure、甲骨文和脸书。看到开发人员在容器管理和安全性方面的进展将是令人兴奋的。毫无疑问，这项技术将会继续存在！

我的其他出版物:[https://ercindedeoglu.github.io/](https://ercindedeoglu.github.io/#publications)
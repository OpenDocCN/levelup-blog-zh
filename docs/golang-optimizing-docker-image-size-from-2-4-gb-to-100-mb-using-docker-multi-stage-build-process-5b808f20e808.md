# Golang:使用 Docker 多阶段构建流程将 Docker 映像大小从 2.4 GB 优化到 100 MB。

> 原文：<https://levelup.gitconnected.com/golang-optimizing-docker-image-size-from-2-4-gb-to-100-mb-using-docker-multi-stage-build-process-5b808f20e808>

![](img/5f874efa421b8438d74017ba52930f91.png)

马克·奥尔森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在开发人员想到的数百种优化中，减少 docker 图像大小是最不重要的。我们也是。

结果，我们的 docker 映像开始重约 2.4 GB，这间接提高了我们开发人员的工作效率。以下是我们面对较大 docker 图像尺寸时遇到的问题

**- >运行自动化集成测试的延迟**

每当我们向发布分支提出合并请求时，我们都有一个自动化的过程，它从提出的 MR 代码中构建一个新的映像，从它旋转出一个新的容器，并针对当前版本构建(**连接到产品数据库**)和稳定的生产构建发出数千个自动化请求，并比较两个响应，以检查当前发布是否没有破坏现有的功能，作为自动化集成测试的一部分。

**痛点**

*   这里的构建过程没有缓存，因此每次有发布的合并请求时，构建映像需要 20-25 分钟，集成测试需要 10 分钟。
*   如果其中一个集成测试失败了，我们就退出这个过程，开发人员不得不假设一些极端的情况，修复，并再次重新推送，其中仍然不能保证修复后测试会通过。开发人员不得不一直等待这个成功，这会影响他/她的生产力。

**- >创建构建标签的延迟**

一旦集成测试通过，代码被审查并被推送到发布，OpenCI 自动化就会启动并创建一个构建标签(**release-<version>**)。在这个过程中，应用程序的依赖项被提取，然后最终生成的图像被推送到工件。

然后，我们继续创建一个带有发布标签和后期批准的 JIRA 标签， **Edge Automation** 开始工作并创建新的容器。

**痛点**

*   提取依赖关系并通过网络将生成的庞大图像推送到工件会消耗大量时间，并且速度取决于网络带宽。

**- >自动缩放**

有关 docker 图像大小 w.r.t，自动缩放效果的详细信息，请参考我下面的另一篇文章。

[](/effects-of-docker-image-size-on-autoscaling-w-r-t-single-and-multi-node-kube-cluster-29c4f689cd99) [## Docker 图像大小对自动缩放 w.r.t 单节点和多节点 Kube 集群的影响

### 当你问开发人员提高应用程序性能的两个关键因素是什么时，答案是…

levelup.gitconnected.com](/effects-of-docker-image-size-on-autoscaling-w-r-t-single-and-multi-node-kube-cluster-29c4f689cd99) 

既然我们已经了解了拥有如此巨大的 docker 图像的痛点，让我们试着分析一下是什么让 docker 图像如此巨大。为此，让我们看一看我们最初的 docker 形象。

```
**FROM centos:7**RUN \
  yum install -y epel-release bison python-setuptools bzip2 wget make gcc gcc-c++ zlib-devel **git** lsof && \
  easy_install supervisor &&  \
  mkdir -p /opt/logs /etc/supervisord.d  && \
  yum clean all && \
  rm -f /etc/localtime && \
  ln -s /usr/share/zoneinfo/Asia/Kolkata /etc/localtime#Install Go
**RUN \
  cd /tmp && \
  wget** [**https://storage.googleapis.com/golang/go1.12.6.linux-amd64.tar.gz**](https://storage.googleapis.com/golang/go1.12.6.linux-amd64.tar.gz) **&& \
  tar -C /usr/local -xzf go1.12.6.linux-amd64.tar.gz && \
  ln -s /usr/local/go/bin/go /bin/go && \
  ln -s /usr/local/go/bin/gofmt /bin/gofmt** ENV *PATH*=$*PATH*:/usr/local/go/bin:/usr/local/goibibo/<projectName>/bin
ENV *GO111MODULE*=auto *CGO_ENABLED*=1
ENV *GODEBUG*="madvdontneed=1"ARG *GIT_TOKEN*RUN git config --global url."https://$*GIT_TOKEN*@github.com/goibibo/".insteadOf "https://github.com/goibibo/"
EXPOSE 80
#Set argument env - to receive input
ARG *env*WORKDIR /usr/local/goibibo/<projectName>#Add supervisord and functions in startup scripts
COPY ./init/supervisord /etc/rc.d/init.d/
COPY ./init/services/* /etc/supervisord.d/COPY ./go.mod . RUN go mod download#Copy source directory
**COPY ./ .**RUN makeRUN chmod 0644 /etc/deployments/<build>.sh && \
    chmod 0755 /etc/rc.d/init.d/supervisord
```

让我们继续分析增加图像大小的步骤(粗线)。

*   首先，我们将 centos 7 作为基础映像。
*   然后，我们安装所有需要的外部软件。
*   然后，我们在 centos base image 上下载并安装相应的 Golang 版本来构建代码。
*   然后我们执行 **go mod download** 来提取所有的模块依赖关系，并将所有的内部代码模块复制到工作目录中，最后 **make** 来生成二进制文件。

每个步骤都需要一些存储，所有提取的依赖项都将被复制到最终生成的映像中。

但是对于在生产环境中运行的代码，我们所需要的只是一个从代码中生成的二进制文件和一个我们可以在其上执行二进制文件的基本操作系统(足够执行二进制文件的功能)。但是在我们的 docker 映像中，一旦生成了二进制文件，我们就有一大堆无用的依赖项，其中包括(Git、GCC、GCC-C++、Golang、Go 模块、代码模块等),占用了将近 2 GB 的空间。**除此之外，对于我们的用例，我们不需要完整的 centos 功能来运行二进制文件。一个裸露的 Alpine OS 映像(5 MB)足以运行二进制文件。**

有两种方法可以避免将这 2 GB 的数据复制到最终映像。

*   步骤 1 ->作为构建二进制文件后的最后一步，我们可以进入有不必要模块的单独目录并修剪目录。这一过程会导致太多的手动工作来识别并在每个安装了不需要的依赖项的目录中使用一个 **rm -R** 命令，并且在我们需要向现有映像添加更多依赖项时会变得非常忙碌。
*   步骤 2 ->使用 docker 多阶段构建过程的便捷方式。让我们详细了解一下多阶段构建过程。

那么，docker 多阶段是如何工作的呢？

多阶段帮助我们做各种杂耍来下载或安装构建二进制文件所需的依赖项，而不需要将依赖项复制到最终映像。我们来看看改造后的多级 docker 形象，过一遍。

```
**# Stage 1** FROM alpine:3.11.5 AS *builder*RUN apk update \
    && apk add --virtual build-dependencies \
        bash \
        build-base \
        coreutils \
        ca-certificates \
        gcc \
        g++ \
        git \
        make \
        lsof \
        wget \
        curl \
        musl-dev \
        tzdata \
        pkgconfig \
        pkgconf#Install Go
**RUN \
  cd /tmp && \
  wget** [**https://storage.googleapis.com/golang/go1.12.6.linux-amd64.tar.gz**](https://storage.googleapis.com/golang/go1.12.6.linux-amd64.tar.gz) **&& \
  tar -C /usr/local -xzf go1.12.6.linux-amd64.tar.gz && \
  ln -s /usr/local/go/bin/go /bin/go && \
  ln -s /usr/local/go/bin/gofmt /bin/gofmt**RUN ln -s /usr/local/go/bin/go /bin/go && \
  ln -s /usr/local/go/bin/gofmt /bin/gofmtENV *PATH*=$*PATH*:/usr/local/go/bin:/usr/local/goibibo/<projectName>/binENV *GO111MODULE*=auto *CGO_ENABLED*=1
ENV *GODEBUG*="madvdontneed=1"
ENV *GOPRIVATE* <Private-Repo-Link>ARG *GIT_TOKEN*RUN git config --global url."https://$*GIT_TOKEN*@github.com/goibibo/".insteadOf "https://github.com/goibibo/"WORKDIR /usr/local/goibibo/<projectName>COPY ./pkg ./pkg
COPY ./go.mod .RUN go mod download#Copy source directory
COPY ./ .RUN make
 *-----------------***# Final Stage - Stage 2**
**FROM alpine:3.11.5 as *baseImage*****RUN apk update \
    && apk add --virtual build-dependencies \
        tzdata \
        supervisor \
        ca-certificates****RUN \
  mkdir -p /opt/logs /etc/supervisord.d  && \
  rm -f /etc/localtime && \ln -s /usr/share/zoneinfo/Asia/Kolkata /etc/localtime** **ARG *BINARY_PATH*=/usr/local/goibibo/<projectName>/bin****ENV *PATH*=$*PATH*:/usr/local/go/bin:/usr/local/goibibo/<projectName>/bin****COPY --from=*builder* $*BINARY_PATH* $*BINARY_PATH*****WORKDIR /usr/local/goibibo/<projectName>****#Add supervisord and functions in startup scripts
COPY ./init/supervisord /etc/rc.d/init.d/
COPY ./init/services/* /etc/supervisord.d/
COPY ./deployments/ /etc/deployments/****RUN chmod 0644 /etc/deployments/<build>.sh && \
    chmod 0755 /etc/rc.d/init.d/supervisord**
```

让我们来看一下修改后的多阶段 docker 构建流程。我们将整个构建过程分为两个阶段。

第一阶段(**阶段名为构建者**)是我们获取生成代码构建所需的所有软件和外部模块依赖关系的阶段。

在第二阶段，我们所做的就是

*   将 **Alpine** 作为基本图像(5 MB)大小。安装 supervisor(用于维护流程)和其他一些必需的依赖项(20 MB)。
*   使用以下命令将阶段 1 生成的二进制文件复制到阶段 2。在我们的例子中，二进制文件的大小是 65mb(**COPY—from =*<stage name>*<*from path*>*<*toPath*>*)在使用上述命令进行多阶段构建的情况下，我们可以以自顶向下的方式从任何阶段复制到任何阶段。****
*   *这里我们没有将代码或 go 模块复制到最后阶段。它只是二进制的。*
*   *将静态文件复制到最终图像。*
*   *有一个 RUN 命令来启动执行二进制文件的管理程序，并监控由二进制文件启动的进程的健康状况。*

*通过这种方式，我们使用多阶段构建流程，以最小的努力将一个庞大的 2.4GB docker 映像转换为一个小巧的 100 MB( **取决于二进制文件的大小和所需的其他依赖关系** ) docker 映像。*

*如果这篇文章需要更详细的解释或任何改进，请在评论部分告诉我。*
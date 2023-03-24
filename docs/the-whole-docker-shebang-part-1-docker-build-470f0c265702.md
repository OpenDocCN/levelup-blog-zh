# 整个码头工程:码头建筑

> 原文：<https://levelup.gitconnected.com/the-whole-docker-shebang-part-1-docker-build-470f0c265702>

通过示例了解 Docker 构建的每个选项的作用，以及如何使用它

![](img/3e17a9c8f878348faf48db25b517b586.png)

由 [Saanvi Vavilala](https://unsplash.com/@saanvi2116?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

所以如果你正在读这篇文章，你至少应该熟悉什么是[容器](https://www.docker.com/resources/what-container)和[停靠器](https://www.docker.com/)。

你可能已经知道了基本知识，如果你不知道，请随时查看 Udemy (附属链接)中的[课程。](https://www.udemy.com/course/docker-crash-course-learn-from-experience-for-beginners/?referralCode=BAC861F284EACA8695FD)

但是你知道`docker build`背靠背吗？你知道每个单独的构建选项有什么作用吗？

没有任何进一步的麻烦，确保您已经安装了 [Docker 和](https://docs.docker.com/get-docker/)并打开了命令行。

请**注意**对于每个选项，您都有一个测试 docker 文件，您必须将它保存到一个名为`Dockerfile`的文件中，该文件与您运行`docker build`命令的路径相同。

当你看着`docker build --help`时，你会得到相当多的[几个选项](https://docs.docker.com/engine/reference/commandline/build/)。

一个接一个，让我们看看它们是如何工作的。

# `--add-host`

您可以在构建时通过使用一个或多个`—-add-host`标志将其他主机添加到中间容器的`/etc/hosts`
文件中。

对于本例，我们将使用以下 docker 文件:

```
FROM alpine
RUN cat /etc/hosts
```

考虑这个构建，我们为主机`docker`添加了一个静态地址:

```
$ docker build --add-host=docker:10.180.0.1 .Sending build context to Docker daemon  2.048kB
Step 1/2 : FROM debian AS build-env
 ---> 7a4951775d15
Step 2/2 : RUN cat /etc/hosts
 ---> Running in 4f4501c62199
127.0.0.1 localhost
::1 localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
**10.180.0.1 docker** 172.17.0.2 4f4501c62199...
Removing intermediate container 4f4501c62199
 ---> ac7aeb6b6379
Successfully built ac7aeb6b6379
```

请记住，该主机仅在构建时可用。要使定制的主机到 IP 映射对容器可用，请使用带有`docker run`命令的`--add-host`选项。

# `--build-arg`

使用这个选项，您可以参数化您的 Docker 构建。考虑以下 Dockerfile 文件中的参数`param1`:

```
FROM alpine
ARG param1=defaultValue
RUN echo $param1
```

默认情况下，它采用值`defaultValue`。但是您可以在构建时通过执行以下操作来更改它:

```
# docker build --build-arg param1=MYNEWVALUE .Sending build context to Docker daemon  2.048kB
Step 1/3 : FROM debian AS build-env
 ---> 7a4951775d15
Step 2/3 : ARG param1=defaultValue
 ---> Using cache
 ---> 13274abd003b
Step 3/3 : RUN echo $param1
 ---> Running in 4cbc04f56883
**MYNEWVALUE**
Removing intermediate container 4cbc04f56883
 ---> 1569fce8d8a5
Successfully built 1569fce8d8a5
```

# `--cache-from`

此选项需要 BuildKit 后端。到目前为止，您已经注意到，当您从同一个 Docker 文件中重新构建一个映像时，没有任何更改，Docker 将使用以前构建的缓存来加速您当前的构建。你可以从上面的例子中看到这一点，在第 2/3 步，Docker 告诉你“ *- >使用缓存*”。

通常，Docker 使用本地缓存，但是您也可以决定使用远程缓存，以便在您第一次构建某个映像的机器上加速映像构建。

假设您正在使用**机器 A** ，并且您正在使用以下 Docker 文件构建一个名为 *ccordeiro/cache-from* 的 Docker 映像:

```
FROM alpine
RUN apk add curl
```

为了使这个图像在`--cache-from`选项中可用，需要用一个特殊的参数来构建它:

```
$ # enable BuildKit and use the special BUILDKIT_INLINE_CACHE arg
$ DOCKER_BUILDKIT=1 docker build -t ccordeiro/cache-from -f D --build-arg BUILDKIT_INLINE_CACHE=1 .[+] Building 2.5s (7/7) FINISHED
=> [internal] load build definition                                                                                                                                                          => => transferring dockerfile: 63B                                                                                                                                                                       => [internal] load .dockerignore                                                                                                                                                                         => => transferring context: 2B                                                                                                                                                                           => [internal] load metadata for docker.io/library/alpine:latest                                                                                                                                          => CACHED [1/2] FROM docker.io/library/alpine                                                                                                                                                            => [2/2] RUN apk add curl                                                                                                                                                                                => exporting to image                                                                                                                                                                                    => => exporting layers                                                                                                                                                                                   => => writing image sha256:bea0e1eb14c6ede29db907c262f8969f90a93f10e65aaefe57465c7d2605bbfb                                                                                                              => => naming to docker.io/ccordeiro/cache-from                                                                                                                                                           **=> exporting cache                                                                                                                                                                                       => => preparing build cache for export**$ # now you can push this cache image to a Docker registry
$ docker push ccordeiro/cache-from
```

如果您现在移动到**机器 B** ，并且您有以下 Dockerfile:

```
FROM alpine
RUN apk add curl
RUN apk add jq
```

您可以使用*ccordeiro/cache-from*Docker 镜像来加速这个新镜像的构建，方法是为步骤 2(`curl`安装)使用一个缓存，即:

```
$ # enable BuildKit and build the image
$ DOCKER_BUILDKIT=1 docker build --cache-from ccordeiro/cache-from .[+] Building 0.6s (8/8) FINISHED
=> [internal] load build definition from Dockerfile1                                                                                                                                                     => => transferring dockerfile: 92B                                                                                                                                                                       => [internal] load .dockerignore                                                                                                                                                                         => => transferring context: 2B                                                                                                                                                                           => [internal] load metadata for docker.io/library/alpine:latest                                                                                                                                          => importing cache manifest from ccordeiro/cache-from                                                                                                                                                    => [1/3] FROM docker.io/library/alpine                                                                                                                                                                   => **CACHED** [2/3] RUN apk add curl                                                                                                                                                                         => [3/3] RUN ls                                                                                                                                                                                          => exporting to image                                                                                                                                                                                    => => exporting layers                                                                                                                                                                                   => => writing image sha256:615204c4038225c2d45420a44a04cf9dcdcd01923e4c988483a90f027f668542 
```

注意上面的步骤 2，它是从缓存中取出的，尽管您从未在**机器 B** 上构建过这个映像。

# `--cgroup-parent`

无需深入研究 *cgroups* ，这个构建选项只允许您为构建期间使用的容器定义一个父 *cgroup* 。请注意，该选项不会将**中的**表示的*组*保留在最终的 Docker 图像中。所以如果你想让你的容器也有一个父 *cgroup* ，你仍然需要使用`docker run`选项`--cgroup-parent`。

所以让我们考虑下面的 Dockerfile:

```
FROM alpine
RUN sleep 60
```

这个 docker 文件的目标是让我们构建一个映像，并有足够的时间来实际检查这个构建所使用的运行容器。所以让我们从构建图像开始:

```
$ docker build -t my-alpine --cgroup-parent my-cgroup --no-cache .Sending build context to Docker daemon  4.096kB
Step 1/2 : FROM alpine
 ---> d4ff818577bc
Step 2/2 : RUN sleep 60
 ---> Running in **8430cd382553**# the build will hang here for 60 seconds
```

在这 60 秒睡眠期间，打开另一个终端。从上面的片段中，注意 ID **8e3f458ebf15** 。这是为我们的构建运行睡眠指令的容器的 ID。所以让我们来检查一下:

```
$ docker inspect 8e3f458ebf15 -f '{{.HostConfig.CgroupParent}}'my-cgroup
```

给你。您的构建容器正在使用我们在构建时指定的父组 *cgroup* 。

# `--compress`

当您构建 Docker 映像时，Docker 将利用 Docker 文件和**上下文**(无论文件和文件夹位于构建的指定路径或 URL 中，通常都是“.”).

构建上下文越大，完成构建所需的时间就越长，因为 Docker 会加载所有的上下文。

当您使用这个`--compress`构建选项时，您基本上是在要求 Docker 压缩上下文，从而加快您的构建时间。让我们用这个 docker 文件做一个例子:

```
FROM alpine
```

假设我们有一个大的上下文(大文件):

```
$ du -sh *
4.0K Dockerfile
109M big-file
```

现在，如果您在没有任何选项的情况下构建您的映像，您将看到如下内容:

```
$ time docker build .Sending build context to Docker daemon  **114.3MB**
Step 1/1 : FROM alpine
 ---> d4ff818577bc
Successfully built d4ff818577bc**real 0m10.528s** user 0m0.653s
sys 0m0.730s
```

大约 10 秒钟建立一个超级小码头形象！这太多了，都是因为我们上下文中的一个文件，我们甚至没有使用它😛。

现在让我们做同样的事情，但是使用`--compress`:

```
$ time docker build --compress .Sending build context to Docker daemon  **12.91MB**
Step 1/1 : FROM alpine
 ---> d4ff818577bc
Successfully built d4ff818577bc**real** **0m4.392s**
user 0m3.146s
sys 0m0.695s
```

注意现在的上下文大小(几乎比以前小了 10 倍)。这是因为 Docker 在将它发送到我们的构建过程之前对它进行了压缩。因此，我们将构建时间减少了 50%以上。

# `--cpu-period, --cpu-quota`

这两个选项一起使用，它们基本上允许您指定构建容器可以使用多少可用的 CPU 资源。

对于这个例子，让我们假设你正在从你自己的 docker 文件(不管那是什么)构建，并且你的计算机只有一个 CPU。

如果您希望您 Docker 映像构建器只利用 50%的 CPU，您应该如下运行您的构建器:

```
$ docker build --cpu-period=100000 --cpu-quota=50000 .
```

注意，选项的值应该以微秒为单位传递。

# `--cpu-shares/-c`

只有当机器的 CPU 周期受限时，该选项才有意义。它基本上为您的构建容器的重量提供了一个软限制，这成比例地转化为它们对机器 CPU 周期的访问级别。

Docker 文档为这个选项提供了一个很好的例子:

> 例如，考虑一个具有三个以上内核的系统。如果您用运行一个进程的`-c=512`启动一个容器`{C0}`，用运行两个进程的`-c=1024`启动另一个容器`{C1}`，这会导致 CPU 份额的如下划分:
> `PID | container | CPU | CPU share
> 100 | {C0} | 0 | 100% of CPU0
> 101 | {C1} | 1 | 100% of CPU1
> 102 | {C1} | 2 | 100% of CPU2`

# -CPU set-CPU

假设您的机器有 4 个 CPU。对于 Docker 来说，这些可以通过数字来识别，从 0 到 3。因此，如果您希望 Docker 在 Docker 映像构建期间使用一个或多个特定的 CPU，您可以使用该选项，并指定要使用的 CPU 的范围或逗号分隔的列表。

例如，假设您希望仅使用第一个 CPU 构建 Docker 映像:

```
$ docker build --cpuset-cpus=0 .
```

也许您想使用第一个和第三个 CPU:

```
$ docker build --cpuset-cpus=0,2 .
```

或者您可能希望使用除最后一个以外的所有 CPU:

```
$ docker build --cpuset-cpus=0-2 . 
```

# - cpuset-mems

对于 NUMA 系统，这个选项提供了与上面非常相似的东西，允许您选择在 Docker 构建期间使用哪些 NUMA 内存节点。

例如，您希望将构建容器限制为仅使用第一个可用的内存节点，您应该:

```
$ docker build --cpuset-mems=0 .
```

# `--disable-content-trust`

顾名思义，此选项允许您对构建中使用的图像禁用信任检查。显然，这仅适用于在 DOCKER 设置中启用了 DOCKER_CONTENT_TRUST 的情况，默认情况下**不是**。

但是为了这个教程，让我们启用它并运行一个示例。对于这个例子，我们将需要使用一个无符号的 Docker 图像，否则，我们将无法区分(所有的 Ubuntu/Alpine/等)。码头工人的图像是官方的，默认情况下是签名的)。让我们使用下面的 Dockerfile，它是我的一个基本图像:

```
FROM ccordeiro/from-cache
# this is the cache image we've built above...
# good enough for this exercise
```

现在，让我们确保启用了 Docker_CONTENT_TRUST，并尝试从此 DOCKER 文件构建一个新的 DOCKER 映像:

```
$ DOCKER_CONTENT_TRUST=1 docker build .Sending build context to Docker daemonerror during connect: Post "[http://%2Fvar%2Frun%2Fdocker.sock/v1.41/build?buildargs=%7B%7D&cachefrom=%5B%5D&cgroupparent=&cpuperiod=0&cpuquota=0&cpusetcpus=&cpusetmems=&cpushares=0&dockerfile=Dockerfile&labels=%7B%7D&memory=0&memswap=0&networkmode=default&rm=1&shmsize=0&target=&ulimits=null&version=1](http://%2Fvar%2Frun%2Fdocker.sock/v1.41/build?buildargs=%7B%7D&cachefrom=%5B%5D&cgroupparent=&cpuperiod=0&cpuquota=0&cpusetcpus=&cpusetmems=&cpushares=0&dockerfile=Dockerfile&labels=%7B%7D&memory=0&memswap=0&networkmode=default&rm=1&shmsize=0&target=&ulimits=null&version=1)": Error: **remote trust data does not exist for docker.io/ccordeiro/cache-from**: notary.docker.io does not have trust data for docker.io/ccordeiro/cache-from
```

正如您所看到的，Docker 会抱怨，因为它不信任 Docker 的基本形象。但是假设你可以信任我😬无论如何，您都想构建…您可以 DOCKER_CONTENT_TRUST=0、**或**:

```
$ DOCKER_CONTENT_TRUST=1 docker build --disable-content-trust .Sending build context to Docker daemon  2.048kB
Step 1/1 : FROM ccordeiro/cache-from
 ---> bea0e1eb14c6
**Successfully** built bea0e1eb14c6
```

就这样。

# `--file/-f`

这个很简单。它允许您告诉 Docker 在构建中使用什么 Docker 文件。我们知道，默认情况下，Docker 会简单地将您当前路径中的任何文件命名为“Dockerfile ”,但是如果您想要使用另一个 Dockerfile 呢？

让我们假设您有以下文件，名为“Dockerfile”:

```
FROM alpine
RUN echo "I am the original Dockerfile"
```

让我们假设在同一个目录中有第二个文件，叫做“旧文件”:

```
FROM alpine
RUN echo "I am the old Dockerfile, with a custom filename"
```

如果您只是像我们到目前为止所做的那样运行一个构建，您会得到:

```
$ docker build .Sending build context to Docker daemon  3.072kB
Step 1/2 : FROM alpine
---> d4ff818577bc
**Step 2/2 : RUN echo "I am the original Dockerfile"
---> Running in 69b3cb013ce1
I am the original Dockerfile**
Removing intermediate container 69b3cb013ce1
---> b14d64d6d99e
Successfully built b14d64d6d99e
```

因此，如果您想从您的“旧文件”构建，您必须:

```
$ docker build -f OldDockerfile .Sending build context to Docker daemon  3.072kB
Step 1/2 : FROM alpine
 ---> d4ff818577bc
**Step 2/2 : RUN echo "I am the old Dockerfile, with a custom filename"
 ---> Running in 350aaf37f180
I am the old Dockerfile, with a custom filename**
Removing intermediate container 350aaf37f180
 ---> 39d840852b9c
Successfully built 39d840852b9c
```

# `--force-rm`

每当您构建 Docker 映像时，Docker 都会创建 Docker 容器来执行您的 Docker 文件中的指令。Docker 的一个很酷的故障诊断功能是，如果构建出错，Docker 将不会销毁引发异常的容器。通过使用这个`--force-rm`选项，您告诉 Docker 无论如何都要删除那些构建容器。

让我们以下面的 Dockerfile 为例:

```
FROM alpine
RUN echo Hello World
```

如果您从这个 Dockerfile 构建，您将看到:

```
$ docker build .Sending build context to Docker daemon  2.048kB
Step 1/2 : FROM alpine
 ---> d4ff818577bc
Step 2/2 : RUN echo Hello World
 ---> Running in 1d41f5398782
**Hello World**
Removing intermediate container 1d41f5398782
 ---> 569b42fa341b
Successfully built 569b42fa341b
```

因为它是成功的，你会看到没有容器是这个构建遗留下来的:

```
$ docker ps -aCONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

但是，如果我们在 Dockerfile 中引入运行时错误:

```
FROM alpine
# echox is not a valid command
RUN echox Hello World 
```

那么构建将失败:

```
$ docker build .Sending build context to Docker daemon  2.048kB
Step 1/2 : FROM alpine
 ---> d4ff818577bc
Step 2/2 : RUN echox Hello World
 ---> Running in 3af17dec07b4
**/bin/sh: echox: not found** The command '/bin/sh -c echox Hello World' returned a non-zero code: **127**
```

你会发现这个建筑遗留下来的一个容器:

```
$ docker ps -aCONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS                        PORTS     NAMES
3af17dec07b4   d4ff818577bc   "/bin/sh -c 'echox H…"   58 seconds ago   Exited (127) 58 seconds ago             affectionate_satoshi$ docker container prune  # just to clean this up
```

这是 Docker 的标准行为。现在，如果您不想以这些有缺陷的容器结束，您可以在构建过程中使用`--force-rm`选项，就像这样(使用上面相同的有缺陷的 Dockerfile):

```
$ docker build --force-rm .Sending build context to Docker daemon  2.048kB
Step 1/2 : FROM alpine
 ---> d4ff818577bc
Step 2/2 : RUN echox Hello World
 ---> Running in 3af17dec07b4
/bin/sh: echox: not found
The command '/bin/sh -c echox Hello World' returned a non-zero code: 127$ # but this time, no leftover containers
$ docker ps -aCONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

# `--iidfile`

所有的 Docker 图片都有一个 ID，对吗？这是每次构建结束时都会得到的字符串，如"*成功构建****<id>****"*。使用这个构建选项，为了方便起见，您可以要求 Docker 将 ID 保存到一个文件中。

所以让我们假设最简单的 Dockerfile:

```
FROM alpine 
```

然后运行:

```
$ docker build --iidfile my-image-id .Sending build context to Docker daemon  2.048kB
Step 1/1 : FROM alpine
 ---> d4ff818577bc
Successfully built **d4ff818577bc**
```

然后您会在同一个目录中找到一个新文件“my-image-id ”:

```
$ cat my-image-idsha256:**d4ff818577bc193b309b355b02ebc9220427090057b54a59e73b79bdfe139b83**
```

# `--isolation`

如您所知，在 Linux 操作系统上，Docker 利用内核名称空间为容器提供一个隔离层。

然而，在非 Linux 系统(如 Windows)上，您可以选择要使用的隔离技术。在这种情况下，您可以使用`--isolation`来告诉 Docker 在您的映像构建期间使用哪种隔离技术。

可接受的值为:

*   **默认:【Linux 用的就是这个。例如，在 Windows 上，该值取自 Docker 守护进程的配置。如果不存在，则**默认**回退到**流程**，**
*   **流程:**同名称空间隔离，
*   **hyperv:** Hyper-V 虚拟机管理程序隔离。

# `--label`

顾名思义，这个选项允许您为 Docker 图像设置标签。大多数 Docker 图像都有标签。这是一个很好的实践。例如，让我们看看现有的 Docker 图像:

```
$ docker pull nginx 
$ --format '{{json .Config.Labels}}'{"maintainer":"NGINX Docker Maintainers <docker-maint@nginx.com>"}
```

如您所见，图像有元数据，您可以在其中留下一些对其他开发人员和最终用户有用的附加信息。

您可以通过 Dockerfile 中的`LABEL`指令来设置这些标签，但是也可以在构建时通过 CLI 来设置它们。所以让我们假设下面的 Dockerfile:

```
FROM alpine
LABEL my_dockerfile_label foo
```

现在让我们在没有任何额外选项的情况下构建这个映像:

```
$ docker build -t myimage .Sending build context to Docker daemon   5.12kB
Step 1/2 : FROM alpine
 ---> d4ff818577bc
Step 2/2 : LABEL my_dockerfile_label foo
 ---> Running in d326ccec797c
Removing intermediate container d326ccec797c
 ---> 80de62a0f1f0
Successfully built 80de62a0f1f0
Successfully tagged myimage:latest$ docker inspect myimage --format '{{json .Config.Labels}}'
**{"my_dockerfile_label":"foo"}**
```

你可以看到我们的 Dockerfile 标签已经设置好了。但是让我们使用构建选项`--label`再添加几个:

```
$ docker build -t myimage --label my_build_label=bar --label x=1 .Sending build context to Docker daemon   5.12kB
Step 1/4 : FROM alpine
 ---> d4ff818577bc
Step 2/4 : LABEL my_dockerfile_label foo
 ---> Using cache
 ---> 80de62a0f1f0
Step 3/4 : LABEL my_build_label=bar
 ---> Running in 789de28ba48d
Removing intermediate container 789de28ba48d
 ---> f48f1fb8f187
Step 4/4 : LABEL x=1
 ---> Running in c1483a7ac7d8
Removing intermediate container c1483a7ac7d8
 ---> 9fa6da3c3818
Successfully built 9fa6da3c3818
Successfully tagged myimage:latest$ docker inspect myimage --format '{{json .Config.Labels}}'
**{"my_build_label":"bar","my_dockerfile_label":"foo","x":"1"}**
```

# `--memory`

类似于上面提到的`--cpu` *选项，该选项允许您定义映像构建可以占用的内存量的限制。

请**注意**这个选项只有在你的内核支持并且相应的内存 cgroup 被启用时才有效。否则，该选项将不起作用，您将在构建过程中看到如下内容:

> —-->[警告]您的内核不支持内存限制功能，或者未安装 cgroup。取消限制。

尽管如此，让我们假设您的系统支持这个选项，让我们使用下面的 docker 文件

```
FROM alpine
# Generate some memory load
RUN tail /dev/zero
```

如果在没有任何附加选项的情况下从这个 docker 文件构建，您会注意到构建永远不会结束(如预期的那样)，而底层构建容器会过度消耗系统资源:

```
$ docker build .
```

(然后在一个单独的外壳中)

```
$ docker statsName: wonderful_wozniak MemPerc: 44.49% MemUsage: 1.711GiB / 3.844GiB
```

现在让我们假设你不希望你的构建占用超过 10MB 的内存。然后我们做:

```
$ docker build --memory 10000000 .Sending build context to Docker daemon   5.12kB
Step 1/2 : FROM alpine
 ---> d4ff818577bc
Step 2/2 : RUN tail /dev/zero
 ---> Running in 93385ffc4a40
**The command '/bin/sh -c tail /dev/zero' returned a non-zero code: 137**
```

您会注意到构建会立即退出，并出现一个奇怪的非显式错误。然而，如果你检查你的 Docker 系统日志，你会发现这个(或类似的):

```
[1052731.872450] Memory cgroup out of memory: Killed process 89836 (tail) total-vm:20848kB, anon-rss:8960kB, file-rss:716kB, shmem-rss:0kB, UID:0 pgtables:80kB oom_score_adj:0
```

# `--memory-swap`

这和上面的完全一样，只是交换内存。您可以传递“-1”来启用无限制交换。

# `--network`

就像你运行一个容器一样，你也可以定义一个网络来构建图像。这意味着您的构建容器将在构建期间连接到该网络。

所以让我们考虑下面的 Dockerfile:

```
FROM alpine
RUN apk add curl
RUN curl http://mywebserver
```

正如你所看到的，在构建过程中，我们试图用一个自定义的本地 DNS 名称“mywebserver”来访问某种 web 服务。

出于演示目的，让我们创建这个自定义的本地 web 服务器:

```
$ docker network create my-custom-net
$ docker run -d --net my-custom-net --name mywebserver nginx
```

因此，如果我们只是建立我们的形象，而没有任何`--network`选项，就会出现这种情况:

```
$ docker build .Sending build context to Docker daemon   5.12kB
Step 1/3 : FROM alpine
 ---> d4ff818577bc
Step 2/3 : RUN apk add curl
 ---> Running in 0373f7596592
fetch [https://dl-cdn.alpinelinux.org/alpine/v3.14/main/x86_64/APKINDEX.tar.gz](https://dl-cdn.alpinelinux.org/alpine/v3.14/main/x86_64/APKINDEX.tar.gz)
fetch [https://dl-cdn.alpinelinux.org/alpine/v3.14/community/x86_64/APKINDEX.tar.gz](https://dl-cdn.alpinelinux.org/alpine/v3.14/community/x86_64/APKINDEX.tar.gz)
(1/5) Installing ca-certificates (20191127-r5)
(2/5) Installing brotli-libs (1.0.9-r5)
(3/5) Installing nghttp2-libs (1.43.0-r0)
(4/5) Installing libcurl (7.78.0-r0)
(5/5) Installing curl (7.78.0-r0)
Executing busybox-1.33.1-r2.trigger
Executing ca-certificates-20191127-r5.trigger
OK: 8 MiB in 19 packages
Removing intermediate container 0373f7596592
 ---> e3e65f857725
Step 3/3 : RUN curl [http://mywebserver](http://mywebserver)
 ---> Running in 63ded670458d
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0curl: (6) **Could not resolve host: mywebserver**
The command '/bin/sh -c curl [http://mywebserver'](http://mywebserver') returned a non-zero code: 6
```

我们正期待着呢，对吗？我们的构建容器如何找到这样的宿主呢？

现在，如果我们让我们的构建在与之前部署的`mywebserver` Nginx 容器相同的 Docker 网络中运行，会怎么样？

```
$ docker build --network my-custom-net .Sending build context to Docker daemon   5.12kB
Step 1/3 : FROM alpine
 ---> d4ff818577bc
Step 2/3 : RUN apk add curl
 ---> Using cache
 ---> e3e65f857725
Step 3/3 : RUN curl mywebserver
 ---> Running in af0f8eff84e0
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p><p>For online documentation and support please refer to
<a href="[http://nginx.org/](http://nginx.org/)">nginx.org</a>.<br/>
Commercial support is available at
<a href="[http://nginx.com/](http://nginx.com/)">nginx.com</a>.</p><p><em>Thank you for using nginx.</em></p>
</body>
</html>
100   612  100   612    0     0  81372      0 --:--:-- --:--:-- --:--:--   99k
Removing intermediate container af0f8eff84e0
 ---> 04ce6c5fce49
Successfully built 04ce6c5fce49
```

我们走吧！

# `--no-cache`

我们已经简要讨论了构建选项`--cache-from`的缓存机制。所以你现在可能已经注意到，当你从同一个 Docker 文件重建一个映像而不改变它的指令时，Docker 将利用以前的构建的缓存来加速你当前的构建。

这个选项的作用是指示 Docker**不要使用其缓存**，即使 Docker 文件保持不变。示例:

```
FROM alpine
RUN apk add wget
```

第一次**从这个 Dockerfile 文件构建时，您会得到:**

```
$ docker build .Sending build context to Docker daemon   5.12kB
Step 1/2 : FROM alpine
 ---> d4ff818577bc
Step 2/2 : RUN apk add wget
 ---> Running in 4a4c1cc30b18
fetch [https://dl-cdn.alpinelinux.org/alpine/v3.14/main/x86_64/APKINDEX.tar.gz](https://dl-cdn.alpinelinux.org/alpine/v3.14/main/x86_64/APKINDEX.tar.gz)
fetch [https://dl-cdn.alpinelinux.org/alpine/v3.14/community/x86_64/APKINDEX.tar.gz](https://dl-cdn.alpinelinux.org/alpine/v3.14/community/x86_64/APKINDEX.tar.gz)
(1/3) Installing libunistring (0.9.10-r1)
(2/3) Installing libidn2 (2.3.1-r0)
(3/3) Installing wget (1.21.1-r1)
Executing busybox-1.33.1-r2.trigger
OK: 8 MiB in 17 packages
Removing intermediate container 4a4c1cc30b18
 ---> 093923a789ae
Successfully built 093923a789ae
```

然而，**第二次**要短得多，也快得多，因为有缓存:

```
$ docker build .Sending build context to Docker daemon   5.12kB
Step 1/2 : FROM alpine
 ---> d4ff818577bc
Step 2/2 : RUN apk add wget
 ---> **Using cache**
 ---> 093923a789ae
Successfully built 093923a789ae
```

所以如果你不想这样，你可以使用`--no-cache`选项，而**第三次**你从它那里构建你又得到:

```
$ docker build --no-cache .Sending build context to Docker daemon   5.12kB
Step 1/2 : FROM alpine
 ---> d4ff818577bc
Step 2/2 : RUN apk add wget
 ---> Running in 21533387bb7a
fetch [https://dl-cdn.alpinelinux.org/alpine/v3.14/main/x86_64/APKINDEX.tar.gz](https://dl-cdn.alpinelinux.org/alpine/v3.14/main/x86_64/APKINDEX.tar.gz)
fetch [https://dl-cdn.alpinelinux.org/alpine/v3.14/community/x86_64/APKINDEX.tar.gz](https://dl-cdn.alpinelinux.org/alpine/v3.14/community/x86_64/APKINDEX.tar.gz)
(1/3) Installing libunistring (0.9.10-r1)
(2/3) Installing libidn2 (2.3.1-r0)
(3/3) Installing wget (1.21.1-r1)
Executing busybox-1.33.1-r2.trigger
OK: 8 MiB in 17 packages
Removing intermediate container 21533387bb7a
 ---> ff4325b5e414
Successfully built ff4325b5e414
```

# `--pull`

您的构建总是有一个父映像(除非您是从`scratch`构建的，但是我们不要去那里)。根据您使用的父映像，更新可能发生在上游，并且您的本地 Docker 守护进程不会获取它们，因为您已经在磁盘上有了那个映像标记。例如，每次有新的 Ubuntu 版本时,`ubuntu:latest`都会在 Docker Hub 上更新，但是在本地，你可能仍然有 6 个月前发布的`ubuntu:latest`映像。

使用`--pull`选项，您可以告诉 Docker 总是检查更新，并提取您试图在构建中使用的父映像。

```
FROM alpine
```

所以:

```
$ docker build --pull .Sending build context to Docker daemon   5.12kB
Step 1/1 : FROM alpine
**latest: Pulling from library/alpine
Digest: sha256:eb3e4e175ba6d212ba1d6e04fc0782916c08e1c9d7b45892e9796141b1d379ae
Status: Image is up to date for alpine:latest**
 ---> 021b3423115f
Successfully built 021b3423115f
```

观察步骤 1/1，Docker 正在检查我们的“alpine”图像的更新。

# `--quiet`

顾名思义，通过防止 Docker 将最终图像 ID 之外的任何输出打印到您的终端，悄悄地构建您的图像。

所以以下面的 Dockerfile 为例:

```
FROM alpine
RUN echo hello world
```

通常，我们会得到:

```
$ docker build .Sending build context to Docker daemon   5.12kB
Step 1/2 : FROM alpine
 ---> 021b3423115f
Step 2/2 : RUN echo hello world
 ---> Running in 2ecee6b0bec0
**hello world**
Removing intermediate container 2ecee6b0bec0
 ---> a54c702708de
Successfully built a54c702708de
```

但是，通过使用此选项:

```
$ docker build --quiet .sha256:a54c702708deae44841b07c858fb06e6dfea3f6cd09c2f1656aafe56c641a3af
```

这对于编程构建非常有用，可以让您的脚本将生成的图像 ID 直接保存到变量中。

# `--rm`

还记得上面的`--force-rm`选项吗？这很相似，但它只适用于成功的构建。默认情况下，在成功构建之后，用于该构建的所有中间容器都将被移除。这意味着你再也看不到它们了，即使有了`docker ps -a`。

因为这已经是默认的 Docker 行为，所以你不需要明确这个选项……事实上，你根本不需要使用它。

# `-`-安全-选择

在大多数系统中，当您使用此选项时，您会发现类似以下的错误:

> 来自守护程序的错误响应:此平台上的守护程序不支持在构建时设置安全选项

Docker 开发人员自己声称这种选择对于构建是有限的，因为它会导致跨平台的问题。

由于在运行容器时可以使用这个选项，在我看来，它不值得研究，因此我建议你跳过它(事实上，在最新的 Docker 构建引擎 BuildKit 中，这个选项甚至不再存在)。

# `-`-shm-尺寸

如果您的构建需要 I/O 和/或使用大量临时文件，那么您可能希望增加构建容器可以使用的共享内存，以便加快速度。

考虑以下 Dockerfile 文件:

```
FROM alpine
RUN df -hk /dev/shm
```

现在，默认情况下，当您构建这个时，Docker 会给您 64MB 的共享内存:

```
$ docker build --no-cache .Sending build context to Docker daemon   5.12kB
Step 1/2 : FROM alpine
 ---> 021b3423115f
Step 2/2 : RUN df -hk /dev/shm
 ---> Running in 43765967cbf1
Filesystem                Size      Used Available Use% Mounted on
**shm                      64.0M         0     64.0M   0% /dev/shm** Removing intermediate container 43765967cbf1
 ---> 2da9e462a529
Successfully built 2da9e462a529
```

但是，假设您需要 100MB:

```
docker build --shm-size 100m --no-cache .Sending build context to Docker daemon   5.12kB
Step 1/2 : FROM alpine
 ---> 021b3423115f
Step 2/2 : RUN df -hk /dev/shm
 ---> Running in 5a3b9fff2da1
Filesystem                Size      Used Available Use% Mounted on
**shm                     100.0M         0    100.0M   0% /dev/shm** Removing intermediate container 5a3b9fff2da1
 ---> b7563e0bd9b8
Successfully built b7563e0bd9b8
```

# `-`-标签/-t

这可能是 Docker 构建中最常用的选项。它基本上通过在构建时给你的图像分配一个标签来节省一些时间，而不是在之后用命令`docker tag`。

假设您有这样一个 docker 文件:

```
FROM alpine
```

并且您想要构建以下标签:" *mylocalimage:v1"* 和 *"myrepo/image:latest"* 。你应该这样做:

```
$ docker build -t mylocalimage:v1 -t myrepo/image:latest .Sending build context to Docker daemon   5.12kB
Step 1/1 : FROM alpine
 ---> 021b3423115f
Successfully built 021b3423115f
**Successfully tagged mylocalimage:v1
Successfully tagged myrepo/image:latest**
```

# `-`-目标

此选项仅对多阶段构建有用。因此，让我们假设下面的模拟 mock 文件:

```
FROM alpine as stage1
RUN echo stage 1
RUN touch /tmp/myfileFROM alpine as stage2
RUN echo stage 2
COPY --from=stage1 /tmp/myfile /
```

构建此映像时，所有构建阶段都将按顺序执行:

```
$ docker build --no-cache .Sending build context to Docker daemon   5.12kB
Step 1/6 : FROM alpine as stage1
 ---> 021b3423115f
Step 2/6 : RUN echo stage 1
 ---> Running in 5e9de2e786c6
stage 1
Removing intermediate container 5e9de2e786c6
 ---> 6eada12d7407
Step 3/6 : RUN touch /tmp/myfile
 ---> Running in dc14a0455666
Removing intermediate container dc14a0455666
 ---> a85f2471ec88
Step 4/6 : FROM alpine as stage2
 ---> 021b3423115f
Step 5/6 : RUN echo stage 2
 ---> Running in 234689881d2b
stage 2
Removing intermediate container 234689881d2b
 ---> d9b11230c9a2
Step 6/6 : COPY --from=stage1 /tmp/myfile /
 ---> c55de6ea33dd
Successfully built c55de6ea33dd
```

但是，有时您可能只想构建一个特定阶段:

```
$ docker build --target stage1 --no-cache .Sending build context to Docker daemon   5.12kB
Step 1/3 : FROM alpine as stage1
 ---> 021b3423115f
Step 2/3 : RUN echo stage 1
 ---> Running in cb7f20322f89
stage 1
Removing intermediate container cb7f20322f89
 ---> f3b9169b7860
Step 3/3 : RUN touch /tmp/myfile
 ---> Running in 8c1c2dc49874
Removing intermediate container 8c1c2dc49874
 ---> 153965a2d5a0
Successfully built 153965a2d5a0
```

因此，在这种情况下，不考虑“阶段 2”。

**请注意**，如果你在这个例子中说`docker build --target stage2`，那么“阶段 1”也将在“阶段 2”之前构建。

# `-`-乌利米特

If 基本上允许您为构建容器设置“ulimit”标志值。该选项指定了软限制和硬限制:`<type>=<soft limit>[:<hard limit>]`。因此，使用这个 Dockerfile 文件:

```
FROM alpine 
RUN ulimit -n
```

我们可以证明我们的“ulimit”是由 Docker 设置的，如下所示:

```
$ docker build --ulimit nofile=2024:2024 .Sending build context to Docker daemon   5.12kB
Step 1/2 : FROM alpine
 ---> 021b3423115f
Step 2/2 : RUN ulimit -n
 ---> Running in 6a349b2a3c69
**2024**
Removing intermediate container 6a349b2a3c69
 ---> ccd9b4c94333
Successfully built ccd9b4c94333
```

乌夫😅我想现在差不多就这样了。最新的 Docker BuildKit 提供了一些额外的选项，所以我们将在本系列的另一篇文章中介绍。

我希望这对你有用，并作为我的[我的 Docker 初学者课程](https://www.udemy.com/course/docker-crash-course-learn-from-experience-for-beginners/?referralCode=BAC861F284EACA8695FD)(附属链接)的一个很好的补充。
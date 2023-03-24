# 缓存如何节省位桶管道中的构建时间

> 原文：<https://levelup.gitconnected.com/how-caching-can-save-build-minutes-in-bitbucket-pipelines-219d310ab277>

![](img/6f6f5baec961ebe1f6663b1a5d842375.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Aron 视觉](https://unsplash.com/@aronvisuals?utm_source=medium&utm_medium=referral)拍摄的照片

在这篇文章中，我将帮助你学习如何在 Bitbucket 管道中正确设置缓存。这将帮助您加快管道的构建时间，因此您可以更快地交付，并花费更少的构建时间。我还将分享另一个关于如何加快你的管道的想法。

# 什么是高速缓存？

> 缓存(发音为“cashing”)是将数据存储在[缓存](https://searchstorage.techtarget.com/definition/cache)中的过程。
> 
> 缓存是一个临时存储区域。例如，您通过查看网页自动请求的文件存储在您硬盘上浏览器的[目录](https://searchwindowsserver.techtarget.com/definition/directory)下的缓存子目录中。当您返回到最近查看过的页面时，浏览器可以从缓存而不是原始服务器中获取这些文件，从而节省您的时间并减轻网络的额外流量负担。
> 
> 来源:[whatis.techtarget.com](https://whatis.techtarget.com/definition/caching)

# 比特桶管道中可以缓存什么？

> 位桶管道能够在构建之间缓存外部构建依赖项和目录，如第三方库，从而提供更快的构建，并减少所消耗的构建分钟数。
> 
> 来源:[support.atlassian.com](https://support.atlassian.com/bitbucket-cloud/docs/cache-dependencies/)

根据您在管道中所做的事情，不同的内容可以缓存在 Bitbucket 管道中。最大的好处是存储由包管理器检索的下载的第三方库，这样你就不必一遍又一遍地下载外部库了。缓存 Docker 图像/层或缓存构建步骤也可以节省大量时间，后者也可以作为[工件](https://support.atlassian.com/bitbucket-cloud/docs/use-artifacts-in-steps/)在步骤之间共享。

您可以使用预定义的缓存，如`docker`、`pip`、`node`和`maven`，这些已经缓存了特定的文件夹。还可以设置[自定义缓存](https://support.atlassian.com/bitbucket-cloud/docs/cache-dependencies/#Custom-caches-for-other-build-tools-and-directories)，这里要自己设置需要缓存的路径。

# 很高兴知道

下面简短扼要地总结一下你应该知道的事情。

*   成功构建时将存储一个缓存(当还没有缓存时)
*   仅存储小于 1GB(压缩)的缓存
*   缓存将在一周后过期
*   您不应该缓存敏感数据
*   您可以手动清除缓存

# 缓存第三方包

我将分享几个 Bitbucket 配置文件，展示如何缓存下载的第三方包，使用`node`和`pip`作为包管理器。我将提供有缓存和没有缓存的管道运行时间的结果。

## 节点缓存

我从一个简单的管道配置开始，在那里我们安装了`tensorflow`。我认为在管道中你永远不会需要 tensorflow，但这只是因为我想要一个更大的例子。这当然可以是任何包，甚至是 requirements.txt。

```
image: node:10.15.3pipelines:
  default:
    - step:
        script: 
          - npm install tensorflow@0.7.0
```

第一次运行这个管道用了 40 秒，第二次和第三次分别用了 15 秒和 14 秒。让我们看看启用缓存是否真的改善了管道运行时间，这使得平均 **23 秒**。

```
image: node:10.15.3pipelines:
  default:
    - step:
        caches:
          - node
        script: 
          - npm install tensorflow@0.7.0
```

第一次运行上述管道用了 20 秒，在第一次运行时，它还没有缓存，所以它还负责创建和存储缓存，这当然需要更长的时间。我们应该在第二次和第三次运行中看到一些差异。第二次跑用了 11 秒，第三次跑用了 12 秒，平均约为 **14 秒。**因此在节点包上使用缓存已经节省了至少几秒钟。

## Pip 缓存

我们可以对 Python 和 pip 做同样的事情，并希望我用 tensorflow 库再次演示它。下面是我用过的管道配置。

```
image: python:3.7.3pipelines:
  default:
    - step:
        script: 
          - pip install tensorflow==2.3.0
```

第一次运行，没有缓存，用了 47 秒，第二次用了 38 秒，第三次用了 1 分 28 秒。没有缓存的平均运行时间:大约 **58 秒**。我更改了管道配置，并为`pip`添加了缓存。

```
image: python:3.7.3pipelines:
  default:
    - step:
        caches:
          - pip
        script: 
          - pip install tensorflow==2.3.0
```

启用缓存会产生以下结果:1 分 14 秒(没有现有缓存)、42 秒和 43 秒。平均运行时间: **53 秒**。从平均值来看，与没有缓存的管道相比，这又是一个改进。

## 缓存的评估和缺点

结果上下都有“离群值”，这让我怀疑它们是不是真的很好的例子。相同的配置有时可能相差几十秒，尽管缓存启用管道的运行时间看起来更稳定，这可能是由于下载第三方包的延迟。

当已经存在同名的缓存时，缓存将不会更新，即使您缓存的文件夹中的内容发生了变化。当您想要创建新的缓存时，应该在 Bitbucket 中的“管道”页面删除特定的缓存。此外，1 GB 的限制可以很快达到，因此缓存仍然很少或没有用处。

# 其他方法

当您的步骤中需要外部库时，另一种方法是为您的管道创建自己的 Docker 容器。您可以为整个管道设置 Docker 容器，也可以按步骤设置。如果您有一个 lint 步骤(使用已经安装了 pylint 的 Python 映像)和一个测试步骤(安装了测试 Python 应用程序的所有包),这将非常方便。这确保您不必一次又一次地安装这些依赖项。请看下面的例子，假设镜像是我自己构建并部署到 DockerHub 的，包含 Python 3.7.3、pylint 2.6.0 和 pytest 6.0.2，这些名字和版本都是 Docker 镜像名称和标签的一部分。

```
image:
  name: sschrijver/python-pylint-pytest:3.7.3-2.6.0-6.0.2pipelines:
  default:
    - step:
        name: Lint
        script: 
          - pylint .
    - step:
        name: Test
        script: 
          - pytest .
```

此图像的 docker 文件可能如下所示:

```
FROM python:3.7.3RUN pip install pylint==2.6.0 pytest==6.0.2
```

或者你可以使用与上面提到的相同的原理来设置每一步的容器。

```
pipelines:
  default:
    - step:
        name: Lint
        image: sschrijver/python-pylint:3.7.3-2.6.0
        script: 
          - pylint .
    - step:
        name: Test
        image: sschrijver/python-pytest:3.7.3-6.0.2
        script: 
          - pytest .
```

# 问题、建议或反馈

如果您对本文有任何问题、建议或反馈，请告诉我！
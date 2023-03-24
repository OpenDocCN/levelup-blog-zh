# 使用 Docker 和 DigitalOcean 托管一个简单的 Flask 应用程序

> 原文：<https://levelup.gitconnected.com/using-docker-and-digitalocean-to-host-a-simple-flask-app-5d78c9f50ba4>

我听说过很多关于 Docker 的讨论，关于它如何利用容器来简化部署，使它们更易于移植和管理。

![](img/cc8ea7abee5e8188c791ff235d419d95.png)

为了熟悉这个过程，我决定将我以前构建的一个现有项目 dockerize，我的 Letterkenny 句子" *DeGen* erator "，一个简单的 Flask (Python)应用程序，它利用 NLP a la Markov 链的基本形式来分析语料库并生成新句子。

![](img/9e5e5eff09bda6ebdfd5468b7562ff74.png)

这款应用当时部署在 Heroku 上，但在 Docker 和 Digital Ocean 的帮助下，它现在可以在我自己的域上自行托管和部署。呜！

以下是我为此所做的:

> 1.注册一个域名
> 2。创建数字海洋水滴
> 3。将水滴连接到域(制作一个 A 记录)
> 4 .制作一个 Dockerfile(或者 docker-compose.yml 文件)
> 5。用 ca cover
> 6 设置服务器。将容器部署到服务器
> 7。庆祝一下！

## 步骤 1:注册一个域

我使用 NameCheap.com 主要是因为我已经有了一个现有的域名。**专业提示:**如果你是一名学生，还没有这样做，我会研究一下 [Github 学生开发者包](https://education.github.com/pack)，它会给你提供大量免费的开发者工具，包括注册免费域名的几个选项。

## 步骤 2:创建一个数字海洋水滴

*【这里假设你有一个 DigitalOcean 账户。如果您在上一步中获得了 Github 学生开发包，它也应该包含一些 DigitalOcean 信用，但如果您没有，您也可以使用我的* [*DigitalOcean 推荐链接*](https://m.do.co/c/bb60c25bc1f2) *来获得 200 美元的信用，供 60 天试用。】*

为了让这变得简单，我使用了[cap over](https://caprover.com/)，这是一个“非常容易使用的应用程序/数据库部署& web 服务器管理器”。CapRover 是一个方便的数字海洋一键式应用程序，所以你还在等什么，点击那个按钮！

![](img/f749c0edbac965eeb757a828144f95ce.png)

从那里，点击“创建 CapRover Droplet ”,让它做自己的事情。一旦完成，你现在应该有一个 droplet 和它的 IP 地址。下一步你会用到它。

## 步骤 3:将 Droplet 连接到域

导航回 NameCheap，或您的域名提供商。我们将向您的域名添加一个 A 记录，以指向您的新 droplet。转到域列表您的域管理高级 DNS 主机记录添加新记录

从那里，选择一个记录，输入您想要的主机(子域)和您的 droplet 的 IP 地址，然后点按勾号。

![](img/c16400d94c4ac73cd0fed16209d0ba72.png)

对于我的主机，我选择了`*.dev`，这意味着我在 droplet 上托管的任何应用都将位于`[appname].dev.mydomain.com`。如果你不耐烦的话，你可以将 TTL 设置为自动或者更早，但这不是必须的。因此，在我们等待它上线的同时，让我们进入下一步。

## 第四步:制作一个文档

是时候做一个 docker 文件了。想想您通常需要在 CLI 中键入的命令。Dockerfile 是一个简单的文本文件，包含制作图像所需的所有命令。Docker 允许您使用 docker 文件来创建自动化的构建过程。(自动化！祝福。)

docker 文件进入您的项目目录的根目录。下面是我的逐行注释的样子:

好吧，让我们建立它，看看它是否运行！

`docker build -t [imagename] .`

`-t`标志可以让你用一个名字*标记*你的图片，这样你就可以很容易地回忆起你的未来。(PS。不要忘记你的图像名称后的`.`。)

现在让我们试一试！

`docker run -p 5000:5000 --rm --name [containername] [image]`

> 一些注意事项:
> -`-p`标志暴露了`host:container`端口
> -`--rm`标志用于清理，如果你想让 Docker 在容器退出时自动移除 FS。默认情况下，容器的文件系统即使在容器退出后仍然存在。
> -`--name`标志命名你的容器，这使得它在类似`docker ps`的命令输出中更容易识别

好的，前进！

## 步骤 5:用 CapRover 设置服务器

还记得我们在步骤 3 中为你的 droplet 做了 A 记录的 IP 地址吗？这次我们还需要它。(这是 [CapRover 入门](https://caprover.com/docs/get-started.html)页面上的第 3 步)

*   安装盖罩:`npm install -g caprover`
*   然后，运行 serversetup: `caprover serversetup`
*   按照提示，使用:
    - IP 地址:`your droplet IP` -ca cover 服务器根域:`subdomain.yourdomain.com`[如果你像我一样在 A 记录中使用星号作为一个总括(`*.dev`)，请确保在这里格式化它而不使用`*.`，所以对于我的，它将是`dev.mydomain.com`
    - PS。保留那个密码

如果一切顺利，您现在应该能够使用您保留的密码在`captain.subdomain.yourdomain.com`登录。

![](img/73fce7031364c0aae556b70bb2d0494c.png)

我们进去了。

好了，最后的倒计时开始了。

## 步骤 6:将容器部署到服务器

从这里，导航到应用程序，键入您的应用程序名称，然后点击创建新应用程序。然后，(这是我第一次尝试时出错的地方)导航到您新创建的应用程序，并在 docker 文件的“Container HTTP Port”框中输入您暴露的端口

![](img/6f3c9b4cc5e87d00addd272e4997249c.png)

我的是 5000

那你在这一点上应该不错。去吧`caprover deploy`！你应该看到你的应用程序，现在生活和彩色！如果您看到的是:

![](img/96c7427e04a931afb3118e4f21334231.png)

这是我第一眼看到的？第二？我该试试这个了

继续，哦任性的一个！您看到这种情况可能是因为下列情况。

## 如果你的应用程序连接到一个数据库或者有任何环境变量(API 键和你所拥有的):

还有更多的事情要做。我的应用程序还连接到一个数据库，并有它的环境变量，但我们还有希望！我们的数据库只需要作为一个单独的应用程序创建，也由 CapRover 管理。

*   点击侧边栏上的主应用程序链接，然后点击一键应用程序/数据库按钮。
*   在列表中找到你的数据库，我用的是 MongoDB。
*   填写要求的信息并部署！
*   记下你的 MongoDB 的用户名、密码(你刚刚输入的，但也可以在应用程序配置页面上找到)和名称(它应该可以作为`srv-captain-yourdb`在其他队长应用程序内部使用)

从这里开始，您需要返回到初始应用程序，并将该信息添加到 DB 的环境变量中。对我来说，这是我的`MONGODB_URI`，我编辑说`mongodb://user:password@srv-captain-mydb:27017/myapp`。由于我还使用 GIPHY API 在每次生成一个句子时随机加载一个 GIF，所以我还将我的 GIPHY API 键添加到我的环境变量中。

好吧！现在轮到我们`caprover deploy`了！你有它，在你自己的领域上的一个活的应用程序！你表现如何？

## 结束语

因为我部署了一个非常简单的应用程序，所以我能够使用我的导师的可靠启动代码[顺利完成这个过程，只有一个小的变化。展望未来，我将更深入地研究如何使用 docker-compose 文件并尝试多阶段构建。](https://github.com/droxey/docker-flask)

你可以在这里查看我的 Letterkenny 句子*DeGen*operator[及其在](http://letterkenny.dev.okayauco.net/) [Github](https://github.com/aucoeur/tweetgen_lk-docker) 上的源代码。

干杯！
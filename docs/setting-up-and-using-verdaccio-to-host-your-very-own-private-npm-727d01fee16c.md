# 设置并使用 Verdaccio 来托管您自己的私人 NPM

> 原文：<https://levelup.gitconnected.com/setting-up-and-using-verdaccio-to-host-your-very-own-private-npm-727d01fee16c>

![](img/011166b429bedc01b416509a6b389ff8.png)

由[西尔维娅·巴蒂泽尔](https://unsplash.com/@sylwiabartyzel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/safe?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

如果你在一个对共享专有代码有着非常严格的规则的组织中工作，或者你只是想把你的库放在你自己的私有节点包管理器(NPM)上而不被人窥探，你可能会想到创建你自己的包托管服务。

您可能还希望确保存储库易于更新、轻量级且易于托管。

输入 [Verdaccio](http://www.verdaccio.org) 。

Verdaccio 是一个麻省理工学院许可的轻量级私有节点包管理器(NPM)注册表，使您能够轻松地托管自己的 NPM，而不必绞尽脑汁。相信我，这是一个相当艰巨的任务，但有了 Verdaccio，这可以在比你的方便面准备好所需的时间更短的时间内完成(当然，这取决于你的互联网连接)。

维基百科中意大利语 Verdaccio 的实际定义是:

> **Verdaccio** 是黑色、白色和黄色颜料混合物的意大利名称，这种混合物会产生浅灰色或淡黄色(取决于比例)柔和的绿棕色。

# 经由 NPM 设置

您需要做的第一件事是在您的服务器上安装 Verdaccio。为了安装这个 NPM 软件包，您需要在要运行 Verdaccio 的服务器上预先安装 Node.js。

是的，我也感觉到了，在你说什么之前，是的，我知道这有点讽刺，你需要 NPM 来安装 Verdaccio，但是请原谅我。

确保首先在服务器上安装 Node 的长期支持(LTS)版本。

一旦安装了 Node，就可以在服务器上安装 Verdaccio 了。为此，您只需运行以下命令:

```
npm install -g verdaccio
```

这将在您的服务器上安装 Verdaccio，完成后，您可以使用以下命令运行 Verdaccio:

```
verdaccio
```

你应该可以在你的浏览器- > `http://<server address>:8443`上进入服务器的端口`8443`来查看你的 NPM 网站

# 通过 Docker 设置

或者，更快的方法是使用 [Docker](https://www.docker.com/) 。

> *Docker 是什么？*
> 
> 我的解释是:想象一个图书馆，有一台复印机，里面有免费的书。例如，如果你想要 Xyz 先生做的蛋糕，只需拿出 Xyz 先生做的蛋糕书(称为图像)并影印它，它就是你带回家的。

docker 中的一些图像可供公共使用，一些图像是私有的。

因为 Verdaccio 的映像可以通过 docker 很容易地获得，所以您不需要设置 Node。只需直接从 Docker 中提取一个 Verdaccio 映像，并像这样启动它:

```
docker pull verdaccio/verdaccio
```

这将把 verdaccio 映像拉入您的服务器。你现在需要做的就是像这样运行它:

```
docker run -it --rm --name verdaccio -p 4873:4873 verdaccio/verdaccio
```

好了，现在您已经让您的 NPM 从端口`4873`运行在您的服务器上

你应该可以通过浏览器> `http://<server address>:8443`的服务器端口`8443`查看你的 NPM 网站

# 将您的 JS 代码发布到您自己的 Verdaccio

一旦你运行了你自己的 NPM，很明显你想在上面发布你的代码。假设您已经准备好部署一个节点包项目，您所要做的就是运行以下命令:

```
npm publish myproject --registry http://<server address>:4873
```

这将自动将您的节点包项目推入您自己的注册表。

# 安装托管在您的 Verdaccio 上的私有软件包

要将您的私有节点包安装到项目中，您需要执行以下操作:

```
npm install myproject --save --registry http://<server address>:4873
```

是的，就这么简单。你有它，你自己的 NPM 运行在你自己的私人服务器上。从这一点出发，您可以放心地编写代码，因为您知道您的代码是安全的。
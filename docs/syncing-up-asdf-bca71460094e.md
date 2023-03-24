# 同步“asdf”

> 原文：<https://levelup.gitconnected.com/syncing-up-asdf-bca71460094e>

![](img/b36b909278fa6c6f6236863c78453e17.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[猎人哈利](https://unsplash.com/@hnhmarketing?utm_source=medium&utm_medium=referral)拍摄的照片

如果你从事多个具有独特工具需求的软件项目，你可能已经采用了 [*asdf*](https://github.com/asdf-vm/asdf) 。这是管理许多工具的许多版本的简单而有效的方法。它基本上是一个上下文敏感的包管理器。有了 asdf，任何目录都可以包含一个*。tool-versions* 文件，当您切换到该目录时，所有列出的工具和版本都将可用。这不是什么新概念，Ruby、Java 等早就有类似的东西了。但是 asdf 已经支持了数百个包，如果你愿意的话，很容易添加新的包。

## 正在准备

不过，还有一点缺失，*同步*。我的意思是，假设你在一个团队中，每个人都使用 asdf。许多存储库都有*。刀具版本*文件。当您在各种不同的项目中工作时，asdf 将确保您拥有正确事物的正确版本， ***如果您准备好安装了适当的插件和版本*** *。*例如，你一直在用 Java 工作，第一次跳到一个需要 Go 的仓库。对于 asdf 来说，这是基于*的魔法。你需要安装 Go 插件和指定的版本。如果你在很多不同的项目上工作，逆向工程那些需求会变得很烦人。*

## 添加同步器

我给 asdf 加了一个 [*syncher*](https://github.com/nwillc/syncher) 。出于对他人的礼貌，它允许您创建一个脚本，他们可以使用该脚本来同步存储库的所有需求。您只需在带有*的目录中运行 *asdf 同步程序*。tool-versions* ，它将生成一个脚本，您也可以保存在存储库中。当某人第一次开始在存储库中工作时，他们可以通过运行脚本来同步他们的 asdf 设置和存储库需求。Syncher 只是它自己的另一个 asdf 插件。

让我们看看它是如何工作的。如果您是存储库所有者:

```
# One time add of the syncher plugin...
asdf plugin-add syncher https://github.com/nwillc/syncher.git
asdf install syncher 0.0.20
asdf global syncher 0.0.20
asdf reshim syncher# Then using syncher...
asdf syncher > asdf-sync.sh
```

为了一个*。工具版本*如:

```
golang 1.15.3
helm 3.3.4
istioctl 1.7.4
k9s 0.22.1
kubectl 1.19.2
kubectx 0.9.1
```

生成的脚本如下所示:

```
#!/bin/bash
asdf plugin-add golang [https://github.com/kennyp/asdf-golang.git](https://github.com/kennyp/asdf-golang.git)
asdf install golang 1.15.3
asdf plugin-add helm [https://github.com/Antiarchitect/asdf-helm.git](https://github.com/Antiarchitect/asdf-helm.git)
asdf install helm 3.3.4
asdf plugin-add istioctl [https://github.com/rafik8/asdf-istioctl.git](https://github.com/rafik8/asdf-istioctl.git)
asdf install istioctl 1.7.4
asdf plugin-add k9s [https://github.com/looztra/asdf-k9s](https://github.com/looztra/asdf-k9s)
asdf install k9s 0.22.1
asdf plugin-add kubectl [https://github.com/Banno/asdf-kubectl.git](https://github.com/Banno/asdf-kubectl.git)
asdf install kubectl 1.19.2
asdf plugin-add kubectx [https://gitlab.com/wt0f/asdf-kubectx.git](https://gitlab.com/wt0f/asdf-kubectx.git)
asdf install kubectx 0.9.1
```

那么，那些对存储库不熟悉的人可以:

```
./asdf-sync.sh
```
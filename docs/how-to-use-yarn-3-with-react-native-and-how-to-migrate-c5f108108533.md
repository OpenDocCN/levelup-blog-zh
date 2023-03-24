# 如何将纱线 3 与 React Native 配合使用

> 原文：<https://levelup.gitconnected.com/how-to-use-yarn-3-with-react-native-and-how-to-migrate-c5f108108533>

![](img/421eb9a665e4f4fb99dcbac0a61ad458.png)

Yarn Berry(又名 Modern Yarn 或 Yarn 2+)是经典 Yarn 的替代品，它提供了性能改进、对工作区的更好支持以及更干净的开发人员体验。

它最显著的特性之一是即插即用(以下简称“PnP”)，这是一种全新的依赖性管理方法，它消除了运行`yarn install`和抛弃`node_modules`文件夹的需要。

然而，React Native 是唯一不支持 PnP 的著名项目之一，而且这种情况不太可能很快改变。

本文将解释什么是 PnP，为什么 React Native 不支持它，以及我们如何解决这个问题以享受 Yarn 3 的大部分优势。

# PnP 是什么？

在经典的 Yarn(和`npm`)中，我们在一个名为`package.json`的清单文件中定义我们的依赖关系。我们称之为直接依赖。然而，我们的依赖性也可以有自己的依赖性。这些是我们的[传递依赖](https://en.wikipedia.org/wiki/Transitive_dependency)。

每个依赖项都有许多自己的依赖项，形成一棵“依赖树”，这很正常。这个树结构存储在`yarn.lock`中，以防止不同版本的包管理器创建不同的树，这可能会导致各种各样的意外行为。

当我们运行 yarn install 时，使用文件和文件夹重新创建完整的依赖树，并存储在名为`node_modules`的文件夹中。

在 PnP 中，我们没有`node_modules`文件夹。相反，每个直接的和可传递的依赖项被压缩成一个 zip 文件，然后存储在`.yarn/cache`中。一个名为`.pnp.js`的文件包含了[‘魔法’](https://yarnpkg.com/features/pnp#fixing-node_modules)，它允许 node 从这个新结构中读取依赖关系。由于其大小和复杂性([和所有其他原因](https://byjoeybaker.com/why-you-should-never-commit-node-modules-in-nodejs))，我们不应该提交我们的`node_modules`文件夹，但是提交这些 zip 文件很简单:每个依赖项一个文件。

既然我们有了一个提交依赖关系的存储库，那么在切换分支或检出项目时就不需要运行`yarn install`。因此，PnP 也被称为“零安装”。

# 为什么 PnP 不能和 React Native 一起使用？

简单来说:React Native 做出依赖于`node_modules`文件夹存在的假设。由于 PnP 中没有`node_modules`，这些引用需要更新。

2018 年末，Github 问题在`react-native-community/cli`上开放，以跟踪 PnP 支持的进展。然而——差不多四年过去了— [,进展并不大。](https://github.com/react-native-community/cli/issues/27#issuecomment-819320665)

前 Meta 工程师[克里斯托夫·中泽友秀](https://twitter.com/cpojer) [今年早些时候发推文](https://twitter.com/cpojer/status/1495511984330412034)称，内部曾尝试过 switch Yarn Berry，但由于技术问题而被放弃。似乎 PnP 支持不是 Meta 的优先考虑事项，所以我们现在能做的最好的事情就是解决它。

# 引入节点链接器

为了让反应土著高兴，我们需要带回`node_modules`。幸运的是，Yarn 3 包括一个单线配置选项，使这变得简单。

配置是通过一个`.yarnrc.yml`文件完成的，该文件通常位于项目的根目录下。向该文件添加`nodeLinker: node-modules`指示 Yarn 在我们运行`yarn install`时创建`node_modules`文件夹，就像经典 Yarn 一样。这意味着我们仍然可以在`.yarn/cache`中缓存我们的依赖项，并将它们提交给 repo，同时也满足 React Native 的要求。

显然我们不能再称之为‘零安装’了，但这一步最多需要几秒钟。由于依赖关系树是预先计算的，并且所有的依赖关系都存在于磁盘上的`.yarn/cache`中，当我们从缓存中组装巨大的`node_modules`文件夹时，唯一限制这一步速度的是磁盘写入速度。

# 迁移

以下是如何迁移您的项目。

> 这是[官方移民指南](https://yarnpkg.com/getting-started/migration/#gatsby-focus-wrapper)的摘要，你也应该看一下。
> 
> 本指南假设您没有使用新的选择加入[节点核心包](https://nodejs.org/api/corepack.html)功能。尽管这是 Yarn 团队安装新版本的首选方式，但它在 Node 中仍被标记为“实验性的”,因此我们将暂时退出。

1.  **在你的资源库中运行** `**yarn set version berry**` **。** 该命令将下载最新的 Yarn 3 二进制文件，存储在`.yarn/releases`并创建一个`.yarnrc.yml`文件。
2.  **添加** `**nodeLinker**` **选项。** 在`.yarnrc.yml`中，加上`nodeLinker: node-modules`
3.  **更新你的** `**.gitignore**` **。** 添加以下几行:
    `.yarn/*
    !.yarn/cache
    !.yarn/patches
    !.yarn/plugins
    !.yarn/releases
    !.yarn/sdks
    !.yarn/versions`
4.  **更新脚本。** 用`yarn dlx` [代替`yarn global`为什么？](https://github.com/yarnpkg/berry/issues/821)
    用`yarn --immutable`替换`yarn --frozen-lockfile`
5.  **更新自定义注册表设置。** 在经典纱线中，`.npmrc`用于定义自定义注册表设置。在 Yarn Berry 中，它们必须在`.yarnrc.yml`中定义。
    请注意，您可以同时拥有全局和特定于项目的`.yarnrc.yml`。
    此步骤仅适用于使用需要自定义配置的私有托管包的情况。
6.  **安装。** 运行`yarn`。您的锁定文件将被迁移，并且您的所有依赖项将被缓存在`.yarn/cache`中。
7.  **提交**。Git 添加所有新缓存的依赖项。最初的差异将是数百个文件，但将来添加和更新包时，每次只会更改几个。
8.  **推。** 当其他贡献者退出回购时，他们会自动开始使用 comitted Yarn 3 二进制。不再有管理工具版本！🚀

# 🫐浆果时光

现在，即使没有 React Native 的一流支持，您也可以享受 Yarn 3 的(大部分)优势。在我看来，仅性能改进一项就使它成为绿地项目的明确选择，安装时间的减少可以在 CI 方面为您节省大量资金。

你觉得纱莓怎么样？Meta 应该更加关注对 PnP 的支持吗，或者他们现在忽略它是正确的做法吗？有想法就留言评论吧。

我的名字是汤姆·麦金托什，我喜欢打造完美的用户体验。如果你正在开发一个应用程序，需要有人帮助你把想法从草图变成真正的设备，我很乐意和你聊聊。我还致力于现有应用的重构、性能改进和升级。

你可以通过 [tjmc.dev](https://tjmc.dev) 联系到我。
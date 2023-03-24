# 如何使用 npm 和 Yarn 保持您的依赖关系最新

> 原文：<https://levelup.gitconnected.com/how-to-keep-your-dependencies-up-to-date-with-npm-and-yarn-5ee7e3a4981e>

![](img/f5e0283a3860b93fc9166dc97a0cff8a.png)

# 介绍

[包管理器](https://en.wikipedia.org/wiki/Package_manager)使得日常使用他人的代码更加流畅和标准化。无法以一致的方式完成常见任务的日子已经一去不复返了，这些任务包括:

*   安装/卸载节点程序包及其依赖项
*   创建/发布依赖关系
*   **保持软件包版本以及*其*依赖项版本的最新状态**

这最后一点，也就是本文将要讨论的内容。今天，有了像 [npm](https://www.npmjs.com/) 和 Yarn 这样的包管理器，就有了方便地更新包及其依赖项的方法。

# 依赖关系和版本控制

npm 和 Yarn 都遵循[语义版本化](https://semver.org/)的规则来表示一个包的给定版本。每个包版本从`1.0.0`开始，在不同的点上进展，像这样分解。

许多软件包使用其他现有的软件包来提供其独特的功能。这些包被称为“依赖项”。下一节将展示如何更新单个依赖项。

# 更新单个包依赖项

要检查一个包中过时的依赖项，使用带有 npm 或 Yarn 的`outdated`命令:

```
$ npm outdated $ $ yarn outdated
```

这将显示可以更新到较新版本的包依赖项列表。以下是更新单个依赖项的一些方法。

## npm

`npm update`命令，当与一个特定的包名一起使用时，更新那个包。一些次要的语法要点需要注意:

假设我们已经安装了过时版本的:

```
$ npm update lodash@4.17.10$ npm update lodash@latest
```

## 故事

在 Yarn 中，命令是相似的。不要用`update`，用`up`。

继续使用 lodash 示例，下面是对特定版本的更新:

这是最新版本的更新:

```
$ yarn up lodash
```

# 更新所有包依赖项

虽然我们可以使用`npm update`或`yarn upgrade`来更新`package.json`文件约束内的所有依赖项，但本节涵盖了将所有依赖项更新到它们的*最新主版本*。

有一个叫做`[npm-check-updates](https://www.npmjs.com/package/npm-check-updates)`的包，它被设计用来更新所有的依赖关系，而不管`package.json`中指定了什么。它的速记别名是`ncu`。

因为 npm 和 Yarn 都可以访问 npm 注册表，`npm-check-updates`与两者都兼容！

*原载于【https://blog.mydevdiary.net】[](https://blog.mydevdiary.net/how-to-keep-your-dependencies-up-to-date-with-npm-and-yarn-cktbm9mc901gutjs1chnj73mw)**。***
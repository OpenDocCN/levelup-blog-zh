# 在开发工作流中实现良好的提交消息约定

> 原文：<https://levelup.gitconnected.com/implement-good-commit-message-conventions-in-your-development-workflow-15dd78b7a86e>

## 使用传统提交工作流编写更好的提交消息

![](img/66ac9e696e2ae50ef155973b1996773e.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

实现改进和编写良好提交消息的更好方法可以为开发团队节省大量的时间和精力，而不是什么都没有。提交消息是当今任何软件开发人员的日常茶。编写好的可读的提交消息很方便，尤其是当您有一个大型团队在一个项目上协作时。

提交消息有助于轻松理解对代码库所做的更改的性质。编写更好的提交消息可能是一个巨大的挑战，尤其是当您在开发工作流中没有配置更好的提交约定时。

**为什么更好地提交消息？**

编写更好的提交消息有很多好处，例如:

*   帮助开发人员就变更的内容和原因交流变更的范围。
*   确保提交消息格式的一致性。
*   同步语义版本化(即，自动版本化，生成变更日志)。
*   帮助未来的开发人员轻松了解您的变更状态。
*   提高开发人员的生产力。
*   更好的结构化提交历史，因此更容易做出贡献。

虽然有许多约定在不同的团队中使用，但是有一个通用的标准被广泛采用。

## 常规提交

常规提交是一种为提交消息添加人类和机器可读含义的规范。

> 传统的提交规范是提交消息之上的轻量级约定。它为创建显式提交历史提供了一组简单的规则；这使得在之上编写自动化工具变得更加容易。通过描述提交消息中的特性、修复和重大更改，这个约定与 [**SemVer**](http://semver.org/) 相吻合。

## **常规提交消息结构**

传统的提交消息采用以下结构。

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

类型是必需的，可以采用以下形式。

**专长:**新特性，**修复:**问题/错误修复，**文档**:文档相关变更， **ci:** 持续集成， **perf:** 性能改进，**测试:**测试相关变更，**杂务:**工具/配置变更，**重构:**代码重构，**风格:**风格变更，**回复**

正文和页脚是可选的，您可以提供来进一步改进和解释您提交的更改。

描述是强制性的，包含已提交变更的实际摘要消息。如果您想进一步解释您的更改，您可以在可选的正文部分提供它们。

**警示突变**

您还可以在作用域的末尾添加`!`来引起对重大变化的注意。

```
chore!: drop support for Vue 2
```

***下面考虑两个提交消息的场景***

**常规提交消息**

```
endpoint to check if user is a publication member
```

**常规提交消息**

```
feat(api): endpoint to check if user is a publication member
```

哪个提交消息看起来很详细？

第二个提交消息精确地详细说明了更改的原因和内容。仅仅通过阅读，开发者就能够在不打开代码的情况下识别出这是一个 api 特性。

**在开发工作流程中配置常规提交**

在这一部分中，我们将深入并配置开发工作流中的常规提交。该配置将在基于 JavaScript 的开发环境中进行。

**安装**

你可以使用 NPM 或纱取决于你最喜欢的包经理。

**使用国家预防机制**

```
npm install --save-dev @commitlint/config-conventional @commitlint/cli
```

运行下面的命令来配置***commit list***使用传统配置

```
Configure commitlint to use conventional config
echo "module.exports = {extends: ['@commitlint/config-conventional']}" > commitlint.config.js
```

**使用纱线**

```
yarn add --dev @commitlint/{config-conventional,cli}
```

在提交历史中创建之前，我们将需要 Husky 为 lint 提交提供的`commit-msg`挂钩。首先，我们需要安装哈士奇。

```
yarn add husky // or npm install husky
```

运行下面的命令来添加`commit-msg`钩子。

```
yarn husky add .husky/commit-msg 'yarn commitlint --edit $1'
```

找到 ***package.json*** 文件，在脚本部分添加下面的`"postinstall":"husky install"` ***。*** 这将在运行 ***纱线安装*** 的任何情况下自动安装 git 挂钩。

至此，您应该已经在开发工作流中成功地配置了常规提交。

现在，每当您编写提交消息时，git 钩子都会检查消息是否符合传统提交所规定的格式。如果你提供了一个没有应用规则的提交消息， ***commit-lint*** 将抛出一个错误和一个可能的解决方法。

**资源**

*   [https://github.com/conventional-changelog](https://github.com/conventional-changelog)
*   [https://www . conventionalcommits . org/en/v 1 . 0 . 0/#规范](https://www.conventionalcommits.org/en/v1.0.0/#specification)

**出发前**

在开发工作流中，有一个更好的约定来管理提交消息是非常方便的。这使得小团队和大团队成员更容易为项目做出贡献，而不那么担心。

每周三，我都会发送一封独家邮件，里面有我发现的有用的、与技术写作相关的技巧、文章、应用、书籍和想法。

[***加入像你一样想提高技术写作水平的人。***](https://artisanal-thinker-2556.ck.page/6e2ba71172)

**更多阅读内容**

[](/5-best-practices-to-follow-for-rest-api-development-3cff92987136) [## REST API 开发要遵循的 5 个最佳实践

### 开发 REST API 时要遵循的最佳实践

levelup.gitconnected.com](/5-best-practices-to-follow-for-rest-api-development-3cff92987136) [](/5-advanced-and-in-depth-learning-resources-for-vue-js-fec1146ea41f) [## Vue.js 的高级和深入学习资源

### 帮助您学习 Vue.js 的高级概念并超越基础知识的资源

levelup.gitconnected.com](/5-advanced-and-in-depth-learning-resources-for-vue-js-fec1146ea41f) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**软件工程师的顶级工作**](https://jobs.levelup.dev/jobs?utm_source=pub&utm_medium=post)
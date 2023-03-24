# 改进 JavaScript 中的提交和版本控制

> 原文：<https://levelup.gitconnected.com/improve-your-commits-and-versioning-in-javascript-56f72c0ab761>

使用传统的提交和语义版本控制来改进和简化 JS 项目中的提交和版本冲突。

![](img/4c409e075ad373d624bbc4d05cf9a66b.png)

[扬西·敏](https://unsplash.com/@yancymin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

开发人员经常忽视或忽略标准或约定的使用。可能是因为他们觉得这不重要，也可能是因为他们觉得这是不必要的额外工作。我的经验告诉我，这两种假设都与现实相去甚远。

事实是，标准的存在是为了简化你的工作。如果不是这样，那么很可能是该标准的实现有问题。

在本文中，我将尝试展示如何使用标准和约定通过自动化来简化和加速某些任务。

但是首先，在我们进入“如何做”之前，让我们从“什么”开始和“为什么？”。

![](img/e4c95b0d492c4b64e539047555e29790.png)

由 [Rohit Farmer](https://unsplash.com/@rohitfarmer?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 什么是常规提交？

[常规提交](https://www.conventionalcommits.org)是一个规范，它建立了编写提交消息的标准方式。这实际上非常简单，它基本上说明了提交消息至少应该有一个类型和一个描述。对于这些字段，您可以添加一些可选字段，如范围、正文或页脚。

```
<type>[optional scope]: <description>

[optional body]

[optional footer]
```

最重要的类型是`feat:`和`fix:`，正如它们所暗示的那样，第一种类型用于添加新功能的提交，第二种类型用于修复某个 bug(补丁)。

这些都很重要，因为它们直接链接到了[语义版本](https://semver.org/) (SemVer)规范。如果您熟悉 SemVer，您会知道基本的版本控制在主要。次要补丁格式(如 1.0.0)。

因此，如果你添加了一个专长类型的提交消息，这意味着你的版本将冲击未成年人(如 1。 **1** .0)，而如果你增加了一个修复类型，就会撞上补丁(比如 1.0。 **1** )。

此外，还有中断更改文本，它不是一种类型，应该用在正文或页脚文本的开头。它被用来代表一个重大的改变(例如 **2** .0.0 ),它通常只与专长或固定类型相关联。

您也可以使用其他类型，如`chore:`、`docs:`、`style:`、`refactor:`、`ci:`、`improvement:`

## 为什么在项目中使用常规提交很重要？

*   在团队中轻松识别和交流每个提交的性质；
*   轻松识别哪些提交与版本碰撞相关；
*   使用 SemVer 标准实现版本自动升级；
*   自动化变更日志的创建，并拥有项目中引入的所有变更的有意义的历史视图。

![](img/d19e574e27283a7dbc5aa092227e0a2a.png)

照片由[欧文·史密斯](https://unsplash.com/@mr_vero?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 如何在您的 JavaScript 项目中实现这一点？

仅仅为了这个例子，我将使用一个 React 应用程序，但是这可以应用于任何使用 NPM 的 Javascript 项目。

**1 -因此，让我们从创建一个新的 React 应用程序开始:**

```
npx create-react-app my-app
cd my-app
npm start
```

此时，您应该已经运行了一个基本的 React 应用程序。

**2 -在 git 中设置您的项目，例如，在 Github 中创建一个空的 repo，并将您的项目文件添加到其中:**

```
git remote add origin git@github.com:username/your-repo.git
git branch -M main
git push -u origin main
```

**3 -接下来我们安装**[**commit list**](https://commitlint.js.org/)**，这样我们可以检查提交消息是否符合常规提交:**

```
npm install --save-dev [@commitlint/cli](http://twitter.com/commitlint/cli) [@commitlint/config-conventional](http://twitter.com/commitlint/config-conventional)
```

我们创建一个配置文件 *commitlint.config.js* ，如下所示:

```
module.exports = {
  extends: [
    '[@commitlint/config-conventional](http://twitter.com/commitlint/config-conventional)'
  ]
}
```

现在我们有了自己的项目和检查提交消息是否符合约定的能力，但是最好是自动执行。

**4 -为了自动检查提交消息，我们将使用**[**Husky**](https://github.com/typicode/husky)**:**

```
npm install husky --save-dev
```

激活挂钩:

```
npx husky install
```

添加一个每当有人尝试 git commit 时触发的钩子:

```
## linux / macos
npx husky add .husky/commit-msg 'npx --no -- commitlint --edit "$1"'## windows
npx husky add .husky\commit-msg "npx --no -- commitlint --edit $1"
```

到目前为止，我们已经完成了项目设置，现在我们正在检查我们团队的提交消息是否符合约定。如果没有，将会显示一个错误。

既然我们已经确保了传统的提交将被遵守。是时候相应地自动化版本控制了。

**5 -安装** [**标准版**](https://github.com/conventional-changelog/standard-version) **:**

```
npm i --save-dev standard-version
```

将 npm 运行脚本添加到您的包中。json:

```
{
  "scripts": {
    "release": "standard-version"
  }
}
```

一切就绪后，让我们运行第一个版本:

```
npm run release -- --first-release
```

这将:

1.  生成 CHANGELOG.md 文件；
2.  创建新的提交；
3.  在不影响版本的情况下标记一个版本。

在我们的示例项目中，本应该创建一个新的 CHANGELOG.md 文件，并使用提交消息“gerry(release):0.1.0”将该文件添加到 git 中，因为 package.json 文件中 create-react-app 设置的初始版本是 0 . 1 . 0。

一切都准备好了！

只需构建您的项目，遵循常规提交，每当您想要创建一个新版本时，只需运行:

```
npm run release
```

它将自动检查自上次标记的发布以来的所有提交消息；

1.  将相关的添加到 CHANGELOG.md 文件中；
2.  在 package.json 里撞版本；
3.  用新版本提交并标记发布；

## 重要说明

在您的项目达到版本 1.0.0 之前，标准版本将只更新补丁编号，而不管提交类型，以及带有重大更改文本的次要编号。这是一种有意的行为，因为在 1.0.0 版本之前，您仍处于 Alpha 阶段，因此这可以防止主要版本的碰撞。因此，一旦您准备好发布第一个主要版本，只需运行:

```
npm run release -- --release-as major
```

从现在开始，所有随后的版本将与既定惯例保持同步。

## **延伸阅读**

使用这些工具，您可以做更多的事情，它们可以根据您的需求进行配置和定制。只需查看它们中每一个的链接！

例如，您可以自定义变更日志创建，以更改提交的链接，更改发布标签比较的链接，并通过使用#issueId 约定，为您的问题跟踪平台包括相关的工作项目/问题。如果你只使用 GitHub，标准版已经为你做到了。但如果你使用 Gitlab、Bitbucket、吉拉或任何其他平台，你可能会发现这很有用。
您还可以指定哪些提交类型在变更日志中可见。

为此，只需创建一个配置文件 *.versionrc.json* ，并根据您的情况使用以下示例:

```
{
  "types": [
    {"type": "feat", "section": "New Features"},
    {"type": "fix", "section": "Bug Fixes"},
    {"type": "improvement", "section": "Improvements"},
    {"type": "chore", "hidden": true},
    {"type": "docs", "hidden": true},
    {"type": "style", "hidden": true},
    {"type": "refactor", "hidden": true},
    {"type": "test", "hidden": true}
  ],
  "commitUrlFormat": "https://gitlab.com/{{owner}}/{{repository}}/commit/{{hash}}",
  "compareUrlFormat": "https://gitlab.com/{{owner}}/{{repository}}/compare/{{previousTag}}...{{currentTag}}",
  "issueUrlFormat": "https://gitlab.com/{{owner}}/{{repository}}/issues/{{id}}"
}
```

如果你已经读到这里，感谢你的阅读。如果你熟悉这些库，欢迎在评论中添加更多的技巧！
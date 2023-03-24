# 如何将你的代码释放到野外

> 原文：<https://levelup.gitconnected.com/how-to-release-your-code-into-the-wild-dd144218cb9b>

## …在这个过程中不会发疯

![](img/7f08028b18117c4379dfbdf85ab220df.png)

中途，莫比乌斯和恩基·比拉风格的鸟笼

编码很好。但不可避免的是，总有一天我们需要编译我们生成的代码，并将最终结果发布出去。无论是以应用程序、网站、程序、共享库还是 NPM 模块的形式…

这可能一开始很容易管理。

但是随着项目的增长，**推出代码的复杂性也在增加。活动部件越多，损坏的机会就越多。**

例如，在工作中，我们使用集成了 React 应用程序的 PHP 后端堆栈。因此，将新版本投入生产包括:

*   从我们使用的翻译工具下载任何**翻译**
*   运行**棉绒**检查代码质量，
*   运行**单元测试**，
*   缩小 javascript 代码
*   **更新版本号**
*   **将各种 React 组件**构建成产品包，
*   将编译好的 javascript 推送到 **CDN**
*   在 **AWS** 上将 PHP 代码推送到服务器。

忘记这些步骤中的任何一个都会带来麻烦。我浪费了几个小时试图记住该做什么，以及如何解决困扰部署过程的问题。当你试图记住如何更新一个你已经三个月没做的项目时，情况会更糟。

那么答案是什么呢？

原则上，解决方案很简单:自动化一切可以自动化的东西。

我有好消息告诉你:有一个强大的工具可以让你自动化很多 T21 的事情。它叫做 GitHub Actions。但这令人望而生畏，所以今天我们要看看:

*   如何使用`release-it`和`auto-changelog`来编写你的发布过程
*   如何设置 GitHub 动作来自动化发布过程

# 使用 release-it 释放 NPM 模块

首先，让我们看看如何部署我们的代码。我将使用我创建的 [NPM 模块](https://github.com/Kodaps/faker)来开发一个简化版本。

在继续下去之前，这里有一句忠告。如果使用 yarn 而不是 npm，用 GitHub 动作发布 NPM 模块会复杂得多。我的建议是在你可能有的任何`yarn.lock`文件上运行`git rm`，并且只使用 npm。(使用纱线是可行的，但是有点复杂)。

现在，我每次更新我的 npm 包时都使用五个步骤。

1.  我确保我的代码是最新的(通过 git pull)。
2.  我通过运行单元测试(用 Jest)和林挺(用 ESLint)来确保代码是干净的。
3.  我准备了一个变更日志，列出了自上一版本以来的所有变更。
4.  然后，我设置了新的版本号。
5.  最后，我提交并发布了全部内容，既发布给 GitHub，也发布给 NPM。

**这里有一个快速提示**:每当我自动化任何事情时，我都是从创建一个本地脚本文件开始的，该文件按顺序运行所有的步骤。例如，为了推出代码，我倾向于创建一个名为“up.sh”的批处理文件，它位于我的项目的根目录下，运行所有的任务。

但是我遇到了一个工具，它可以满足我所有的出版需求，甚至更多。这是一个名为 [release-it](https://github.com/release-it/release-it) 的 NPM 模块，我现在在几乎所有的项目中都使用它。请允许我向你展示它是如何工作的。

首先，为了设置它，我们运行:

```
npm init release-it
```

该工具会询问几个问题。它要么将其配置存储在`package.json`中，要么存储在一个名为`.release-it.json`的单独文件中。我个人更喜欢第二种方式，因为我的 package.json 文件通常已经包含了相当多的内容。

现在，在我提到的五个步骤中，一个是跟踪和更新版本号。这已经包含在发布工具中了。一步完成了，还有四步。

默认情况下，如果 git 存储库不处于干净状态，release-它还会拒绝*做任何事情。例如，如果有未提交的工作正在进行。默认情况下，这已经解决了。*

下一件事是确保我们只从实际上应该被发布的 git 分支中发布。为此，我们在配置文件(`.release-it.json`)中添加一个约束，如下所示:

```
{
  "git": {
    "requireBranch": "main",
  }
}
```

这是不言自明的:只有在“主”分支上才运行。在工作中，我们使用一个叫做“预生产”的分支来准备和测试我们的代码，所以我用它来代替。

在这里，我们还可以指定发布包时将创建的提交消息:

```
{
  "git": {
    "requireBranch": "main",
    "commitMessage": "chore: release v${version}",
  }
}
```

现在，接下来的步骤是确保我的代码是最新的，并测试它。我已经使用 Jest 和 EsLint 在我的项目中设置了测试，并使用“npm run test”和“npm run lint”运行它们。

我希望这些在任何事情发生之前就开始运行，所以我使用了一个 release-it 生命周期挂钩，而且有很多这样的挂钩。所以在我的配置文件中，我现在在`before:init`钩子中有一个包含三个项目的列表:

```
{
  "git": {
    "requireBranch": "main",   
    "commitMessage": "chore: release v${version}",
  },
  "hooks": {
    "before:init": ["git pull", "npm run lint", "npm run test"],
  }
}
```

这也是不言自明的。我们为 release-it 定义了一个钩子，它在开始工作之前运行，并且用`git pull`更新代码。然后用`npm run lint`检查林挺错误，用`npm run test`运行单元测试。

我想做的最后一件事是添加一个变更日志；列出我们在发布之间所做的一切。但是我们写的提交已经陈述了我们做了什么，所以我们也可以自动化这个。

为此，我们将使用一个叫做 [auto-changelog](https://github.com/cookpete/auto-changelog) 的工具。

为了触发它，我们将使用另一个名为`after:bump`的钩子。顾名思义，一旦版本号被“升级”,它就会运行。

```
{
  "git": {
    "requireBranch": "main",
  },
  "hooks": {
    "before:init": ["git pull", "npm run lint", "npm run test"],
    "after:bump": "npx auto-changelog -p",
  }
}
```

这里我们添加了一个-p 标志，告诉工具版本号存储在 package.json 文件中。

这将使用提交数据创建一个 changelog 文件，并创建发布信息。

最后，我们指定要将代码发布到 GitHub，并发布到 NPM。

```
{
  "git": {
    "requireBranch": "main",
  },
  "hooks": {
    "before:init": ["git pull", "npm run lint", "npm run test"],
	"after:bump": "npx auto-changelog -p",
  },
  "github": {
    "release": true
  },
  "npm": {
    "publish": true
  }
}
```

现在是检验它的时候了。如果您查看 package.json 文件，init 应该已经在脚本部分添加了一个 release 条目:

```
{
  [...]
  "scripts": {
    "release": "release-it",
    [...]
  },
}
```

这允许我们运行`npm run release`来触发释放过程。当我们这样做的时候，我们会被问到一系列的问题:我们是想把它定义为一个小的升级，一个大的升级，还是一个突破性的改变？我们要出版吗？诸如此类。这允许我们运行整个过程并检查正在发生的事情。

一旦它以您想要的方式工作，现在就是时候…进一步自动化它了！

# 使用 GitHub 动作使事情更加自动化

## 了解工作流文件

为此，我们将使用 GitHub 操作。现在，什么是 GitHub 动作，它们是如何工作的？

假设你在一家公司，一个新的开发人员加入了你的团队。你是做什么的？

嗯，首先你给他买一台新电脑，然后你帮他下载代码，安装好所有东西，然后开始工作。

从某种意义上说，GitHub Actions 做的也是同样的事情。它设置了一个带有操作系统的容器。然后它下载软件和我们代码的当前版本。最后，它执行一组任务…

为此，我们创建了一个名为`.github`的文件夹。在该文件夹中，我们创建另一个名为`workflows`的文件夹。在这个文件夹中，我们创建了一个名为`release.yml`的文件。

这里有三个必填字段:`name`、`on`和`jobs`。它们定义了工作流的名称、触发时间以及采取的操作。

在我们的例子中，工作流将被称为“发布&发布到 NPM ”,所以我们相应地填写名称。

而且会手动触发，所以我们把“开”字段设置为:`“workflow_dispatch”`。(我们可以在这里添加几个不同的触发器，并配置它们，但在我们的例子中，我们不需要这样做)。

我们的发布配置文件现在看起来像这样:

```
name: Release & Publish to NPM
on: workflow_dispatch
```

现在到了有趣的部分，即`jobs`字段！这定义了可以运行的作业列表，每个作业都是步骤列表。我们的第一个(也是唯一的)任务将被称为`release`，现在我们开始配置它！

我们首先使用“runs-on”参数指定我们想要运行的操作系统。这里我指定了 ubuntu。现在我们定义步骤列表。我们从简单的开始，只是为了测试一下

我们做的第一件事是检索代码，为此，我们将使用一个名为“actions/checkout@v2”的预打包动作。这个是由 GitHub 创建的，有许多不同用例的可用操作。

在第二步中，我们安装依赖项，为此，我们只需在步骤中指定我们想要运行的命令。所以这里我们有执行动作`npm ci`(它运行一个干净的安装)。

最后，我们记录工作已经完成，同样，我们只需运行一个“echo”命令。

```
jobs:
  release:
    runs-on: ubuntu-20.04
    steps:
      - name: Checkout source code
        uses: actions/checkout@v2
      - name: Install the dependancies
        run: npm ci
      - name: End message
        run: echo 'All done!'
```

让我们从测试这个开始。为此，我们提交工作流文件并将其推送到主分支。

正如您在访问存储库时看到的，有一个“Actions”选项卡。我们的工作流程现在就列在那里。在这里，我们可以手动触发工作流。为此，我们单击用户界面中的“运行工作流”按钮。

当它完成运行时(或者甚至当它还在运行时！)，我们可以打开日志。我们可以看到不同的步骤在哪里，以及是否有任何错误。

## 配置 git 和 NPM

下一步是完成工作流中 git 和 npm 的设置。为此，我们在列表中添加了另一个步骤，在 checkout 动作之后，我们声明了我们想要使用的 git 用户和电子邮件

```
- name: Initialize Git user
        run: |
            git config --global user.email "david@kodaps.com"
            git config --global user.name "Release Workflow"
```

现在，我们需要能够在不登录的情况下发布到 NPM，为此，我们需要一个“发布”令牌。

为了检索这个，我们需要前往 NPM 网站。登录后，我们需要选择访问令牌。然后回到 GitHub，在存储库页面，我们点击设置，然后秘密>行动。在这里，我们单击“New repository secret”并输入`NPM_TOKEN`作为机密名称，然后粘贴令牌的值。

现在我们需要配置 npm 在与 NPM 注册中心对话时使用令牌。我们使用`npm config set`命令，并传递刚刚生成的秘密`NPM_TOKEN`/

```
- name: Initialise the NPM config
        run: npm config set //registry.npmjs.org/:_authToken $NPM_TOKEN
        env:
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

现在，有一个问题。该命令在容器上创建一个`.npmrc`文件。该文件将阻止发布，因为 git 目录将不再干净。

因此，让我们将该文件添加到`.gitignore`文件中:

```
#.gitignore
node_modules/
.env
.npmrc
```

## 现在发布

现在，让我们使用 release 命令和`--ci`标志(代表持续集成)，用发布包的命令替换最后的日志。

我们还需要为动作提供两个令牌。第一个是 GitHub 令牌，默认情况下由操作提供。

```
- name: Run release
        run: npm run release --ci
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

我们最终的工作流现在看起来像这样

```
*# .github/workflows/release.yml* name: Release & Publish to NPM
on: workflow_dispatch
jobs:
  release:
    runs-on: ubuntu-20.04
    steps:
    - name: Checkout source code
      uses: actions/checkout@v2
    - name: Install the dependancies
      run: npm ci
    - name: Initialise the NPM config
      run: npm config set //registry.npmjs.org/:_authToken $NPM_TOKEN
      env:
        NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
    - name: Initialize Git user
      run: |
        git config --global user.email "david@kodaps.com"
        git config --global user.name "Release Workflow"
    - name: Log git status
      run: git status
    - name: Run release
      run: npm run release --ci
      env:
        NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

# 最后

我发现`release-it`和 GitHub 动作的结合非常强大。无论是在个人项目中还是在工作中，它们一起缓解了我反复出现的痛点。

到目前为止，我只将它应用于 NPM 模块和 NextJS 网站(虽然我也使用 GitHub 动作来自动更新我的个人资料)，但我期待着也使用它们来构建和发布移动应用程序。当然，您可以做很多有趣的事情，比如发送 Slack 或 Discord 通知，设置 cron 来触发 API 调用，等等。如果你想让我解释怎么做，请告诉我！

# 更多资源:

*   [GitHub 动作文档](https://docs.github.com/en/actions)
*   [松开它](https://github.com/release-it/release-it)
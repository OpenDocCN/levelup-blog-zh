# git beautiful 承诺与哈士奇

> 原文：<https://levelup.gitconnected.com/git-prettier-commits-with-husky-e5bcf2da4dac>

![](img/81e7defa19aef370e285887a45bcd3e5.png)

上周，我第一次设置 Node.js 后端，在研究组织发展选项时掉进了兔子洞。输入 [Git 挂钩](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)—这些挂钩允许用户在指定的 [git](https://git-scm.com) 事件发生时运行定制脚本。你可以用任何你喜欢的语言编写钩子，你甚至可以查看 git 在你的项目目录下安装的示例脚本。导航到`.git/hooks`查看它们。

我还碰到了遵循[常规提交](https://www.conventionalcommits.org/en/v1.0.0/)规范的 NPM 包[提交列表](https://github.com/conventional-changelog/commitlint)。这是一个规范，说明了我们的提交消息中应该包含什么，不应该包含什么，这样当其他人(或者你自己，三个星期后)阅读提交消息时，就可以清楚地知道到底更改了什么。当一次查看所有过去的提交时，它还会创建一个更具描述性的 changelog。虽然它非常容易阅读，但最初的规则有点难以理解。

我强烈建议看一看他们的[文档](https://www.conventionalcommits.org/en/v1.0.0/#specification)或[这张幻灯片](https://slides.com/marionebl/the-perks-of-committing-with-conventions#/3)，以充分理解该规范是如何构成的。简而言之，你的评论应该总是以下列“类型”之一开头。

```
build, ci, chore, docs, feat, fix, perf, refactor, revert, style, test
```

您可以包括提交的范围(文件名，应用程序的功能等。)，尽管这不是必需的，随后是对修改的简短描述。

commit list 给出的例子有…

```
chore: run tests on travis cifix(server): send cors headersfeat(blog): add comment section
```

为了确保我的提交消息符合[常规提交](https://www.conventionalcommits.org/en/v1.0.0/)规范，并且代码格式正确，在每次提交时，我们将利用 [Git 钩子](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)。此外，为了让我们自己超级轻松，我们将使用 NPM 套件[哈士奇](https://www.npmjs.com/package/husky)。我们要处理的只是一些 NPM 命令，并稍微编辑一下我们的 package.json。不需要进入 git hooks 目录。我们开始吧！

# 设置 Husky 和 commitlint

首先，让我们安装[哈士奇](https://www.npmjs.com/package/husky)和[commit list](https://github.com/conventional-changelog/commitlint)。因为我们将它用于开发目的，所以记住要抛出`--save-dev`标志。这确保了它不会成为生产版本的一部分，那样只会浪费不必要的空间。为了节省几秒钟的时间，我们将使用下面的命令同时安装这两个包。

```
$ npm i --save-dev husky @commitlint/{config-conventional, cli}
```

接下来，我们需要配置[commit list](https://github.com/conventional-changelog/commitlint)来使用常规配置。(注意:这是一个单行命令。)

```
$ echo "module.exports = {extends: ['@commitlint/config-conventional']}" > commitlint.config.js
```

现在剩下要做的就是编辑我们的`package.json`文件。为 Husky 的 v4 添加以下内容，这是撰写本文时的当前稳定版本。

```
{
  "husky": {
    "hooks": {
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
  }
}
```

太好了！一切就绪。现在当你运行`git commit -m <yourMessage>`时，commitlint 将检查以确保它符合[常规提交](https://www.conventionalcommits.org/en/v1.0.0/)规范。如果符合规范，[commit list](https://github.com/conventional-changelog/commitlint)将允许提交。如果没有，它将返回一个错误，让您知道消息有什么问题。

我还使用了这个 vscode 扩展来确保我的提交消息符合常规提交规范[。然而，有了这个钩子，我再也不用担心其他贡献者的提交消息会违反规范。](https://www.conventionalcommits.org/en/v1.0.0/)

# 用 quiet-quick 设置预提交挂钩

因为我们已经安装了[哈士奇](https://www.npmjs.com/package/husky)，我们需要做的就是安装[漂亮的](https://prettier.io)和[漂亮快速的](https://github.com/azz/pretty-quick)。别忘了`--save-dev`旗！

`$ npm install --save-dev prettier pretty-quick`

然后编辑`package.json` …

```
{
  "husky": {
    "hooks": {
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS",
      "pre-commit": "pretty-quick --staged"
    }
  }
}
```

现在每当你提交的时候，所有的代码都会在提交之前被格式化为更漂亮的规格！

最后要注意的是，如果你像我一样对[Deno](https://deno.land)check out[Velociraptor](https://github.com/umbopepato/velociraptor)感到兴奋。它是“Deno 的脚本运行程序，灵感来自 npm 的 package.json 脚本。”他们即将推出的功能之一是“哈士奇风格的 git 挂钩！”
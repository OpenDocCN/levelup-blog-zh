# 如何使用 GitHub 动作移动 git 标签

> 原文：<https://levelup.gitconnected.com/how-to-move-a-git-tag-using-github-actions-e23a523eb325>

![](img/5597d9895da1a20e2d184ad947352e5a.png)

图片来自 [Pixabay](https://pixabay.com/ru/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=3887038) 的 [Prettysleepy](https://pixabay.com/ru/users/prettysleepy-2973588/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=3887038)

有时，您可能希望将标签作为 GitHub 操作管道的一部分进行移动或推进。

首先，确保您知道自己在做什么— **在 git 存储库中移动标签通常是一个坏主意**，因为它可能会扰乱其他人的存储库，并导致不可再现的构建和部署问题。但是，有一些工作流和情况证明移动标签是合理的。

例如，您的存储库中可能有一个`latest`或者一个`nightly`标签，您用它来执行每夜的构建。

如果您想使用您的本地开发环境手动移动一个标记，这将非常简单:

```
# delete the tag both locally and in the origin remote
git tag -d nightly
git push origin :nightly# recreate the tag at the new location
git tag nightly
git push origin nightly
```

但是手工工作一点也不好玩。让我们尝试使用 [GitHub Actions](https://github.com/features/actions) 来实现自动化。

首先，我们将创建一个新的`advance-nightly-tag.yaml`文件，并把它放到我们的`.github/workflows`文件夹中。出于测试目的，我们只在推送时触发它:

```
name: "advance nightly tag"
on:
    push:
jobs:
    advanceNightlyTag:
      runs-on: ubuntu-latest
```

将有一个使用`actions/github-script`动作的单一步骤:

```
steps:
- name: Advance nightly tag
  uses: actions/github-script@v3
  with:
    github-token: ${{secrets.GITHUB_TOKEN}}          
    script: |
```

在`script`部分，我们基本上可以编写任何 JavaScript 代码。我们将使用 [oktokit/rest.js](https://octokit.github.io/rest.js/) ，它可以在这个脚本中的`github-script`下使用，变量名为`github`。它允许我们使用 JavaScript 调用 GitHub API。这里有一个调用将删除一个`nightly`标签:

```
github.git.deleteRef({
  owner: context.repo.owner,
  repo: context.repo.repo,
  ref: "tags/nightly"
})
```

这里有一个调用将重新创建它，注意指向当前提交的`context.sha`参数:

```
github.git.createRef({
  owner: context.repo.owner,
  repo: context.repo.repo,
  ref: "refs/tags/nightly",
  sha: context.sha
})
```

我们可以一个接一个地运行这两个调用，这很可能会成功。然而，我们将使我们的代码更具弹性:

*   **这些对 API 的调用是异步的**，这意味着第一个调用可能在第二个调用之前在服务器上执行，脚本将会失败。我们将在每次调用前添加`await`关键字，这样第二次调用只在第一次调用完成后执行；
*   如果标签最初不存在，第一次调用将会失败，所以我们将把它包装到`try-catch`中，并提供一个有意义的警告消息。

这里是我们的`advance-nightly-tag.yaml`文件的完整代码:

```
name: "advance nightly tag"
on:
    push:
jobs:
    advanceNightlyTag:
      runs-on: ubuntu-latest
      steps:
      - name: Advance nightly tag
        uses: actions/github-script@v3
        with:
          github-token: ${{secrets.GITHUB_TOKEN}}          
          script: |
            try {
                await github.git.deleteRef({
                  owner: context.repo.owner,
                  repo: context.repo.repo,
                  ref: "tags/nightly"
                })
            } catch (e) {
              console.log("The nightly tag doesn't exist yet: " + e)
            }
            await github.git.createRef({
              owner: context.repo.owner,
              repo: context.repo.repo,
              ref: "refs/tags/nightly",
              sha: context.sha
            })
```

GitHub 动作[的示例项目可以在 GitHub](https://github.com/forketyfork/test-actions) 获得。
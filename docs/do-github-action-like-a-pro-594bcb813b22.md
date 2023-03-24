# 像专业人士一样做 GitHub 动作！

> 原文：<https://levelup.gitconnected.com/do-github-action-like-a-pro-594bcb813b22>

## 最佳 CI/CD 实践

## 一组您希望在发布第一个动作之前就知道的最佳开发实践。

![](img/00af6930af4cdc9f72bcf72b8a20f0d5.png)

Marc Sendra Martorell 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我不知道你怎么想，但是软件工程让我感兴趣，因为它能让事情自动化，让我变得懒惰。现在发生的大多数开发从根本上自动化了一个或另一个服务。然而，任何大规模的事情都意味着将会有更多的任务，作为开发人员工作流的一部分，这些任务只有在自动化的情况下才能帮助加速快速开发。

不，即使听起来有一点相似，我也不想成为 DevOps 的拥护者，因为这个术语的解释非常多样和广泛。

现在，当谈到开发工作流时，有相当多的东西可以自动化，而不仅仅是 CI/CD 服务通常提供的“构建/测试/打包”周期。这就是为什么 GitHub Actions 是处理它的完美工具——因为你已经在一个地方得到了代码、文档、问题、拉请求、代码审查、发布，甚至[包](https://github.com/features/packages)！

# TL；博士；医生

希望现在我已经说服你至少试一试，并且用一杯咖啡阅读文档，我应该消除一个误解——GitHub 动作仍然是代码片段，并且对于曾经编写的每一个代码，它也有自己的一套开发实践要记住。

这些构建可重用操作的推荐实践是本文的重点。所以，事不宜迟，让我澄清一下本文的范围:

*   为什么发布动作而不是编写 bash/python/{ insert scripting language }脚本？
*   动作发布的自动化构建和打包。
*   按照 [GitHub 建议](https://github.com/actions/toolkit/blob/master/docs/action-versioning.md#versioning)自动对您的操作进行适当的版本控制。
*   写测试！(你真的不认为没有它你就能逃脱吗？:D)

# 为什么要使用/发布动作

自动化工作流程需要对该工作流程的每个单独任务进行编程，这需要另一个单位来维护仪器。即使像安装构建依赖项这样简单的任务也可能会突然失败，需要进行调试。那么，像发送短信提醒或发布新闻稿这样的复杂任务呢？

当然，您的团队可能会自己为这些任务编写一个 shell 或 python 脚本，但考虑到长期维护和稳定性，这只是要求额外的工作来拖累开发。

让我们讨论另一种情况，您的团队确实为一个项目编写并维护了这样一个脚本，但是您发现另一个项目需要用类似的用例来编写一个任务。当然，为了解决这个问题，您可能会将该脚本放在它自己的 repo/gist 中，并在需要的地方卷曲它。听起来像黑客？是啊，我也有同感:)。

幸运的是，GitHub 意识到一个简单的 CI/CD 工作流无法满足它，部分原因是上述问题，并提供了一个 [Marketplace](https://github.com/marketplace?type=actions) 来存储和记录所有这些可重用的程序，称为 Actions，它们被用作大多数工作流的构建块。也就是说，工作流的每个作业都有一组步骤，其中每个步骤代表一个单独的任务，并且可以运行命令或这些操作之一。

# 动作发布的自动化构建和打包。

自动化无处不在！:P

在首先，我认为这些动作听起来类似于 NPM 托管的大量 JS 开发库，特别是因为 GitHub runner 完全支持 Javascript 动作。然而，Javascript Actions 和 NodeJS 库有一个主要的区别——***package . JSON*在 Actions** 的情况下完全没有用。虽然编写 npm 脚本在开发这些动作时确实很方便，但 GitHub Action 只需要 *action.yml* manifest 文件，该文件描述了要运行的动作名称、输入和主 JS 文件。见鬼，即使是包版本也没关系，因为动作是通过指定提交引用(标记/分支/提交 SHA)在工作流中使用的。

由于这些动作并没有真正打包并存储在一个中央存储库中(不像 NPM)，而是直接使用 GitHub 在一个正在运行的作业中获取所需的动作，所以它们可以非常灵活地[存储](https://docs.github.com/en/actions/creating-actions/about-actions#choosing-a-location-for-your-action)并在公共和私有存储库中使用。但是，我将只关注将动作存储在它自己的存储库中，简化它的维护、发现和发布管理。

根据 GitHub 的建议，我们应该为他们的动作发布使用标签。然而，这里有一个警告——正在运行的作业获取动作源代码，压缩成一个 tarball，由该标签指向提交。这意味着带标签的提交应该有一个现成的构建代码，即主 JS 来运行。但是，在 VCS 从不提交 [*node_modules/*](https://byjoeybaker.com/why-you-should-never-commit-node-modules-in-nodejs) 或[*build*code](https://kentcdodds.com/blog/why-i-dont-commit-generated-files-to-master/)不是众所周知的惯例吗？难道我们不先提交 [*。忽略任何新项目的第一步？*](https://www.toptal.com/developers/gitignore/)

为了探索避免提交构建代码的方法，我查看了官方的 GitHub 操作，如 [setup-go](https://github.com/actions/setup-go) ，但是你会发现他们确实提交了构建代码，[*dist/index . js*](https://github.com/actions/setup-go/blob/master/dist/index.js)，尽管通过使用 [@zeit/ncc](https://github.com/vercel/ncc) 构建一个单独的文件来避免提交 *node_modules* 。不，我不能忍受那个黑客，特别是知道我很可能在提交之前忘记运行 *npm run build* (虽然 [husky](https://github.com/typicode/husky) 可以解决这个问题，但是这会使我的提交动作有点慢)。

探索更多的动作，我喜欢在它自己的发布分支中“检入”构建的代码，然后标记它的想法，就像在[设置类动作](https://github.com/engineerd/setup-kind)中使用的那样(并在[官方动作模板](https://github.com/actions/typescript-action#publish-to-a-distribution-branch)中记录)。虽然这是一个非常优雅的解决方案，但我也意识到它仍然在我的工作流程中添加了一些手动任务…这是使用 Github Actions 来自动化它的一个完美理由！

我写了这个工作流程，它可以作为你自己行动发布管理需求的模板。让我首先澄清我的一些假设:

1.  根据 *package.json* 对动作进行版本控制。
2.  *主*分支受到保护，任何对其的推送都将有不同的包版本。
3.  “构建包”步骤在 *dist/* 文件夹中输出构建好的代码，不需要 *node_modules* 。

这样，人们也可以为 JS 动作重用用于版本化节点库的标准技术(如 [semver](https://www.npmjs.com/package/semver) )。

名为“添加并提交”的步骤强制添加(因为它在*中)。gitignore* )在 *dist/* 文件夹中构建的代码，提交给它一个新的发布分支，“release/v < version >”并适当地标记它。

“发布”作业在“构建”作业之后运行，根据前一个作业中使用的标签创建一个新的发布，从而很好地管理打包和发布。

**PS:** 目前有一个 GitHub bug，即由 Action 创建的发布不能发布到 Marketplace，尽管这些标签仍然可以在工作流中使用。

# P 对您的操作进行适当的版本控制

大多数开发项目都将语义版本化放在心上，并鼓励开发人员在严格约束和宽松约束之间取得平衡，通常会根据 semver 选择对补丁发布灵活的约束。

然而，GitHub 建议在[他们的文档](https://github.com/actions/toolkit/blob/master/docs/action-versioning.md#versioning)中版本化你自己的动作时，要有一个稍微不同的视角:

> 绑定到主要版本是该主要版本的最新版本(例如，v1 == "1。*" )
> 
> 主要版本应该保证兼容性。主要版本可以添加新功能，但不应破坏现有的输入兼容性或现有的工作流。

通过主版本绑定，用户可以期望操作的主版本包括必要的关键修复和安全补丁，同时仍然与他们现有的工作流保持兼容。每当您的更改影响到兼容性时，您应该考虑发布一个新的主版本。

GitHub 建议了一个[很好的方式](https://github.com/actions/toolkit/blob/master/docs/action-versioning.md#recommendations)来两全其美——语义版本化和主版本绑定。基本上，遵循语义版本化实践，让上面的工作流自己创建一个版本。然后使用 git，强制创建一个[注释标签](http://alblue.bandlem.com/2011/04/git-tip-of-week-tags.html)，使用各自的主要版本，指向发布的标签。

```
git tag -fa v1 -m "Update v1 tag"
git push origin v1 --force
```

就像你可能已经猜到的那样，有一种方法可以自动完成这项艰巨的任务，:D！您可以使用 [actions-tagger](https://github.com/Actions-R-Us/actions-tagger) 来自动保持您的主要版本在任何新版本上保持最新。以下是您可以直接使用的模板工作流程:

# 写测试！

> "所有代码都是有罪的，直到被证明是无辜的."

我希望您不只是计划为您的操作直接发布未测试的代码？:P
虽然在这一点上它是非常多余的，就像任何其他代码一样，但是你应该致力于编写测试，这样它就不会让一个有效的工作流失败。大多数动作使用 [Jest](https://www.npmjs.com/package/jest) 来满足他们的测试需求，所以我想这就足够了。

我有一个建议——确保只有经过测试的代码被合并到 master 中，否则，您可能会无意中创建了一个没有通过测试的新版本。

# 一些令人兴奋的动作！

## [常规变更日志操作](https://github.com/TriPSs/conventional-changelog-action)

难以管理大型项目中的所有 PRs 和版本冲突？不要担心，也有一个行动！假设您的项目遵循[常规提交标准](https://www.conventionalcommits.org/en/v1.0.0/)，

## [ChatOps 动作](https://github.com/machine-learning-apps/actions-chatops)

有没有希望有一个机器人跟踪你的回购问题，监听你的命令来触发一些可怕的行动？是的， [ChatOps](https://www.pagerduty.com/blog/what-is-chatops/) 也入侵了 Github Actions！:D

# 有用的链接

*   [官方 JS 行动模板](https://github.com/actions/typescript-action)
*   [创建一个 JS 动作](https://docs.github.com/en/actions/creating-actions/creating-a-javascript-action)

# 漫无边际的讲话

我开始写我在创建自己的动作时获得的所有知识， [setup-k8s-operator-sdk](https://github.com/shivanshs9/setup-k8s-operator-sdk) 。我确实搞砸了一些初始版本，所以决定记录一些我希望以前就知道的技巧。谢谢你一直读到最后，我希望听到一些反馈和更多有用的建议！
# 修复 Git 认证错误

> 原文：<https://levelup.gitconnected.com/fix-git-authentication-error-ff9cf7f40a07>

## 不到一分钟就修好了！

![](img/a9cf23e8e470e7de367bd4bd43eb14f4.png)

[车头](https://unsplash.com/@headwayio?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[档](https://unsplash.com/s/photos/github-computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍照

我遇到了这个问题，因为没有设置上游分支，所以我无法推送到存储库源。于是我尝试了`git push --set-upstream origin my-branch`。这不起作用，它只是挂在那里，似乎卡在过程中。我取消了它(control-c ),开始谷歌搜索。

我在 GitHub 上发现[这个](https://github.com/thoughtworks/talisman/issues/42)问题与一个叫做 Talisman 的库有关。我没有使用 Talisman，但其行为与我所经历的类似。我试了一下，然后…

![](img/e88b85d4377007ad195103ebc94ac6cb.png)

…它给了我一个错误。

![](img/585b6e20ba7f5315fe0055edeb2e7a1b.png)

但这很好！错误是我们的朋友。它们是有帮助的——至少比一个只是挂起而什么也不做的命令更有帮助。所以我再次使用谷歌，发现[这个](https://stackoverflow.com/questions/17659206/git-push-results-in-authentication-failed)线程堆栈溢出。

被选中的(大部分是被投票支持的)答案提到需要创建一个[个人访问令牌](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)，我已经完成了，但是再看一会儿，答案是这样的:

> 如果存储库的来源设置为 HTTPS，您可能还需要更新它。要切换到 SSH，请执行以下操作:

接下来是:

```
git remote -v
git remote set-url origin git@github.com:USERNAME/REPONAME.git
```

我这样设置遥控器(用我的 Github repo URL 替换上面的占位符),然后可以再次尝试`git push — set-upstream origin my-branch`。这样做之后，我把那些泄露秘密的成功的`git push`行陆续打印在我的终端上。

有不同意见？放在评论里吧！

[***升级您的免费 Medium 会员资格***](https://matt-croak.medium.com/membership) *并接收各种出版物上数千名作家的无限量、无广告的故事。这是一个附属链接，你的会员资格的一部分帮助我为我创造的内容获得奖励。*

*你也可以通过电子邮件* [***订阅，当我发布新内容时，你会收到通知！***](https://matt-croak.medium.com/subscribe)

*谢谢！*

# 参考

[](https://github.com/thoughtworks/talisman/issues/42) [## git push-set-upstream origin my-new-branch 挂起问题#42 thoughtworks/talisman

### 当我在本地创建一个新的分支并想要推送它时，设置上游失败:$ git push - set-upstream origin…

github.com](https://github.com/thoughtworks/talisman/issues/42) [](https://stackoverflow.com/questions/17659206/git-push-results-in-authentication-failed) [## Git 推送导致“认证失败”

### 我昨天开始在 Ubuntu 20.04 中的 Visual Studio 代码上遇到这个问题。我没有对我的…做任何更改

stackoverflow.com](https://stackoverflow.com/questions/17659206/git-push-results-in-authentication-failed) [](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) [## 创建个人访问令牌- GitHub Docs

### 注意:如果您使用 GitHub CLI 在命令行上认证 GitHub，您可以跳过生成个人访问…

docs.github.com](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
# 如何将文件重置为旧版本

> 原文：<https://levelup.gitconnected.com/how-to-reset-a-file-to-an-old-revision-eb9061f61e94>

![](img/18938028df43d7faca066dfb1e03658a.png)

在这篇文章中，让我们来看看将文件重置为特定版本的不同方法。

## 使用结帐

```
git checkout <commit_hash_id> -- <file_path> **Example** git checkout **cc1d4a7** -- example.py
```

我认为上面的命令非常简单明了。

您正在将文件恢复到特定提交。换句话说，git 将获取提到的文件，并对其应用提到的提交更改。

你也可以提到多个文件来恢复。

```
git checkout cc1d4a7 -- example.py index.py
```

## 使用还原

```
git restore --source <commit_hash_id> <file_path> **Example** git restore --source **cc1d4a7** index.py
```

我们在之前的版本中已经看到过**还原**。恢复是一个新命令，它有助于恢复特定的修订。

在上面的命令中，我们使用 **-source** 选项告诉 restore 只使用特定的提交散列。我们还提到了文件路径，以便只对提到的文件应用更改。

您可以使用上述任何命令来重置文件。我个人最喜欢的是**还原**。

感谢您的阅读:)

> 如果你已经来了这么久，那么我想你对 Git 很感兴趣。可以订阅我的简讯[***GitBetter***](https://gitbetter.substack.com/)***获取 Git 的招数、技巧、高级话题。***
# 如何避免在终端中意外删除文件

> 原文：<https://levelup.gitconnected.com/how-to-avoid-accidentally-deleting-files-in-terminal-969d63ab1c02>

在 Mac 和 Linux 上避免文件删除灾难的技术

![](img/e1a32de5f50d02a32efc0229f9ad35c9.png)

照片由 [Gary Chan](https://unsplash.com/@gary_at_unsplash?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我永远不会忘记当我还是一名网络工程师的时候，一位同事因为一个简单的错放的点而让整个大学校园陷入瘫痪。

我同事犯的错误是输入:

```
rm -rf /*
```

而不是:

```
rm -rf ./*
```

第二个命令递归地删除当前目录和所有子目录中的所有文件。第一个从根目录和所有子目录中删除所有文件。Unix 系统上的根目录是包含整个文件系统的顶级目录。

更糟糕的是，我的同事以 root 用户*T21 的身份登录，他拥有完全的管理员权限。`rm`删除文件前不提示确认。*

> 所以，通过 10 次简单的击键，他意外地清除了整个服务器。我只想说网络经理不开心！

# 避免错误

`rm -rf`应该有节制有意识地使用。有些情况下，它是这项工作的合适工具。

然而，可以采取一些简单的步骤来避免灾难。

首先，不要以 root 用户身份登录或使用`sudo`，除非你完全习惯于永久删除你的文件(或者至少从备份中恢复)。

第二，养成在`rm`命令后面添加`-i`的习惯，因为你可能不确定要删除什么。`-i`将使`rm`交互运行，并提示删除文件和目录。

不要试图在 shell 的配置文件中将`rm`别名为`rm -i`。这将产生一个坏习惯，即认为`rm`将总是提示确认，如果您使用没有这个安全网的机器，可能会发生不好的事情。

# 像专业人士一样用 rmtrash 移除

我最喜欢的方法是，只有当我*真的*知道我想删除某个东西时，才使用`rm`。其余时间，我用`rmtrash`。

几乎所有的现代文件管理器(Windows、macOS、Linux GUIs)都带有一个“垃圾桶”。当您“删除”一个文件或文件夹时，它不会立即被删除，而是被移到废纸篓。这是防止意外删除的安全网。当你确定要删除这些文件时，你可以清空垃圾箱。

在 macOS 和大多数 Linux 发行版中，有模拟这种行为的命令行工具。macOS 上有`rmtrash`，可以通过[自制](https://formulae.brew.sh/formula/rmtrash)安装，如下:

```
brew install rmtrash
```

要删除目录类型:

```
rmtrash ./path/to/directory
```

这将把`directory`移动到 macOS 垃圾桶。

在 Linux 系统上，有一个类似的、功能更全的解决方案叫做`trash-cli`。这不仅提供了从命令行删除文件的功能，还提供了清空垃圾桶等功能。关于`trash-cli`的更多信息可以在这里找到[。在 Ubuntu 上，它可以与以下软件一起安装:](https://github.com/andreafrancia/trash-cli/)

```
sudo apt install trash-cli
```

要删除目录，请键入:

```
trash-put ./path/to/directory
```

# 摘要

请小心使用`rm`,并保持定期备份。对于日常文件删除，考虑 macOS 上的`rmtrash`或 Linux 上的`trash-cli`。

我希望这是有用的。如果有人对命令行文件删除有任何进一步的想法，我很乐意在评论中阅读。
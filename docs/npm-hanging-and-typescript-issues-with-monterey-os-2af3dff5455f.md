# 蒙特利操作系统的 NPM 挂起和打字问题

> 原文：<https://levelup.gitconnected.com/npm-hanging-and-typescript-issues-with-monterey-os-2af3dff5455f>

![](img/5add060b557e964f0b95d72f9011a75b.png)

娜塔莉·邱晨在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

最近我遇到了 NPM 拒绝安装的问题。它一直挂起，吹走 node_module 文件夹或清除 NPM 缓存都不起作用。如果你发现自己处于这种情况，这里有一些让你重新变得高效的方法。

# NPM 绞刑问题

第一类问题是由于一个复杂的 lerna 项目设置，我花了相当多的时间才解决，直到我最终尝试了第 4 步。无论我做什么，我都无法安装应用程序。为了节省您的时间，当您遇到类似问题时，可以尝试以下步骤。

1.  首先试着吹掉你的`node_modules`文件夹，用`npm ci`重新安装。
2.  使用`npm cache clean --force`清除 NPM 缓存，并按照步骤 1 重新安装。
3.  通过删除并运行`npm install`来查看它是否与您的`package-lock.json`文件相关。
4.  如果这些措施都不起作用，那么是时候选择核武器了。
    在 Mac 上，打开 finder，选择前往菜单- >前往文件夹，键入`~/` 前往您的个人目录。点击`Cmd + Shift + .`显示隐藏文件。然后删除`.npm`文件夹。
    Windows 用户，我还没有验证这一点，但你应该可以通过删除这些文件夹`C:\Users\YOUR_USER_NAME\AppData\Roaming\npm`
    `C:\Users\YOUR_USER_NAME\AppData\Roaming\\npm-cache`清理 NPM

# 蒙特雷操作系统打字稿问题

我最近有一位同事，他的笔记本电脑更新到了最新的操作系统，却发现自己由于一系列随机错误而无法高效工作。希望苹果或 Node 能在不久的将来解决根本原因。在此之前，这里有一些变通办法。

## 第一种错误是 Z_DATA_ERROR。

```
npm ERR! code Z_DATA_ERROR
npm ERR! errno -3
npm ERR! zlib: incorrect data check
```

如果你开始得到这个，你需要降级你的 node 版本。至于那是什么版本，请看这篇相关的帖子了解更多信息。

[https://stack overflow . com/questions/69452504/zlib-error-when-attempting-to-run-NPM-install-or-yarn](https://stackoverflow.com/questions/69452504/zlib-error-when-attempting-to-run-npm-install-or-yarn)

## 第二种错误是当您的代码开始出现随机的 typescript 错误时。

这里的解决方案是将正在使用的 typescript 版本降低到 v4.0.3。这消除了 typescript 问题。

总之，我希望这篇文章对你们中的一些人有所帮助。如果我事先知道这些建议，我就不会沮丧几天了。
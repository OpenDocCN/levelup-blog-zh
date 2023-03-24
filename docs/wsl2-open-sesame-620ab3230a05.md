# 如何使用相关的 Windows 应用程序打开任何文件或目录

> 原文：<https://levelup.gitconnected.com/wsl2-open-sesame-620ab3230a05>

## 在 WSL 中，有时您可能希望打开一个文件或目录及其相关的 Windows 应用程序。让我们想想怎么做。

![](img/bf3aa117e6e068244c9c529eb7f4e383.png)

罗马·卡夫在 Unsplash[上拍摄的照片](https://unsplash.com/s/photos/treasure?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

如果你曾经在 macOS 上使用过终端，你可能知道你可以使用`open`命令来打开任何文件或目录及其默认应用程序。据我所知，在 Windows 上使用 WSL 没有直接的方法来做到这一点，最近这让我有点困扰。有时我只想用相关的 Windows 应用程序打开 PDF 或 ZIP 或任何其他文件类型或路径。

# 解决办法

经过一番修修补补，我想到了以下解决方案:

将此功能添加到您的**中。bashrc** 或者**。zshrc** 或等效的**、**取决于您使用的外壳；确保 Windows 路径对于您的系统是正确的。保存并获取文件或重启终端。

## **示例用法:**

```
open .open *.pdfopen fileopen file1 file2open diropen dir1 dir2
```

当然，这不像 macOS 上的 open 命令那样通用，但是它完成了工作，并且它完全符合我的需要。试一试，如果不适合你，可以随意扩展。
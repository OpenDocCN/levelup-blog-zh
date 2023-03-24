# 如何从命令行访问系统剪贴板(复制/粘贴)

> 原文：<https://levelup.gitconnected.com/how-to-access-the-system-clipboard-copy-paste-from-the-command-line-c8615d510ae2>

## 在 Windows，Mac，或者 Linux 上怎么做？

![](img/4f93362a505672e76cede4f3e01b2cbe.png)

照片由 [JJ 英](https://unsplash.com/@jjying?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

复制/粘贴可以节省很多精力。那么如何从命令行快速复制粘贴呢？

# Windows 操作系统

在 Windows 上，可以使用`clip`和`powershell get-clipboard`。

将标准输出复制到剪贴板:

```
# Syntax
<command> | clip# Examples
dir | clip
git diff | clip
cat 1.txt | clip
```

将文件内容复制到剪贴板:

```
# Syntax
clip < <filename># Examples
clip < 1.txt
clip < readme.md
```

输出剪贴板的内容:

```
# Syntax
powershell get-clipboard
```

将剪贴板的内容粘贴到指定命令的输入中:

```
# Syntax
powershell get-clipboard | <command># Examples
powershell get-clipboard | grep txt
```

将剪贴板的内容粘贴到指定文件:

```
# Syntax
powershell get-clipboard > <filename># Examples
powershell get-clipboard > 1.txt
```

# 苹果个人计算机

在 macOS 上，可以使用`pbcopy`和`pbpaste`。

它们的语法相似，下面是一些例子:

```
# Copy the current file list to the clipboard:
ls | pbcopy# Copy the contents of a file to the clipboard:
pbcopy < 1.txt# Copy part of a file to the clipboard:
grep 'test' 1.txt | pbcopy# Paste the contents of the clipboard to the specified file:
pbpaste > 1.txt# Replace spaces in the clipboard text with %20:
pbpaste | sed 's/ /%20/g' | pbcopy# Replace the current contents of the clipboard with a base64 encoded version:
pbpaste | base64 | pbcopy
```

# Linux 操作系统

在 Linux 中，我们可以使用`xclip`或者`xsel`。

例如下面是`xsel`的用法:

```
# Copy parameters
xsel --clipboard --input
# Shorthand
xsel -ib# Copy the output of the command to the clipboard:
echo "Test" | xsel -ib# Copy the file contents to the clipboard:
xsel --clipboard < 1.txt
# Shorthand:
xsel -b < 1.txt# Paste parameters
xsel --clipboard --output
# Shorthand
xsel -ob# Paste the contents of the clipboard into a file:
xsel -ob > 1.txt
# Instead of using overrides, use appends:
xsel -ob >> 1.txt
```

# 结论

请注意，这些命令与使用环境密切相关，如果您的用户环境没有这些命令，请安装它或使用回退方法。使用鼠标或触控板手动拷贝粘贴不方便且耗时，如果您熟悉这些命令，它会让您的生活更轻松。

*感谢阅读。如果你喜欢这样的故事，想支持我，请考虑成为* [*中会员*](https://medium.com/@islizeqiang/membership) *。每月 5 美元，你可以无限制地访问媒体内容。如果你通过* [*我的链接*](https://medium.com/@islizeqiang/membership) *报名，我会得到一点佣金。*

你的支持对我很重要——谢谢。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)
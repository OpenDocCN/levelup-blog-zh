# 您可能还不知道的有用的 Vim 快捷方式

> 原文：<https://levelup.gitconnected.com/useful-vim-shortcuts-you-probably-dont-know-yet-289f92717a91>

![](img/65159cd1ffa64ba5dcf6f610f4086655.png)

[Joshua Sortino](https://unsplash.com/@sortino?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

随着时间的推移，我已经构建了一个包含一些巧妙技巧的知识库，您可以使用这些技巧轻松地在 vim 上操作文件。我觉得这对更广泛的 vim 社区是有用的。

## 将文件还原到较早的时间

```
:earlier 30m
```

这将把文件恢复到 30 分钟前的状态。瞧啊。

## 从 vim 执行命令

在 vim 中使用`!`,后跟您的 Linux 命令。

```
:! [your-command]
```

额外提示:使用`.!`会将命令的输出直接转储到当前文件中！例如，下面的命令会将当前日期写入文件。

```
:.! date
```

## 在更改列表中移动

在更改列表中，使用`g;`向前移动，使用`g,`向后移动。这在从文档的一部分复制内容时非常有用，比如通过搜索，然后返回到原始位置粘贴。整洁！

## 多种方式轻松删除

使用`diw`删除当前单词。
使用`di(`删除当前括号内的所有内容。
使用`di"`删除当前引号之间的所有内容。

## 查找上次编辑

使用`';`转到最后编辑的行。现在你知道实习生在做什么了吧！

## 轻松复制东西

*   `bye`复制当前单词。
*   `cc`剪切当前行。
*   `yy`复制当前行。
*   `ce`切穿单词的末尾。

## 额外收获:将网页的源代码直接发送到 vim！

vim 完全有能力读取网站源代码并将其直接带到文件中。只管打

```
vim url
```

例如，如果你点击下面的命令—

```
vim [https://www.google.com/](https://www.google.com/)
```

将使用该页面的源代码创建一个新文件。

我希望这些提示对您有用，这样您在使用 vim 时会更有效率。编码快乐！
# 使用 Vim 模板减少样板文件

> 原文：<https://levelup.gitconnected.com/reducing-boilerplate-with-vim-templates-83831f8ced12>

如果你和我一样是个终极瘾君子，你会喜欢这个的。今天我们将解释如何避免编写没有任何插件的样板代码。我们将要探索的特性在 Vim 中被称为模板或骨架。

![](img/3f8753c3a294429f29b95b673685fa2e.png)

杰克·吉文斯在 Unsplash 上的照片

# 骨骼和 Vim

不要害怕，骨架文件(或模板)只是普通文件。之所以这样称呼它们，是因为它们将在创建特定文件时用作框架。为了创建 JSX 类型脚本`.tsx`文件，Vim 可以使用预定义的模板(框架)文件并添加一个 React 类。看起来怎么样-你一定在问。下面来看看。

我们可以像这样在`~/.vim/skeletons/react-typescript.tsx`中定义一个框架文件:

然后，我们可以在我们的`.vimrc`中添加下面一行:

让我们把它分解一下:

*   `autocmd BufNewFile`–当我们尝试在 Vim 中创建新文件时，运行以下命令
*   `*.tsx`–这是您希望新文件匹配的模式
*   `0r`–从第 0 行第一行开始读入缓冲区
*   `~/.vim/skeletons/react-typescript.tsx`–读入新文件的文件

就这样了。每当你创建一个新的 React 类型脚本文件，就会有一个新的功能组件。让我们看看还有哪些有用的模板可以使用。

# 有用的骨架

我在 GitHub 上的[我的点文件中添加了几个骨架。请自便，用你喜欢的东西。下面我也会分享一下。](https://github.com/nikolalsvk/dotfiles/)

# Bash 脚本框架

人们建议的最常见的模板是 Bash 脚本模板。它可能看起来像这样:

您还需要在您的`.vimrc`中使用以下代码行:

# HTML 框架

我在 2021 年从 [HTML 样板文件中找到了这个。](https://www.matuzo.at/blog/html-boilerplate/)

您可以在`.vimrc`中添加下一行，以便在创建新的 HTML 文件时应用 HTML 样板文件:

# 博客文章降价框架

下面这个是我最喜欢的，因为我一直在研究如何搭建一个新的博客帖子。看一看:

```
---
title: TODO
description: TODO
slug: TODO
date: 2021-1-1
published: true
tags:
  **- TODO
---**
```

# 哪里保存骨骼

如果这一部分被断章取义，我们就有麻烦了。但是，玩笑归玩笑，你能在哪里存储模板文件呢？这其实并不重要。我将它们保存在版本控制中的点文件中。他们在`~/Documents/dotfiles/skeletons`里面。有些人喜欢《T1》或《T2》中的角色。我喜欢在 git repo 的[点文件中保存我的文件，在那里我可以很容易地修改并把它们推送到 GitHub。](https://github.com/nikolalsvk/dotfiles/)

# 手动使用

如果您不喜欢在创建时使用 Vim 自动填充文件，那么您可以尝试使用手动方式。要简单地用另一个文件的内容填充一个文件，请尝试使用`:read`命令:

`~/.vim/skeletons/react-typescript.tsx`的内容会显示在你的光标下面。

# 最后的想法

目前就这些。谢谢收听。考虑订阅我的[时事通讯](https://pragmaticpineapple.com/newsletter)，我们将在下一篇博文中探索添加一些动态模板的可能性。

我从 [VimTricks 的帖子](https://vimtricks.com/p/automated-file-templates/)中得到写这篇博文的想法，向他们大声呼喊。

此外，如果你觉得这篇文章有用，不要忘记在 Twitter 上与你的朋友和同事分享。

直到下一个，干杯。
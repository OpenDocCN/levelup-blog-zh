# 介质上的代码突出显示语法

> 原文：<https://levelup.gitconnected.com/medium-code-blocks-highlighting-c4e77cbd7a28>

## 代码块的突出显示语法现在在中型平台上可用

![](img/19d2a61d963a0ef0b82298d85cc9d54a.png)

布莱克·康纳利在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

当我在写即将在 Medium 上发表的最新文章时，我偶然发现了一个我期待已久的全新功能——我猜这个平台上有很多技术文章！

你猜对了——代码块上的语法高亮现在可用于一些最流行的编程语言和框架，我对此非常兴奋！

## 是时候了，灵媒！

我是 Medium 的忠实粉丝，显然，在过去的几年里，我成功地发表了 200 多篇文章。但是，我必须承认，媒体对游戏来说有点晚了(很抱歉在这里作出判断！).

对于 Medium 上的所有技术作者来说，我认为这个特性是不可协商的。每个人都对原生代码块过去如何出现在文章中感到有点沮丧——我想这对于我们的读者来说更令人沮丧。

没有突出显示的语法会使我们的代码片段看起来混乱，而更长的代码行将溢出到后面的行中。仅仅是..咩！我猜这就是为什么我们中的许多人决定使用其他方式来共享代码块——从 [GitHub Gists](https://gist.github.com/) ，到 [Carbon](https://carbon.now.sh/) ，甚至是截图！

我遇到的另一个问题是，每次我试图从旧的(老式的)中型代码块中复制和粘贴代码时，格式就会变得一团糟！

## 在介质上添加带有突出显示语法的代码块

和以前一样，在媒体文章中嵌入代码块基本上有两种方法。

第一种也可能是最简单的方法是使用快捷方式:

*   Mac: `cmd+option+6`
*   视窗:`ctrl+alt+6`
*   Linux : `ctrl+alt+6`

```
# Code block with Python syntax highlighting
import numpy as np
import pandas as pd

df = pd.DataFrame(
    [
        ('A', True, 100),
        ('B', False, np.nan),
        ('C', False, 132),
        ('D', True, np.nan),
    ],
    columns=['colA', 'colB', 'colC']
)

print(df)
```

或者，您可以简单地点击左侧的`+`图标，然后选择倒数第二个图标`{}`，插入带有高亮语法的新代码块。

![](img/a42319b398ed236f670c6e1a604abfe2.png)

来源:作者

```
cd /home/airflow/
```

## 支持哪些语言？

截至目前，新的代码块特性支持以下语言、框架、工具和数据表示格式:

Bash、C、C++、C#、CSS、Diff、Go、GraphQL、TOML/INI、Java、Javascript、JSON、Kotlin、Less、Lua、Makefile、HTML/XML、Markdown、Objective C、Perl、PHP、PHP template、纯文本、Python、python-repl、R、Ruby、Rust、SCSS、Shell、SQL、Swift、Typescript、VB.NET、WebAssembly 和 YAML。

覆盖面很大，不是吗？

## 改进了内联代码的格式

我还注意到内联代码的字体也经历了某种提升。我认为这个新的更新甚至使内联代码更具可读性！

`print('Thanks Medium team!')`

## 我好像找不到这个功能

我刚刚发现了新的中型代码块。因此，到目前为止，该功能可能还没有普遍提供。**如果你似乎不能使用这个功能，那么有可能正在进行 A/B 测试**——也许我是能够访问它的群体中幸运的一员。如果是这样的话，那么请耐心一点，我很肯定我们很快都会接触到它。

## 最后的想法

对我来说，代码块语法高亮绝对是迄今为止 Medium 上最好的特性版本。我不得不说，我很生 Medium 的气，因为它没有更快地发布。技术文章在平台上蓬勃发展，而我们过去使用的老式代码块根本不可读。这就是我们大多数人一直使用其他工具的原因，比如 GitHub Gists，甚至截图与读者分享代码片段。

最后，我想我们都要感谢 Medium 团队的努力，感谢他们总是倾听社区的声音，并不断努力改善作者和读者的体验！坚持住，伙计们！

[**成为会员**](https://gmyrianthous.medium.com/membership) **阅读媒体上的每一个故事。你的会员费直接支持我和你看的其他作家。你也可以在媒体上看到所有的故事。**

[](https://gmyrianthous.medium.com/membership) [## 通过我的推荐链接加入 Medium-Giorgos Myrianthous

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

gmyrianthous.medium.com](https://gmyrianthous.medium.com/membership) 

**相关文章你可能也喜欢**

[](https://towardsdatascience.com/diagrams-as-code-python-d9cbaa959ed5) [## Python 中作为代码的图

### 用 Python 创建云系统架构图

towardsdatascience.com](https://towardsdatascience.com/diagrams-as-code-python-d9cbaa959ed5) [](https://towardsdatascience.com/big-o-notation-32fb458e5260) [## 大 O 符号

### 用 Big-O 符号计算算法的时间和空间复杂度

towardsdatascience.com](https://towardsdatascience.com/big-o-notation-32fb458e5260) [](https://towardsdatascience.com/parallel-computing-92c4f818c) [## 什么是并行计算？

### 理解并行计算在数据工程环境中的重要性

towardsdatascience.com](https://towardsdatascience.com/parallel-computing-92c4f818c)
# 以下是我如何优化跟踪项目进展的方法

> 原文：<https://levelup.gitconnected.com/heres-how-i-optimized-tracking-project-evolution-changelog-md-188280779331>

在本文中，我将分享我的经验，如何很好地记录我的代码帮助我成为一名更好的开发人员。

# 问题是

![](img/18d934f71e1d420074b493a9d378d68e.png)

塞巴斯蒂安·赫尔曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

作为一个小规模公司的小软件开发团队的一员，我可以说我们大部分时间都在写代码，因为我们每天都在战场上，仍然在努力完成昨天*的优先任务*！！！有时这导致在短时间内调整我们代码结构中的一些东西来使它工作；很少或完全不注意文档。这使得跟踪这些变更或者对当前代码的其他修改变得不可能。

因为，每一次*发布*都围绕着发布新产品，为现有产品添加新功能，修复旧缺陷；我们通常很少或没有时间花在代码优化上。因此，在我们公司，我们有一句谚语，*“暂时是永久的”(双关语！).好吧，我相信你们中的许多人都会感同身受。不是吗？*

现在，既然我们意识到了我在组织我的项目的发展和跟踪我们在每一个版本中对代码所做的无止境的改变时所面临的挑战，让我们来谈谈解决方案吧！有什么猜测吗？！

# 解决方案

CHANGELOG.md，惊喜惊喜！

![](img/32d1998e7a1448e0743d6e6fa2ff5307.png)

[斯科特·格雷厄姆](https://unsplash.com/@sctgrhm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

**为什么要用？**

在软件行业中，changelog，顾名思义，是记录对特定软件程序所做的所有更改的文件。创建和保存变更日志的原因很简单；当贡献者或最终用户想要查看软件程序是否有任何更改时，他们可以通过读取 changelog 轻松而准确地完成。他们所需要做的就是进入 changelog，它会显示在特定软件的不同版本或发布之间所做的任何更改，以及更改的时间。

**包括什么？**

尽管不存在关于你能包括什么和不包括什么的标准或规则，这里有一些在行业内广泛使用的推荐主题。

*   发布/新版本应在顶部列出最新版本(按时间倒序排列)
*   每个新版本都应该显示其发布日期
*   这些日期应遵循 ISO 标准格式 YYYY-MM-DD
*   影响特定源文件的更改
*   如果一个特定的源文件被重命名或移动，如果是的话，有什么变化？
*   影响数据结构的给定功能/宏/定义的变化
*   如果一个数据结构的函数/宏/定义被重命名或从另一个文件中移走，如果是这样，有什么变化？
*   删除函数/宏/数据结构的更改
*   给定变更背后的基本原理及其主要思想
*   关于变更的任何其他信息，如果有，在哪里可以找到？

我喜欢这样保存我的日志

```
# Changelog

All notable changes to this project will be documented in this file.

## Released
### Semantic Version | Release 20XX | YYYY-MM-DD

#### Added
- For any new features that have been added since the last version was released
- [Issue Number](Link to your ISSUE) | Added this.
- [Issue Number](Link to your ISSUE) | Added this too.

#### Changed
- To note any changes to the software’s existing functionality
- [Issue Number](Link to your ISSUE) | Changed this.

#### Deprecated 
- To note any features that were once stable but are no longer and have thus been removed

#### Fixed
- Any bugs or errors that have been fixed should be so noted

#### Removed
- This notes any features that have been deleted and removed from the software

#### Security 
- This acts as an invitation to users who want to upgrade and avoid any software vulnerabilities
```

正如一位技术圣人曾经说过的，“数周的编码节省了数小时的计划。”你知道，一个很好的主意是从计划中偷出一些时间来制作一个 CHANGELOG.md，只是说！

好了，这是一个总结，感谢您的时间，我希望这篇文章可以为您的时间提供一些价值。

**参考文献:**

 [## CHANGELOG.md

### 在软件行业中，changelog，顾名思义，是一个记录对某个特定文件所做的所有更改的文件。

changelog.md](https://changelog.md/)  [## 变更日志(GNU 编码标准)

### 保留一个 changelog 来描述对程序源文件所做的所有更改。这样做的目的是让人们…

www.gnu.org](https://www.gnu.org/prep/standards/html_node/Change-Logs.html)
# Github 上的代码不是免费的

> 原文：<https://levelup.gitconnected.com/code-on-github-is-not-free-197f93e4cd44>

## 学习开源许可，了解哪些代码属于哪个许可

![](img/72400be7f2b9e0c6824bd92af5e61d19.png)

照片由 [Richy Great](https://unsplash.com/@richygreat?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄

开发者一直在使用 Github。我们使用它有两个原因:学习一些东西或者保存我们项目的副本。问题是当我们从 Github 克隆一个项目时，我们大多数人只是忽略了项目的许可。

公开你的 GitHub 项目和许可你的项目是不同的。作为一个雇主，我会毫不犹豫地选择一个在开源许可方面有丰富知识的开发者，而不是一个没有知识的开发者，因为我不希望我的公司陷入任何法律问题。

> *对开源项目最常见的误解是，如果 Github 项目没有许可证，就可以使用。如果一个库没有许可证，那么* ***所有权利都被保留，它不是开源或免费的*** *。没有版权所有者的明确许可，您不能修改或重新分发此代码。*

我告诉你的是许可证的基本知识。我不是律师，如果你的项目是敏感的，并使用一些许可代码，最好咨询律师。但是这篇文章会给你一个每个开发者都应该有的基本思路，相信我，这 5 分钟是你今天要度过的最美好的 5 分钟。

# 开发人员通常会做什么

当开发人员向 Github 添加项目时，他们会将其作为公共或私有存储库添加。我今天不会谈论私有库，因为其他开发者不能拥有它们。我会谈到公共储存库。

如果您将项目添加为公共存储库，但没有添加任何许可证，这意味着您没有授予其他开发人员任何权限来使用、分发、修改或贡献您的项目。GitHub 的服务条款涵盖公共存储库。

其他开发人员只能查看和派生您的项目。但这并不是很多开发者的目的。许多开发者不会介意其他开发者从他们的项目中获益。

那么你应该怎么做呢？在使用别人的代码时，你应该有帮助别人和保护自己的知识。

# 为什么了解开源许可非常重要

## 保护你自己

保护你自己和你的作品。假设你不知道许可证，不知何故，你使用了 Github 项目中某人的大部分代码，这些代码受到许可证的保护，但许可证并没有授权你将这些代码用于任何商业项目。

你可能会陷入法律问题。

## 保护您的作品

如果你不申请许可，每一个参与该项目的开发者也成为他们作品的专有版权持有者。没有人，甚至你不能使用，分发或修改他们的贡献。但是如果有人为你的项目做贡献，你肯定希望有一些控制权。

## 成为更好的程序员

我们经常在生产中使用这些代码。这可能会让公司陷入困境。您不需要投入大量时间来选择合适的许可证。

高级程序员知道这些东西，而初级程序员经常忽略这些重要的问题。编程不仅仅是敲代码。一个更好的程序员知道的不仅仅是编码和编程语言或框架。

作为开发人员，最终您必须知道哪些项目是开放使用或修改的。

# 如何选择适合您的许可证

有大量现成的许可证可供您使用。你只需要确定你的需求并找到合适的。有一个很棒的网站“chooselicense.com”可以帮你选择合适的许可证。在这里，我将基本需求分为三个常见的场景。

## 案例 1:如果你需要在社区工作

如果您正在为一个现有的项目做贡献，最简单的方法是继续使用该项目的许可证。您可以在名为`LICENSE`或`COPYING.`的文件中找到项目许可证，有时项目需要使用相同的许可证。

如果你想加入，一些社区需要/推荐一个特定的许可证。这里我找到了一些来自 [Chooselicense](https://choosealicense.com/community/) 的例子。

*   [**阿帕奇**](https://www.apache.org/licenses/) - > [**阿帕奇许可证 2.0**](https://choosealicense.com/licenses/apache-2.0/)
*   [**云原生计算基础**](https://github.com/cncf/toc/blob/master/process/project_proposals.adoc)->[**Apache License 2.0**](https://choosealicense.com/licenses/apache-2.0/)
*   [**GNU**](https://www.gnu.org/licenses/license-recommendations.html)->[**GNU GPLv3**](https://choosealicense.com/licenses/gpl-3.0/)
*   [**NPM 套餐**](https://libraries.io/search?platforms=NPM) - > [**麻省理工**](https://choosealicense.com/licenses/mit/) 或 [**ISC**](https://choosealicense.com/licenses/isc)
*   [**WordPress**](https://wordpress.org/about/license/)->[**GNU GPL v2**](https://choosealicense.com/licenses/gpl-2.0/)(或更新)

如果你不认为你的项目是任何特定社区的一部分，你可以 [**自己选择一个开源许可**](https://choosealicense.com/) 。

如果您有一个没有任何许可证的依赖项，您可以要求它的维护者添加一个许可证。

## 案例 2:如果你关心共享改进

GNU GPLv3 的[](https://choosealicense.com/licenses/gpl-3.0/)**在权限和可用性方面几乎与麻省理工学院的许可证相似。但是他们不允许发布闭源版本。**

**[**Bash**](https://git.savannah.gnu.org/cgit/bash.git/tree/COPYING) 和 [**GIMP**](https://git.gnome.org/browse/gimp/tree/COPYING) 使用 GNU GPLv3。**

## **案例 3:如果你想要简单和宽容**

**麻省理工学院许可证是一个非常受欢迎的短期许可证。它让开发者可以对你的项目做任何他们想做的事情。它还允许开发者发布封闭的源代码版本。**

**[](https://github.com/babel/babel/blob/master/LICENSE)**[**。NET Core**](https://github.com/dotnet/runtime/blob/master/LICENSE.TXT) ， [**Rails**](https://github.com/rails/rails/blob/master/MIT-LICENSE) 使用 MIT 许可。****

# ****外卖食品****

****所以这篇文章的要点是:****

1.  ****公共存储库可能不会授予您使用或修改代码的权利。****
2.  ****在使用或修改您的产品代码之前，您必须检查您从 Github 派生的任何项目的许可证。****
3.  ****开源许可的基础知识和输入代码一样重要。****
4.  ****成为一名更好的程序员也很重要。用人单位如此看重这个属性。****

******参考:**https://choosealicense.com/T2[https://opensource.guide/legal/](https://opensource.guide/legal/)****
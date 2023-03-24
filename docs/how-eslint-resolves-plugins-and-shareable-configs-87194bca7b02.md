# ESLint 如何解析插件和可共享配置

> 原文：<https://levelup.gitconnected.com/how-eslint-resolves-plugins-and-shareable-configs-87194bca7b02>

## 它与包管理器和对等依赖有什么关系？

![](img/6014c7c3d560b62bdc28c73b4f3b69e1.png)

由 [mostafa meraji](https://unsplash.com/@mostafa_meraji?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

*原载于*[*https://zaicevas.com*](https://zaicevas.com)

**TLDR:** 插件相对于**最终用户的项目、**被搜索，而可共享的配置相对于它们出现的**配置文件被搜索。**

如果你用过`eslint-config-airbnb`(最流行的 ESLint 可共享配置之一)，你可能会疑惑**为什么要安装** [**这么多的对等依赖**](https://github.com/airbnb/javascript/blob/master/packages/eslint-config-airbnb/package.json#L89) : `eslint-plugin-import`、`eslint-plugin-jsx-a11y`、`eslint-plugin-react`、`eslint-plugin-react-hooks`。**不能** `**eslint-config-airbnb**` **带那些依赖？**

或者，如果你曾经构建过 ESLint 插件或可共享配置，你很可能面临过**ESLint 如何解析插件和可共享配置的难题。**

这就是我在这篇博文中要回答的。对于大多数人来说，理解并不重要。然而，当你构建你自己的 ESLint 插件或者一个可共享的配置时，它变得非常有用。这也是一个给好奇者的馅饼😏

## 插件与可共享配置

首先，ESLint 有**插件**和**可共享配置**。这是两码事:

*   **可共享配置**—保存一个可重用的配置(把它想象成一个规则集)。示例:`eslint-config-airbnb`
*   **插件**——可以做可共享配置所做的一切+可能包括**自定义规则**。例子:`eslint-plugin-jest`

更实际的例子，[https://prateeksurana . me/blog/difference-between-eslint-extends-and-plugins/](https://prateeksurana.me/blog/difference-between-eslint-extends-and-plugins/)是一个清晰、轻量级的读物。

注意:我将互换使用*配置*和*可共享配置*。

## 插件和配置的解析方式不同

令人惊讶的是，当 ESLint 搜索插件和配置时，它会查看不同的路径。这在 ESLint 文档中有描述:

> R 属性中的相关路径和可共享配置名称从它们出现的配置文件位置解析。
> 
> 来源:[https://eslint . org/docs/user-guide/configuring/configuration-files # extending-configuration-files](https://eslint.org/docs/user-guide/configuring/configuration-files#extending-configuration-files)
> 
> 基本配置中的插件(由`extends`设置加载)相对于派生的配置文件。
> 
> 来源:[https://eslint . org/docs/user-guide/configuring/plugins # configuring-plugins](https://eslint.org/docs/user-guide/configuring/plugins#configuring-plugins)

*派生配置文件*指消费者项目配置文件，如`your-project/.eslintrc.js`

不过，这听起来可能还是很神秘。让我为你简化一下:

*   **配置** —如果您扩展`eslint-config-airbnb`，并且`eslint-config-airbnb`扩展`eslint-config-prettier`，ESLint 搜索`your-project/node_modules/eslint-config-airbnb/node_modules/eslint-config-prettier`
*   **插件**—如果你扩展`eslint-config-airbnb`并且`eslint-config-airbnb`扩展`eslint-plugin-react`，ESLint 搜索`your-project/node_modules/eslint-plugin-react`

换句话说，ESLint 希望所有可共享的配置消费者安装所有的插件。

这就是为什么`eslint-config-airbnb` [包含插件作为对等依赖](https://github.com/airbnb/javascript/blob/master/packages/eslint-config-airbnb/package.json#L89)。

在 ESLint 文档中也有说明:

> 如果你的可共享配置依赖于一个插件，你也应该将它指定为一个`peerDependency` ( **插件将相对于最终用户的项目被加载，因此最终用户需要安装他们需要的插件**)。但是，如果您的可共享配置依赖于第三方解析器或另一个可共享配置，您可以将这些包指定为`dependencies`。
> 
> 来源:[https://eslint . org/docs/developer-guide/shareable-configs # publishing-a-shareable-config](https://eslint.org/docs/developer-guide/shareable-configs#publishing-a-shareable-config)

## 独立的 ESLint 插件/配置

如果您想知道为什么 ESLint 不能调整这种行为，并允许 ESLint 插件/配置自包含(将其他插件作为依赖项)，那么您并不孤单。

有一个 GitHub 问题可以追溯到 2015 年:[支持在可共享配置中有插件作为依赖](https://github.com/eslint/eslint/issues/3458)。也有相当多的戏剧😅甚至有一个 [ESLint 补丁](https://github.com/microsoft/rushstack/tree/master/eslint/eslint-patch)可以帮助实现这一点。虽然，就我个人而言，我会犹豫是否使用它，因为它要求所有消费者项目在配置文件中显式地使用它。

ESLint 团队已经在致力于实现一个[平面配置](https://github.com/eslint/eslint/issues/13481)，这意味着 ESLint 将让用户调用`require()`并解析插件/配置。

## 然而，独立的插件/配置已经成为可能😈

这就是不同的**包管理器**之间的细微差别。

你看，包经理是`node_modules`的主人。如果一个包管理器能够把可传递的依赖关系(例如`eslint-config-airbnb`的依赖关系)放到`your-project/node_modules`中，它们可以被 ESLint 解决。

不同的包管理器有不同的管理方式`node_modules`:

1.  `npm` v3 及更高版本—平板`node_modules` ✅
2.  `yarn` v1—平`node_modules` ✅
3.  `yarn` v2 —即插即用❌
4.  `pnpm` — [起重机 ESLint 插件/配置](https://pnpm.io/npmrc#public-hoist-pattern) ✅

**可以看出，** `**yarn**` **v2 是奇数。**`**npm**`**`**pnpm**`**已经把 ESLint 可传递依赖关系直接放入** `**your-project/node_modules**`。**

**换句话说，如果你安装了`eslint-config-airbnb`，并且它把`eslint-plugin-import`作为一个依赖项，那么它将位于`your-project/node_modules/eslint-plugin-import`，而不是`your-project/node_modules/eslint-config-airbnb/node_modules/eslint-plugin-import`或者其他地方。**

**因此，如果一个 ESLint 插件/配置能够提供**而不是**来支持`yarn`，将其他 ESLint 插件放入`dependencies`有助于实现一个独立的插件/配置。这意味着消费者项目不需要安装插件作为对等依赖项。**

**然而，只是重申一下，它不能与`yarn` v2 一起工作，所以我对`eslint-config-airbnb`没有像`dependencies`一样包含插件并不感到惊讶。**

# **摘要**

**我希望这篇博文对你有用！让我总结一下:**

*   **ESLint **插件**和**可共享配置**是两回事**
*   **ESLint 解析插件和配置**不同****
*   **相对于**最终用户的项目** ( `your-project/node_modules/eslint-plugin-foo`)来搜索插件**
*   **相对于出现的**配置文件搜索配置****
*   **由于插件是相对于最终用户的项目来搜索的，所以建议将它们作为`**peerDependencies**`放在 ESLint config/plugin 中，这意味着消费者项目必须安装它们**
*   **插件和配置能够带来其他插件作为`dependencies`，与`peerDependencies`相反。这与`npm` ✅和`pnpm` ✅兼容，但与`yarn` v2 ❌不兼容**
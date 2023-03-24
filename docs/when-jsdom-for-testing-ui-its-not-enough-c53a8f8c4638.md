# 当用于测试 UI 的 JSDOM 不够用时

> 原文：<https://levelup.gitconnected.com/when-jsdom-for-testing-ui-its-not-enough-c53a8f8c4638>

![](img/891382eb4ac54a6d968e3b03cef2ba4e.png)

如果你在测试你的组件，React 组件，Web 组件或者任何你可能在 JSDOM 中测试的东西。JSDOM 是一个很好的“模拟”真实浏览器 DOM 的节点库。但有时这还不够。

假设你有一个**滑块**组件，你想拖动其中一个手柄几个像素，并检查是否有变化。在 JSDOM 中真的做不到。因为你真的不能拖动手柄。一个**无限卷轴组件**也是如此。或者测试伪状态像**:悬停，** **:活动。你就是不能。好吧。你可以…但你应该嘲笑一切…有时结果还不够好。**

很难测试这类组件的原因很简单:缺乏布局功能。JSDOM 是 DOM 的纯 javascript 实现，所以它甚至没有被设计成像真正的浏览器一样。这是为了做一些单元测试。而且做起来很棒，很快。但是对于一些特殊的部件，我们需要更多。我们需要一个真正的浏览器。如果我们能拥有所有三个主引擎就更好了:)

# 剧作家

你们中的许多人可能已经通过谷歌知道了木偶师。这让我们可以从命令行以编程方式运行 Chrome 浏览器。但它只是 Chrome，而 Firefox 在撰写本文时仍处于实验阶段。这是目前比较成熟的项目，但是如果你需要跨浏览器的功能，那么也许是时候转换到微软的剧作家了。

[](https://github.com/microsoft/playwright) [## 微软/剧作家

### 剧作家是一个 Node.js 库，用一个 API 来自动化 Chromium、Firefox 和 WebKit。剧作家是为了…

github.com](https://github.com/microsoft/playwright) 

有了剧作家你可以运行最新版本的 Chromium 89.0.4330.0，WebKit 14.1，Firefox 84.0b5，它可以在 linux，mac 和 windows 上运行。

# **在 React / Storybook 项目中配置它**

在本文中，我将向您展示如何在 react 项目中配置它来运行一些简单的测试。

如果你已经安装了 Jest，从你的列表中跳过它。现在让我们在不同的文件夹中创建一个临时 jest 配置

一旦我们搭建了 jest 配置，让我们修改它。该示例使用。mjs 文件扩展名，因为我假设您至少有节点 v12。如果不是这样(:D，你真可耻),你可以切换到旧的模块导出规范。

我们真正改变的是 **testEnvironmentOptions** 字段。我们将使用**jest-剧作家**而不是 JSDOM。

# **测试示例**

如果你有一个故事书实例，你可以用你的浏览器实例导航到你的组件页面并开始测试。

这里我们使用**剧作家测试库**，它与 **pptr 测试库非常相似。除了用户事件，这两个库都遵循测试库 API。**

# **添加一个 npm 脚本**

作为一种快捷方式，我们可以在 package.json 脚本中添加这一行

并运行它

# **将剧作家的埃斯林规则添加到。eslintrc**

为了避免 eslint 抱怨注入的页面变量，在你的 eslint 配置中添加**插件:jest-剧作家/推荐的**

# **有用的资源**

*   [https://github.com/microsoft/playwright](https://github.com/microsoft/playwright)
*   [https://github.com/playwright-community/jest-playwright](https://github.com/playwright-community/jest-playwright)
*   [https://github.com/playwright-community/expect-playwright](https://github.com/playwright-community/expect-playwright)
*   [https://playwright.dev/#?path=docs/showcase.md](https://playwright.dev/#?path=docs/showcase.md)社区展示案例，提供大量有用资源
*   [https://www.npmjs.com/package/storybook-addon-playwright](https://www.npmjs.com/package/storybook-addon-playwright)棒极了的故事书插件
*   [https://testing-library.com/docs/pptr-testing-library/intro](https://testing-library.com/docs/pptr-testing-library/intro)
*   [https://theheadless.dev/](https://theheadless.dev/)
# 用 Yalc 模块化共享代码

> 原文：<https://levelup.gitconnected.com/modularize-a-shared-code-with-yalc-6f32031b647f>

将你的代码的每一个开发版本发布到 NPM 可能是痛苦的。Windows 上的符号链接？哼，就是不要。Git 子模块？不完全是。用 [Yalc](https://github.com/whitecolor/yalc/) 准备一些轻量级动作。

## TL；博士*(如何派生和使用你最喜欢的库)*

```
git clone fork_of_your_favorite_library
npm/yarn install *(in that fork dir)*
npm/yarn run build *(or similar)* npm/yarn install -g yalc
yalc publish *(still in the fork dir)*
yalc add fork_of_your_favorite_library *(inside your project dir)*
```

![](img/79192e655dab2277160a87ce2160b08c.png)

照片由[泰勒·尼克斯](https://unsplash.com/@jtylernix?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

有很多方法可以将你的代码分割成更小的部分。即使对一个应用程序来说，你也可以使用 monorepo 结构。然而，如果您的团队正在构建多个不紧密相关但一些代码共享是有益的应用程序，该怎么办？

我想有些人可能喜欢只使用`npm/yarn link.`，它在大多数情况下工作得很好，但是你团队中的每个人都必须这样做！我的意思是，除非软件包被发布到 NPM，否则每个团队成员都必须克隆共享回购协议，构建它，并在启动应用程序之前链接它。你想学一种更简单的方法吗？

Yalc 是由[推送(发布+更新)功能](https://medium.com/u/217a289a6992#pushing-updates-automatically-to-all-installations)，这是非常重要的。

在某种程度上，你可能认为这个包足够稳定，你想把它发布到 NPM，这完全没问题(除了你在那里制造了不必要的混乱)。然而，我相信 Yalc 可以长期使用，据我所知没有任何缺点。这也给了你一个很好的机会来保持一个包的私有代码不被外界看到。如果你曾经试图直接从私有回购安装一个包，你现在可能已经意识到 Yalc 提供了一个更好的方法。或者你尝试过使用 Git 子模块吗🙊？

但是有一个重要的问题。您需要适当地指导团队成员，否则他们可能会试图修改`.yalc`目录的内容。为了大家好应该避免。有些实验可以直接在`node_modules`里随意修改文件。任何有意义的更改都应该在带有包源的 repo 中完成，从而导致新的构建和发布。

> 请注意，Yalc 也支持`yalc link`，但我不认为它有用，因为所有团队成员都需要安装 Yalc，并在启动应用程序之前手动链接所有必需的包。

希望这篇短信对你有用。这为我和我的团队节省了大量时间和精力。

[![](img/439094b9a664ef0239afbc4565c6ca49.png)](https://levelup.gitconnected.com)[](https://gitconnected.com/learn) [## 查找最佳编码教程和课程-学习编码| gitconnected

### 使用我们完整的编码资源列表学习任何编程语言或框架。我们分享、汇总和排名…

gitconnected.com](https://gitconnected.com/learn)
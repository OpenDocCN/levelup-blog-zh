# 用反应钩去抖

> 原文：<https://levelup.gitconnected.com/debouncing-with-react-hooks-dcef0ba764c6>

现在 react 钩子已经出现了一段时间，我已经看到了很多不同风格的去抖方法。我想将现有的几种方法整合到一篇文章中，以帮助那些可能正在寻找解决方案的人。这将是短暂而甜蜜的。

1.  第一种也是我最喜欢的方法是将这种去抖逻辑抽象成一个定制的钩子。因此，请跟随这个[链接](https://dev.to/gabe_ragland/debouncing-with-react-hooks-jci)，而不是我重复这篇好文章所说的。我只是简单地派生了他的 codesandbox 应用程序，因为 api 密钥不起作用。请看下面的实际操作。

2.另一个选择是选择第三方库，如 [lodash](https://lodash.com/docs/4.17.15#debounce) 。Lodash 去抖返回一个去抖函数，我们可以存储在`useRef`中。简单来说，`useRef`钩子对于在渲染中保留值非常有用，就像`useState`。不同的是，当值改变时，`useRef`不会导致重新渲染。看看我们如何在下面的 gist/codesandbox 中存储去抖功能。

3.如果前面的选项不太适合您的需求，您可以选择与#2 类似的方法，但是不要使用第三方库。查看下面沙盒中的代码。

如果你有其他方法可以用钩子去抖，请在评论中分享！
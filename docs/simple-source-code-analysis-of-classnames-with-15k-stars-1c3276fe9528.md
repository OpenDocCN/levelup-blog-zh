# 15k 星类名包的简单源代码分析

> 原文：<https://levelup.gitconnected.com/simple-source-code-analysis-of-classnames-with-15k-stars-1c3276fe9528>

## 附带 TypeScript 实现

![](img/0fa505597e79efac947385b14fc5406e.png)

照片由[一年级](https://unsplash.com/@year1?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

如果您正在开发 web 应用程序，您可能听说过 [classnames](https://github.com/JedWatson/classnames) npm 包。它与 React 等一些前端框架一起工作，允许您非常容易地编写 CSS 类。本文将探讨它是如何工作的。

学习一个 npm 包的时候，我的习惯是先用。只有知道如何使用，才能更好地理解它。它的使用教程可以在 [README.md](https://github.com/JedWatson/classnames#readme) 中查看，以下是从中摘录的一些例子。

```
classNames('foo', 'bar'); // => 'foo bar'
classNames('foo', { bar: true }); // => 'foo bar'
classNames({ 'foo-bar': true }); // => 'foo-bar'
classNames({ 'foo-bar': false }); // => ''
classNames({ foo: true }, { bar: true }); // => 'foo bar'
classNames({ foo: true, bar: true }); // => 'foo bar'// lots of arguments of various types
classNames('foo', { bar: true, duck: false }, 'baz', { quux: true }); // => 'foo bar baz quux'// other falsy values are just ignored
classNames(null, false, 'bar', undefined, 0, 1, { baz: null }, ''); // => 'bar 1'
```

可以看到它的 API 非常好用。接下来，让我们看看引擎盖下。

首先，我们需要看一下它的 [package.json](https://github.com/JedWatson/classnames/blob/master/package.json) ，它描述了关于 npm 包的一些信息。为了理解它是如何工作的，最重要的字段是`"main": "index.js",`和`"types": "./index.d.ts"`。

main 字段表示 npm 包的条目文件，types 字段表示 TypeScript 运行时下的类型定义。除此之外，它还提供了另一个`[dedupe](https://github.com/JedWatson/classnames/blob/master/dedupe.js)`版本和`[bind](https://github.com/JedWatson/classnames/blob/master/bind.js)`版本(用于 CSS 模块)

接下来，我们来关注一下`[index.js](https://github.com/JedWatson/classnames/blob/master/index.js)`。

是官方分叉版，我加了一些评论。可以看出它的逻辑是比较简单的，它的一些判断在今天已经考虑得很好了。

下面是我按照它的[类型定义](https://github.com/JedWatson/classnames/blob/master/index.d.ts)制作的类型脚本版本:

我使用了 ES6 语法，它也通过了类名内置测试用例。希望这对你有帮助。如有疑问欢迎评论。

# 参考

[1][https://github.com/JedWatson/classnames](https://github.com/JedWatson/classnames)

*感谢阅读。如果你喜欢这样的故事，想支持我，请考虑成为* [*中等会员*](https://medium.com/@islizeqiang/membership) *。每月 5 美元，你可以无限制地访问媒体内容。如果你通过* [*我的链接*](https://medium.com/@islizeqiang/membership) *报名，我会得到一点佣金。*

你的支持对我来说非常重要——谢谢。
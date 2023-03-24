# 节点 8 中的新功能

> 原文：<https://levelup.gitconnected.com/whats-new-in-node-8-e1cce6539a71>

## 节点 8 中的功能概述

![](img/b63327a83089fd5558276b521155e037.png)

节点 8 已经发布！请查看下面的新内容。

版本 8 是 Node 的最新版本，它包含了很多特性。Node 8 的代号为 Carbon，原定于 4 月底发布，但由于 V8 JavaScript 引擎的[点火](https://v8project.blogspot.com/2016/08/firing-up-ignition-interpreter.html)和[涡轮风扇](http://benediktmeurer.de/2017/03/01/v8-behind-the-scenes-february-edition/)的发布而推迟。节点 8 将使用 V8 5.8，ABI 与 V8 6.0 兼容。这将提供更好的性能，更强的 V8 支持契约，以及由 [Node.js 集合](https://medium.com/the-node-js-collection/node-js-8-0-0-has-been-delayed-and-will-ship-on-or-around-may-30th-cd38ba96980d)指示的节点 8 和 9 之间更小的增量。Node 8 仍将于 2017 年 10 月进入 LTS，并将支持到 2019 年 12 月。

[![](img/26797da0642875dc5a034a11d5991f56.png)](https://gitconnected.com/?utm_source=publication&utm_medium=cta-banner)

# 怎么样

## [**N-API**](https://nodejs.org/dist/latest-v7.x/docs/api/n-api.html) **，一个构建原生插件的 API**

该 API 将是跨 Node.js 版本的应用程序二进制接口(ABI)稳定的。它旨在将插件与底层 JavaScript 引擎的变化隔离开来，并允许为一个版本编译的模块在 Node.js 的更高版本上运行，而无需重新编译。该 API 目前处于试验阶段。它独立于底层 JavaScript 运行时(ex V8 ),作为 Node.js 本身的一部分进行维护。

## **效用承诺**

允许开发者包装回调 API 来返回承诺。该函数的工作开销很小，并且遵循标准的 API。

## **检查员的 JS 绑定**

新的 inspector 核心模块使开发人员能够利用 Chrome inspector 使用的调试协议来检查当前运行的 JavaScript 代码。这个特性是实验性的。

## [NPM**5**](https://github.com/npm/npm/releases/tag/v5.0.0)

Node 8 将搭载最新版本的 NPM。在 Yarn 取得成功和显著改进之后，NPM 在许多领域也紧随其后，为了竞争，它在版本 5 中发布了一个巨大的更新。最显著的特性是离线模式，默认为`--save`，以及一个自动创建的新锁文件。

## **异步 API**

AsyncHooks 使用户能够跨节点的异步操作监控和跟踪状态，从而实现更好的诊断工具和其他实用程序。这类似于流行的延续本地存储库，它提供了设置和获取这些函数调用链生命周期范围内的值的能力。cls 挂钩的库已经创建了一个插件解决方案来取代延续的本地存储，以支持本地 API。请记住，异步仍然是实验性的。

## **零填充缓冲器**

默认情况下，遗留的`new Buffer(num)`构造函数现在将填充零，这通过防止一个常见的陷阱变成安全问题而使节点更加安全。以前，使用`new Buffer(num)`构造函数分配的缓冲区没有用零初始化内存空间，这可能会导致敏感信息泄漏。

## **日期解析**

当没有时区偏移量时，仅日期格式被解释为 UTC 时间，日期时间格式被解释为本地时间。这遵循了 ES2015 中引入的行为。

# 其他值得注意的新增内容:

## 缓冲器

*   缓冲方法现在接受`Uint8Array`作为输入

## 子进程

*   参数和杀死信号验证得到了改进
*   方法接受`Uint8Array`作为输入

## 安慰

*   使用控制台方法时发出的错误事件现在被抑制

## 域

*   原生 Promise 实例现在是域感知的

## 错误

*   节点已开始为生成的错误分配静态错误代码

## 文件系统

*   实用程序类`fs.SyncWriteStream`已被弃用
*   已弃用的`fs.read()`字符串接口已被移除

## 超文本传送协议

*   改进了对用户实现的代理的支持
*   传出的 Cookie 标头被连接成一个字符串
*   `httpResponse.writeHeader()`方法已被否决
*   `OutgoingMessage`中增加了访问 HTTP 头的新方法

## 解放运动

*   旧的 linkedlist 模块已被删除

## 取代

*   REPL 魔法模式已被否决

## 科学研究委员会

`—-debug`命令行参数已被删除

## 溪流

*   Stream 现在支持`destroy()`和`_destroy()`API
*   Stream 现在支持`final()` API

## 坦克激光瞄准镜（Tank Laser-Sight 的缩写）

*   `rejectUnauthorized`选项现在默认为`true`

## 跑龙套

*   使用`util.inspect()`时，现在默认显示符号键
*   `toJSON`格式化`%j`时会抛出错误
*   将`inspect.styles`和`inspect.colors`转换为无原型对象

## Zlib

*   在 Zlib 便利方法中支持`Uint8Array`
*   Zlib 错误现在一致地使用`RangeError`和`TypeError`

## **网址:**

*   WHATWG URL 实现现在是完全受支持的 Node.js API

发布的完整列表和相关代码可以在 [GitHub](https://github.com/nodejs/node/pull/12220) 上找到

*如果您觉得本文有帮助，请点击*👏*。* [*关注我*](https://medium.com/@treyhuffine) *了解更多关于 React、Node.js、JavaScript 和开源软件的文章！你也可以在*[*Twitter*](https://twitter.com/treyhuffine)*或者*[*git connected*](https://gitconnected.com/treyhuffine)*上找到我。*

[](https://gitconnected.com/treyhuffine) [## git connected——开发者和软件工程师社区

### 创建一个帐户或登录 gitconnected，这是连接像您这样的人的最大网络。关注最新打开的…

gitconnected.com](https://gitconnected.com/treyhuffine)
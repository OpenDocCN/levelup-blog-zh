# 节点 10 的新功能

> 原文：<https://levelup.gitconnected.com/whats-new-in-node-10-ad360ae55ee4>

## Node.js v10 功能概述

![](img/d2e4b73c7c5abb9e11e27f84a8fd917e.png)

版本 10 是 Node.js 的最新版本，它包含了许多功能。Node 10 的代号为“Dubnium”，于 2018 年 4 月 24 日发布，将于 2018 年 10 月进入长期支持(LTS)。JavaScript 开发人员今天一直在兴奋地等待，所以让我们来看看这个巨大版本最显著的特性。

[](https://gitconnected.com/learn/node-js) [## 学习 Node.js -最佳 Node.js 教程(2019) | gitconnected

### Node.js .教程的前 32 门课程由开发人员提交并投票，使您能够找到最好的 Node.js…

gitconnected.com](https://gitconnected.com/learn/node-js) 

# 添加错误代码

节点中的错误现已标准化，具有可重复模式的一致代码。

在节点环境中处理错误是一件痛苦的事情。以前，它们只包含一个字符串消息，没有其他相关的标识符。如果开发人员希望程序根据特定的消息采取行动，唯一的选择就是对错误内容进行字符串比较。

因为错误处理需要精确的字符串匹配，所以即使是最小的消息更新也不能添加到下一个主要节点版本中，这样就不会破坏 SemVer。通过将错误从消息中分离出来，这将使开发人员更容易使用它们，并允许 Node 在不引入重大更改的情况下改进错误消息。要了解更多信息，请阅读麦可·道森的文章节点错误代码。

# N-API 不再是实验性的

节点文档将 N-API 描述为构建本地插件的 API。它独立于底层 JavaScript 运行时(例如 V8 ),作为 Node.js 本身的一部分进行维护。该 API 将是跨 Node.js 版本的应用程序二进制接口(ABI)稳定的。它旨在将插件与底层 JavaScript 引擎的变化隔离开来，并允许为一个版本编译的模块在 Node.js 的更高版本上运行，而无需重新编译。

N-API 是在 Node 8 中实验性引入的，并将在 Node 10 中保持稳定。节点版本之间的升级将不再引起模块损坏的问题。为了 Node.js v6.x 和 v8.x 的兼容性，它也将被向后移植。

# 本地节点 HTTP/2 变得稳定

Node 8 中引入了一个实验性的 HTTP/2 模块，这是对 Node 的重大升级。HTTP/2 比标准的 HTTP 协议有所改进

*   多路技术
*   单一连接
*   服务器推送
*   优化
*   标题压缩

通过离开实验阶段，本地 HTTP/2 模块将有助于改进节点服务器和它们提供的 web 体验。

# 节点 10 的新功能

## Node.js v10 功能概述

![](img/d2e4b73c7c5abb9e11e27f84a8fd917e.png)

版本 10 是 Node.js 的最新版本，它包含了许多功能。Node 10 的代号为“Dubnium”，于 2018 年 4 月 24 日发布，将于 2018 年 10 月进入长期支持(LTS)。JavaScript 开发人员今天一直在兴奋地等待，所以让我们来看看这个巨大版本最显著的特性。

[](https://gitconnected.com/learn/node-js) [## 学习 Node.js —最佳 Node.js 教程(2019) | gitconnected

### Node.js .教程的前 32 门课程由开发人员提交并投票，使您能够找到最好的 Node.js…

gitconnected.com](https://gitconnected.com/learn/node-js) 

# 添加错误代码

节点中的错误现已标准化，具有可重复模式的一致代码。

在节点环境中处理错误是一件痛苦的事情。以前，它们只包含一个字符串消息，没有其他相关的标识符。如果开发人员希望程序根据特定的消息采取行动，唯一的选择就是对错误内容进行字符串比较。

因为错误处理需要精确的字符串匹配，所以即使是最小的消息更新也不能添加到下一个主要节点版本中，这样就不会破坏 SemVer。通过将错误从消息中分离出来，这将使开发人员更容易使用它们，并允许 Node 在不引入重大更改的情况下改进错误消息。要了解更多信息，请阅读麦可·道森的文章节点错误代码。

# N-API 不再是实验性的

节点文档将 N-API 描述为构建本地插件的 API。它独立于底层 JavaScript 运行时(ex V8 ),作为 Node.js 本身的一部分进行维护。该 API 将是跨 Node.js 版本的应用程序二进制接口(ABI)稳定的。它旨在将插件与底层 JavaScript 引擎的变化隔离开来，并允许为一个版本编译的模块在 Node.js 的更高版本上运行，而无需重新编译。

N-API 是在 Node 8 中实验性引入的，并将在 Node 10 中保持稳定。节点版本之间的升级将不再引起模块损坏的问题。为了 Node.js v6.x 和 v8.x 的兼容性，它也将被向后移植。

# 本地节点 HTTP/2 变得稳定

Node 8 中引入了一个实验性的 HTTP/2 模块，这是对 Node 的重大升级。HTTP/2 比标准的 HTTP 协议有所改进

*   多路技术
*   单一连接
*   服务器推送
*   优化
*   标题压缩

通过离开实验阶段，本地 HTTP/2 模块将有助于改进节点服务器和它们提供的 web 体验。

[![](img/26797da0642875dc5a034a11d5991f56.png)](https://gitconnected.com/?utm_source=publication&utm_medium=cta-banner)

# V8 发动机 v6.6 的性能改进

Node 使用 Chromium 中使用的 V8 JavaScript 引擎，Node.js v10 配备了最新版本。对于浏览器，Chrome 66 附带的 V8 引擎 v6.6 将 JavaScript 的解析和编译时间减少了约 20-40%。因此，我们可以期待 Node 10 在这方面也能实现巨大的优势。此外，它还提供异步生成器和阵列性能改进。

对于软件来说，速度是王道，最新版本在改进方面没有令人失望。查看 V8 团队的文章以[了解更多](https://v8project.blogspot.com/2018/04/improved-code-caching.html)。

# 更好地支持 ES 模块(ESM)

```
// ESM
import pkg from “./pkg”
export default { a, b: 2 }vs.// CJS
const pkg = require(“./pkg”)
module.exports = { a, b: 2 }
```

虽然我们不会在 Node 10 中看到对 ES 模块的完全支持，但在本地支持它们的努力仍在继续。

节点一直使用 CommonJS (CJS ),这是 require and module.exports 语法。在 2015 年发布的 epic ES6 中，引入了一种新的模块系统，称为 ECMAScript 模块(ESM)。作为 ECMA 的官方实现，随着开发人员的喜爱和广泛采用，Node 一直在努力实现自己的 ESM 规范。

将 ESM 集成到 Node 并不是一条完全平坦的道路，因为它确实与当前系统相冲突。但是，我们注意到了协调的必要性，Node 正在努力提供解决方案。[吉尔·塔亚尔](https://medium.com/u/c845cd91bc98)写了一篇关于这个主题的很棒的文章，如果你想[了解更多](https://medium.com/@giltayar/native-es-modules-in-nodejs-status-and-future-directions-part-i-ee5ea3001f71)。

# 改进的诊断跟踪和事后分析

添加了跟踪事件，使开发人员能够更好地了解他们的 Node.js 应用程序。这种新的见解可以提供关于时间和性能问题的改进指标。API 允许用户在运行时打开或关闭事件，从而能够按需诊断问题。

# npm v6 立即发货

npm 最近从 v5.7 升级到了 v6.0，Node 10 将立即提供更新。npm 的这个主要版本在包括性能、安全性和稳定性在内的所有方面都有所改进。在他们的[博客](https://medium.com/npm-inc/announcing-npm-6-5d0b1799a905)上阅读更多关于 npm v6 的内容。

# 升级到 OpenSSL 版本 1.1.0

节点配备了现代加密支持，支持备受期待的 ChaCha20 密码和 Poly1305 验证器。TLS 1.3 最近已经完成，当 Node.js v10 在 10 月份到达 LTS 时，它将完全支持该标准。

# “fs”函数的实验性承诺版本

与文件系统交互是许多 Node 应用程序的主要功能，Node 10 将发布实验版的`fs`包，并承诺。以前，这些函数通过回调处理异步动作，但是可以使用 Node 8 附带的`util.promisify()`函数进行转换。现在开发者可以利用`fs`进行承诺，而不需要额外的步骤。

# 结束 Node 的重要一天

Node 团队继续添加一些特性，让我们开发人员的生活更加愉快，同时也让我们能够为用户创造更好的体验。Node 10 进一步推动了技术的前沿，并使评估错误、构建原生插件或了解我们的应用程序变得更加方便——以及许多其他改进。

节点 10 将从 2018 年 10 月到 2021 年 4 月留在 LTS。节点团队遵循特定的偶数/奇数[发布周期](https://github.com/nodejs/Release)。进入 LTS 后，Node 11 也将于 10 月发布。奇数版本意味着实验，偶数版本是 LTS 版本。这也标志着对 Node 4 的长期支持被弃用。

祝 Java 编写愉快！

![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)[](https://gitconnected.com/portfolio-api) [## 组合 API —轻松发展您的编码事业| gitconnected

### 消除在每个单独位置手动更新您的详细信息的痛苦。只需在您的中更改一次数据…

gitconnected.com](https://gitconnected.com/portfolio-api) ![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)

# V8 发动机 v6.6 的性能改进

Node 使用 Chromium 中使用的 V8 JavaScript 引擎，Node.js v10 配备了最新版本。对于浏览器，Chrome 66 附带的 V8 引擎 v6.6 将 JavaScript 的解析和编译时间减少了约 20-40%。因此，我们可以期待 Node 10 在这方面也能实现巨大的优势。此外，它还提供异步生成器和阵列性能改进。

对于软件来说，速度是王道，最新版本在改进方面没有令人失望。查看 V8 团队的文章至[了解更多](https://v8project.blogspot.com/2018/04/improved-code-caching.html)。

# 更好地支持 ES 模块(ESM)

```
// ESM
import pkg from “./pkg”
export default { a, b: 2 }vs.// CJS
const pkg = require(“./pkg”)
module.exports = { a, b: 2 }
```

虽然我们不会在 Node 10 中看到对 ES 模块的完全支持，但在本地支持它们的努力仍在继续。

节点一直使用 CommonJS (CJS ),这是 require and module.exports 语法。在 2015 年发布的 epic ES6 中，引入了一种新的模块系统，称为 ECMAScript 模块(ESM)。作为 ECMA 的官方实现，随着开发人员的喜爱和广泛采用，Node 一直在努力实现自己的 ESM 规范。

将 ESM 集成到 Node 并不是一条完全平坦的道路，因为它确实与当前系统相冲突。但是，我们注意到了协调的必要性，Node 正在努力提供解决方案。[吉尔·塔亚尔](https://medium.com/u/c845cd91bc98)写了一篇关于这个主题的很棒的文章，如果你想[了解更多](https://medium.com/@giltayar/native-es-modules-in-nodejs-status-and-future-directions-part-i-ee5ea3001f71)。

# 改进的诊断跟踪和事后分析

添加了跟踪事件，使开发人员能够更好地了解他们的 Node.js 应用程序。这种新的见解可以提供关于时间和性能问题的改进指标。API 允许用户在运行时打开或关闭事件，从而能够按需诊断问题。

# npm v6 立即发货

npm 最近从 v5.7 升级到了 v6.0，Node 10 将立即提供更新。npm 的这个主要版本在包括性能、安全性和稳定性在内的所有方面都有所改进。在他们的[博客](https://medium.com/npm-inc/announcing-npm-6-5d0b1799a905)上阅读更多关于 npm v6 的内容。

# 升级到 OpenSSL 版本 1.1.0

节点配备了现代加密支持，支持备受期待的 ChaCha20 密码和 Poly1305 验证器。TLS 1.3 最近已经完成，当 Node.js v10 在 10 月份到达 LTS 时，它将完全支持该标准。

# “fs”函数的实验性承诺版本

与文件系统交互是许多 Node 应用程序的主要功能，Node 10 将发布实验版的`fs`包，并承诺。以前，这些函数通过回调处理异步动作，但是可以使用 Node 8 附带的`util.promisify()`函数进行转换。现在开发者可以利用`fs`进行承诺，而不需要额外的步骤。

# 结束 Node 的重要一天

Node 团队继续添加一些特性，让我们开发人员的生活更加愉快，同时也让我们能够为用户创造更好的体验。Node 10 进一步推动了技术的前沿，并使评估错误、构建原生插件或了解我们的应用程序变得更加方便——以及许多其他改进。

节点 10 将从 2018 年 10 月到 2021 年 4 月留在 LTS。节点团队遵循特定的偶数/奇数[发布周期](https://github.com/nodejs/Release)。进入 LTS 后，Node 11 也将于 10 月发布。奇数版本意味着实验，偶数版本是 LTS 版本。这也标志着对 Node 4 的长期支持被弃用。

祝 Java 编写愉快！

![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)[](https://gitconnected.com/portfolio-api) [## 组合 API —轻松发展您的编码事业| gitconnected

### 消除在每个单独位置手动更新您的详细信息的痛苦。只需在您的中更改一次数据…

gitconnected.com](https://gitconnected.com/portfolio-api) ![](img/aced8a676f6e35c5f7a618a71ff5fd7b.png)

*如果您觉得本文有帮助，请点击👏。* [*关注我*](https://medium.com/@treyhuffine) *了解更多关于 React、JavaScript、区块链和开源软件的文章！也可以在*[*Twitter*](https://twitter.com/treyhuffine)*或者*[*git connected*](https://gitconnected.com/treyhuffine)*上找到我。*
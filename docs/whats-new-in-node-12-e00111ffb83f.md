# 节点 12 中的新功能

> 原文：<https://levelup.gitconnected.com/whats-new-in-node-12-e00111ffb83f>

## JavaScript 运行时的 Node.js 12 特性和更新概述

![](img/9634c1dfe1297a0101db3f9543706120.png)

[Node.js](https://nodejs.org/en/) 在版本 12(代号`Erbium`)中继续其每年的发布周期。由于这是偶数版本，它将从 2019 年 10 月开始进入长期支持(LTS ),并于 2022 年 4 月达到其生命周期终点。

> 相比之下，奇数版本是非 LTS 版本，在 LTS 版本之间存在 6-8 个月。这些用于为下一个 LTS 版本做准备。

Node 12 包含了强大的特性和对运行时的显著升级。此外，由于 Node 使用由 Google 维护并在 Chrome 中使用的 [V8 引擎](https://v8.dev/)，Node 也将从该引擎接收所有更新。

[](https://gitconnected.com/resume-builder) [## 软件工程师简历生成器和示例| gitconnected

### 一份有价值的简历模板，使用您个人资料中的详细信息构建。从你的投资组合网站链接到你的简历或…

gitconnected.com](https://gitconnected.com/resume-builder) 

# 节点 12 功能

## 支持导入/导出语句(不需要捆绑器)

节点 12 进入 ECMAScript 模块的[阶段 3，这对应于节点内部模块的稳定性路径。最初，它仍将在`--experimental-modules`旗帜后面，计划在 10 月节点 12 进入 LTS 时移除旗帜。](https://github.com/nodejs/modules/blob/master/doc/plan-for-new-modules-implementation.md#phase-3-path-to-stability-removing---experimental-modules-flag)

自从在 ES6 中标准化以来，`import` / `export`语法已经成为 JavaScript 开发人员的首选模块语法，Node 团队一直在努力使其成为本机语法。实验性支持始于 Node 8.0 的第 0 阶段，随着最新 Node 版本的发布，实验性支持迈出了重要的一步。所有主流浏览器[都通过`<script type="module">`支持](https://caniuse.com/#feat=es6-module) ECMAScript 模块，所以这对 Node 来说是一个巨大的更新。

阶段 3 将支持来自 es 模块文件的 3 种类型的`import`(适用于所有内置节点包):

```
// default exports
import module from 'module'// named exports
import { namedExport } from 'module'// namespace exports
import * as module from 'module'
```

如果您从一个 CommonJS 包中`import`，那么您只能使用默认的导出语法`import module from 'cjs-library'`进行导入。

您可以使用动态的`import()`表达式在运行时加载文件。`import()`语法返回一个`Promise`并与 ES 模块和 CommonJS 库一起工作。

## V8 发动机

节点 12 最初将在 V8 7.4 上运行，并最终在其生命周期内升级到 7.6。V8 团队已经同意为这个系列提供 ABI(应用程序二进制接口)稳定性。V8 7.4 的显著改进是更快的 JavaScript 执行速度、更好的内存管理和更广泛的 ECMAScript 语法支持。

*   异步堆栈跟踪
*   更快地解析 JavaScript
*   参数不匹配时调用速度更快
*   更快`await`

## 私有类字段

节点 12 将带有私有类字段(由 V8 启用)。私有类字段变量只能在类内部访问，不能对外公开。私有类字段是通过在变量前加上`#`符号来声明的。

```
class Greet {
  #name = 'World'; get name() {
    return this.#name;
  } set name(name) {
    this.#name = name;
  } sayHello() {
    console.log(`Hello, ${this.#name}`);
  }
}
```

在上面的例子中，如果你试图在类之外访问`#name`，你会收到一个语法错误。

```
const greet = new Greet()
greet.#name = 'NewName';
// -> SyntaxError
console.log(greet.#name)
// -> SyntaxError
```

## 提高启动性能

节点 12 将在构建之前为内置库构建代码缓存，并将其作为二进制文件嵌入。主线程能够使用这个代码缓存，将启动时间缩短了 30%。

## TLS 和安全性

节点现在支持 [TLS 1.3](https://www.ietf.org/blog/tls13/) ，这提供了更高的安全性和更低的延迟。TLS 1.3 是对该协议的一个巨大更新，并且正在被积极地集成到整个 web 中。通过实施 TLS 1.3，节点应用将增强最终用户隐私，同时通过减少 HTTPS 握手所需的时间来提高请求的性能。另外，TLS 1.0 和 1.1 已经默认禁用，`crypto`库已经移除了不推荐使用的函数。

## 堆大小的改进

以前使用的默认 V8 堆大小对应于 700MB (32 位系统)或 1400MB (64 位系统)。节点现在将根据可用内存确定堆大小，这将确保它不会使用超过允许的资源。

## 堆转储功能

Node 12 提供了生成堆转储的能力，从而更容易调查内存问题。

## 实验诊断报告

Node 通过提供实验性诊断报告功能，提高了诊断应用程序中的问题(性能、CPU 使用率、内存、崩溃等)的能力。

## 本机模块 N-API 改进

N-API 的发布是为了提供一个更加稳定和本地的节点模块系统，通过在本地 JavaScript APIs 上提供一个 ABI 稳定的抽象来防止库在每次发布时崩溃。Node 12 结合工作线程提供了对 N-API 的改进支持。

## 其他显著改进

*   工作线程不再需要标志
*   `http`已经将其默认解析器更新为 [llhttp](https://llhttp.org/)
*   `assert`验证所需的参数并调整松散的断言
*   `buffer`改进使其更加稳定和安全
*   `async_hooks`删除不推荐使用的功能
*   制造`global.process`、`global.Buffer`吸气剂来提高`process`
*   `repl`的新欢迎信息

# 结论

Node 团队非常努力地为 Node 和整个 JS 生态系统提供每年一次的更新，12 版本没有让他们失望。完整的`CHANGELOG`可以在 GitHub 上找到[以及](https://github.com/nodejs/node/blob/master/doc/changelogs/CHANGELOG_V12.md)[官方发布文章](https://medium.com/@nodejs/introducing-node-js-12-76c41a1b3f3f)上的更多细节。

2019 年已经是 Node 的大年，Node.js 基金会与 js 基金会合并成立 [OpenJS 基金会](https://openjsf.org/)。随着 Node version 12 的发布，我们继续了 JavaScript 激动人心的一年。

[](https://gitconnected.com/learn/node-js) [## 学习 Node.js -最佳 Node.js 教程(2019) | gitconnected

### 前 33 个 Node.js 教程-免费学习 Node.js。课程由开发人员提交和投票，使您能够…

gitconnected.com](https://gitconnected.com/learn/node-js)
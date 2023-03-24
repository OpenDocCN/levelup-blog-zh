# 编写 TypeScript 自定义 AST 转换器(第 1 部分)

> 原文：<https://levelup.gitconnected.com/writing-typescript-custom-ast-transformer-part-1-7585d6916819>

![](img/ecb80a67927b7925387194365ac77242.png)

[Utsman 媒体](https://unsplash.com/@utsmanmedia?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 背景

Babel 的生态系统非常依赖插件，插件允许你做很多事情，从与其他生态系统的互操作(例如 CSS)到优化(例如提升/内联)。另一方面，TypeScript 编译器被设计成一个包含电池的整体，开箱即用，因此需要更少的插件来构建。

然而，当 TS 编译器开始支持定制的 AST Transformer 时，这种情况在 TypeScript 2.3-ish 中又部分变了回来。这为将类似的功能从 babel 带到 TS 提供了很多可能性，而不必链接这些工具，也可以充分利用 TS typechecker。

# 我为什么在乎？

首先，如果您对了解 toolchain 如何在幕后工作以及一般的 AST 转换感兴趣，这可能会让您感兴趣。

对我来说，现实世界有趣的一面是弄清楚在源代码中写什么和编译器能为你做什么之间的界限。这改变了我写代码的方式，特别是手动优化某些代码路径和执行惯例。理解这是如何工作的以及可以做些什么允许我将其中一些从手工代码审查转移到编译时转换。

此外，这为编译器宏和与其他生态系统的互操作提供了机会，同时在源代码中强制执行显式依赖。

这将是一个由多个部分组成的系列，第 1 部分专门讨论样板文件。

# 样板文件

不幸的是，现在`tsconfig.json`不允许指定定制的 AST 变压器。有几个可供选择的方法，每个都有自己的注意事项:

1.  【webpack 生态系统的 https://github.com/TypeStrong/ts-loader
2.  [https://github.com/TypeStrong/ts-node](https://github.com/TypeStrong/ts-node)REPL
3.  [https://github.com/cevek/ttypescript](https://github.com/cevek/ttypescript)进行`tsc`更换
4.  编写自己的编译器包装器

我将在这篇文章中专门讨论选项 4，主要是因为它更有趣，而且除了`typescript`本身之外，它没有任何外部依赖性。

# 简单编译器包装器

TypeScript wiki 已经有了一套关于如何使用我强烈推荐的编译器 API[的不错的文档。对于我的大多数 TS Transformer 项目，我的样板文件如下所示:](https://github.com/Microsoft/TypeScript/wiki/Using-the-Compiler-API)

这种包装可以分为三个主要部分:

## 解析`tsconfig.json`

```
ts.getParsedCommandLineOfConfigFile(configFilePath, undefined, host); 
```

在高层次上，TS 编译器接受两个主要参数:要编译的文件列表和用来编译它们的`CompilerOptions`。您可以选择手动指定以下选项:

然而，使用`getParsedCommandLineOfConfigFile`有几个好处

1.  将配置集中在`tsconfig.json`周围，这意味着您不必手动将`glob`文件传递给编译器。
2.  这将处理 CLI 覆盖。
3.  与手动指定相比，此函数生成的最终`CompilerOptions`略有不同。

## 创建`Program`和`emit`结果

这是构建 TS `Program`的大部分工作，开始编译&将`.js`和`.d.ts`发送到指定的输出目录。这也是您指定自定义 AST 转换器的地方，例如:

TS 本身默认自带[很多 ESNext - > ES5 变形金刚](https://github.com/Microsoft/TypeScript/tree/master/src/compiler/transformers)。pipeline 允许您以特定的方式订购定制变压器:

1.  `before`意味着您的转换器在 TS 之前运行，这意味着您的转换器将获得原始 TS 语法而不是转换语法(例如`import`而不是`require`或`define`)
2.  `after`表示您的转换器在 TS 转换器之后运行，这将获得 transpiled 语法。
3.  `afterDeclarations`意味着您的转换器在`d.ts`生成阶段运行，允许您转换输出类型声明。

我写的大多数变形金刚都是`before`变形金刚，因为那里是大多数用例出现的地方。如果你潜在地修改类型，那么`after` & `afterDeclarations`是必要的。例如:

1.  [https://github.com/longlho/ts-transform-json](https://github.com/longlho/ts-transform-json)将 JSON 键内联到输出文件中，这也需要修改`d.ts`，否则类型声明仍然会保留原始的`json`文件`import`，这使得它变得不那么有用。
2.  [https://github.com/dropbox/ts-transform-import-path-rewrite](https://github.com/dropbox/ts-transform-import-path-rewrite)修改`import` & `export`路径，因此需要`d.ts`匹配新重写的路径，否则类型检查将失败。

这是第一部分。我将开始剖析一个定制的 AST 转换器，并介绍我在后续部分中编写的一些转换器:)

[](https://gitconnected.com/learn/typescript) [## 学习 TypeScript -最佳 TypeScript 教程(2019) | gitconnected

### 18 大 TypeScript 教程-免费学习 TypeScript。课程由开发人员提交并投票，从而实现…

gitconnected.com](https://gitconnected.com/learn/typescript)
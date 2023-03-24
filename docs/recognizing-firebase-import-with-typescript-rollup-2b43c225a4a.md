# 如何在 Rollup + TypeScript 中初始化 Firebase

> 原文：<https://levelup.gitconnected.com/recognizing-firebase-import-with-typescript-rollup-2b43c225a4a>

![](img/a303fd1c4ca5adbdad36f5a3f33c923c.png)

如果您正在使用 TypeScript 和 Rollup 构建 JavaScript 库或应用程序，那么在代码中导入 Firebase 可能会有问题。

> “initializeApp”不是由“node _ modules/firebase/app/dist/index . ESM . js”导出的
> 或

在本文中，我将展示如何在不弄乱您的 TypeScript 代码的情况下修复这个问题。

我正在使用下面列出的那些库的版本。

*   燃烧基:7.14.6
*   打字稿:3.4.3
*   汇总:2.10.5
*   汇总插件类型脚本 2: 0.27.1
*   rollup-plugin-commonjs: 10.1.0

首先，您将在 TypeScript 代码中导入 Firebase:

```
import * as firebase from 'firebase/app';
```

并导入您将使用的 Firebase 模块。例如，我正在导入 Firestore 模块。

```
import 'firebase/firestore';
```

现在是魔术的第一部分。忽略 Firebase 初始化时的类型脚本林挺。这样，它将允许改变`initializeApp`调用。

```
// [@ts](http://twitter.com/ts)-ignore
const app = firebase.initializeApp(config);
```

然后您将使用汇总替换插件( [@rollup/plugin-replace](http://twitter.com/rollup/plugin-replace) )来更改汇总配置中的 Firebase 初始化调用。

```
import replace from '[@rollup/plugin-replace](http://twitter.com/rollup/plugin-replace)'
// ...
{
 // ...
 plugins: [
  replace({
    'firebase.initializeApp': 'firebase.default.initializeApp'
   }),
 ....
 ]
}
```

就是这样！现在，Firebase 库可以在带有 Rollup 环境的 TypeScript 中工作了。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)
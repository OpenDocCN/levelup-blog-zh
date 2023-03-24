# 编写 TypeScript 自定义 AST 转换器(第 2 部分)

> 原文：<https://levelup.gitconnected.com/writing-typescript-custom-ast-transformer-part-2-5322c2b1660e>

![](img/0e34d87b613012c9b0171ee656ae94c6.png)

Todd Quackenbush 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

这是第 1 部分的继续，在那里我谈到了高级 TS 编译器 API 样板文件。这篇文章将集中讨论必要的工具，并介绍我编写的一个简单的 AST transformer。

# 编译过程

你可以在这里读到更多关于这个的细节。但是一般来说这个过程是这样的:

1.  预处理:读取源文件，聚集依赖关系，构造依赖关系树
2.  解析:将`SourceFile`转换成`Node`。
3.  绑定:从`Node`创建`Symbol`并构造`TypeChecker`来巩固`Symbol`
4.  发射:将`Node` s 转换成 JS 并打印出相应的输出文件

现在，AST transformers 只能影响发送过程，而不能操纵`Symbol`表或向依赖图中添加新文件，这需要一个更复杂的`TypeChecker`插件系统

# 高水位流量

AST transformer 的操作类似于任何节点遍历机制(例如 XML 解析器)。每次 TS 编译器访问一个节点，它都会触发 transformer 函数，潜在地用一个新节点替换它。您的转换器的入口点看起来像这样:

其中`transformNode`可以通过结合`ts.visitEachChild`调用自身来递归遍历树。我将在下面的演练中详细讨论这一点。

为了转换节点，我通常遵循以下步骤:

1.  找到我感兴趣的节点(使用`ts.isFunctionDeclaration`等)
2.  用`ts.getMutableClone`克隆它
3.  操纵克隆节点的某些属性
4.  用`ts.setSourceMapRange`设置源地图文本范围
5.  返回修改的节点

在计算要寻找和转换到的正确节点时，https://astexplorer.net/是一个重要的工具。

# 简单示例演练

最简单的例子是 1-1 节点转换，因为它通常不涉及范围操作，或者桥接外部编译过程(像 PostCSS)。[https://github.com/dropbox/ts-transform-import-path-rewrite](https://github.com/dropbox/ts-transform-import-path-rewrite)是我在 Dropbox 时编写的一个转换器，它处理路径重写，允许我们的代码适应不同的构建工具链(绝对与相对导入/导出路径，或者不同的`npm` `node_module`分辨率)。

我特别关心的节点是:

```
import * as foo from './foo'; // Namespace import
import {foo} from './foo'; // Named import
export {foo} from './foo'; // Named re-export
export * as foo from './foo'; Namespace re-export
import('./foo'); // Dynamic Import
```

节点分解如下所示:

```
import * as foo from './foo'; // ts.ImportDeclaration
* as foo --> ts.ImportDeclaration.importClause
'./foo' --> ts.ImportDeclaration.moduleSpecifier
```

在这种情况下，由于我们只关心重写路径，我们可以在这个节点中操纵`moduleSpecifier`。按照上述步骤:

## 第一步

找到感兴趣的节点

## 第二步

克隆它

## 第三步

操纵它的属性，在这种情况下我们可以用新路径替换`moduleSpecifier`

有几件事要记住:

1.  `moduleSpecifier`包含引号，因为这是一个 AST 节点(例如，它的值实际上是`"./foo"`，而不是`./foo`
2.  你必须将一个常规的`string`转换成它的 AST 等价物`StringLiteral`

## 第四步

更新源映射(可选)

最后返回新的节点。

在实际的生产代码中，它要稍微复杂一些，因为它必须处理上面提到的其他 4 种情况。然而，这应该作为你如何操纵发射过程的框架。

其他一些超级简单但有用的 AST 转换器包括[https://github.com/dropbox/ts-transform-react-jsx-source](https://github.com/dropbox/ts-transform-react-jsx-source)，它将`__source`注入到每个`JSX`节点，因此 React 可以打印出更好的调试消息…

在下一部分中，我将介绍一个稍微复杂一点的转换器，并讨论如何编写编译器宏！

[](https://gitconnected.com/learn/typescript) [## 学习 TypeScript -最佳 TypeScript 教程(2019) | gitconnected

### 18 大 TypeScript 教程-免费学习 TypeScript。课程由开发人员提交并投票，从而实现…

gitconnected.com](https://gitconnected.com/learn/typescript)
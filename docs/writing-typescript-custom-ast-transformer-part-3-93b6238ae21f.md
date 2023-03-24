# 编写类型脚本定制 AST 转换器(第 3 部分)

> 原文：<https://levelup.gitconnected.com/writing-typescript-custom-ast-transformer-part-3-93b6238ae21f>

![](img/7556cd8d92deddb9b9318752e6419ebc.png)

这是第二部分的延续，也是对我在编写变形金刚时使用的一些先进技术的深入探究。

# 非 TS 进口

非 TS 导入允许我声明对静态资源的显式依赖，如 CSS/png/svg、其他非 JS 文件，甚至外部构建工具链。这在处理大型代码库时很重要，因为当 dep 图中有任何变化时，跟踪依赖链和重构会更容易。

这是我写[https://github.com/longlho/ts-transform-css-modules](https://github.com/longlho/ts-transform-css-modules)的首要原因

CSS 模块 AST 变压器

流程相当简单，每当我们遇到一个`import`节点，它的`moduleSpecifier`以`.css`结尾，我们就试图将该节点转换成一个`const foo = {className: '1nahjk'}`散列。

有趣的是，这允许您内联运行 PostCSS 构建管道，只要它是[同步的](https://medium.com/@longho/the-postcss-ecosystem-issue-b549c47b1a9)。

还建议在 sourcemap 中引用外部 CSS 源代码，以便 devtools 可以相应地下载它。

# 编译器宏

编译器宏是传统编译器中相当常见的概念。在巴别塔世界中，[巴别塔插件宏](https://github.com/kentcdodds/babel-plugin-macros)很好地引入了这个概念。这允许你做一些事情，比如标记一段特定的代码用于提取，预评估内联脚本，嵌入编译时变量等等。

从我的角度来看，`import`可以被视为某种编译器宏，在这里导入的资源可以得到不同的处理。另一个常见的例子是 i18n 的字符串提取，类似于[https://github.com/longlho/ts-transform-react-intl](https://github.com/longlho/ts-transform-react-intl)

每次遇到从`ts-transform-react-intl`导入的`_`符号，我都会将参数标记为`MessageDescriptor`并提取它们进行翻译。转换后的代码变成:

在这个过程中，我们还可以针对格式中声明的 ICU 语法运行 linter，以捕捉无效语法、表情符号、供应商限制等等。

# 利用类型检查器

检查`import`语句中的`_`可能不准确，尤其是当涉及到别名:`import {_ as _d} from 'ts-transform-react-intl'`

然而，TypeChecker API 允许 access 解析别名`typeChecker.getAliasedSymbol`和各种其他 API，这些 API 在将符号和类型连接在一起时变得非常方便。这肯定比 estree 之类的基于 AST 的解析器强大得多。

# 范围修改/注入

虽然 inline mod 非常方便，但在某些情况下，您可以优化代码，比如将变量提升到范围之外。虽然这可以在源代码中手动完成，但在开发和代码审查过程中肯定会耗费大量精力，而提取 const React 元素之类的事情可以自动完成:[https://github . com/Dropbox/ts-transform-React-constant-elements](https://github.com/dropbox/ts-transform-react-constant-elements)

# 一般流程

## 找到正确的词汇环境(范围)

`ctx.startLexicalEnvironment`:在堆栈中添加一个词法环境来存储区域设置变量/函数声明

`ctx.suspendLexicalEnvironment`和`ctx.resumeLexicalEnvironment`:通常在访问参数列表时，阻止创建新的作用域。

`ctx.endLexicalEnvironment`:弹出堆栈。

`ctx.hoistVariableDeclaration`:在堆栈中记录当前词法环境中的一个变量。

然而，一个新的作用域经常被创建，因此对它在哪里被提升的控制很少(例如，进入一个函数体创建了一个作用域，而提升只写入堆栈中的当前作用域，所以对提升没有控制)。

在我们的用例中，注入到顶部完全没问题。

## 查找顶部节点以注入此变量

`#!/env/node`:低于 shebang(不在规格中，可能在 Esprima 中被淘汰)

`"use strict";`:在序言指令下面(例如:“使用严格指令”)

`// ts-lint:disable`:低于顶级注释

`import * as React from 'react';`:下面导入 React

## 查找常量元素

因为我们不能遍历备份，我们必须完全遍历树，过滤掉具有不可变属性的节点并收集它们

## 改变

在收集了这些节点之后，我们必须使用`ts.createUniqueName(prefix)`将它们转换成预声明的变量名。

之后，我们可以通过添加`[reactNode, …hoistedVarStatements]`在 top React 导入节点之后追加。最后，进行另一次遍历，用之前创建的变量替换那些`ReactElement`。

# 管道链接

您甚至可以链接这些 AST 变压器，如下例所示:

希望你们能学到新东西！我在这个过程中学到了很多，包括阅读规范和深入生态系统中的其他构建管道。
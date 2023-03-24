# JavaScript 模块简介

> 原文：<https://levelup.gitconnected.com/an-intro-to-javascript-modules-36c07c5d4c9c>

## 更好的编程

## 了解如何使用导出和导入语句

![](img/85161a00a65dc3d782655ebe8957a798.png)

由 [Unsplash](https://unsplash.com/@lastnameeaster?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [La-Rel 复活节](https://unsplash.com/@lastnameeaster?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄的照片

谈到 JavaScript 模块和它们到底是如何工作的，以及为什么我们可以以不同的形式使用它们，似乎有些混乱。今天我将解释导出和导入模块的不同方式。

# JavaScript 模块的一些背景知识

JavaScript 程序开始时是简单的脚本或应用程序，代码库很小，但是随着它的发展和使用的增加，代码库的规模也急剧增加。为了支持这种增长，语言需要支持一种机制，在这种机制下，可以将代码分离或拆分成更小的、可重用的单元。节点。JS 拥有这种能力已经有一段时间了，直到它通过一个叫做模块的特性被整合到 JavaScript 中。因此，最终他们成功进入了语言本身和浏览器。

根据定义，一个模块只是一个文件，它可以通过指令`export`和`import`的帮助从其他模块(或文件)导入:

*   `export`:关键字标记应该可以从当前模块外部访问的变量和函数。
*   `import`:允许从其他模块导入功能。

稍后我们将回到更多的内容。

# 介绍一个例子

为了演示模块的使用，我们将创建一个简单的`user`模块，它将公开一个`User`类。让我们回顾一下项目的基本结构:

```
index.html
scripts/
    index.js
    modules/
        user.js
```

我们的应用程序将非常简单，它将只在屏幕上显示用户的名字，但有趣的是，这个名字将来自一个`User`类的对象实例。让我们通过现场演示来看看它的实际效果:

[在 codesandbox 中打开](https://codesandbox.io/s/an-intro-to-javascript-modules-j05tw?fontsize=14&hidenavigation=1&theme=dark)

让我们按部件详细看看那里发生了什么

# 导出模块用户

要访问`User`类，我们需要做的第一件事是从模块中导出它。为此，我们使用了`export`语句。

当创建 JavaScript 模块时，使用 export 语句从模块中导出函数、对象或基元值的动态绑定，以便其他程序可以使用 import 语句使用它们。

让我们看看我们的代码:

```
// file: scripts/modules/user.js
export class User {
  constructor(name) {
    this.name = name;
  }
}
```

既然模块已经导出，我们可以通过导入在其他模块中使用它。

# 正在导入模块用户

静态导入语句用于导入由另一个模块导出的只读活动绑定。无论是否声明，导入的模块都处于严格模式。import 语句不能在嵌入式脚本中使用，除非这样的脚本具有 type="module "。导入的绑定称为活动绑定，因为它们是由导出绑定的模块更新的。

让我们看看我们的例子

```
//file: scripts/index.js
import { User } from './modules/user.js'const user = new User('Juan')document.getElementById('user-name').innerText = user.name;
```

`import`语句允许我们从模块中导入特定的绑定。有几种不同的方法来指定我们要导入什么，我们将在后面的文章中讨论它们。现在，在我们的例子中，我们只是从指定的模块(或文件)导入`User`。

导入后，我们可以使用该对象，因为它是同一个文件的一部分。

# 默认导出与命名导出

到目前为止，我们通过名称导出了一个类，但是有 2 种不同的方式导出模块

*   命名导出(每个模块零个或多个导出)
*   默认导出(每个模块只有一个)

以下是一些命名导出的示例:

```
// export features declared earlier
export { myFunction, myVariable }; // export individual features (can export var, let, const, function, class)
export let myVariable = Math.sqrt(2);
export function myFunction() { ... };
```

默认导出:

```
// export feature declared earlier as default
export { myFunction as default };// export individual features as default
export default function () { ... } 
export default class { .. }
```

命名导出对于导出多个值非常有用。在导入过程中，必须使用与相应对象相同的名称。但是默认导出可以用任何名称导入，例如:

```
// file: myk.js
const k = 12
export default k// file: main.js
import m from './myk'
console.log(m)
```

使用命名导出时，也可以为导出的值分配一个自定义名称，如下例所示:

```
const name = 'value'
export {
  name as newName
}
```

导出的值现在可以作为`newName`而不是`name`导入。

# 进口

我们已经看到了一些如何从模块中导入命名或默认导出的例子。但在进口方面，这里有更多的选择。

# 导入默认导出

```
import something from 'mymodule'console.log(something)
```

# 导入命名导出

```
import { var1, var2 } from 'mymodule'console.log(var1)
console.log(var2)
```

# 重命名导入

```
import { var1 as myvar, var2 } from 'mymodule'// Now myvar will be available instead of var1
console.log(myvar)
console.log(var2)
```

# 从模块导入所有内容

```
import * as anyName from 'mymodule'console.log(anyName.var1)
console.log(anyName.var2)
console.log(anyName.default)
```

到目前为止，我们在这里描述的所有方法都是静态导入，这意味着您将它们放在文件的顶部，模块的内容总是被导入。但也不尽然，你也可以有动态导入。

# 动态导入

这允许您仅在需要时动态加载模块，而不是预先加载所有内容。这有一些明显的性能优势；让我们继续读下去，看看它是如何工作的。

这个新功能允许您将 import()作为一个函数调用，并将模块的路径作为一个参数传递给它。它返回一个承诺，这个承诺通过一个模块对象来实现，这个模块对象允许您访问该对象的导出，例如

```
import('./modules/myModule.js')
  .then((module) => {
    // Do something with the module.
  });
```

# 组合默认导出和命名导出

你没看错！可以将 default 和 named 组合在一起，正如您所料，您可以同时导入它们。让我们看一个例子:

```
//file: mymodule.js
export const named = 'named export'export function test() {
  console.log('exported function')
}export default 'default export';
```

我们可以使用以下任一方案导入它们:

```
//another file:
import anyName from './mymodule' // where anyName is the default export// or both named exports
import { named, test } from './mymodule';// or just one
import { named } from './mymodule';// or all of them together
import anyName, { named, test } from './mymodule';
```

# 结论

JavaScript 模块是一个强大的特性，它允许我们更好地组织代码，但是它也允许我们跨项目共享模块。我希望你今天喜欢并学到了一些新东西。

感谢阅读！
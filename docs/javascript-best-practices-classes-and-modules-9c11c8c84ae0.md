# JavaScript 最佳实践—类和模块

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-classes-and-modules-9c11c8c84ae0>

![](img/5d8dbb145d39ce5f827fe1173a571458.png)

[David Clode](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在这篇文章中，我们将看看在我们的类方法中应该有什么，以及使用模块的最佳方式。

# 类方法应该要么引用它，要么成为静态方法

JavaScript 类可以有静态或实例方法。如果是实例`method`，那么应该引用`this`。否则，它应该是一个静态方法。

例如，我们应该编写一个类似以下代码的类:

```
class Person {
  constructor(name) {
    this.name = name;
  } static greet() {
    return 'hi'
  } greetWithName() {
    return `hi ${this.name}`;
  }
}
```

在上面的代码中，我们有不引用`this`的静态`greet`方法和引用`this.name`的`greetWithName`方法。

静态方法与类的所有实例共享，实例方法是实例的一部分，所以它引用`this`是有意义的。

# 在非标准模块系统上使用 ES6 模块导入和导出

在 ES6 中，模块是 JavaScript 的标准特性。我们可以将我们的代码划分成模块，只导出我们想对外公开的代码。

此外，我们可以有选择地从另一个模块导入成员。

在 ES6 之前，有各种各样的模块系统，如 RequireJS 和 CommonJS。它们类似于今天的 ES6 模块。然而，ES6 模块可以做它们所做的，它们是一个 JavaScript 标准。

因此，我们应该只使用 ES6 模块而不使用其他类型的模块，这样我们就不必担心这些类型的模块会消失或者与其他类型的模块系统的兼容性问题。

例如，不用编写以下代码来导出和导入模块:

`module.js`

```
module.exports = {
  foo: 1
};
```

`index.js`

```
const { foo } = require("./module");
console.log(foo);
```

我们写道:

`module.js`

```
export const foo = 1;
```

`index.js`

```
import { foo } from "./module";
console.log(foo);
```

在第一个例子中，我们使用`module.exports`将成员导出为对象的属性。

然后我们用`require`函数导入了`foo`属性。这是 CommonJS 模块的老方法，在 JavaScript 将模块作为标准特性之前使用。

第二个例子使用标准的 JavaScript 模块做同样的事情。我们在`module.js`中导出成员`foo`，然后在`index.js`中导入`foo`。

JavaScript 模块已经成为标准很长时间了，从版本 12 开始，浏览器和 Node.js 都支持它，我们可以在任何地方使用常规的 JavaScript 模块。

如果没有，我们可以使用 Browserify、Webpack 或 package 这样的 transpilers 将模块代码转换成 ES5 代码。

# 不要使用通配符导入

通配符导入导入所有内容。它用星号表示。

例如，我们通过编写以下代码进行通配符导入:

`module.js`

```
export const foo = 1;
```

`index.js`

```
import * as module from "./module";
console.log(module.foo);
```

在上面的代码中，我们通过使用`*`和`as`关键字命名我们导入的模块来导入整个`module.js`模块，以便我们可以在下面的行中引用它。

这并不好，因为我们通常不需要一个模块的所有成员，所以导入所有的东西是没有效率的。

相反，我们应该只导入我们需要的成员。我们应该写:

```
import { foo } from "./module";
console.log(foo);
```

只导入我们需要的`foo`成员。

![](img/d6e511923573a6a5a1e74dfbd3d3a84a.png)

由[丘特尔 snap](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 不要直接从导入导出

我们不应该从进口直接出口。这意味着我们不应该使用`as`关键字直接在导出中将导出重命名为其他名称。

把他们和自己的线分开更清楚。例如，我们可以编写以下代码来实现这一点:

`module.js`

```
export const foo = 1;
```

`bar.js`

```
export { foo as bar } from "./module";
```

`index.js`

```
import { bar } from "./bar";
console.log(bar);
```

在上面的代码中，我们通过使用`export`语句将`module.js`中的`foo`成员导出为`bar`。

我们不应该这样做，因为不清楚我们是否同时从`module.js`导入并作为`bar`导出成员。

相反，我们应该在`bar.js`中写下以下内容:

```
import { foo } from "./module";
export { foo as bar } from "./module";
```

这样，很明显我们在一个模块中导入和导出。

# 结论

如果是实例方法，类方法应该引用`this`，否则应该是静态的。

我们应该使用 ES6 模块，因为它是一个 JavaScript 标准。此外，我们应该将导入和导出分开，不应该从一个模块中导入所有内容。
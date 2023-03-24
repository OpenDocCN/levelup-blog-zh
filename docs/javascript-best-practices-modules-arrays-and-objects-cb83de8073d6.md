# JavaScript 最佳实践—模块、数组和对象

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-modules-arrays-and-objects-cb83de8073d6>

![](img/72f612f39d6dd45ad6bd21c4a591da31.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的 JavaScript 代码。

在本文中，我们将探讨使用模块、数组和对象的最佳方式。

# 如果我们只有一个导出，那么我们应该使用默认导出

如果我们的模块只有一个导出，那么我们应该使用默认导出，而不是命名导出。

它更具可读性和可维护性。

例如，不写:

```
export const foo = () => {};
```

我们写道:

```
const foo = () => {}
export default foo;
```

# 将所有进口置于非进口声明之上

将所有的`imports`放在非导入语句之上，使它们更容易阅读。

例如，我们应该写:

```
import { foo } from "./foo";foo();
```

# 多行导入应该像多行数组和对象文字一样缩进

如果我们有大量的进口，我们不应该写:

```
import {foo, bar, baz, qux, longName} from 'foo';
```

相反，我们应该写:

```
import {
  foo,
  bar,
  baz,
  qux,
  longName
} from "foo";
```

# 不要在 import 语句中使用 Webpack Loader 语法

如果我们使用特定于 Webpack 的语法来导入东西，那么当我们试图切换到另一个模块加载器时就会有问题。

相反，我们应该使用标准语法来避免 Webpack 特定语法的问题。

而不是写:

```
import barSass from 'css!sass!bar.scss';
```

我们写道:

```
import barCss from 'bar.css';
```

# 不要在导入中添加 JavaScript 文件扩展名

我们不需要向导入添加 JavaScript 文件扩展名。

当我们改变扩展名时也会产生问题，

因此，我们不应该包括它。

例如，我们可以写:

```
import { bar } from './foo.js';
```

相反，我们写道:

```
import { bar } from './foo';
```

# 迭代器和生成器

迭代器和生成器是 ES6 新增的 JavaScript 特性，允许我们顺序返回项目。

# 不要使用迭代器来操作数组

我们应该使用数组方法来操作数组，而不是使用循环。

例如，不写:

```
let arr = [1, 2, 3];
let result = [];
for (const a of arr) {
  result.push(a ** a);
}
```

我们写道:

```
const arr = [1, 2, 3];
const result = arr.map(a => a ** a);
```

它更短，没有环。

另一个原因是我们不想改变数组条目。

例如，如果我们写:

```
let arr = [1, 2, 3];
for (let i = 0; i < arr.length; i++) {
  arr[i] = arr[i] ** arr[i];
}
```

然后，当我们遍历它时，我们改变`arr`的每个条目。

如果我们可以避免突变，那么我们应该这样做。

下面是一些我们可以用来操作数组的数组方法:

*   `map` —将条目从一个映射到另一个，并返回包含映射条目的数组
*   `every` —检查每个数组条目是否满足某些条件
*   `find` —返回满足某些条件的第一个条目
*   `findIndex` —返回满足某些条件的第一个条目的索引
*   `reduce` —将数组条目组合成一个值并返回
*   `some` —检查一些数组条目是否满足给定的条件

我们还可以用这些方法获得对象键和值:

*   `Object.keys` —获取对象自己的字符串键
*   `Object.values` —获取对象的自身值
*   `Object.entries` —获取自己的对象键值对

# 不要使用发电机

如果我们在构建中以 ES5 为目标，那么我们不应该使用生成器，因为它们不能很好地移植。

# 正确定义空间生成器

我们应该如下分隔函数定义:

```
function* foo() {
  // ...
}
```

# 对象属性

如果我们访问对象属性，需要注意一些事情。

![](img/5b141ac7ac6f99e6872eb60577f98a5a.png)

照片由[蒂姆·库珀](https://unsplash.com/@tcooper86?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 访问属性时使用点标记法

当我们访问作为有效 JavaScript 标识符的对象属性时，应该使用点符号。

例如，不写:

```
const bar = foo['bar'];
```

我们写道:

```
const bar = foo.bar;
```

# 结论

如果我们只导出一个模块的一个成员，我们应该有一个默认的导出。

我们应该使用数组方法来操作数组，而不是使用循环。

如果我们需要操作对象的键和值，我们可以用一些`Object`静态方法得到它们。

此外，尽可能使用点符号来访问对象属性。
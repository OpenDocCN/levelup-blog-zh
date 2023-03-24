# JavaScript 最佳实践—源文件和模块

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-source-files-and-modules-31ea33701433>

![](img/d35efe43dc38002563b022de09f15ef6.png)

照片由 [Rodion Kutsaev](https://unsplash.com/@frostroomhead?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的 JavaScript 代码。

在这篇文章中，我们将看看如何命名我们的文件和适当的文件编码。

此外，我们还会看到处理模块的正确方式。

# 文件名

文件名应该全部小写，可以包含下划线或破折号。

我们不应该在文件名中添加任何额外的标点符号。

文件名应该以`.js`结尾，这样我们就知道它是一个 JavaScript 文件。

例如，`foo.js`是一个很好的文件名。

# 文件编码

文件的编码应该是 UTF-8。这在所有平台上都是标准的，所以当我们在任何地方运行它们时都不会出现问题。

# 空白字符

ASCII 水平空白字符应该是源文件中唯一的字符。

其他空白字符应该被转义。

制表符不应该用于缩进，因为它们在不同的平台上可能有不同的解释。

但是，制表符可以自动转换为空格。

# 特殊转义序列

特殊转义序列应该有相应的数字转义。

例如，`\'`和`\x27`是一样的。

# 非 ASCII 字符

我们应该在代码文件中对非 ASCII 字符使用 Unicode 等价物。

# 源文件结构

我们可能希望在源文件中加强一些结构。

例如，我们可能有文档的 JSDoc 注释。

我们可以把它们放在文件夹里，这样在我们的项目结构中就有一个清晰的层次。

# ES 模块

现在大多数新的 JavaScript 项目都使用模块。

因此，我们应该注意一些关于模块的规则。

# 导入长度

`import`语句的长度应不超过 100 个字符，以免溢出屏幕。

这样，没有人需要水平滚动来阅读整行。

# 导入路径

我们应该使用`import`语句导入 ES 模块。

例如，我们可以写:

```
import './foo';
```

或者:

```
import * as foo from './foo.js';
```

或者:

```
import { name } from './foo.js';
```

导入文件时我们不需要扩展名。

# 多次导入同一文件

我们不应该在不同的`import`语句中导入同一个文件的多个成员。

例如，不写:

```
import { bar } from './foo';
import { baz } from './foo';
```

我们写道:

```
import { bar, baz } from './foo';
```

# 命名导入

我们可以用关键字`as`命名导入。

名字应该是骆驼案。

例如，我们写道:

```
import * as fooBar from './fooBar';
```

# 命名默认导入

我们也可以用 camelCase 命名默认导入。

例如，我们可以写:

```
import fooBar from './fooBar';
```

# 命名指定的导入

此外，我们可以更改命名导入的名称，以便我们可以使用我们想要使用的名称。

例如，我们可以写:

```
import { Cat as FatCat } from './animal';
```

对于构造函数导入或:

```
import { cat as fatCat } from './animal';
```

对于其他进口。

# 命名导出与默认导出

我们可以像导入一样命名和默认导出。

例如，我们可以通过编写以下内容来创建默认导出:

```
export class Foo { ... }
```

和一个命名导出:

```
export { Foo }
```

# 导出容器类和对象

我们不应该导出容器类和对象

因此，不要写以下内容:

```
export class Container {
  static foo() {
    return 1;
  }
}
```

我们写道:

```
export function foo() {
  return 1;
}

export const FOO = 1;
```

# 出口的可变性

不要导出可变的变量。

这意味着用`let`声明的任何东西都不应该被导出。

例如，不写:

```
export let foo = 1;
```

我们写道:

```
export const foo = 1;
```

# 从报表导出

我们应该将`export from`语句换行，使其保持在每行 100 个字符以内。

所以我们写道:

```
export * from './foo';
```

或者:

```
export { bar } from './another.js';
```

![](img/820677bf807607ceea3a3d715d60127e.png)

照片由[希亚姆](https://unsplash.com/@thezenoeffect?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# es 模块中的循环依赖

我们不应该直接或间接地在 ES6 模块之间创建循环依赖。

例如，我们不应该写:

`b.js`:

```
import './a';
```

并且:

`a.js`

```
import './b';
```

# 结论

我们在处理文件名和模块时应该小心。

文件名应以小写字母命名，并带有下划线或破折号，

我们应该使用模块，它们不应该有循环依赖。

代码文件应该是 UTF-8 编码的，以避免运行时出现问题。
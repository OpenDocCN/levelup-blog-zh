# TypeScript 最佳实践—命名空间、异常和类型定义

> 原文：<https://levelup.gitconnected.com/typescript-best-practices-namespaces-exceptions-and-type-definitions-131d85579fa3>

![](img/ae60ecc48d2506332f848f95bfbed4a3.png)

加布里埃尔·亨德森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

TypeScript 是一个简单易学的 JavaScript 扩展。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的类型脚本代码。

在本文中，我们将研究使用 TypeScript 编写代码时要遵循的最佳实践，包括禁止使用 TypeScript 模块和命名空间。

此外，在可选的链接表达式之后，我们不需要非空断言。如果我们创建和使用模块，我们应该使用 JavaScript 模块。我们应该抛出异常，而不是抛出文字。

而且，我们可能想用一种方式而不是两种方式来声明类型。

# 不要使用自定义 TypeScript 模块和命名空间

因为我们有 ES6 模块作为 JavaScript 的标准，所以我们不需要定制的 TypeScript 模块和名称空间来组织我们的代码。

相反，我们应该使用带有`import`和`export`的标准 JavaScript 模块。

例如，不写:

```
module foo {}
namespace foo {}
```

或者:

```
declare module foo {}
declare namespace foo {}
```

我们写道:

```
export default foo;
```

这将从一个模块中导出`foo`对象。

我们还可以导出模块的单个成员:

```
export foo;
export bar;
```

# 不要在可选链表达式后使用非空断言

我们不应该在可选链表达式后使用非空断言，因为它们与可选链表达式相反。

根据其性质，表达式可以返回`undefined`。

例如，不写:

```
foo?.bar!;
```

或者:

```
foo?.bar()!;
```

我们写道:

```
foo?.bar;
```

或者:

```
foo?.bar();
```

# 不要使用非空断言！后缀运算符

非空断言取消了严格的空检查模式的好处。

因此，我们可能要删除额外的`!`操作符。

例如，不写:

```
interface Foo {
  bar?: string;
}const includesBaz: boolean = foo.bar!.includes('qux');
```

相反，我们应该写:

```
interface Foo {
  bar?: string;
}const hasQux: boolean = foo.bar && foo.bar.includes('qux');
```

# 不要在类构造函数中使用参数属性

对于刚接触 TypeScript 的人来说，参数属性很容易混淆，所以我们可能希望停止使用它。

这是一种不太明确的声明和初始化类成员的方式。

例如，不写:

```
class Foo {
  constructor(readonly name: string) {}
}
```

我们写道:

```
class Foo {
  constructor(name: string) {}
}
```

# 不要使用 require()来导入模块

既然 ES6 模块是标准的，我们就不用再用`require`来导入 CommonJS 模块了。

因此，与其写:

```
const lib = require('lib');
```

我们写道:

```
import { foo } from 'lib';
```

# 不要给这个取别名

现在我们有了箭头函数，我们不需要将`this`设置为另一个变量来保持它的值。

例如，不写:

```
const self = this;

setTimeout(function() {
  self.foo();
});
```

我们应该写:

```
setTimeout(() => {
  this.foo();
});
```

# 不要将文字作为异常抛出

我们应该用`throw`而不是文字来抛出`Error`对象，因为它给了我们更多的信息，比如发生异常的行、堆栈跟踪和错误类型。

例如，不写:

```
throw 'error';
```

我们写道:

```
const err = new Error();
throw err;
```

或者:

```
class BadError extends Error {
  // ...
};
throw new BadError();
```

![](img/103d0bed1210c7bdd1fb1be23fa54a9b.png)

照片由 [Eiliv-Sonas Aceron](https://unsplash.com/@shootdelicious?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 不要使用类型别名

我们可能希望禁止使用类型别名。

类型别名可以作为其他类型的别名，这样我们就可以用更简单的名称来引用它们。

例如，我们可以写:

```
type Person = {
    firstName: string,
    lastName: string,
    age: number
};let person: Person;
```

而不是:

```
let person: {
    firstName: string,
    lastName: string,
    age: number
};
```

它还可以充当接口，就像前面的例子一样。

或者它也可以作为一个映射，让我们快速修改。

例如，我们可以写:

```
type ReadOnly<T> = { readonly [P in keyof T]: T[P] };type Person = {
  firstName: string;
  lastName: string;
  age: number;
};
```

对于它们表现得像接口的情况，也许我们只是想把它们转换成接口。

这样，我们得到了一种类型注释，而不是代码中的两种。

# 结论

如果我们的类型别名像接口一样使用，我们可能希望坚持使用接口来声明类型。

同样，现在我们有了箭头函数，我们不需要`this`的别名。

当我们引发异常时，我们应该抛出`Error`对象，而不是抛出文字。
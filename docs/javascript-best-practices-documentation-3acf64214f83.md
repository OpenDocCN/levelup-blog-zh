# JavaScript 最佳实践—文档

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-documentation-3acf64214f83>

![](img/fb4fe3b3f8f78d5a034c5428b5e33a51.png)

[王晓龙](https://unsplash.com/@runblue?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的 JavaScript 代码。

在本文中，我们将研究用 JSDoc 编写文档的最佳实践。

# 顶层/文件级注释

一个文件可能在注释中命名诸如版权声明、作者信息之类的东西。

如果不重复代码中的内容，我们也可以包括文件内容的描述。

# 班级评论

一个类可能有自己的注释，包括描述和参数。

例如，我们可以写:

```
/**
 * A class to store Fruit data
 */
class Fruit extends Food {
  /**
   * @param {string} name An argument for the name.
   * @param {Array<number>} dimensions List of numbers for the  
   *    dimensions.
   */
  constructor(name, dimensions) {
    // ...
  }
};
```

我们在类上面有一个类的描述。

在类内部，我们有一个注释块来解释构造函数中的项目。

每个参数都有自己的解释，说明它包含什么以及它们的类型。

这样，如果需要，我们可以使用程序来帮助我们创建文档。

# 枚举注释

我们可以为枚举写注释。

例如，我们可以写:

```
/**
 * Types of weight units.
 * @enum {string}
 */
const WeightType = {
  GRAM: 'gram',
  POUND: 'type',
};
```

我们为一个具有重量单位列表的枚举添加了一个注释。

注释有一个`@enum`标签来解释每个枚举值的类型。

在这个例子中，它们都是字符串。

# 方法和函数注释

像类和对象一样，我们可以给方法添加注释。

例如，我们可以写:

```
/** A class */
class FooClass extends BaseClass {
  /**
   * Operates on a FooClass instance and returns item.
   * @param {!FooClass} foo Some object.
   * @param {!OtherClass} other Another object.
   * @return {boolean} Whether something occurred.
   */
  doSomething(foo, other) { ... }

  /** @override */
  overriddenMethod(param) { ... }
}
```

在上面的代码中，我们在顶部有一个 clas 的注释。

然后我们有一个注释块来描述一个方法做什么。

此外，我们对每个参数可以容纳的内容以及它们的数据类型进行了详细说明。

此外，我们使用了`@override`标签来解释这个方法覆盖了父类的一个方法。

同样，我们可以有一些功能相似的东西。

例如，我们可以写:

```
/**
 * A function that makes an object.
 * @param {TYPE} arg
 * @return {!TYPE2}
 * @template TYPE
 */
function makeObject(arg) { ... }
```

我们有一个接受给定类型参数的函数，所以我们写一个注释来解释它。

此外，我们使用了`@return`标签来返回`TYPE2`的某个对象。

# 属性注释

此外，我们可以向属性添加注释。

例如，我们可以写:

```
/** A class. */
class AClass {
  /** @param {string=} arg */
  constructor(arg = 'something') {
    /** @const {string} */
    this.arg = arg;
  }
}
```

我们有一个类，它有一个带注释的构造函数。

我们为构造函数的参数添加了一个注释。

此外，我们还有另一个实例变量。

我们用`@const`标签表明它是常数。

# 键入注释

我们在`@param`、`@return`、`@this`和`@type`标签后添加数据类型注释。

此外，我们可以选择在`@const`、`@export`和任何可见性标签上添加它们。

![](img/910f5b7b7480cacd101065863486aae2.png)

布鲁克·拉克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 可空性

修饰符`!`和`?`分别表示不可为空和可为空。

另外，我们为原语添加了类型注释，包括`string`、`numbner`、`boolean`、`symbol`、`undefined`和`null`。

注释的文字类型包括函数:

```
{function(...): ...}
```

和对象:

```
{{foo: string...}}
```

引用类型是大写字母。

# 模板参数类型

我们应该在类型中指定参数。

例如，我们写道:

```
const /** !Array<string> */ names = [];
```

如果我们的数组是字符串数组。

# 结论

我们可以为参数、构造函数、类和对象属性添加注释。

比如它们的类型，如果适用的话，函数返回什么，等等。可以添加。

在文件的顶部，我们可以放一些信息，比如版权声明之类的。

也有表示可空性的符号。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)
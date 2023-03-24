# JavaScript 最佳实践—名称和注释

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-names-and-comments-9ab7e7487aaf>

![](img/e61eb513e5762e6ee6b3cde1e8046a21.png)

[Asnim Asnim](https://unsplash.com/@asnim19?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种简单易学的编程语言。编写运行并执行某些操作的程序很容易。然而，很难考虑所有的用例并编写健壮的 JavaScript 代码。

在本文中，我们将研究在 JavaScript 项目中命名事物的最佳实践。

我们应该看看 JSDoc 的正确用法。

# 文件和模块名称

用小写字母命名文件。

例如，我们可以将一个文件命名为`fooBar.js`。

# 类名

类名应该是大写的。例如，我们可以写:

```
class FooBar {
  //...
}
```

# 方法名称

冰毒的名字应该用小写字母写。

例如，我们应该写:

```
class FooBas {
  //...
  doSomething() {
    //...
  }
}
```

如果我们有 getters 和 setters，那么我们应该确保我们的名字反映了它们的作用。

例如，我们应该用像`getFoo`这样的名字来命名 getters。如果方法返回一个布尔值，我们也可以命名为`isFoo`或`hasFoo`。

如果我们有一只塞特犬，我们应该给它们起名叫`setFoo`。

# 枚举名称

枚举名称应该大写。

例如，我们可以通过编写以下代码创建一个对象来定义一个枚举:

```
const Unit = {
  METER: 'meter',
  FOOT: 'foot',
}
```

`Unit`是枚举名称。

此外，我们应该使用单数名词。

# 常量名称

常量名应该在 CONSTANT_CASE 中命名。

名称用大写字母表示，单词之间用下划线分隔。

常量有`const`声明，但并非所有模块局部`const`都是常量。

`const`值可以是可变的，只是它们不能被赋予新值。

# 本地别名

我们应该创建本地别名来提高可读性。

例如，如果我们想要引用具有深层嵌套属性的对象，我们可以通过编写以下内容来创建别名:

```
const baz = obj.foo.bar.baz;
```

现在我们可以只引用`baz`而不引用`obj.foo.bar.baz`。

它更短，更容易阅读。

别名必须是`const`，因此它们不能被重新分配给新值。

# 非常数字段名

字段名，不管是静态的还是其他的，都应该用大写字母书写。

例如，我们可以用像`foo`或`numFoo`这样的名字来表示非常数字段名。

# 局部变量名

局部变量名用 camelCase 编写，除非我们有常量。

函数作用域中的常量名也应该用 camelCase 编写。

即使变量包含构造函数，也应该使用 camelCase。

# JSDoc

我们可以编写 JSDoc 注释来记录我们的代码。

例如，我们可以写:

```
/**
 * Bunch of text,
 * and more text.
 * @param {number} num A number to do something to.
 */
function doSomething(num) { 
  //...
}
```

注释位于函数之上，它有一个`@param`注释，用于解释参数的含义。

我们可以在自己的行上使用`/**`和`*/`来扩展注释块。

从 JSDoc 中提取注释的工具有很多。

此外，还有根据注释执行代码验证和优化的工具。

# 降价

我们在 Markdown 中编写 JSDoc 注释。

我们在写评论时遵循 Markdown 语法。

例如，我们写道:

```
/**
 * Computes volume based on
 *
 *  - length
 *  - width
 *  - height
 */
```

例如，我们用`-`来表示一个列表条目。

# JSDoc 标签

我们可以在 JSDoc 中使用各种标签。

`@param`是我们上面看到的常用标签。

其他常见标签包括:

*   `@constructor` —将函数标记为构造函数
*   `@enum` —将对象标记为枚举
*   `@extends` —标记类继承结构
*   `@implements` —标记实现的接口
*   `@return` —指定函数的返回类型

我们可以在注释中添加更多的标签来记录我们的代码。

![](img/6b38b5f55d91013cf5bbd7cfc1b18bb0.png)

托马斯·塔克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 换行

换行的块标记应该缩进 4 步。

我们不应该水平对齐注释文本。

例如，我们可以写:

```
/** 
 * @param {string} very very very very long
 *     description
 * @return {number} very very very very longtoo long 
 *     description
 */
function bar(foo) {
  return 5;
};
```

# 结论

我们可以用 JSDoc 注释我们的代码，它是基于 Markdown 的。

长行要换行，换行要缩进。

此外，名称的大小写在 JavaScript 中相当标准。我们对大多数的名字都有驼色，对其他的名字有帕斯卡和大写。

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)
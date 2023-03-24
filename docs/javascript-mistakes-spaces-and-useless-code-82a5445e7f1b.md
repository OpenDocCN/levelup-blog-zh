# JavaScript 错误——空格和无用代码

> 原文：<https://levelup.gitconnected.com/javascript-mistakes-spaces-and-useless-code-82a5445e7f1b>

![](img/dc26426d3a8dfe556f7e2fcfe542fc98.png)

照片由 [NASA](https://unsplash.com/@nasa?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们看看如何消除可能破坏代码的无用字符。

# 不规则空白字符

有些空格字符并不是在所有地方都被认为是空格。因此，我们可能会遇到意外的令牌错误。

此外，它在现代浏览器中没有显示，这使得代码很难正确可视化。

行分隔符在 JSON 中也是无效字符，会导致更多的解析错误。

不规则字符的完整列表是这里的。

# 字符类语法中具有多个码位的字符

字符串中不允许有多个码位的字符，如`U+2747`。我们应该在字符串中使用由单个代码点组成的字符，如常规的字母数字字符和标点符号。

# 将全局对象属性作为函数调用

像`Math`、`JSON`和`Reflect`这样的一些全局对象属性不应该被称为函数，因为只有它们的方法应该被调用，而属性应该被访问。

例如，以下内容无效:

```
let math = Math();
let json = JSON();
```

使用上述对象的正确方法是访问它们的属性:

```
let pi = Math.PI;
let obj = JSON.parse('{}');
```

# 直接使用 Object.prototypes 内置

来自`Object.prototype`的一些方法，如`hasOwnProperty`、`isPrototypeOf`和`propertyIsEnumerable`，并不意味着被实例直接调用。

例如，以下是不正确的:

```
let bar = {};
let foo = {};
const hasBarProperty = foo.hasOwnProperty("bar");
const isPrototypeOfBar = foo.isPrototypeOf(bar);
const barIsEnumerable = foo.propertyIsEnumerable("bar");
```

它们的名称如下:

```
let bar = {};
let foo = {};
const hasBarProperty = Object.prototype.hasOwnProperty.call(foo, "bar");
const isPrototypeOfBar = Object.prototype.isPrototypeOf.call(foo, bar);
const barIsEnumerable = Object.prototype.propertyIsEnumerable(foo, "bar");
```

# 正则表达式中的额外空格

多重空白难以阅读。很难判断正则表达式中有多少空格。因此，我们应该使用单个空格，然后指定我们想要匹配的空格的数量。

例如，不写:

```
const re = /a   b/;
```

我们应该写:

```
const re = /a {3}b/;
```

# 从 Setters 返回值

Setters 的返回值被忽略，即使它在那里。因此，返回值在 setter 内部是没有用的。这也为错误创造了更多的机会。

这适用于`Object.create`、`Objecr.defineProperty`、`Object.defineProperties`和`Reflect.defineProperty`中的对象文字、类声明和表达式以及属性描述符。

例如，我们应该在下面的代码中返回值:

```
class Person {
  set firstName(value) {
    this._firstName = value;
    return value;
  }
}
```

或者:

```
const person = {
  set firstName(value) {
    this._firstName = value;
    return value;  
  }
}
```

或者:

```
const Person = class {
  set firstName(value) {
    this._firstName = value;
    return value;
  }
}
```

或者:

```
let person = {};
Object.defineProperty(person, "firstName", {
  set(value) {
    this._firstName = value;
    return false;
  }
});
```

相反，我们应该从上面的例子中删除`return`语句，如下所示:

```
class Person {
  set firstName(value) {
    this._firstName = value;
  }
}
```

或者:

```
const person = {
  set firstName(value) {
    this._firstName = value;
  }
}
```

或者:

```
const Person = class {
  set firstName(value) {
    this._firstName = value;
  }
}
```

或者:

```
let person = {};
Object.defineProperty(person, "firstName", {
  set(value) {
    this._firstName = value;
    return false;
  }
});
```

![](img/5750ba24cdcf569d665032febd186f12.png)

安德鲁·查尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 稀疏阵列

JavaScript 中允许数组中有空槽。只包含逗号的数组文本是有效的。然而，它们可能会让开发人员感到困惑。

例如，开发人员可能会对以下数组感到困惑:

```
const items = [, ];
const strs = ["foo", , "bars"];
```

相反，我们应该这样写:

```
const items = [];
const strs = ["foo", "bars", ];
```

我们可以有尾随逗号。

# 常规字符串中的模板文字占位符语法

常规字符串不应该有模板文字占位符，因为它们在常规字符串中不起作用，而且很容易被开发人员误认为是模板字符串。

例如，以下可能是错误:

```
"Hello ${name}!";
'Hello ${name}!';
"Sum: ${1 + 2}";
```

相反，我们应该写:

```
`Hello ${name}!`;
`Sum: ${1 + 2}`;
```

# 结论

有许多字符不应该出现在 JavaScript 代码中，尽管有些字符是允许的。代码中不应该出现不规则的空白字符。由多个代码点组成的字符不应该出现在 JavaScript 字符串中。

不应该直接调用全局对象。只有它们的属性才应该被访问。

正则表达式字符串和文字中有多余的空格，所以不应该包含它们。还有，setters 中的返回值是没有用的，所以不应该添加。

JavaScript 数组中允许有多个连续的逗号，但是它们会让开发人员感到困惑，所以应该避免使用。
# JavaScript 最佳实践—比较和旧结构

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-comparison-and-old-constructs-dbf68348fa9>

![](img/fee5952a2167881146de6385da7238a3.png)

Gabrielle Costa 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将研究编写 JavaScript 代码时的一些最佳实践，包括比较、访问器、对象和循环。

# ===而且！==都比==和好！=

`===`和`!==`省去了我们很多 JavaScript 类型强制的麻烦。因为使用了`==`和`!=`进行比较的算法。

使用`==`,我们会得到这样的结果:

*   `[] == false`
*   `[] == ![]`
*   `2 == "02"`

全部返回`true`。

在[https://www.ecma-international.org/ecma-262/5.1/#sec-11.9.3](https://www.ecma-international.org/ecma-262/5.1/#sec-11.9.3)为`==`和`!=`列出了很多我们必须知道的规则。

因此，为了避免记住所有这些并避免上面列出的奇怪结果，我们应该使用`===`和`!==`进行比较。

所以，我们应该避免编写下面的代码:

```
if (x == 1) {}
```

相反，写下:

```
if (x === 1) {}
```

其他好代码的例子包括:

```
a === b
foo === true
apple !== 1
value === undefined
typeof foo === 'undefined'
'foo' !== 'bar'
```

# 将对象文字和类中的访问器对分组

将同一个字段的 getters 和 setters 分组在一起是有意义的。它更容易阅读，我们不必在整个代码中寻找它们。

例如，不写:

```
const obj = {
  get a() {
    return this.val;
  }, b: 1, set a(val) {
    this.val = val;
  }
}
```

并且:

```
class Foo {
  get a() {
    return this.val;
  } b() {
    return 1;
  } set a(val) {
    this.val = val;
  }
}
```

我们应该改为写:

```
const obj = {
  get a() {
    return this.val;
  }, set a(val) {
    this.val = val;
  }, b: 1,
}
```

并且:

```
class Foo {
  get a() {
    return this.val;
  } set a(val) {
    this.val = val;
  } b() {
    return 1;
  }
}
```

# 在 for…in 循环中检查自己的属性

`for...in` loop 循环通过对象自身的属性以及原型链上的属性。

这将导致意外的项目在循环中循环。

为了避免这种情况，我们应该添加一个检查来查看我们是否在遍历一个对象自己的属性，这样我们就不会意外地遍历一个对象原型的属性。

因此，与其写:

```
for (const key in foo) {
  console.log(foo[key]);
}
```

我们应该改为编写以下代码:

```
for (const key in foo) {
  if (Object.prototype.hasOwnProperty.call(foo, key)) {
    console.log(foo[key]);
  }
}
```

下面的代码也不错:

```
for (const key in foo) {
  if ({}.hasOwnProperty.call(foo, key)) {
    console.log(foo[key]);
  }
}
```

在这两个例子中，我们用`call`方法调用`hasOwnPropterty`,将`hasOwnProperty`中的`this`值更改为`foo`,并将`key`作为第一个参数传递给`hasOwnProperty`,这样我们可以检查`key`是否在`foo`本身中。

# 每个文件不要有太多的类

每个文件有太多的类会增加阅读和导航的难度。结构也更差。

最好将每个代码文件限制为一个责任。例如，不是写入一个文件:

```
class Foo {}
class Bar {}
```

我们应该将它们分成两个文件:

`foo.js`:

```
class Foo {}
```

`bar.js`:

```
class Bar {}
```

![](img/df027afa68e62542e6cb9d614d89434d.png)

照片由[赛义德·艾哈迈德](https://unsplash.com/@syedabsarahmad?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 不允许使用警报

JavaScript 的`alert`、`confirm`和`prompt`函数作为 UI 元素很显眼，应该用模态、对话框或其他不那么显眼的东西来代替。

它还经常用于调试代码。因此，我们应该避免使用它们，除非我们真的用它们来提醒用户和询问问题。

即便如此，也应该谨慎使用。

# `Stop Using arguments.caller`和`arguments.callee`

`arguments.caller`和`arguments.callee`使得一些代码优化变得不可能。它们在严格模式下也是被禁止的。

它们在箭头函数中也不起作用，因为它们没有绑定到`arguments`。

因此，如果不需要绑定到`this`，我们应该使用箭头函数。如果我们需要使用传统的函数，那么我们不应该在函数中引用这两个属性。

所以:

```
function foo() {
  var callee = arguments.callee;
}
```

或者:

```
function foo() {
  var caller = arguments.caller;  
}
```

会是我们想要避免的坏代码。

另一种方法是直接引用调用者和被调用者函数。所以`arguments.callee`就是`foo`。

# 结论

我们应该使用`===`和`!==`进行比较，以避免`==`和`!=`比较运算符的混淆结果。

如果我们使用访问器，那么我们应该将相同的值访问器组合在一起。

此外，每个文件的最大类应该是有限的。越少越好。这将任何代码文件限制为一个责任。

应该尽可能少用警告、确认和弹出提示。

最后，我们不应该在传统函数中使用`arguments.caller`和`arguments.callee`。
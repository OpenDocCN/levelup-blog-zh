# JavaScript 错误—异步、类型检查和值

> 原文：<https://levelup.gitconnected.com/javascript-mistakes-async-type-checking-and-values-a10f9d0ce29a>

![](img/f5e548f94143600bb64b281d04b02e00.png)

由 [Alexandru Rotariu](https://unsplash.com/@rotalex?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 是一种非常宽容的语言。编写可以运行但有错误的代码很容易。

在本文中，我们将研究如何在运行多个承诺时避免竞态条件等等。

# 由于让步和等待，可能导致竞争条件的赋值

当使用承诺时，如果我们没有恰当地组合多个承诺，很容易产生竞争条件。

例如，如果我们有:

```
let total = 0;const getNum = async (num) => {
  return num;
}const add = async (num) => {
  total += await getNum(num);
}Promise.all([add(1), add(2)]).then(() => {
  console.log(total);
});
```

然后我们调用`add`异步函数，该函数从`getNum`承诺中获取结果，并将其添加到`total`中。

然后我们调用`Promise.all`，它并行运行代码。在此期间，分配给`total`的第一个值将被覆盖，因为`total`在每个承诺被解析之前仍被设置为初始值。

因此，我们将看到 2，而不是我们预期的 3。

我们可以通过将`add`中的解析值赋给一个变量，然后将它们添加到`total`中来修复这个错误，如下所示:

```
const add = async (num) => {
  const res = await getNum(num);
  total += res;
}
```

或者我们可以等待两者都解决，然后将结果相加:

```
const getNum = async (num) => {
  return num;
}const add = async (num) => {
  return await getNum(num);
}Promise.all([add(1), add(2)]).then((nums) => {
  const total = nums.reduce((a, b) => a + b, 0);
  console.log(total);
});
```

# 不使用 isNaN 检查 NaN

`NaN`是 JavaScript 中的一个特殊值。与`==`或`===`相比，它不等于它自己。例如，以下内容:

```
NaN === NaN
NaN == NaN
```

返回`false`和:

```
NaN !== NaN
NaN != NaN
```

返回`true`。

`NaN`检查与`indexOf`和`lastIndexOf`也不起作用:

```
const arr = [NaN];
console.log(arr.indexOf(NaN));
console.log(arr.lastIndexOf(NaN));
```

在上面的代码中，我们将从控制台日志中返回-1。

因此，我们应该用`isNaN`功能检查`NaN`，如下所示:

```
const foo = NaN;
if (isNaN(foo)){
  console.log('is NaN');
}
```

我们也可以使用`Object.is`方法来检查`NaN`，如下所示:

```
const foo = NaN;
if (Object.is(foo, NaN)) {
  console.log('is NaN');
}
```

要检查`NaN`是否在一个数组中，我们可以编写自己的函数来完成:

```
const arr = [NaN];
console.log(arr.some(a => isNaN(a)));
```

# 将表达式类型与无效字符串进行比较

写字符串很容易出现错别字。因此，我们应该检查我们用`typeof`检查的类型是否正确。

例如，下面的代码在字符串中有错别字，这将导致`typeof`不像我们预期的那样工作:

```
typeof foo === "strnig"
typeof foo == "undefimed"
typeof bar === "nunber"
typeof bar === "fucntion"
```

我们应该确保我们比较的字符串拼写正确，如下所示:

```
typeof foo === "string"
typeof foo == "undefined"
typeof bar === "number"
typeof bar === "function"
```

![](img/0d24bd59abea3a58822645e838a36bef.png)

Victor Grabarczyk 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 在对象和类中，Getters 和 Setters 应该在一起

没有相应的 getter，setter 就没什么用。例如，如果我们有:

```
const obj = {
  set foo(val) {
    this._foo = val
  }
}
```

那么就没有办法得到`foo`的值，即使我们设置了值，当我们引用`obj.foo`时也会得到`undefined`。

因此，我们应该给对象添加一个相应的 getter 来返回值。例如，我们应该这样写:

```
const obj = {
  get foo() {
    return this._foo;
  },
  set foo(val) {
    this._foo = val
  }
}
```

要用带有 getters 和 setters 的`defineProperty`定义一个新属性，我们可以编写如下代码:

```
const obj = {};
Object.defineProperty(obj, 'foo', {
  get() {
    return this._foo;
  },
  set(val) {
    this._foo = val
  }
});
```

然后如果我们给`foo`赋值，就可以得到`foo`属性的值。同样，我们可以对类做同样的事情，如下所示:

```
class Obj {
  get foo() {
    return this._foo;
  } set foo(val) {
    this._foo = val;
  }
}const obj = new Obj();
obj.foo = 1;
```

这与 object 示例的工作方式相同，只是我们必须先用`new`实例化它。

# 结论

我们不应该用像`+=`这样的操作进行赋值，用`await`或`yield`作为右操作数，因为值将被覆盖，而不是与现有值组合。

此外，我们应该使用`isNaN`来检查`NaN`而不是使用`===`或`==`操作员来检查。

当使用`typeof`时，我们应该检查字符串中的错别字。

没有 getters 的 Setters 也不是很有用，因为我们不能得到我们设置的值。
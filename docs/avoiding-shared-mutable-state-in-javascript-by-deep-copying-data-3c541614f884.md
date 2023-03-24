# 通过深度复制数据避免 JavaScript 中的共享可变状态

> 原文：<https://levelup.gitconnected.com/avoiding-shared-mutable-state-in-javascript-by-deep-copying-data-3c541614f884>

![](img/91cb501492f84b34fdacb4c14a2cd271.png)

约根·哈兰在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

与其他编程语言一样，JavaScript 将数据存储在变量中，这些变量可以随时更改。这可能会导致一个问题，因为我们可能会意外地改变正在共享的变量。让许多代码共享同一个可变状态是很难追踪的。这使得调试和阅读代码变得困难。

例如，如果我们在不同的函数中改变同一个数组如下:

```
let arr = [];
const foo = () => {
  arr = [1, 2, 3];
}const bar = () => {
  arr = [4, 5, 6];
}
```

然后`arr`的值根据最后调用的是`foo`还是`bar`而变化。如果`foo`被调用，那么`arr`就是`[1, 2, 3]`。另一方面，如果调用了`bar`，那么`arr`就是`[4, 5, 6]`。

这是一个问题，因为代码越复杂，函数调用就越多。如果许多函数都这样做，那么跟踪值就很困难，调试也很混乱。

此外，很难理解调用带有这些副作用的函数时逻辑是如何流动的。

在本文中，我们将研究如何通过制作数据的深层副本来进行数据的深层复制，以防止程序中共享状态的突变。此外，我们还研究了防止从类方法中暴露的数据突变的方法。

# 深层拷贝

## 嵌套扩展

我们可以在对象的每一层使用 spread 操作符来手动进行深度复制。

例如，假设我们有以下对象:

```
const obj = {
  foo: {
    bar: 1,
    baz: 2
  },
  a: 3
}
```

我们可以对其进行如下深度复制:

```
const objCopy = {
  foo: {
    ...obj.foo
  },
  a: obj.a
};
```

正如我们所看到的，当我们有更多的级别和属性时，这将是一个问题。然而，我们确实得到了原始对象的深层副本。

# 通过 JSON.stringify 和 JSON.parse 进行深度复制

我们可以调用`JSON.stringify`返回一个对象的字符串，然后调用 stringify 上的`JSON.parse`将其返回到原始形式。

这适用于 JSON 支持的所有属性和值，这意味着像符号和函数这样的实体被排除在外。

例如，我们可以写:

```
const obj = {
  foo: {
    bar: 1,
    baz: 2
  },
  a: 3
}const objCopy = JSON.parse(JSON.stringify(obj));
```

`objCopy`将是`obj`的深度复制。这是因为如果一个字符串是不可变的，并且`JSON.parse`返回一个新的解析过的 stringified 对象的副本。

# 复制类的实例

我们可以比复制对象更容易地复制一个类的实例。我们可以编写一个返回对象实例的`clone`方法来完成这个任务。

例如，我们可以编写以下代码来创建一个从另一个类继承的类:

```
class Person {
  constructor(name) {
    this.name = name;
  }
}class Employee extends Person {
  constructor(name, employeeCode) {
    super(name);
    this.employeeCode = employeeCode;
  } clone() {
    return new Employee(this.name, this.employeeCode);
  }
}
```

然后我们调用`clone`来创建一个新的复制对象:

```
const employee = new Employee('Joe', 1);
const employeeClone = employee.clone();console.log(employee.__proto__);
console.log(employeeClone.__proto__);
```

在`Employee`类的`clone`方法中，我们返回了一个`Employee`的新实例。

然后在前 2 个`console.log`输出中，我们看到`employee`和`employeeClone`具有相同的原型。

![](img/28fd67e4964c85f2826f4387012771c6.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[bùI Thanh TM](https://unsplash.com/@tamtit24?utm_source=medium&utm_medium=referral)拍摄的照片

# 为什么复制有助于防止共享状态突变？

复制防止了共享状态的突变，因为我们在试图改变共享数据之前复制了它。这很方便，因为它可以防止我们意外地更改共享数据。

如果我们不对共享数据进行更改，那么跟踪它就很容易。那么我们就不必担心代码的不同部分会意外地改变相同的状态。

# 在进行更改之前复制公开的内部类数据

在对内部类数据进行修改之前，我们应该复制一份，然后在方法中返回它。这使得我们无法直接更改类数据。

例如，如果我们有下面的类:

```
class NumArray {
  constructor() {
    this._arr = [];
  } add(num) {
    this._arr.push(num);
  } getParts() {
    return this._arr;
  } toString() {
    return this._arr.join('');
  }
}
```

我们通过编写以下代码以各种方式调用这些方法:

```
const numArr = new NumArray();
numArr.add(1);
numArr.add(2);
console.log(numArr.toString());
numArr.getParts().length = 0;
console.log(numArr.toString());
```

我们可以看到第一个`console.log`的值是`'1,2'`，第二个记录了一个空字符串。

这是因为我们改变了由`getParts`方法公开的数组的`length`属性。我们将其设置为 0，因此数组被清空。

为了防止这种情况，我们可以在`getParts`中返回数组之前复制它。我们改为编写以下内容:

```
class NumArray {
  constructor() {
    this._arr = [];
  } add(num) {
    this._arr.push(num);
  } getParts() {
    return [...this._arr];
  } toString() {
    return this._arr.join(',');
  }
}
```

然后，当我们对`NumArray`类的方法进行同样的调用时，如下所示:

```
const numArr = new NumArray();
numArr.add(1);
numArr.add(2);
console.log(numArr.toString());
numArr.getParts().length = 0;
console.log(numArr.toString());
```

我们在两个`console.log`输出中都得到`'1,2'`。

我们可以通过各种方式对数据进行深度复制。首先，我们可以通过在每个级别上重复使用 spread 操作符来手动复制数据。

同样，我们可以使用`JSON.stringify`和`JSON.parse`来复制可以包含在 JSON 中的数据。

对于类实例，我们可以创建一个方法，通过用相同的数据返回一个新的数据实例来返回类的实例。这保留了数据和继承结构。

最后，我们可以防止类方法中公开的数据被修改，方法是在方法中返回数据之前对其进行复制。
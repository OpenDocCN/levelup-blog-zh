# JavaScript 最佳实践——以新换旧

> 原文：<https://levelup.gitconnected.com/javascript-best-practice-replacing-old-with-new-dd7a6c8228fb>

![](img/14a944276d00fe29e0aa4b574506ece2.png)

[巴西托普诺](https://unsplash.com/@b620?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

像任何其他编程语言一样，JavaScript 有自己的最佳实践列表，使程序更容易阅读和维护。JavaScript 有很多棘手的部分，我们可以遵循一些最佳实践来改进我们的代码。

自从 ES6 被引入以来，新的结构取代旧的结构是有原因的。它更短、更清晰、更容易理解。

在本文中，我们将看看哪些旧的构造可以被新的替换，包括用`async`和`await`替换`then`，用`Map` s 替换字典对象，用扩展操作符替换`apply`，以及用类语法替换函数构造器。

# 用 Async / Await 替换 Then

当我们链接承诺时，我们通常使用`then`方法，然后在传递给`then`的回调函数中返回另一个承诺。

这意味着我们有类似这样的代码:

```
Promise.resolve(1)
  .then((val) => {
    console.log(val);
    return Promise.resolve(2);
  })
  .then((val) => {
    console.log(val);
    return Promise.resolve(3);
  })
  .then((val) => {
    console.log(val);
  })
```

引入了`async`和`await`语法，这只是重复调用`then`方法的一种速记。我们可以这样写上面的代码:

```
(async () => {
  const val1 = await Promise.resolve(1);
  console.log(val1);
  const val2 = await Promise.resolve(2);
  console.log(val2);
  const val3 = await Promise.resolve(3);
  console.log(val3);
})();
```

它们都输出 1、2 和 3，但是第二段代码要短得多。它是如此清晰，以至于没有理由回到旧的语法。

此外，我们可以通过使用`for-await-of`循环来循环遍历承诺并一个接一个地运行它们。我们可以通过重写如下代码来循环执行上面的承诺:

```
(async () => {
  const promises = [
    Promise.resolve(1),
    Promise.resolve(2),
    Promise.resolve(3),
  ] for await (let p of promises) {
    const val = await p;
    console.log(val);
  }
})();
```

像上面这样的代码可以非常方便地循环遍历许多承诺或即时创建的承诺，这在前面的例子中是做不到的。

# 用地图替换字典对象

ES6 还引入了`Map`构造函数，它让我们无需使用带有字符串键的 JavaScript 对象就可以创建散列图或字典。

也更好，因为它们有自己的方法来获取和操作条目。

例如，不要写:

```
const dict = {
  'foo': 1,
  'bar': 2
}
```

我们写道:

```
const dict = new Map([
  ['foo', 1],
  ['bar', 2]
])
```

然后我们可以通过使用`get`方法得到一个条目，如下所示:

```
console.log(dict.get('foo'));
```

我们可以使用`set`方法通过条目的键来设置现有条目的值:

```
dict.set('foo', 2);
```

同样，我们可以用`has`方法检查给定键的条目是否存在:

```
dict.has('baz');
```

还有`keys`和`entries`方法分别获取地图的所有键和所有条目。

例如，我们可以写:

```
console.log(dict.keys());
```

用`Map`的键获得一个迭代器对象。这意味着我们可以用`for...of`循环遍历它们，或者用 spread 操作符将其转换成一个数组。

类似地，`entries`方法返回一个迭代器对象，其中包含了`Map`中的所有条目，每个条目都是一个`[key, value]`数组。

还有一个`value`方法，用`Map`中的所有值获取一个迭代器对象。

我们也可以使用其他原始值作为键。如果我们使用对象，当我们查找它们时，我们不能取回值，因为查找是用返回`false`的`===`操作符完成的，这是两个没有相同引用的对象，即使它们有相同的内容。

这些方法在常规对象中不可用。此外，如果我们使用`for...in`循环，我们可能会意外地获得或修改对象原型的属性，而不是它自己的属性。

因此，不再有很多理由使用常规对象作为字典。

![](img/188847940ffc66fd35c117f2afbe8be2.png)

[David Clode](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 用跨页替换应用

如果我们不想在函数中改变`this`的值，就没有理由在函数中使用`apply`方法。

如果我们只想调用一个有许多参数的函数，我们可以在为我们的参数传入数组时使用 spread 运算符，如下所示:

```
const arr = [1, 2, 3, 4, 5];
const add = (...args) => args.reduce((a, b) => a + b, 0);
console.log(add(...arr));
```

我们所要做的就是在我们的数组上使用 spread 操作符，也就是最后一行中的 3 点操作符，然后数组将被分隔成一个逗号分隔的参数列表。

# 用类替换构造函数

ES6 的另一个伟大特性是构造函数的类语法。简单的语法糖使得构造函数看起来像一个类。

它的好处是让继承变得容易。

例如，如果我们想从一个构造函数中继承，我们必须像这样写:

```
function Person(name) {
  this.name = name;
}function Employee(name, employeeCode) {
  Person.call(this, name);
  Employee.prototype.constructor = Person;
  this.employeeCode = employeeCode;
}const employee = new Employee('Joe', 1);
console.log(employee)
```

这种语法来自基于类的面向对象语言，如 Java，看起来很奇怪。

然而，对于使用其他语言多于 JavaScript 的开发人员来说，类语法使事情看起来更加熟悉。我们可以将上面的代码改写为:

```
class Person {
  constructor(name) {
    this.name = name;
  }
}class Employee extends Person {
  constructor(name, employeecode) {
    super(name);
    this.employeecode = employeecode;
  }
}const employee = new Employee('Joe', 1);
console.log(employee)
```

代码做的事情和上面的一样。然而，更清楚的是我们从哪里继承，因为我们有`extends`关键字来指示我们从哪里继承。

有了构造函数，我们就要担心在`call`方法的第一个参数中`this`的值，以及我们传递给后续参数`call`的内容。

有了类语法，我们就不用担心这个了。如果我们忘记像下面这样调用`super`:

```
class Person {
  constructor(name) {
    this.name = name;
  }
}class Employee extends Person {
  constructor(name, employeecode) {   
    this.employeecode = employeecode;
  }
}const employee = new Employee('Joe', 1);
console.log(employee)
```

我们将得到错误“未捕获的引用错误:在访问“this”或从派生构造函数返回之前，必须调用派生类中的`super`构造函数。”

少了一个犯错的机会。

如果我们像在`Employee`构造函数中一样省略`Person.call`，我们不会得到任何错误，因为浏览器不知道我们想要`Employee`从`Person`继承。

此外，当我们记录`employee`的原型时，我们得到`employee`的原型是`Person`，正如类语法所预期的。

然而，除非我们把容易忘记的`Employee.prototype.constructor = Person;`放进去，否则我们不会得到那个。

# 结论

`async`和`await`和`for-await-of`是新的构造，使得链接承诺更加清晰。因为它，用它们比用`then`好得多。

`for-await-of`还让我们循环通过动态生成的承诺，这是我们单独用`then`或`async`和`await`无法做到的。

对于散列和字典来说，s 比普通对象好得多，因为它有自己的方法来操作和获取条目。此外，我们可能会意外地访问普通对象的原型的属性。

如果我们不想改变函数内部的`this`的值，我们可以用 spread 操作符替换调用带有参数数组的函数的`apply`方法，它做同样的事情。

最后，构造函数的类语法比原始函数语法好得多，因为我们可以从父类继承，这比设置构造函数的原型构造函数更容易。
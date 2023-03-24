# JavaScript 基础—对象

> 原文：<https://levelup.gitconnected.com/javascript-basics-objects-3e94605131e0>

![](img/6583b09cf41445bed7030366250eb11b.png)

照片由[周延前](https://unsplash.com/@chn008?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 是世界上最流行的编程语言之一。为了有效地使用它，我们必须了解它的基本知识。

在本文中，我们将研究 JavaScript 对象。

# 地图

映射是一组键值对。

例如，我们可以写:

```
const ages = {
  james: 10,
  alex: 20
}
```

然后我们用名字作为关键字，用年龄作为相应的属性。

对象属性可以是字符串，也可以是符号，但大多数时候，我们用的是字符串。

我们不能在一个对象中使用对象作为键。因此，JavaScript 有一个`Map`构造函数，比使用对象进行映射更加灵活。

例如，我们可以写:

```
const ages = new Map();
ages.set("james", 10);
ages.set("alex", 20);
```

`set`方法是`Map`对象的一部分。我们用它向地图添加条目。

如果我们想把一个对象当作一个地图，我们可以使用`Object.keys`来返回一个对象自己的字符串键。

另一种方法是使用`in`操作符或`hasOwnProperty`。

例如，我们可以通过编写以下代码来使用`in`操作符:

```
'james' in ages
```

或者:

```
ages.hasOwnProperty('james')
```

# 多态性

我们可以覆盖继承的方法，让它们做我们想做的事情。

例如，我们可以覆盖`Object.prototype.toString`方法，让它做一些更有用的事情。

我们可以写:

```
function Dog(name) {
  this.name = name;
}Dog.prototype.toString = function() {
  return this.name;
}
```

现在我们在一个`Dog`实例上调用`toString`:

```
const dog = new Dog('joe');
console.log(dog.toString());
```

我们得到`'joe'`日志。

如果我们没有覆盖，那么我们得到`[object Object]`记录。

# 标志

多个接口可能对不同的事情使用相同的属性名。

然而，我们不能用字符串键这样做，因为它们会互相覆盖。

JavaScript 通过添加 symbol 原语类型增加了具有相同名称的对象键的能力。

没有两个符号实例是相同的。

例如，如果我们有:

```
const sym = Symbol("name");
const sym2 = Symbol("name");
```

如果我们比较它们:

```
console.log(sym === sym2);
```

我们得到`false`，即使它们的内容是相同的。

我们可以将它们用作对象标识符。

例如，我们可以写:

```
const speak = Symbol("speak");
const dog = {
  [speak]() {
    console.log('hi');
  }
}
```

那么我们只能通过引用同一个`speak`符号来调用它:

```
dog[speak]();
```

# 迭代程序

我们可以用 for-of 循环遍历任何可迭代对象。

一个 iterable 对象必须有`Symbol.iterator`方法。

该方法应该是迭代器。

迭代器有一个`next`方法，该方法返回一个具有`value`属性和`done`布尔属性的对象。

例如，我们可以写:

```
class Id {
  constructor(max) {
    this.id = 0;
    this.max = max;
  }next() {
    this.id++;
    if (this.max === this.id) {
      return {
        done: true
      }
    }
    return {
      done: false,
      value: this.id
    }
  }
}class IdIterator {
  [Symbol.iterator]() {
    return new Id(10);
  }
}for (const id of new IdIterator(10)) {
  console.log(id);
}
```

我们有`Id`类，它有`next`方法。当`this.id`等于`this.max`时，它返回一个`done`设置为`true`的对象。

否则，它返回下一个`this.id`值。

然后我们有`IdIterator`类，它有`Symbol.iterator`方法，我们在那里返回迭代器。

![](img/a483eea080b0c2694ff9f58fd8937071.png)

[保罗·格伦](https://unsplash.com/@pgreen1983?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# Getters 和 Setters

JavaScript 类可以有 getters 来返回一些值作为属性。

例如，我们可以写:

```
const random = {
  get num() {
    return Math.floor(Math.random() * 100);
  }
};
```

我们有一个由关键字`get`指示的`num` getter。

它返回一个介于 0 和 100 之间的随机数。

要访问返回值，我们可以写:

```
console.log(random.num);
```

那么`random.num`将是一个随机数。

同样，我们可以使用`set`关键字创建一个 setter:

```
class Cat {
  get name() {
    return this._name
  } set name(name) {
    this._name = `cat ${name}`;
  }
}
```

我们有一个带有 setter 的`Cat`类。

关键字`set`表明我们有了`name` setter 方法。

我们将名称与`name`一起设置为`'cat'`。

现在我们可以写:

```
const cat = new Cat();
cat.name = 'mary';
console.log(cat.name);
```

我们得到了:

```
'cat mary'
```

已登录控制台。

# 结论

映射让我们在 JavaScript 代码中存储键值对。

迭代器对于创建我们可以用 for-of 循环来循环的对象很有用。

Getters 和 setters 让我们设置值。
# JavaScript 最佳实践—函数和变异

> 原文：<https://levelup.gitconnected.com/javascript-best-practices-functions-and-mutations-43d76e280cfe>

![](img/8b7bc3610201f5b446b9f4d9a8faf3ae.png)

Ashim D'Silva 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

像任何类型的应用程序一样，JavaScript 应用程序也必须写得很好。否则，我们以后会遇到各种各样的问题。

在本文中，我们将研究一些使用箭头函数的最佳实践。此外，我们看看为什么和如何避免突变。

# 不要使用 Getters 和 Setters

因为 getters 和 setters 会带来副作用，所以如果我们想创建易于使用的函数，就不应该使用它们。

例如，我们不应该写:

```
const person = {
  name: 'james',
  get age() {
    return this._age;
  },
  set age(n) {
    if (n < 0) {
      this._age = 0;
    } else if (n > 200) {
      this._age = 200;
    } else {
      this._age = n;
    }
  }
};
```

还有，我们应该叫`__defineGetter__`或者`__defineSetter__`:

```
person.__defineGetter__('name', () => {
  return this.name || 'james';
});
```

或者:

```
person.__defineSetter__('name', (name) => {
  this.name = name.trim();
});
```

相反，我们创建纯函数:

```
const restrictAge = (n, min, max) => {
  if (n <= min) {
    return min;
  }
  if (n >= max) {
    return max;
  }
  return n;
}const setAge = (age, person) => {
  return Object.assign({}, person, { age: restrictAge(age, 0, 120 )});
}
```

这样，我们就有了所有的纯函数，并且它比我们的 setter 实现更加通用，因为我们可以改变`min`和`max`。

# 减少循环的使用

JavaScript 代码中的大多数循环都可以用数组方法重写。

这是因为已经有很多像`map`、`filter`和`reduce`这样的方法可以让我们以更短的方式处理数组。

因此，与其写:

```
for (const a of arr) {
  result.push(a * 10);
}
```

我们可以写:

```
const result = arr.map(a => a  * 10);
```

使用`map`比 for-of 循环容易得多。

# 不要将变量作为第一个参数使用 Object.assign

`Object.assign`对第一个参数进行变异。

因此，为了在不改变现有数据的情况下使用它，我们应该传入一个空对象作为第一个参数。

而不是写:

```
const a = { foo: 1, bar: 2 };
const b = { baz: 3 };Object.assign(a, b);
```

我们写道:

```
const c = Object.assign({}, a, b);
```

# 减少变异数组方法的使用

因为我们有 spread 操作符和可选的数组方法，所以我们可以避免在代码中使用变异的数组方法。

例如，不要写:

```
arr.pop();
```

我们可以写:

```
const arr = arr.slice(0, arr.length - 1);
```

唯一比较难替换的是`sort`。在这种情况下，我们可以用 spread 操作符复制它，然后在该数组上调用`sort`。

所以我们可以写:

```
const copy = [...arr];
copy.sort();
```

我们可以考虑使用`filter`或`slice`来代替`splice`。

如果我们想用给定的索引删除一个元素，我们可以写:

```
const removed = arr.filter((a, i) => i !== index);
```

然后我们移除索引`index`处的项，并将其赋给一个新变量。

# 减少变异运算符的使用

很容易不小心变异数据。

所以我们可能希望避免使用变异操作符。

例如，我们可能希望避免使用`+=`、`/=`、`-=`、`%=`或`*=`操作符。

相反，我们可以使用非变异数组方法或者给一个新变量赋值。

我们可以写:

```
const b = a + 10;
```

而不是:

```
a += 10;
```

如果我们必须通过重复添加来更新变量，我们可以使用`reduce`:

```
const total = arr.reduce((total, a) => total + a, 0);
```

# 在小心 null 或 undefined 之前

我们应该始终小心使用`null`或`undefined`值，因为它们是许多误差的来源。

因此，在使用它们之前，我们应该确保变量或属性不是`null`或`undefined`。

为了检查两者，我们可以写:

```
if (val === null || val === undefined){
  //...
}
```

或者:

```
if (val === null || typeof val === 'undefined'){
  //...
}
```

![](img/1f2908103d6ca2d6edd9af1c6aff41d2.png)

照片由[阿玛纳斯·塔德](https://unsplash.com/@amarnathtade?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们应该避免改变数据，这样我们就不会不小心改变它们。

测试不改变数据的函数也更容易。

还有，要小心`null`或者`undefined`。
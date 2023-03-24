# 通过实现 Lodash 方法—对象来学习 JavaScript

> 原文：<https://levelup.gitconnected.com/learning-javascript-by-implementing-lodash-methods-objects-d00d33beca90>

![](img/4db26e9c9c93b0c17a957be980f42359.png)

[大卫·克洛德](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Lodash 是一个非常有用的工具库，让我们可以轻松地处理对象和数组。

然而，现在 JavaScript 标准库正在赶上诸如 Lodash 之类的库，我们可以用简单的方法实现许多功能。

在本文中，我们将研究一些可以用普通 JavaScript 实现的 Lodash 对象方法。

# `forInRight`

`forInRight`方法以相反的顺序遍历对象的自有和继承属性。在每次迭代中，它用`value`、`key`和`object`参数调用`iteratee`函数，直到它返回`false`。然后循环将结束。

`value`是被循环的对象属性的值。`key`是被循环的属性的键，`object`是被遍历属性的对象。

我们可以自己实现，通过使用`for...in`循环按照它返回的顺序获取所有的键，并将它们放入一个数组中。

然后我们可以通过调用数组实例的`reverse`方法来反转这些键，并使用`for...of`循环来遍历这些键:

```
const forInRight = (object, iteratee) => {
  const keys = [];
  for (const key in object) {
    keys.push(key);
  }
  const reversedKeys = keys.reverse();
  for (const key of reversedKeys) {
    const result = iteratee(object[key], key, object);
    if (result === false) {
      break;
    }
  }
}
```

在上面的代码中，我们首先使用`for...in`循环向前遍历一个对象的键，并将它们全部放入`keys`数组中。

在`for...in`循环中，我们将键推入`keys`数组。然后我们通过调用`reverse`来反转`keys`数组中的键，然后将反转后的数组赋值为`reversedKeys`的值。

然后我们可以使用`for...of`循环遍历`reversedKeys`，然后用`object[key]`、`key`和`object`调用`iteratee`。我们将`iteratee`的返回值赋给`result`。

当`result`为`false`时，我们打破循环。

现在当我们用`Foo`构造函数调用它时，如下所示:

```
function Foo() {
  this.a = 1;
  this.b = 2;
}Foo.prototype.c = 3;forInRight(new Foo(), (value, key) => console.log(key));
```

我们得到的结果是，我们按顺序将`c`、`b`和`a`作为记录值返回。

# findKey

Lodash `findKey`查找对象的第一个属性，其中`predicate`返回`true`。

我们可以通过遍历一个对象的项目并调用`predicate`函数来检查给定的属性值是否与`predicate`返回的条件相匹配来实现。

例如，我们可以这样实现:

```
const findKey = (object, predicate) => {
  for (const key of Object.keys(object)) {
    if (predicate(object[key])) {
      return key
    }
  }
}
```

在上面的代码中，我们使用了`Object.keys`来获取`object`自己的密钥。然后，我们用`for...of`循环遍历这些键。

在循环内部，我们用`object`的值调用`predicate`来查看它是否满足`preicate`函数中定义的条件。

那么当我们运行`findKey`功能时如下:

```
const users = {
  'foo': {
    'age': 15,
    'active': true
  },
  'bar': {
    'age': 30,
    'active': false
  },
  'baz': {
    'age': 1,
    'active': true
  }
};const result = findKey(users, o => o.age < 40);
```

我们有一个带有多个键的`users`对象和一个带有`age`和`active`属性作为值的对象。

然后我们用它调用了`findKey`，并将`predicate`函数设置为`o => o.age < 40`。

然后我们得到`'foo'`是`result`的值。

![](img/388c0744c18c6cf297f682919063d564.png)

由[paweczerwi ski](https://unsplash.com/@pawel_czerwinski?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# `findLastKey`

Lodash 的`findLastKey`功能与`findKey`类似，除了按键以相反的顺序循环。

要实现它，我们可以通过调用由`Object.keys`返回的 keys 数组上的`reverse`方法来改变我们的`findKey`实现。

我们可以这样做:

```
const findLastKey = (object, predicate) => {
  for (const key of Object.keys(object).reverse()) {
    if (predicate(object[key])) {
      return key
    }
  }
}
```

然后我们调用我们的`findLastKey`函数如下:

```
const users = {
  'foo': {
    'age': 15,
    'active': true
  },
  'bar': {
    'age': 30,
    'active': false
  },
  'baz': {
    'age': 1,
    'active': true
  }
};const result = findLastKey(users, o => o.age < 40);
```

我们得到`'baz'`作为`result`的值，而不是`'foo'`,因为我们反向循环了密钥。

# 结论

通过用`for...in`循环遍历自己的和继承的，然后将它们放入数组，可以实现`forInRight`方法。

然后我们可以用`for...of`循环遍历它们，并在上面调用`iteratee`函数。

`findKey`和`findKeyRight`功能可以通过循环按键，然后调用上面的`predicate`功能来实现。
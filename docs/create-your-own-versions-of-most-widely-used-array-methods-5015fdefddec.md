# 创建您自己版本的最广泛使用的数组方法

> 原文：<https://levelup.gitconnected.com/create-your-own-versions-of-most-widely-used-array-methods-5015fdefddec>

## Javascript 面试中用来测试你的知识的常见面试问题

![](img/e17d64f389fdbf5ed947db205d086db8.png)

格伦·卡斯滕斯-彼得斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

今天我们将看到如何实现我们自己版本的流行数组方法，如`filter`、`reduce,`、`forEach`、`map`等。

这是常见的面试问题，测试你对 Javascript、原型、函数调用方法的了解。

如果你想了解最有用的数组方法，那么点击这里查看我以前的文章

## 让我们从一些基础开始

在 Javascript 中，一切都是对象。`Array`是一个对象，`Date`是一个对象，`String`是一个对象，以此类推。

`Object`位于对象层次结构的顶层，所有其他类型都从这里继承属性和方法。

如果我们有这样一个物体

```
const obj = { name: 'David', age: 30 };
```

然后，要向对象添加任何方法，我们需要将其添加到原型中。

```
const obj = { name: 'David', age: 30 };Object.prototype.display = function() {
 console.log(`My name is ${this.name} and I'm ${this.age} years old.`);
}obj.display(); // My name is David and I'm 30 years old.
```

如果我们有一个数组:

```
const array = ["sunday","monday","tuesday","wednesday"];
```

然后，我们可以向其中添加我们自己版本的自定义打印方法。

```
const array = ["sunday","monday","tuesday","wednesday"];Array.prototype.print = function() {
  console.log(this); // Here, **this** will point to the object used to call this print function
}

array.print(); // ["sunday", "monday", "tuesday", "wednesday"]
```

如果是字符串，我们也可以把每个元素转换成大写

```
const array = ["sunday","monday","tuesday","wednesday"];

Array.prototype.print = function() {

  const converted = this.map(function(value) {
   if(typeof value === "string")    {
    return value.toUpperCase();
   } else {
    return value;
   }
  });
  return converted;
}

console.log(array.print()); // ["SUNDAY", "MONDAY", "TUESDAY", "WEDNESDAY"]

const numbers = [10, 20, 30, 40];
console.log(numbers.print()); // [10, 20, 30, 40]
```

所以我们可以使用这种技术来创建我们自己版本的数组方法。

大多数允许循环的内置数组方法具有以下语法

```
Array.prototype.method(callback(element[, index[, array]])[, thisArg])
```

在哪里

1.  ***元素*** *是数组*的元素
2.  ***索引*** *是数组*中元素的索引
3.  ***数组*** *是调用该方法的原始数组。*

**函数调用方法:**

我们创建的所有内置和自定义函数都继承了一个`call`方法。

`call`方法允许我们通过改变其上下文来调用任何函数，上下文是调用方法的第一个参数，函数的参数在调用方法的第一个参数后以逗号分隔值的形式传递。

```
const display = function (a, b) {
  console.log('values passed', a, b);
};display(1, 2); // values passed 1 2
display.call(this, 1, 2); // values passed 1 2
```

现在，我们熟悉了基础知识，让我们开始编写我们自己的数组方法的实现。

## 数组 forEach 方法

```
Array.prototype.customForEach = function (callback) {
  for (let i = 0; i < this.length; i++) {
    callback(this[i], i, this);
  }
};const arr = [1, 2, 3, 4, 5];
arr.customForEach(function (value, index, array) {
  console.log(value);
}); // 1 2 3 4 5
```

## 阵列映射方法

```
Array.prototype.customMap = function (callback) {
  const arr = [];
  for (let i = 0; i < this.length; i++) {
    arr.push(callback(this[i], i, this));
  }
  return arr;
};const arr = [1, 2, 3, 4, 5];
console.log(
  arr.customMap(function (value, index, array) {
    return value * 2;
  })
); // [2, 4, 6, 8, 10]
```

## 阵列过滤方法

```
Array.prototype.customFilter = function (callback) {
  const arr = [];
  for (let i = 0; i < this.length; i++) {
    if (callback(this[i], i, this)) {
      arr.push(this[i]);
    }
  }
  return arr;
};const arr = [1, 2, 3, 4, 5];
const element = 3;
console.log(
  arr.customFilter(function (value, index, array) {
    return value !== element;
  })
); // [1, 2, 4, 5]
```

## 数组简化方法

```
Array.prototype.customReduce = function (callback, initialValue) {
  let accumulator = initialValue && initialValue;
  for (let i = 0; i < this.length; i++) {
    if (accumulator) {
      accumulator = callback.call(undefined, accumulator, this[i], i, this);
    } else {
      accumulator = this[i];
    }
  }
  return accumulator;
};const arr = [1, 2, 3, 4, 5];
console.log(
  arr.customReduce(function (acc, value, index, array) {
    return acc + value;
  })
); // 15console.log(
  arr.customReduce(function (acc, value, index, array) {
    return acc + value;
  }, 5)
); // 20
```

## **阵列每一个方法**

```
Array.prototype.customEvery = function (callback) {
  for (let i = 0; i < this.length; i++) {
    if (!callback(this[i], i, this)) {
      return false;
    }
  }
  return true;
};arr = [1, 2, 3, 4, 5];
console.log(
  arr.customEvery(function (value, index, array) {
    return value > 0;
  })
); // truearr = [1, 2, -3, 4, 5];
console.log(
  arr.customEvery(function (value, index, array) {
    return value > 0;
  })
); // false
```

## 数组一些方法

```
Array.prototype.customeSome = function (callback) {
  for (let i = 0; i < this.length; i++) {
    if (callback(this[i], i, this)) {
      return true;
    }
  }
  return false;
};arr = [0, 2, 3, 4, 5];
console.log(
  arr.customeSome(function (value, index, array) {
    return value > 0;
  })
); // truearr = [-4, -5, -3];
console.log(
  arr.customeSome(function (value, index, array) {
    return value > 0;
  })
); // false
```

## 数组查找方法

```
Array.prototype.customFind = function (callback) {
  for (let i = 0; i < this.length; i++) {
    if (callback(this[i], i, this)) {
      return this[i];
    }
  }
};const arr = [
  { name: 'David', age: 20 },
  { name: 'Mike', age: 50 }
];console.log(
  arr.customFind(function (element, index, array) {
    return element.name === 'Mike';
  })
); // { name: 'Mike', age: 50 }
```

## 数组 findIndex 方法

```
Array.prototype.customFindIndex = function (callback) {
  for (let i = 0; i < this.length; i++) {
    if (callback(this[i], i, this)) {
      return i;
    }
  }
  return -1;
};const arr = [0, 2, 3, 4, 5];
console.log(
  arr.customFindIndex(function (value, index, array) {
    return value === 4;
  })
); // 3
```

> 注意:这些并不是数组方法的实际实现，只是为了向您展示我们如何实现同样的功能来为面试做准备。因此，这些自定义方法可能无法处理所有情况，如数组中的空值和未定义的值。

今天到此为止。我希望你学到了新东西。

**别忘了订阅我的每周简讯，里面有惊人的技巧、诀窍和文章，直接在你的收件箱** [**这里。**](https://yogeshchavan.dev/)

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)
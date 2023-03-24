# 以函数方式操作 JavaScript 数组

> 原文：<https://levelup.gitconnected.com/manipulate-javascript-arrays-the-functional-way-690fef7f4275>

![](img/d52111c4e024e27d05467103ab267565.png)

[Oleg Danylenko](https://unsplash.com/@alezhano?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 RFPhoto

函数式编程是一种编程范式，它声明我们创建计算作为函数的评估，并避免改变状态和可变数据。

为了以函数的方式编写程序，我们使用纯函数和不可变的数据结构。

在 JavaScript 中，我们可以通过使用其内置的数组方法轻松地将函数原则应用于数组。

在本文中，我们将研究让我们以函数方式操纵它们的数组方法。

# 过滤项目

我们可以使用`filter`方法返回一个数组，该数组包含来自原始数组的条目，该数组的条件是`filter`方法的回调函数返回。

例如，如果我们有以下数组:

```
const store = [{
    name: "Apple",
    price: 1
  },
  {
    name: "Banana",
    price: 2
  },
  {
    name: "Grape",
    price: 1.2
  }
];
```

我们可以得到`price`小于 2 的项目如下:

```
const cheapItems = store.filter(s => s.price < 2);
```

然后我们得到`cheapItems`的如下结果:

```
[
  {
    "name": "Apple",
    "price": 1
  },
  {
    "name": "Grape",
    "price": 1.2
  }
]
```

使用`filter`方法符合函数范式，因为函数是纯函数，给定相同的数组和相同的条件，我们总是得到相同的返回结果。

它也不会改变它所调用的数组的现有元素，这意味着我们不会意外地改变原始数组的任何内容。

# 映射数组对象

`map`用于将一个数组的条目映射到其他东西。它经常被用来转换和变换数据。使用`map`符合函数式编程范式，因为它是一个纯函数，对于给定的输入给出相同的输出，并且不改变原始数组。

只要我们传递给`map`函数来组合值的函数是纯的，`map`方法也应该是纯的，因为它只是调用这个回调函数并将更新后的值返回到一个新的数组中。

例如，给定以下数组:

```
const volumesInLiters = [{
    name: "Milk",
    volumeInLiters: 1
  },
  {
    name: "Apple Juice",
    volumeInLiters: 2
  },
  {
    name: "Orange Joice",
    volumeInLiters: 1.2
  }
];
```

我们可以将`volumeInQuarts`字段添加到对象的每个条目中，并通过编写以下内容以夸脱为单位将体积设置为每个条目的值:

```
const volumesInQuarts = volumesInLiters.map(v => {
  v = {
    ...v,
    volumeInQuarts: v.volumeInLiters * 1.057
  };
  return v;
})
```

对于每个条目，我们转换`v.volumeInLiters * 1.057`并将其设置为`volumeInQuarts`。

然后我们得到:

```
[
  {
    "name": "Milk",
    "volumeInLiters": 1,
    "volumeInQuarts": 1.057
  },
  {
    "name": "Apple Juice",
    "volumeInLiters": 2,
    "volumeInQuarts": 2.114
  },
  {
    "name": "Orange Joice",
    "volumeInLiters": 1.2,
    "volumeInQuarts": 1.2684
  }
]
```

![](img/a6d4d6ed5c222450be85ccb49c38d1d1.png)

伊莎贝拉·艾奇逊在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 使用 Reduce 合并数组条目的值

像`filter`和`map`，`reduce`也符合函数范式，因为我们用它来收集数组的条目，并按照我们指定的方式返回一个值。

只要我们传递给`reduce`函数来组合值的函数是纯的，`reduce`方法也应该是纯的。该函数只是调用我们传递给`reduce`的回调函数来计算组合值。

例如，给定以下数组:

```
const store = [{
    name: "Apple",
    price: 1
  },
  {
    name: "Banana",
    price: 2
  },
  {
    name: "Grape",
    price: 1.2
  }
];
```

我们可以编写以下代码来获得所有项目的总价:

```
const totalPrice = store.map(s => s.price).reduce((subtotal, b) => subtotal + b, 0);
```

我们必须使用`map`将`store`数组中的所有条目映射到价格数字。然后我们可以使用`reduce`方法将新值添加到`subtotal`。

那么我们应该为`totalPrice`得到`4.2`，这是`store`中 3 个商品所有价格的总和。

`reduce`的第二个参数是缩减的起始值，或者将值合并为一。

# 结论

`filter`、`map`和`reduce`数组方法是以函数方式操作数组的主要方法。它们都返回值，并且不改变被调用的数组。这使得无意中更改这些值变得很困难。

`filter`返回一个数组，该数组满足我们在传入的回调中返回的条件。

`map`返回一个新数组，该数组将原始数组的值以我们指定的方式映射到新值。

`reduce`通过使用一个回调函数将数组条目的值合并成一个值，我们传递该函数来进行合并并返回合并后的值。

如果我们将纯函数作为回调传递给它们，那么这些函数就是纯函数，因为它们只是调用我们传递的函数来进行操作。
# JavaScript 中的克隆传奇第 3 部分:JavaScript 中的深度复制

> 原文：<https://levelup.gitconnected.com/clone-saga-in-javascript-part-3-deep-copying-in-javascript-fd45bc2a31ad>

![O](img/709408cfc9acccb3f7e693966c5f5992.png)  O 你的 JavaScript 克隆传奇的最终部分已经到来！在这个系列中，我们探讨了用 JavaScript 复制对象的主题，这是一个由三部分组成的系列。在第一篇文章[中，我们讨论了 JavaScript](https://pandaquests.medium.com/clone-saga-in-javascript-part-1-different-ways-of-how-to-copy-objects-11f625fa156a) 中存在哪种复制。在之前的文章中，我们将深入浅出的复制。在这篇文章中，我们将研究深度复制。系好你的安全带。越来越深了。

这只是我们写的许多关于 JavaScript 或软件开发的文章中的一篇。我们试图每天发表一篇文章——即使是在节假日。关注或订阅我们，这样你就不会错过任何一个。

![](img/9b05295d810f87a8a9c67f084175cf9f.png)

菲尔·肖在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 JavaScript 中，深层副本是对象的副本，它使用原始对象的所有属性和对象创建新对象，包括任何嵌套对象及其属性。在 JavaScript 中有不同的方法可以实现这一点。

*   使用 JSON.parse()和 JSON.stringify()方法:

```
function deepCopy(obj) {
 return JSON.parse(JSON.stringify(obj));
}
const original = { a: 1, b: { c: 3 } };
const copy = deepCopy(original);
```

这通过使用 JSON.stringify()将原始对象序列化为 JSON 字符串，然后使用 JSON.parse()将 JSON 字符串解析回对象来创建新对象。这个方法只适用于可序列化为 JSON 的对象，不包括函数、未定义的和其他一些数据类型。还有一些其他的限制。在本文中阅读更多关于这种技术的局限性的内容。所以，这不是最优解。

*   使用递归函数:

```
function deepCopy(obj) {
  if (typeof obj !== 'object' || obj === null) {
    return obj;
  }
  const copy = Array.isArray(obj) ? [] : {};
  for (const key in obj) {
    if (obj.hasOwnProperty(key)) {
      copy[key] = deepCopy(obj[key]);
    }
  }
  return copy;
}
const original = { a: 1, b: { c: 3 } };
const copy = deepCopy(original);
```

这将创建一个新对象，并使用检查每个属性的值的类型的函数递归地将原始对象的属性复制到该对象中。如果该值是一个对象，则该函数调用自身来创建该对象的新副本。此方法适用于任何对象，包括具有函数、未定义和其他数据类型的对象。

*   或者，您可以使用类似 lodash 或下划线的库，它们提供了 _。可用于创建对象深层副本的 cloneDeep()函数:

```
const _ = require('lodash');
const original = { a: 1, b: { c: 3 } };
const copy = _.cloneDeep(original);
```

此方法适用于任何对象，包括具有函数、未定义和其他数据类型的对象。

以下是用 JavaScript 创建对象深层副本的几种方法:

*   使用 Object.getOwnPropertyNames()和 Object.defineProperties()方法:

```
function deepCopy(obj) {
  const copy = {};
  const propNames = Object.getOwnPropertyNames(obj);
  Object.defineProperties(copy, propNames.map(name => ({
    [name]: Object.getOwnPropertyDescriptor(obj, name)
  })));
  for (const name of propNames) {
    if (typeof obj[name] === 'object' && obj[name] !== null) {
      copy[name] = deepCopy(obj[name]);
    }
  }
  return copy;
}
const original = { a: 1, b: { c: 3 } };
const copy = deepCopy(original);
```

这将创建一个新的对象，并使用 Object.getOwnPropertyNames()方法获取对象自己的属性名数组，使用 Object.defineProperties()方法定义新对象的属性，将原始对象自己的属性复制到该对象中。然后，它使用一个循环来迭代数组，如果属性值是一个对象，则递归调用 deepCopy()函数来创建该对象的新副本。

*   使用 Reflect.ownKeys()和 reflect . getownpropertydescriptor()方法，如下所示:

```
function deepCopy(obj) {
  const copy = {};
  const propKeys = Reflect.ownKeys(obj);
  for (const propKey of propKeys) {
    copy[propKey] = obj[propKey];
  }
  return Reflect.ownKeys(obj).reduce((acc, key) => {
  if (typeof obj[key] === 'object' && obj[key] !== null) {
    acc[key] = deepCopy(obj[key]);
  }
  return Reflect.defineProperty(acc, key, Reflect.getOwnPropertyDescriptor(obj, key));
 }, copy);
}
const original = { a: 1, b: { c: 3 } };
const copy = deepCopy(original);
```

这将创建一个新的对象，并使用 Reflect.ownKeys()方法将原始对象的属性复制到该对象中，以获得该对象自己的属性键数组和一个循环来迭代该数组并将属性复制到新对象中。如果属性值是一个对象，它递归地调用 deepCopy()函数来创建对象的新副本。它还使用 reflect . getownpropertydescriptor()和 Reflect.defineProperty()方法将原始对象自身属性的属性描述符复制到新对象中。

此方法适用于任何对象，包括具有函数、未定义和其他数据类型的对象。如果您需要创建对象的深层副本并保留原始对象自身属性的属性描述符，这是一个不错的选择。

*   使用 Reflect.ownKeys()方法获取对象自己的属性键数组，使用 Object.create()方法创建新对象并定义其上的属性:

```
function deepCopy(obj) {
  const copy = Object.create(Object.getPrototypeOf(obj));
  Reflect.ownKeys(obj).forEach(key => {
    if (typeof obj[key] === 'object' && obj[key] !== null) {
      copy[key] = deepCopy(obj[key]);
    } else {
      Reflect.defineProperty(copy, key, Reflect.getOwnPropertyDescriptor(obj, key));
    }
  });
  return copy;
}
const original = { a: 1, b: { c: 3 } };
const copy = deepCopy(original);
```

这将使用 Object.create()方法创建一个新对象，并将其原型设置为原始对象的原型。然后，它使用 Reflect.ownKeys()方法获取对象自己的属性键数组，并使用 Reflect.defineProperty()方法循环遍历该数组并定义新对象的属性。如果属性值是一个对象，它递归地调用 deepCopy()函数来创建对象的新副本。

此方法也适用于任何对象，并保留原始对象自身属性的属性描述符。

*   使用 structuredClone()

```
const original = { a: 1, b: { c: 3 } };
const copy = structuredClone(original);
```

这可能是最简单的方法，因为它是 JavaScript 的内置函数。它使用[结构化克隆算法](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Structured_clone_algorithm)来深度复制对象。在用 JavaScript 克隆对象时，这是我最喜欢的方法:)然而，这是相当新的方法。仅从 2022 年 9 月和 Node 17 开始提供。因此，如果您使用的是旧系统，structuredClone()可能不可用。

![](img/8cc2bdbd0385c621d36651253412f4c5.png)

Phewww。我们谈了很多。难怪这是一个三部分系列。如果您喜欢这个系列，请关注或订阅我们，这样您就不会错过我们关于 JavaScript 或软件开发的任何精彩内容。愿原力与你同在。
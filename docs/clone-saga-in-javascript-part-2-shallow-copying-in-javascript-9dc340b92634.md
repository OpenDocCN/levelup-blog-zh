# JavaScript 中的克隆传奇第 2 部分:JavaScript 中的浅层复制

> 原文：<https://levelup.gitconnected.com/clone-saga-in-javascript-part-2-shallow-copying-in-javascript-9dc340b92634>

![T](img/97e826c971e9cf5664ff0bfa07877590.png)  T 他的是 JavaScript 克隆传奇的第二部分。在[上一篇文章中，我们探讨了 JavaScript 中存在什么样的复制](https://pandaquests.medium.com/clone-saga-in-javascript-part-1-different-ways-of-how-to-copy-objects-11f625fa156a)。在这篇文章中，我们将深入浅出的复制。在本系列的下一篇文章中，我们将深入研究深度复制。

这只是我们写的许多关于 JavaScript 或软件开发的文章中的一篇。我们试图每天发表一篇文章——即使是在节假日。关注或订阅我们，这样你就不会错过任何一个。

![](img/a3be51a5b572f5542d63d206616597c8.png)

克劳福德·乔利在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 JavaScript 中，浅表副本是一个对象的副本，它创建一个与原始对象具有相同属性的新对象，但不复制原始对象引用的对象。查看上一篇文章，了解什么是 JavaScript 中的浅层复制。

以下是在 JavaScript 中创建对象的浅层副本的几种方法:

*   使用 Object.assign()方法:

```
const original = { a: 1, b: { c: 3 } };
const copy = Object.assign({}, original);
```

这将创建一个新对象，并将原始对象的属性复制到其中。

*   使用扩展运算符(…)

```
const original = { a: 1, b: { c: 3 } };
const copy = { …original };
```

这将创建一个新对象，并将原始对象的属性复制到其中，就像 Object.assign()一样。

*   使用 Object.create()方法:

```
const original = { a: 1, b: { c: 3 } };
const copy = Object.create(Object.getPrototypeOf(original), Object.getOwnPropertyDescriptors(original));
```

这将创建一个与原始对象具有相同原型的新对象，并将自己的属性及其描述符从原始对象复制到新对象中。

*   使用循环:

```
function shallowCopy(obj) {
  const copy = {};
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      copy[key] = obj[key];
    }
 }
 return copy;
}
const original = { a: 1, b: { c: 3 } };
const copy = shallowCopy(original);
```

这将创建一个新对象，并使用一个循环将原始对象的自身属性复制到其中。

*   使用 Object.keys()和 Array.prototype.reduce()方法:

```
function shallowCopy(obj) {
  return Object.keys(obj).reduce((acc, key) => {
    acc[key] = obj[key];
    return acc;
  }, {});
}
const original = { a: 1, b: { c: 3 } };
const copy = shallowCopy(original);
```

这将创建一个新的对象，并使用 Object.keys()方法获取对象自己的属性名数组，使用 Array.prototype.reduce()方法迭代数组并将属性复制到新对象中，从而将原始对象自己的属性复制到该对象中。

*   使用 for…in 循环:

```
function shallowCopy(obj) {
  const copy = {};
  for (const key in obj) {
    copy[key] = obj[key];
  }
  return copy;
}
const original = { a: 1, b: { c: 3 } };
const copy = shallowCopy(original);
```

这将创建一个新对象，并使用 for…in 循环将原始对象的自身属性复制到其中。请注意，for…in 循环还将迭代继承的属性，因此您可能希望添加一个检查，以便只复制自己的属性，如下所示:

```
function shallowCopy(obj) {
  const copy = {};
  for (const key in obj) {
    if (obj.hasOwnProperty(key)) {
      copy[key] = obj[key];
    }
  }
  return copy;
}
```

*   使用 Object.entries()和 Object.fromEntries()方法:

```
function shallowCopy(obj) {
  return Object.fromEntries(Object.entries(obj));
}
const original = { a: 1, b: { c: 3 } };
const copy = shallowCopy(original);
```

通过使用 Object.entries()获取对象自身的可枚举属性[key，value]对的数组，然后使用 Object.fromEntries()从该数组创建一个对象，从而创建一个新的对象。

*   使用 Object.getOwnPropertyNames()和 Object.defineProperties()方法:

```
function shallowCopy(obj) {
  const copy = {};
  const propNames = Object.getOwnPropertyNames(obj);
  Object.defineProperties(copy, propNames.map(name => ({
    [name]: Object.getOwnPropertyDescriptor(obj, name)
  })));
  return copy;
}
const original = { a: 1, b: { c: 3 } };
const copy = shallowCopy(original);
```

这将创建一个新的对象，并使用 Object.getOwnPropertyNames()方法获取对象自己的属性名数组，使用 Object.defineProperties()方法定义新对象的属性，将原始对象自己的属性复制到该对象中。

*   使用 Object.getOwnPropertyNames()和循环:

```
function shallowCopy(obj) {
  const copy = {};
  const propNames = Object.getOwnPropertyNames(obj);
  for (const propName of propNames) {
    copy[propName] = obj[propName];
  }
  return copy;
}
const original = { a: 1, b: { c: 3 } };
const copy = shallowCopy(original);
```

这将创建一个新的对象，并使用 Object.getOwnPropertyNames()方法将原始对象的自身属性复制到该对象中，以获取该对象自身属性名称的数组和一个循环来迭代该数组并将属性复制到新对象中。

*   使用 Object.getOwnPropertySymbols()和 object . getownpropertydescriptors()方法:

```
function shallowCopy(obj) {
  const copy = {};
  const propSymbols = Object.getOwnPropertySymbols(obj);
  const propDescriptors = Object.getOwnPropertyDescriptors(obj);
  for (const propSymbol of propSymbols) {
    copy[propSymbol] = obj[propSymbol];
  }
  return Object.defineProperties(copy, propDescriptors);
}
const original = { a: 1, b: { c: 3 } };
const copy = shallowCopy(original);
```

这将创建一个新的对象，并使用 Object.getOwnPropertySymbols()方法将原始对象自己的符号属性复制到该对象中，以获取该对象自己的符号属性的数组和一个循环，以迭代该数组并将属性复制到新对象中。它还使用 object . getownpropertydescriptors()方法和 Object.defineProperties()方法将原始对象自己的数据属性复制到新对象中。

*   使用 Reflect.ownKeys()和 reflect . getownpropertydescriptor()方法:

```
function shallowCopy(obj) {
  const copy = {};
  const propKeys = Reflect.ownKeys(obj);
  for (const propKey of propKeys) {
    copy[propKey] = obj[propKey];
  }
  return Reflect.ownKeys(obj).reduce((acc, key) => {
    return Reflect.defineProperty(acc, key, Reflect.getOwnPropertyDescriptor(obj, key));
  }, copy);
}
const original = { a: 1, b: { c: 3 } };
const copy = shallowCopy(original);
```

这将创建一个新的对象，并使用 Reflect.ownKeys()方法将原始对象的属性复制到该对象中，以获得该对象自己的属性键数组和一个循环来迭代该数组并将属性复制到新对象中。它还使用 reflect . getownpropertydescriptor()和 Reflect.defineProperty()方法将原始对象自身属性的属性描述符复制到新对象中。

*   使用 Object.entries()和 Object.defineProperties()方法:

```
function shallowCopy(obj) {
  const copy = {};
  Object.defineProperties(copy, Object.entries(obj).map(([key, value]) => ({
    [key]: {
      value,
      writable: true,
      enumerable: true,
      configurable: true
    }
   })));
   return copy;
}
const original = { a: 1, b: { c: 3 } };
const copy = shallowCopy(original);
```

这将创建一个新对象，并使用 Object.entries()方法将原始对象自己的属性复制到该对象中，以获得对象自己的可枚举属性[key，value]对的数组，并使用 Object.defineProperties()方法定义新对象的属性。它将属性描述符设置为默认值 writable: true、enumerate:true 和 configurable: true。

*   使用 Object.keys()和 Object.defineProperties()方法:

```
function shallowCopy(obj) {
  const copy = {};
  Object.defineProperties(copy, Object.keys(obj).map(key => ({
    [key]: {
      value: obj[key],
      writable: true,
      enumerable: true,
      configurable: true
    }
  })));
  return copy;
}
const original = { a: 1, b: { c: 3 } };
const copy = shallowCopy(original);
```

这将创建一个新的对象，并使用 Object.keys()方法获取该对象自己的可枚举属性名称数组，使用 Object.defineProperties()方法定义新对象的属性，将原始对象自己的属性复制到该对象中。它将属性描述符设置为默认值 writable: true、enumerate:true 和 configurable: true。

![](img/750133d9577bd1e07bcae69c6dc25e89.png)

所有这些方法都创建对象的浅表副本，这意味着如果原始对象包含对其他对象的引用，副本仍将包含对与原始对象相同的对象的引用。如果您想创建一个对象的深层副本，这将创建原始对象引用的所有对象的新副本，请查看我们的下一篇文章。敬请关注。

如果你喜欢这个系列，请关注或订阅我们，这样你就不会错过 JavaScript 克隆传奇的下一部分。愿原力与你同在。
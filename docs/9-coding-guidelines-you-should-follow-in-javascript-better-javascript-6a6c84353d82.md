# JavaScript |更好的 JavaScript 应遵循的 9 条编码准则

> 原文：<https://levelup.gitconnected.com/9-coding-guidelines-you-should-follow-in-javascript-better-javascript-6a6c84353d82>

![](img/f6e480e279b6964b207c419b9dc2c9b0.png)

[JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

JavaScript 世界中有很多可用的工具和插件。你可能以前听说过多重编码指南或林挺规则。在这篇文章中，我列出了一些我个人最喜欢的，并附有适当的代码解释。这些编码指南将帮助您的应用程序在 JavaScript 代码中更具可伸缩性和可维护性。

## 主题:

1.  使用`enum`
2.  使用可选的链接/Elvis 运算符
3.  对象速记的使用
4.  hasOwnProperty 的使用
5.  扩展运算符的使用
6.  使用来自的**数组**
7.  使用析构来提取值
8.  使用“of”和“in”运算符迭代对象
9.  可选参数的使用

## **使用枚举**

你可能没听说过什么神奇的数字。它说我们不应该创造随机数，没有有意义的名字的字符串。

```
// TypeScript Sampleenum HTTP_RESPONSE_STATUS {
  OK = 200,
  CREATED,
  MOVED = 301,
  BAD_REQUEST = 400,
  UNAUTHORIZED,
  FORBIDDEN = 403,
}console.log(HTTP_RESPONSE_STATUS);/**
 * 
{
  '200': 'OK',
  '201': 'CREATED',
  '301': 'MOVED',
  '400': 'BAD_REQUEST',
  '401': 'UNAUTHORIZED',
  '403': 'FORBIDDEN',
  OK: 200,
  CREATED: 201,
  MOVED: 301,
  BAD_REQUEST: 400,
  UNAUTHORIZED: 401,
  FORBIDDEN: 403
}
*/export const createUsers = async (user) => {
  try {
    const newUser = await db.createUser(user);
    return { data: newUser, status: HTTP_RESPONSE_STATUS.OK };
  } catch (error) {
    return { error: error.message, status: HTTP_RESPONSE_STATUS.BAD_REQUEST };
  }
};
```

上面的例子是用 TypeScript 写的。**“枚举”**是 TypeScript 的一级成员。但是，`enum`在**普通 JavaScript** 中是没有的。您可以使用键值对象来实现相同的功能。

```
const HTTP_RESPONSE_STATUS = Object.entries({
  OK: 200,
  CREATED: 201,
  MOVED: 301,
  BAD_REQUEST: 400,
  UNAUTHORIZED: 401,
  FORBIDDEN: 403,
}).reduce((m, [key, value]) => {
  m[key] = value;
  m[value] = key;
  return m;
}, {});console.log(HTTP_RESPONSE_STATUS); 
```

你可能还注意到我们有交叉值和键的对象。也就是说，对于每个**键-值**，都有一个值-键映射。这是值-键映射，对交叉引用检查很有用。

*你可以在*[*implement-enum-in-vanilla-JavaScript-using-proxy-class*](https://medium.com/swlh/implement-enum-in-vanilla-javascript-using-proxy-class-391edd1dd608)*中阅读更多关于 enum 实现的内容。*

## **使用可选的链接/Elvis 操作符**

猫王运营商被定义为`**?.**`。Elvis 运算符对于在属性链的深层读取属性值非常有用。

```
const user = {
  name: "Deepak",
  address: {
    street: "30 Lorem Address",
    pin: 67090,
  },
  fullAddress() {
    return `${this.address.street}, ${this.address.pin}`;
  },
};// Case 1: Extract Property
console.log(user?.address?.street);
/// 30 Lorem Address// Case 2: Extract Property and call function
console.log(user.fullAddress?.());
// 30 Lorem Address, 67090
```

在案例 1 中，您从**地址**中提取**街道**属性。如果没有定义 prop 地址，首先会返回`**undefined**`。否则它将导航并获取**地址**中**街道**的值。

在例 2 中，还可以寻找函数`user.fullAddress**?.()**`的属性并在验证后调用。

**真实生活用例**:在 SSR(服务器端渲染)中从 navigator 安全地获取语言

```
const currentLanguage = window?.navigator?.language
```

## **使用物体速记**

**ES6** 引入了许多漂亮清晰的语法，与**类**和**对象**一起工作。如果您使用过 ES6 类语法和函数，您可以对对象使用相同的类函数符号。*查看以下示例。*

```
// Bag
const lukeSkywalker = "Luke Skywalker";
const atom = {
  value: 1,
  lukeSkywalker: lukeSkywalker, **// Too verbose**
  addValue: function (value) {
    return atom.value + value;
  },
};
```

上面的例子可以写成

```
// Good 
const atom = {
  value: 1,
  lukeSkywalker, **// Short hand for Property**
  addValue(value) { **// Short hand for function**
    return atom.value + value;
  },
};
```

速记使得用值给对象分配属性变得容易得多。而且有了`()`语法，就不需要显式定义函数关键字了。

## hasOwnProperty 的使用

您可能已经在对象的键迭代中看到了对 **hasOwnProperty** 的检查。基本上，每当我们对所有属性进行循环时，我们都希望确保不包含一些原型属性。为此，我们可以在对象上使用 **hasOwnProperty** 方法。

```
class A {
  constructor() {
    this.a = "A";
  }
}
A.prototype.c = "c";class B extends A {
  constructor() {
    super();
    this.b = "B";
  }
}
const b = new B();for (let key in b) {
  if (b.hasOwnProperty(key)) {
    console.log("hasOwnProperty: ", key);
  } else {
    console.log("hasOwnProperty: != ", key);
  }
}
// hasOwnProperty:  a
// hasOwnProperty:  b
// hasOwnProperty: !=  c
```

在上面的例子中，属性**【a】****b】**直接和间接地附加到对象 be 上。然而，属性**“c”**可以使用原型成员访问。然而，根据经验，您不应该使用`for-in` 循环遍历类属性。

你可以在[对象/hasOwnProperty](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty) 中阅读更多关于 **hasOwnProperty** 的内容。

## 扩展运算符的使用

要使一个**对象**或**数组**变浅，可以使用一个扩展操作符`…`。您也可以使用 **Object.assign** 来复制对象。但是， **Object.assign** 更容易出错，应该只在想要修改实际对象的时候使用。

```
// good
const original = { a: 1, b: 2 };
const original2 = { c: 3, d: 4 };const copy = { ...original, c: 3 }; 
// copy => { a: 1, b: 2, c: 3 }const mergedObject = { ...original, ...original2 }; 
// { a: 1, b: 2, c: 3, d: 4 }const { a, ...noA } = copy; // noA => { b: 2, c: 3 }const originalArray = [1, 2, 3];
const originalArray2 = [4, 5, 6];
const copyArray = [...originalArray, 4]; // [1,2,3,4]const without1 = originalArray.slice(1); //[2,3,4]const mergedArray = [...originalArray, ...originalArray2]; ///[1,2,3,4,5,6]
```

类似的想法，但一个糟糕的代码。

```
const original = { a: 1, b: 2 };
const copy = Object.assign(original, { c: 3 }); // this mutates `original` ಠ_ಠ
delete copy.a; // so does this
```

你可以在这里阅读更多关于[物体-休息-传播](https://airbnb.io/javascript/#objects--rest-spread)的内容。

## 使用 Array.from

**Array.from** 是利用不足的方法之一。使用 Array.from 可以将任何看起来像数组的东西转换成实际的数组。

```
const numbers = [1, 2, 3, 4];
const squares = [...numbers].map((num) => num * num);
console.log(squares); // [ 1, 4, 9, 16 ]const squares2 = Array.from(numbers, (num) => num * num);
console.log(squares2); // [ 1, 4, 9, 16 ]
```

在上面的例子中，您可以看到您可以使用 spread operator 和 map 从给定的数字列表中创建一个新的数组。映射函数有助于创建操作返回数据。你可以用的**数组做同样的事情。 **Array.from** 将映射函数作为第二个参数。**

**Array.from** 也有助于将一个看起来像数组的元素转换成数组。然而，如果你没有映射器的用例。建议使用 spread 运算符而不是 Array.from。

```
const foo = document.querySelectorAll('.foo');// good
const nodes = Array.from(foo);// best
const nodes = [...foo];
```

## 使用析构来提取值

spread 运算符用于创建元素的副本。析构是用来打破一个对象(或数组)。

```
const directions = [
  [-1, 0],
  [0, 1],
  [1, 0],
  [0, -1],
];const getNighbours = (i, j) => {
  const [up, right, down, left] = directions;
  console.log(up, right, down, left);
  /// [ -1, 0 ] [ 0, 1 ] [ 1, 0 ] [ 0, -1 ]
};
```

如果你正在处理一个图形问题，你可以很容易地创建一个每个相邻点的数组，并且可以使用数组的析构来访问。析构对于从函数中返回元组作为值也很有用。

```
const getMaxValue = (array) => {
  let [index, max] = [-1, -Infinity];
  for (let i in array) {
    if (array[i] > max) {
      index = i;
      max = array[i];
    }
  }
  return [max, index];
};const [max, i] = getMaxValue([1, 3, 5, 2, 7]);console.log(max, i); // 7 4
```

析构对于访问对象的属性值也很有用。您可以从对象的属性创建变量/常量。

```
const MyReactComponents = ({ name, onSubmit }) => {
  console.log(name);
  onSubmit("On callback function");
};MyReactComponents({ name: "Deepak", onSubmit: console.log });// Deepak
// On callback function
```

**注意:** *同样不建议使用数组重组返回 3 个以上的参数。*如果你想阅读更多关于[析构 _ 赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)的内容，请点击链接。

## **使用“of”和“in”运算符迭代对象**

你应该总是使用高阶函数迭代器和数组。然而，使用高阶函数打破循环/提前返回是一项繁琐的任务。万一，你只是在处理值。可以使用`**for-of**` 循环**。**

```
const contains = (array, num) => {
  for (let value of array) {
    if (value === num) return true;
  }
  return false;
};console.log(contains([1, 2, 3, 4], 5)); // false
```

类似地，您可以使用`for-in`循环迭代所有键。

```
const contains = (object, num) => {
  for (let key in object) {
    if (object[key] === num) return true;
  }
  return false;
};console.log(contains({ a: 1, b: 2 }, 2)); // true
```

如果你对循环更感兴趣。你可以阅读我的文章[如何在 javascript 中打破循环](https://javascript.plainenglish.io/weird-part-how-to-break-the-loop-in-javascript-8bba3e658267)。

## 可选参数的使用

使用 ES6，您可以为给定函数的可选参数定义默认值。这对 JavaScript 中的`function overload`非常有用。

```
const request = (url, options = { method: "GET" }) => {
  return fetch(url, options);
};console.log(request("test.com")); //get request
console.log(request("test.com", { method: "POST" })); //get request
```

**注意:**所有可选参数都应该在结束参数处定义。可能有一个以上的可选参数。

## 结论

*很难在一篇文章中涵盖所有的编码指南。我想在接下来的文章中讨论一些其他更好的编写代码的方法。*

## 参考资料:

[https://airbnb.io/javascript/](https://airbnb.io/javascript/#arrays--from)

[https://developer.mozilla.org/en-US/](https://developer.mozilla.org/en-US/)

[https://google.github.io/styleguide/jsguide.html](https://google.github.io/styleguide/jsguide.html)
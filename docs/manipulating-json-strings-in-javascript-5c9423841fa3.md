# JSON 指南及其在 JavaScript 中的处理方式

> 原文：<https://levelup.gitconnected.com/manipulating-json-strings-in-javascript-5c9423841fa3>

## 概述 JSON 以及可以用来操作和转换它的方法

![](img/d860a1378ca12da43825e7789d02e6d6.png)

[Adri Tormo](https://unsplash.com/@tormius?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

JSON 代表 JavaScript 对象符号。它是一种用于序列化数据的格式，这意味着它可以用于在不同的源之间传输和接收数据。在 JavaScript 中，有一个`JSON`实用程序对象，它提供了将 JavaScript 对象转换成 JSON 字符串的方法，反之亦然。不能构造或调用`JSON`实用程序对象——只有两个静态方法，分别是`stringify`和`parse`,用于在 JavaScript 对象和 JSON 字符串之间进行转换。

# JSON 的属性

JSON 是一种用于序列化对象、数组、数字、布尔值和`null`的语法。它基于 JavaScript 对象语法，但它们不是一回事。并非所有的 JavaScript 对象属性都可以转换为有效的 JSON，JSON 字符串必须正确格式化才能转换为 JavaScript 对象。

对于对象和数组，JSON 属性名必须用双引号括起来，并且禁止在对象后面加逗号。数字不能有前导零，小数点后必须至少有一位数字。不支持`NaN`和`Infinity`，JSON 字符串不能有`undefined`或注释。另外，JSON 不能包含函数。

任何 JSON 文本都必须包含有效的 JavaScript 表达式。在某些浏览器引擎中，JSON 中的字符串和属性键允许使用 U+2028 行分隔符和 U+2029 段落分隔符，但是在 JavaScript 代码中使用它们会导致语法错误。这两个字符可以用`JSON.parse`解析成有效的 JavaScript 字符串，但是传递给`eval`时会失败。

无关紧要的空白可以包含在 JSONNumber 或 JSONString 之外的任何地方。数字中不能有空格，字符串会被解释为字符串中的空格，否则会导致错误。制表符(U+0009)、回车符(U+000D)、换行符(U+000A)和空格(U+0020)是 JSON 中唯一有效的空白字符。

# JSON 对象的基本用法

在`JSON`实用程序对象上有 2 个方法。有将 JavaScript 对象转换成 JSON 字符串的`stringify`方法和将 JSON 字符串转换成 JavaScript 对象的`parse`方法。

`parse`方法将一个字符串解析为 JSON，并将一个函数作为第二个参数，以选择性地将 JSON 实体转换为您指定的 JavaScript 实体，并返回结果 JavaScript 对象。如果字符串包含 JSON 语法中不允许的实体，那么就会引发 SyntaxError。此外，传递给`JSON.parse`的 JSON 字符串中不允许有逗号结尾。例如，我们可以在下面的代码中使用它:

```
JSON.parse('{}'); // {}     
JSON.parse('false'); // false      
JSON.parse('"abc"'); // 'abc'       
JSON.parse('[1, 5, "abc"]');  // [1, 5, 'abc']
JSON.parse('null'); // null
```

第一行将返回一个空对象。第二个会返回`false`。第三行将返回`'abc'`。第四行将返回`[1, 5, "abc"]`。第五行会返回`null`。它返回我们期望的结果，因为我们传入的每一行都是有效的 JSON。

![](img/bc9a97d8e19aac7fc1ca88212b9f61f7.png)

照片由[加里·本迪格](https://unsplash.com/@kris_ricepees?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 自定义 Stringify 和 Parse 的行为

或者，我们可以传入一个函数作为第二个参数，将值转换成我们想要的任何值。我们传入的函数将把键作为第一个参数，把值作为第二个参数，并在操作完成后返回值。例如，我们可以写:

```
JSON.parse('{"a:": 1}', (key, value) =>
  typeof value === 'number'
    ? value * 10
    : value     
);
```

然后我们得到返回的`{a: 10}`。如果值的类型是数字，则该函数返回原始值乘以 10。

`JSON.stringify`方法可以将一个函数作为第二个参数，将 JavaScript 对象中的实体映射到 JSON 中的其他东西。默认情况下，`undefined`的所有实例和不受支持的原生数据(如函数)都会被删除。例如，如果我们编写以下代码:

```
const obj = {
  fn1() {},
  foo: 1,
  bar: 2,
  abc: 'abc'
}
const jsonString = JSON.stringify(obj);
console.log(jsonString);
```

然后我们看到在运行`JSON.stringify`之后，从 JSON 字符串中删除了`fn1`，因为 JSON 语法不支持函数。对于`undefined`，我们可以从下面的代码中看到`undefined`属性将被移除。

```
const obj = {
  fn1() {},
  foo: 1,
  bar: 2,
  abc: 'abc',
  nullProp: null,
  undefinedProp: undefined
}
const jsonString = JSON.stringify(obj);
console.log(jsonString);
```

`undefinedProp`不在记录的 JSON 字符串中，因为它已被`JSON.strinfiy`删除。

另外，`NaN`和`Infinity`在转换成 JSON 字符串后都变成了`null`:

```
const obj = {
  fn1() {},
  foo: 1,
  bar: 2,
  abc: 'abc',
  nullProp: null,
  undefinedProp: undefined,
  notNum: NaN,
  infinity: Infinity
}
const jsonString = JSON.stringify(obj);
console.log(jsonString);
```

我们看到:

```
'{“foo”:1,”bar”:2,”abc”:”abc”,”nullProp”:null,”notNum”:null,”infinity”:null}'
```

`NaN`和`Infinity`都变成了`null`而不是原来的值。

对于不受支持的值，我们可以使用第二个参数中的 replacer 函数将它们映射到受支持的值，我们可以选择传入第二个参数。replace 函数将属性的键作为第一个参数，将值作为第二个参数。例如，保留`NaN`、`Infinity`或函数的一种方法是将它们映射到一个字符串，如下面的代码所示:

```
const obj = {
  fn1() {},
  foo: 1,
  bar: 2,
  abc: 'abc',
  nullProp: null,
  undefinedProp: undefined,
  notNum: NaN,
  infinity: Infinity
}const replacer = (key, value) => {
  if (value instanceof Function) {
    return value.toString();
  } else if (value === NaN) {
    return 'NaN';
  } else if (value === Infinity) {
    return 'Infinity';
  } else if (typeof value === 'undefined') {
    return 'undefined';
  } else {
    return value; // no change
  }
}const jsonString = JSON.stringify(obj, replacer, 2);
console.log(jsonString);
```

在最后一行的`jsonString`上运行`console.log`之后，我们看到:

```
{
  "fn1": "fn1() {}",
  "foo": 1,
  "bar": 2,
  "abc": "abc",
  "nullProp": null,
  "undefinedProp": "undefined",
  "notNum": null,
  "infinity": "Infinity"
}
```

`replace`函数所做的是使用键和值添加额外的解析，这些值来自用`JSON.stringify`转换的对象。它检查`value`是否是一个函数，然后我们将它转换成一个字符串并返回它。同样，对于`NaN`、`Infinity`和`undefined`，我们做了同样的事情。否则，我们按原样返回值。

`JSON.stringfy`函数的第三个参数接受一个数字来设置要插入到 JSON 输出中的空格数，以使输出更具可读性。第三个参数也可以接受任何要插入的字符串，而不是空格。请注意，如果我们将一个字符串作为第三个参数，而该参数包含除空格之外的内容，我们可能会创建一个“JSON”一个不是有效 JSON 的字符串。

例如，如果我们写:

```
const obj = {
  fn1() {},
  foo: 1,
  bar: 2,
  abc: 'abc',
  nullProp: null,
  undefinedProp: undefined,
  notNum: NaN,
  infinity: Infinity
}const replacer = (key, value) => {
  if (value instanceof Function) {
    return value.toString();
  } else if (value === NaN) {
    return 'NaN';
  } else if (value === Infinity) {
    return 'Infinity';
  } else if (typeof value === 'undefined') {
    return 'undefined';
  } else {
    return value; // no change
  }
}const jsonString = JSON.stringify(obj, replacer, 'abc');
console.log(jsonString);
```

那么`console.log`将会是:

```
{
abc"fn1": "fn1() {}",
abc"foo": 1,
abc"bar": 2,
abc"abc": "abc",
abc"nullProp": null,
abc"undefinedProp": "undefined",
abc"notNum": null,
abc"infinity": "Infinity"
}
```

这显然不是有效的 JSON。`JSON.stringify`会抛出一个“循环对象值”类型错误。同样，如果一个对象有`BigInt`值，那么转换将失败，并显示“BigInt 值不能在 JSON 中序列化”类型错误。

另请注意，如果符号被用作对象中的关键字，它们会被`JSON.stringify`自动丢弃。所以如果我们有:

```
const obj = {
  fn1() {},
  foo: 1,
  bar: 2,
  abc: 'abc',
  nullProp: null,
  undefinedProp: undefined,
  notNum: NaN,
  infinity: Infinity,
  [Symbol('foo')]: 'foo'
}const replacer = (key, value) => {if (value instanceof Function) {
    return value.toString();
  } else if (value === NaN) {
    return 'NaN';
  } else if (value === Infinity) {
    return 'Infinity';
  } else if (typeof value === 'undefined') {
    return 'undefined';
  } else {
    return value; // no change
  }
}const jsonString = JSON.stringify(obj, replacer, 2);
console.log(jsonString);
```

我们回来了:

```
{
  "fn1": "fn1() {}",
  "foo": 1,
  "bar": 2,
  "abc": "abc",
  "nullProp": null,
  "undefinedProp": "undefined",
  "notNum": null,
  "infinity": "Infinity"
}
```

通过使用与`date.toISOString()`将返回的相同的字符串，日期对象被转换成字符串。例如，如果我们把:

```
const obj = {
  fn1() {},
  foo: 1,
  bar: 2,
  abc: 'abc',
  nullProp: null,
  undefinedProp: undefined,
  notNum: NaN,
  infinity: Infinity,
  [Symbol('foo')]: 'foo',
  date: new Date(2019, 1, 1)
}
const replacer = (key, value) => {
  if (value instanceof Function) {
    return value.toString();
  } else if (value === NaN) {
    return 'NaN';
  } else if (value === Infinity) {
    return 'Infinity';
  } else if (typeof value === 'undefined') {
    return 'undefined';
  } else {
    return value; // no change
  }
}
const jsonString = JSON.stringify(obj, replacer, 2);
console.log(jsonString);
```

我们得到:

```
{
  "fn1": "fn1() {}",
  "foo": 1,
  "bar": 2,
  "abc": "abc",
  "nullProp": null,
  "undefinedProp": "undefined",
  "notNum": null,
  "infinity": "Infinity",
  "date": "2019-02-01T08:00:00.000Z"
}
```

正如我们所见，`date`属性的值在转换成 JSON 后现在是一个字符串。

# 深层复制对象

我们也可以使用`JSON.stringify`和`JSON.parse`来制作 JavaScript 对象的深层副本。例如，要在没有库的情况下做一个对象的深层拷贝，你可以`JSON.stringify`然后`JSON.parse`:

```
const a = { foo: {bar: 1, {baz: 2}}
const b = JSON.parse(JSON.stringfy(a)) // get a clone of a which you can change with out modifying a itself
```

这是对对象的深层复制，这意味着对象的所有级别都被克隆，而不是引用原始对象。这是因为`JSON.stringfy`将对象转换成了一个不可变的字符串，当`JSON.parse`解析字符串返回一个不引用原始对象的新对象时，就会返回一个副本。
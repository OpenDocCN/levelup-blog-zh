# 理解 JavaScript 中 BigInt 的威力

> 原文：<https://levelup.gitconnected.com/learn-bigint-in-javascript-df9b61bc19ef>

## 了解如何在 JavaScript 中使用 BigInt 来存储大数

![](img/32de156755862f5a8ee5a06066ad0009.png)

照片由[米卡·鲍梅斯特](https://unsplash.com/@mbaumi?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/numbers?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

`**BigInt**`是一个内置对象，它提供了一种存储大于`Number.MAX_SAFE_INTEGER`的数字的方法。

需要`BigInt`的原因是

```
var maxIntNum = 9007199254740991;var largeNumber = maxIntNum + 2; // 9007199254740992
```

上述操作的预期结果是`9007199254740993`，但是我们得到的是`**9007199254740992**`。原因是 JavaScript 使用 64 位来存储一个数，所以一旦我们试图创建一个大于这个数的数，就无法存储了。

要解决这个问题，我们可以选择`BigInt`。

# 正在创建 BigInt

1.  将`n`添加到数字的末尾

```
var num =  100000000000000000n;num  + 10n;  // 100000000000000010n// using binary literal with n appendedvar num = 0x1fffffffffffff**n**num; // **9007199254740991n**
```

2.使用`BigInt`构造函数

```
var num = BigInt(100000000000000000);num+10n; //100000000000000010nvar fromHex = **BigInt('0x1fffffffffffff')**;fromHex; // **9007199254740991n**
```

将一个`BigInt`转换成一个对象

```
var bigIntAsObject = Object(1n);bigIntAsObject; // BigInt {1n}
```

检查一个数字是否是一个`BigInt`

```
typeof num === "bigint";**typeof bigIntAsObject;** "object"
```

请记住，我们不能将正常数字与 BitInt 混合，如果我们试图这样做，就会导致错误。

```
num + 10;// Uncaught TypeError: Cannot mix BigInt and other types, use explicit conversions
```

为了解决这个问题，我们可以通过将我们的正常数字转换为 BigInt 来完成以下操作:

```
num + BigInt(10);
```

# 用`BigInt`进行算术运算

```
var num = BigInt(10);**Addition**
num + 10n; // 20n**Subtraction**
num - BigInt(5); // 15n**Multiplication** 
num * 10n; // 150n**Exponent operator** num ** 10n; // 10000000000n**DIVISION**// when we perform division , the fractional part in the result is removedvar n  = 5n;
**n / 2n;  // 2n****MODULO**var n  = 5n;
**n % 2n; // 1n**
```

# 平等比较

```
10n == 10; // true
```

当`BigInt`与非`BigInt`数字比较时，使用严格相等的`===`总是得到`false`。

```
10n === 10; // false
```

# 关系运算

这类似于数字比较

```
1n < 2
// true

2n > 1
// true

2 > 2
// false

2n > 2
// false

2n >= 2
// true
```

# 转换为布尔值

```
Boolean(0n) // falseBoolean(12n) // true!0n; // true!12n; // false.
```

# 用 JSON 解析

```
var n = 100n;JSON.stringify(n); 
// Uncaught TypeError: Do not know how to serialize a BigInt
```

# 方法

## toString

BigInt 的`toString`方法将返回一个表示指定的`BigInt`对象的字符串，在数字的末尾没有`n`。

```
var a = 10n;a.toString(); //10
```

我们可以使用`toString()`方法来解决由 stringify 方法引起的错误。为了解决这个问题，我们将在`BigInt`原型中添加`toJSON`方法。

```
BigInt.prototype.toJSON = function() { **  return this.toString();**}
```

## 的价值

`**valueOf()**`方法返回一个`BigInt`对象的包装原始值。

```
var obj = Object(10n);obj.valueOf(); // 10n
```

参考: [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt)

跟随 [Javascript 吉普🚙💨](https://medium.com/u/f9ffc26e7e69?source=post_page-----df9b61bc19ef--------------------------------)。

[](https://sitepoint.tapfiliate.com/p/payout-methods/new/) [## 登录|站点点

### 不支持的浏览器虽然我们的跟踪技术支持旧的浏览器，不幸的是我们的网站不支持…

sitepoint.tapfiliate.com](https://sitepoint.tapfiliate.com/p/payout-methods/new/) [](/15-vs-code-extension-to-save-your-time-and-make-you-a-better-developer-506f79baec53) [## 15 VS 代码扩展节省您的时间，让您成为更好的开发人员

### 对前端开发人员有用的 VS 代码扩展列表

levelup.gitconnected.com](/15-vs-code-extension-to-save-your-time-and-make-you-a-better-developer-506f79baec53) [](https://medium.com/swlh/dot-vs-bracket-notation-in-accessing-javascript-object-9a3cd116a151) [## 访问 JavaScript 对象中的点与括号符号。

### 了解 JavaScript 中何时使用点和括号符号。

medium.com](https://medium.com/swlh/dot-vs-bracket-notation-in-accessing-javascript-object-9a3cd116a151) [](https://medium.com/better-programming/different-ways-to-duplicate-objects-in-javascript-c199be34ecb7) [## 在 JavaScript 中复制对象的不同方法

### 原来复制物体有很多不同的方法

medium.com](https://medium.com/better-programming/different-ways-to-duplicate-objects-in-javascript-c199be34ecb7)
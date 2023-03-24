# Javascript 中的取幂**(幂)运算符

> 原文：<https://levelup.gitconnected.com/exponentiation-operator-in-javascript-e38e8255062c>

**运算符将第一个变量的结果乘以第二个变量的幂。也就是`**Math.pow(a,b)**`。

![](img/ab7a64a2dd9e6381740f857e954009ab.png)

克里斯·贾维斯在 [Unsplash](https://unsplash.com/s/photos/math?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

```
var a = 2;
var b = 5;**a ** b**; // 32**Math.pow(a, b); // 32**
```

如果我们确实喜欢`**a ** b ** c**` ，那么运算是从右向左计算的，也就是`**a ** (b**c)**`

```
var a = 5, b = 2, c = 2;a ** b ** c; // 625// Execution order of a ** b ** c;(5 ** (2 ** 2) )(5 ** 4)625
```

不能将一元运算符(`**+/-/~/!/delete/void/typeof**`)直接放在基数前面。

```
// Invalid Operations+a ** b;-a ** b;~a ** b;!a ** b;delete a ** b;void a ** b;typeof a ** b;// All the above operation are invalid and result in **Uncaught SyntaxError: Unary operator used immediately before exponentiation expression. Parenthesis must be used to disambiguate operator precedence**
```

处理负数

```
-2 ** 2; //invalid// The above expression can be converted into (-2) ** 2; // 4(-2) ** 3; //-8 
```

在`NaN`上的任何操作都是`NaN`

```
NaN ** 1; //NaNNaN ** NaN;  // NaN
```

使用 undefined 会发生什么？

```
1  ** undefined; // NaN// because1 ** Number(undefined);1 ** NaN; // NaN
```

同理当我们对`null`、`**Number(null) → 0**`做`**`时，那么 0 的幂就是 1。

```
10 ** null; // 1// because10 ** Number(null); // Number(null) --> 010 ** 0; // 1
```

如果你发现这个有用的惊喜🎁我这里[](https://www.paypal.me/jagathishSaravanan?source=post_page---------------------------)****。****

**如果你觉得快乐，就分享吧😃 😆 🙂。**

****跟随** [**Javascript Jeep🚙💨**](https://medium.com/u/f9ffc26e7e69?source=post_page-----e38e8255062c--------------------------------)**

**[](https://gitconnected.com/learn/javascript) [## 学习 JavaScript -最佳 JavaScript 教程(2019) | gitconnected

### JavaScript 是世界上最流行的编程语言之一——它随处可见。JavaScript 是一种…

gitconnected.com](https://gitconnected.com/learn/javascript)**
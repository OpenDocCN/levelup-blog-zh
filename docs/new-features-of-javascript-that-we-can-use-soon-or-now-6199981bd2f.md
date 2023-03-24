# 您现在可以使用 ES2020 中的新 JavaScript 特性

> 原文：<https://levelup.gitconnected.com/new-features-of-javascript-that-we-can-use-soon-or-now-6199981bd2f>

## **JavaScript 中新的强大特性:类中的私有字段、可选链接、无效合并操作符和 BigInts。**

![](img/5e6fa55f14c926d7b0bece90dff636ed.png)

[Jari hytnen](https://unsplash.com/@jarispics?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

自 2015 年 ES6 发布以来，JavaScript 一直在快速发展，每次迭代都会推出大量新功能。JavaScript 语言规范的新版本每年都会更新，新的语言特性提案比以往任何时候都更快地完成。这意味着新特性正以前所未有的速度融入现代浏览器和其他 JavaScript 运行时引擎，如 Node.js。

2019 年，有许多新功能处于“阶段 3”阶段，这意味着它非常接近完成，浏览器/节点现在开始支持这些功能。如果我们想在生产中使用它们，我们可以使用 Babel 之类的东西将它们转换到旧版本的 JavaScript 中，以便在需要时可以在旧版本的浏览器(如 Internet Explorer)中使用它们。

**在这篇文章中，我们来看看类中的私有字段、可选链接、nullish 合并操作符和 BigInts。**

# 类中的私有字段

最新的提议之一是在类中添加私有变量的方法。我们将使用`#`符号来表示一个类变量是私有变量。这样，我们就不需要使用闭包来隐藏我们不想暴露给外界的私有变量。例如，我们可以编写一个简单的类来递增和递减数字，如下面的代码所示:

```
class Counter {
  #x = 0; increment() {
    this.#x++;
  } decrement() {
    this.#x--;
  } getNum(){
    return this.#x;
  }
}const c = new Counter();
c.increment(); 
c.increment(); 
c.decrement(); 
console.log(c.getNum());
```

我们应该从上面代码最后一行的`console.log`中得到值 1。`#x`是一个私有变量，不能在类外访问。所以如果我们写:

```
console.log(c.#x);
```

我们会得到`Uncaught SyntaxError: Private field '#x'`。

私有变量是 JavaScript 类非常需要的特性。这一功能现已出现在最新版本的 Chrome 和 Node.js v12 中。

# 可选链接运算符

目前，如果我们想访问一个对象的深层嵌套属性，我们必须检查每个嵌套层中的属性是否是用长布尔表达式定义的。例如，我们必须检查每个级别中定义的每个属性，直到我们可以访问我们想要的深层嵌套属性，如下面的代码所示:

```
const obj = {
  prop1: {
    prop2: {
      prop3: {
        prop4: {
          prop5: 5
        }
      }
    }
  }
}obj.prop1 &&
  obj.prop1.prop2 &&
  obj.prop1.prop2 &&
  obj.prop1.prop2.prop3 &&
  obj.prop1.prop2.prop3.prop4 &&
  console.log(obj.prop1.prop2.prop3.prop4.prop5);
```

幸运的是，上面的代码在我们想要访问的每个级别中都定义了每个属性，所以我们实际上会得到值 5。然而，如果我们有一个嵌套的对象在任何级别都是`undefined`或`null`的对象中，那么如果我们不对它进行检查，我们的程序将会崩溃，其余的将不会运行。这意味着我们必须检查每一关，以确保当它碰到`undefined`或`null`物体时不会崩溃。

使用可选的链接操作符，我们只需要使用`?.`来访问嵌套的对象。如果它遇到一个属性是`null`或`undefined`，那么它就返回`undefined`。使用可选的链接，我们可以改为编写:

```
const obj = {
  prop1: {
    prop2: {
      prop3: {
        prop4: {
          prop5: 5
        }
      }
    }
  }
}console.log(obj?.prop1?.prop2?.prop3?.prop4?.prop5);
```

当我们的程序遇到一个`undefined`或`null`属性时，我们不会崩溃，而是返回`undefined`。不幸的是，这还没有在任何浏览器中实现，所以我们使用最新版本的 Babel 来使用这个功能。

# 零融合算子

来自`null`或`undefined`值的另一个问题是，如果我们想要的变量是`null`或`undefined`，我们必须使用默认值来设置变量。例如，如果我们有:

```
const y = x || 500;
```

如果`x`是`undefined`，那么我们必须使用`||`操作符将它设置为`y`，并设置一个默认值。`||`操作符的问题是，所有的假值，如 0、`false`或空字符串，都将被缺省值覆盖，我们并不总是想这样做。

为了解决这个问题，有人提议创建一个“无效”合并算子，用`??`来表示。使用它，我们只在第一项的值是`null`或`undefined`时设置默认值。例如，使用 nullish 合并运算符，上面的表达式将变成:

```
const y = x ?? 500;
```

例如，如果我们有以下代码:

```
const x = null;
const y = x ?? 500;
console.log(y); // 500const n = 0
const m = n ?? 9000;
console.log(m) // 0
```

`y`将被赋予值 500，因为`x`具有值`null`。然而，`m`将被赋予值`0`，因为`n`不是`null`或`undfined`。如果我们使用了`||`而不是`??`，那么`m`将被赋值为 9000，因为 0 是 falsy。

不幸的是，这个特性还没有出现在任何浏览器或 Node.js 中，所以我们必须使用最新版本的 Babel 来使用这个特性。

![](img/90c1ebc6bc2abd04bf4f9fb70d6213b6.png)

拉梅什·卡斯珀在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# BigInt

要在 JavaScript 中表示大于 2 的 53 次方减 1 的整数，我们可以使用`BigInt`对象。它可以通过算术运算符之类的常规运算来操作，如加、减、乘、除、余数和取幂。它可以由数字和十六进制或二进制字符串构成。此外，它还支持像 AND、OR、NOT 和 XOR 这样的位运算。唯一不起作用的位运算是零填充右移位运算符(`>>>`)，因为 BigInts 都是有符号的。

此外，一元运算符`+`不支持将数字和位相加。只有当所有操作数都是 BigInts 时，才进行这些运算。在 JavaScript 中，`BigInt`与普通数字不同。它与普通数字的区别在于数字末尾有一个`n`。

我们可以用工厂函数`BigInt`定义一个 BigInt。它接受一个参数，该参数可以是整数，也可以是表示十进制整数、十六进制字符串或二进制字符串的字符串。BigInt 不能与内置 Math 对象一起使用。此外，当在数字和 BigInt 之间进行转换时，我们必须小心，因为当 BigInt 转换为数字时，BigInt 的精度可能会丢失。

为了定义一个 BigInt，如果我们想传入一个整数，我们可以写如下:

```
const bigInt = BigInt(1);
console.log(bigInt);
```

然后，当我们运行`console.log`语句时，它会记录`1n`。如果我们想把一个字符串传入工厂函数，我们可以写:

```
const bigInt = BigInt('2222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222222');
console.log(bigInt);
```

我们还可以通过使用一个以`0x`开头的字符串将一个十六进制数字字符串传入工厂函数:

```
const bigHex = BigInt("0x1fffffffffffff111111111");
console.log(bigHex);
```

当我们在`bigHex`上运行`console.log`时，上面的代码会记录`618970019642690073311383825n`。

同样，我们可以传入一个二进制字符串和一个以`0b`开头的字符串来获得一个 BigInt:

```
const bigBin = BigInt("0b111111111111111000000000011111111111111111111111");
console.log(bigBin);
```

当我们在上面运行`console.log`时，上面的代码会让我们得到`281466395164671n`。如果我们想要创建的 BigInt 超出了 number 类型可以接受的范围，那么传入字符串会很方便。

我们也可以用 BigInt 文字定义一个 BigInt。我们可以在一个整数的末尾加上一个`n`字符。例如，我们可以写:

```
const bigInt = 22222222222222222222222222222222n;
console.log(bigInt);
```

然后我们在记录值时得到`22222222222222222222222222222222n`作为`bigInt`的值。我们可以像处理数字一样用 BigInts 进行算术运算，如`+`、`-`、`/`、`*`、`%` 。如果我们想用 BigInts 做这些操作，所有的操作数都必须是 BigInts。但是，如果我们得到分数结果，分数部分将被截断。BigInt 是一个大整数，它不是用来存储小数的。例如，在下面的示例中:

```
const expected = 8n / 2n;
console.log(expected) // 4nconst rounded = 9n / 2n;
console.log(rounded) // 4n
```

对于`expected`和`rounded`，我们得到`4n`。这是因为小数部分从 BigInt 中移除了。

比较运算符可以应用于 BigInts。操作数可以是 BigInt 或 numbers。例如，我们可以将 1n 比作 1:

```
1n === 1
```

上面的代码会评估为`false`，因为 BigInt 和 number 不是同一类型。但是当我们用两个相等替换三个相等时，如下面的代码所示:

```
1n == 1
```

上面的语句评估为`true`，因为只比较值。注意，在这两个例子中，我们混合了 BigInt 操作数和 number 操作数。这对于比较运算符是允许的。BigInts 和 numbers 也可以与其他运算符一起比较，如下例所示:

```
1n < 9
// true9n > 1
// true9 > 9n
// false9n > 9
// false9n >= 9
// true
```

此外，它们可以在一个数组中混合在一起并进行排序。例如，如果我们有以下代码:

```
const mixedNums = [5n, 6, -120n, 12, 24, 0, 0n];
mixedNums.sort();
console.log(mixedNums)
```

这项功能现在可以与最新版本的 Chrome 和 Node.js 一起使用，所以我们可以开始在我们的应用程序中使用它。我们可以使用 Babel 让它与旧的浏览器一起工作。

# 结论

我们可以使用`#`符号来表示一个类变量是私有变量。这样，我们就不必使用闭包来隐藏我们不想暴露给外界的私有变量。为了解决对象中的`null`和`undefined`值的问题，我们使用可选的链接操作符来访问属性，而不用检查每个级别可能是`null`还是`undefined`。使用 nullish 合并操作符，我们可以只在变量为`null`或`undefined`的情况下为变量设置默认值。使用 BigInt 对象，我们可以表示 JavaScript 中常规数字安全范围之外的大数字，并对它们进行标准操作，除了小数部分将从结果中省略。
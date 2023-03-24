# 有用的 JavaScript 技巧——造型和国际化

> 原文：<https://levelup.gitconnected.com/useful-javascript-tips-casting-and-internationalization-1a1b3f992743>

![](img/8150be4808a7eda3b6dc38f3018e5755.png)

照片由[德韦恩希尔斯](https://unsplash.com/@dhillssr?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

像任何类型的应用程序一样，JavaScript 应用程序也必须写得很好。否则，我们以后会遇到各种各样的问题。

在本文中，我们将了解一些应该遵循的技巧，以便更快更好地编写 JavaScript 代码。

# 将字符串转换为数字

`Number`函数可以用来将字符串转换成数字。

例如，如果我们写:

```
Number('1')
```

那么我们得到 1。

字符串在被转换成数字之前被修剪，所以`Number(“ 1 “)`返回相同的结果。

任何空字符串都会产生 0。

另外，`Number`与小数有关。所以`Number(“1.2”)`返回 1.2。

# 将布尔值转换为数字

布尔值也可以转换成数字。

例如，我们可以写:

```
Number(true)
```

然后它返回 1。

如果我们写:

```
Number(false)
```

它返回 0。

# 将日期转换为数字

我们可以将一个`Date`实例传递给一个数字。将一个`Date`实例传递给`Number`会返回一个 UNIX 时间戳。

例如，我们可以写:

```
Number(new Date(2020, 0, 1))
```

它返回 1577865600000。

# 将特殊值转换为数字

我们可以向`Number`传递特殊值，将其转换为数字。

例如，我们可以写:

```
Number(null)
```

然后它返回 0。

`Number(undefined)`返回`NaN`，`Number(NaN)`返回`NaN`。

# 转换为布尔值

`Boolean`将任何值转换为布尔值。

任何错误值都将返回`false`。否则，它返回`true`。

例如，以下所有返回`false`:

```
Boolean(false)
Boolean(0)
Boolean(NaN)
Boolean("")
Boolean(null)
Boolean(undefined)
```

所有其他值返回`true`。

# 运算符的实例

`instanceof`操作符让我们检查值，看它是否是一个构造函数的实例，

例如，我们可以写:

```
class Animal {}
class Dog extends Animal {}
```

然后下面将返回`true`:

```
const dog = new Dog()
dog instanceof Animal
dog instanceof Dog
```

如果右操作数是父类或子类，它将为子类实例返回`true`。

# 新操作员

我们可以使用`new`操作符从构造函数中创建一个新对象。

例如，我们可以写:

```
const date = new Date();
```

我们也可以创建自己的构造函数并使用`new`操作符:

```
function Dog(breed, color) {
  this.breed = breed;
  this.color= color;
}
```

然后我们可以写:

```
const dog = new Dog('poodle', 'white');
```

# 运算符的类型

我们可以使用`typeof`操作符来检查值的类型。

例如，我们可以写:

```
typeof 1
```

我们得到了`'number'`。

```
typeof '1'
```

返回`'string'`。

```
typeof { name: 'james' }
typeof [1, 2, 3]
```

返回`'object'`。

```
typeof true
```

返回`'boolean'`。

```
typeof undefined
```

返回`'undefined'`。

```
typeof (() => {})
```

返回`'function'`和

```
typeof Symbol()
```

返回`'symbol'`。

# 国际化

JavaScript 的标准库有自己的国际化 API。

`Intl.Collator`对象为我们提供了对语言敏感的比较特性的访问。

让我们以语言敏感的方式格式化日期。

`Intl.NumberFormat`让我们以语言敏感的方式格式化数字。

`Intl.PluralRules`让我们以语言敏感的方式格式化复数。

`Intl.RelativeTimeFormat`允许我们访问语言敏感的时间格式功能。

然后`Intl.getcanonicalLocales`让我们检查一个语言环境是否有效。

它接受一个字符串或字符串数组。

例如，我们可以写:

```
Intl.getCanonicalLocales('en')
```

或者:

```
Intl.getCanonicalLocales(['en-us', 'en-gb'])
```

它们都返回`true`,因为它们是有效的地区字符串。

另一方面，如果我们有:

```
Intl.getCanonicalLocales('en_gb')
```

这将返回`false`,因为它不是有效的区域设置。

## 国际机场。校对机

`Intl.Collator`是一个构造函数，我们可以将一个区域设置传递给它，然后根据该区域设置进行比较。

例如，我们可以写:

```
const collator = new Intl.Collator('en-gb')
collator.compare('a', 'c')
```

然后我们得到-1，所以字符串是升序排列的。

另一方面，如果我们有:

```
collator.compare('d', 'c')
```

然后返回 1，所以它们按降序排列。

## 国际机场。日期时间格式

`Intl.DateTimeFormat`构造函数让我们访问语言敏感的日期和时间格式特性。

例如，我们可以如下使用它:

```
const date = new Date();
const formatter = new Intl.DateTimeFormat('fr-ca');
const formatted = formatter.format(date);
```

在上面的例子中，我们有了`Intl.DateTimeFormat`构造函数。

我们只是将一个地区字符串传入构造函数。

然后我们调用`format`方法在其上调用`date`来格式化日期。

所以`formatted`将会是`“2020–05–12”`。

我们还可以调用`formatToParts`函数来返回一个带有格式化日期部分的对象。

例如，我们可以写:

```
const date = new Date();
const formatter = new Intl.DateTimeFormat('fr-ca');
const parts = formatter.formatToParts(date);
```

然后我们得到:

```
[
  {
    "type": "year",
    "value": "2020"
  },
  {
    "type": "literal",
    "value": "-"
  },
  {
    "type": "month",
    "value": "05"
  },
  {
    "type": "literal",
    "value": "-"
  },
  {
    "type": "day",
    "value": "12"
  }
]
```

作为`parts`的值。

![](img/2516407ac91fb04ace0229dd1b3edb00.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Kouji 鹤](https://unsplash.com/@pafuxu?utm_source=medium&utm_medium=referral)拍摄的照片

# 结论

JavaScript 的标准库有许多有用的国际化特性。

此外，还有许多转换数据类型的函数。
# 在 JavaScript 中使用字符串

> 原文：<https://levelup.gitconnected.com/using-strings-in-javascript-f655d60d7065>

![](img/dd689dad9601301706f20639a53c24d2.png)

[Adri Tormo](https://unsplash.com/@tormius?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 定义字符串

它可以有几种形式，如下面的代码:

```
'string'
`string`
'日本語'
```

这些被称为字符串文字。第一个字符串是普通字符串。第二个是模板字符串。它们是原始值，类型为“string”。我们也可以用`String`函数定义字符串，如下面的代码所示:

```
String(1)
```

上面的代码将返回`'1'`,因为我们将数字 1 转换为一个字符串。这可以通过所有原始值来实现，如下例所示:

```
String(true)
String('string')
String(undefined)
String(null)
String(Symbol('a'))
```

如果我们逐行记录上面的代码，我们会得到:

```
true
string
undefined
null
Symbol(a)
```

然而，`String`方法并不能很好地处理如下代码中的对象:

```
const str = String({
  a: 1
});
console.log(str);
```

上面的代码会让我们得到`[object Object]`。然而，如果一个对象有一个`toString()`函数，那么当使用`String`函数将它转换成一个字符串时，我们会得到一些有用的东西。例如，如果我们有:

```
const str = String({
  a: 1,
  toString(){
   return JSON.stringify(this)
  }
});
console.log(str);console.log(String([1, 2, 3]))
console.log(String(new Date()))
```

然后我们得到:

```
{"a":1}
1,2,3
Sun Oct 27 2019 10:30:54 GMT-0700 (Pacific Daylight Time)
```

正如我们所看到的，如果我们给一个对象添加了一个`toString()`方法，那么我们可以用`String`函数得到它的字符串表示。数组和日期对象也是如此，它们都内置了`toString()`方法。

如果我们有:

```
console.log([1, 2, 3].toString())
console.log(new Date().toString())
```

然后我们得到与上一个例子相同的输出。

## 转义字符

除了常规的可打印字符之外，字符串还可以包含可以转义的特殊字符。以下是可以添加到字符串中的转义字符:

*   `\XXX` ( `XXX` = 1 - 3 位八进制数字；范围为 0-377)—U+0000 和 U+00FF 之间的 ISO-8859-1 字符/ Unicode 码位
*   `\'` —单引号
*   `\"` —双引号
*   `\\` — 反斜杠
*   `\n` — 新行
*   `\r` —回车
*   `\v` —垂直标签
*   `\t` —标签
*   `\b` —退格
*   `\f` —表格馈送
*   `\uXXXX` ( `XXXX` = 4 位十六进制数字；范围 0x0000 - 0xFFFF)UTF-16 代码单元/ Unicode 代码点在 U+0000 和 U+FFFF 之间
*   `\u{X}`...`\u{XXXXXX}` ( `X…XXXXXX` = 1 - 6 位十六进制数字；0x0 - 0x10FFFF) UTF-32 代码单元/ Unicode 代码点的范围在 U+0000 和 U+10FFFF 之间
*   `\xXX` ( `XX` = 2 位十六进制数字；范围 0x00 - 0xFF)ISO-8859-1 字符/ Unicode 码位在 U+0000 和 U+00FF 之间

如果需要用`+`操作符换行，长字符串可以连接在一起。例如，如果我们有一个像下面这样的长字符串，我们可以写:

```
let longString = "Lorem ipsum dolor sit amet, consectetur " +
                 "adipiscing elit. Nulla nec facilisis libero, " +
                 "nec pulvinar est.";
```

包装长字符串的更方便的方法是使用斜杠字符或使用字符串模板文字。使用斜杠字符，我们可以用以下内容重写上面的字符串:

```
let longString = "Lorem ipsum dolor sit amet, consectetur \
                 adipiscing elit. Nulla nec facilisis libero, \
                 nec pulvinar est.";
```

要用模板字符串编写它，我们可以这样写:

```
let longString = `Lorem ipsum dolor sit amet, consectetur adipiscingelit. Nulla nec facilisis libero, nec pulvinar est.`;
```

由于模板字符串保留了间距，我们不想添加额外的间距。相反，我们希望让它自动换行到下一行。

## 字符串操作

我们可以通过使用`charAt`方法或者使用括号符号来访问字符串中的一个字符，就像访问数组中的一个条目一样。要使用`charAt`方法，我们可以写:

```
'dog'.charAt(1)
```

这将返回“o”。同样，我们可以使用括号符号来获得与下面代码中相同的字符:

```
'dog'[1]
```

记录时应该得到相同的输出。我们不能用这个符号删除或重新分配一个字符，因为它是不可写的，它的属性描述符不能改变，也不能被删除。

为了比较字符串，我们可以使用普通的比较操作符，就像对其他任何东西一样。例如，我们可以写:

```
console.log('a' < 'b');
console.log('a' > 'b');
console.log('a' == 'b');
console.log('a' === 'b');
```

上面的代码会让我们:

```
true
false
false
false
```

顺序由字符列表中 Unicode 字符代码的顺序或排序规则规范决定，具体取决于区域设置。这意味着不同语言的排序顺序可能不同。字符串比较区分大小写。对于不区分大小写的比较，我们必须将它们转换成相同的大小写。为了获得最佳结果，我们应该将所有字符串转换为大写，因为有些 Unicode 字符在转换为小写时不能正确转换。例如，如果我们想做不区分大小写的等式检查，那么我们可以写:

```
console.log('abc'.toUpperCase() === 'Abc'.toUpperCase());
```

上面的代码会在`console.log`中显示`true`。

在 JavaScript 中，除了字符串文字，还有一个字符串对象。通过使用`new String()`构造函数，可以用`String`构造函数定义字符串。使用`String`构造函数，我们得到字符串将是 object 类型，即使其他都是一样的。字符串文字的类型为 string。例如，如果我们有以下代码:

```
const sPrim = 'foo';
const sObj = new String(sPrim);
console.log(typeof sPrim);
console.log(typeof sObj);
```

第一个`console.log`将显示“字符串”,第二个将显示“对象”。使用`valueOf`方法，我们可以将一个字符串对象转换成一个原始字符串。例如，我们可以写:

```
const sPrim = 'foo';
const sObj = new String(sPrim);
console.log(typeof sPrim);
console.log(typeof sObj.valueOf());
```

然后我们得到两个`console.log`语句都显示‘字符串’。

![](img/68f27185b27a8fef794c7e921761cd8a.png)

乔恩·泰森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 基本方法

为了充分利用字符串，我们必须能够操作它们。幸运的是，字符串有很多可用的方法来简化操作。

## String.substr()

JavaScript 字符串有一个`substr`函数来获取字符串的子串。

它将起始索引作为第一个参数，将起始索引中的一些字符作为第二个参数。

它可以这样使用:

```
const str = "Hello Bob";
const res = str.substr(1, 4); // ello
```

## String.substring()

JavaScript 字符串还有一个`substring`函数，它将起始和结束索引作为两个参数，并返回一个介于起始和结束索引之间的字符串。

包括开始索引，但不包括结束索引。

示例:

```
const str = "Hello Bob";
const res = str.substring(1, 3); // el
```

## String.toLocaleLowerCase()

根据浏览器的区域设置，将字符串转换为小写。返回新字符串，而不是修改原始字符串。

例如:

```
const str = "Hello Bob!";
const res = str.toLocaleLowerCase(); // hello bob!
```

## String.toLocaleUpperCase()

根据浏览器的区域设置，将字符串转换为大写。返回新字符串，而不是修改原始字符串。

例如:

```
const str = "Hello Bob!";
const res = str.toLocaleUpperCase(); // 'HELLO BOB!'
```

## String.toLowerCase()

将字符串转换为小写。返回新字符串，而不是修改原始字符串。

例如:

```
const str = "Hello Bob!";
const res = str.toLocaleLowerCase(); // 'hello bob!'
```

## String.toUpperCase()

将字符串转换为大写。返回新字符串，而不是修改原始字符串。

例如:

```
const str = "Hello Bob!";
const res = str.toLocaleUpperCase(); // 'HELLO BOB!'
```

## String.trim()

从字符串中删除开始和结束空格。

例如:

```
const str = "         Hello Bob!     ";
const res = str.trim(); // 'Hello Bob!'
```

## 字符串.重复()

要使用`repeat`函数，需要将字符串重复的次数作为参数传递。它返回一个新的字符串。

例如:

```
const hello = "hello";
const hello5 = A.repeat(5);
console.log(hello5); // "hellohellohellohellohello"
```

## String.startsWith()

`startsWith`检查字符串是否以传入的子字符串开头。

例如:

```
const str = "Hello world.";
const hasHello = str.startsWith("Hello"); // trueconst str2 = "Hello world.";
const hasHello2 = str.startsWith("abc"); // false
```

## String.endsWith()

`endsWith`检查字符串是否以传入的子字符串结尾。

例如:

```
const str = "Hello world.";
const hasHello = str.endsWith("world."); // trueconst str2 = "Hello world.";
const hasHello2 = str.endsWith("abc"); // false
```

## String.indexOf()

`indexOf`查找子字符串第一次出现的索引。如果未找到，则返回-1。

例如:

```
const str = "Hello Hello.";
const hasHello = str.indexOf("Hello"); // 0
const hasHello2 = str.indexOf("abc"); // -1
```

## String.lastIndexOf()

`lastIndexOf`查找子字符串最后一次出现的索引。如果未找到，则返回-1。

例如:

```
const str = "Hello Hello.";
const hasHello = str.lastIndexOf("Hello"); // 6
const hasHello2 = str.lastIndexOf("abc"); // -1
```

## String.charAt()

`charAt`返回位于字符串索引处的字符。

例如:

```
const str = "Hello";
const res = str.charAt(0); // 'H'
```

## String.search()

`search`获取传递给函数的子字符串的位置。如果在字符串中找不到子字符串，则返回-1。

示例:

```
const str = "Hello";
const res = str.search('H'); // 0
```

## 字符串.包含

`includes`检查传入的子串是否在字符串中。如果在字符串中，返回`true`，否则返回`false`。

```
const str = "Hello";
const hasH = str.includes('H'); // true
const hasW = str.includes('W'); // false
```

## 字符串.替换()

`replace()`函数对于用另一部分字符串替换另一部分字符串很有用。它返回一个新字符串，其中包含替换子字符串后的字符串。

示例:

```
const str = "Hello Bob";
const res = str.replace("Bob", "James"); // 'Hello James'
```

`replace()`也可以使用 Regex `g`全局属性替换所有出现的子字符串。

例如:

```
const str = "There are 2 chickens in fridge. I will eat chickens today.";
const res = str.replace(/chickens/g, "ducks"); // "There are 2 ducks in fridge. I will eat ducks today."
```

如果第一个参数是全局搜索的正则表达式，它将替换子字符串的所有匹配项。

字符串是 JavaScript 中一个重要的全局对象。它代表一系列字符。它可以被各种各样的操作符使用，比如比较操作符，并且它有操作它们的方法。其他重要的方法包括用`includes`方法和`indexOf`方法搜索字符串。他们还有一个`trim()`方法来修剪琴弦。
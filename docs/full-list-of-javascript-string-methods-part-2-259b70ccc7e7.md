# JavaScript 字符串方法的完整列表(第 2 部分)

> 原文：<https://levelup.gitconnected.com/full-list-of-javascript-string-methods-part-2-259b70ccc7e7>

## 我们可以用来操作字符串的所有方法的列表

![](img/8ceebf910d65946238e01a59fb598fdb.png)

照片由 [Larisa Birta](https://unsplash.com/@larisabirta?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

字符串是 JavaScript 中一个重要的全局对象。它们代表一系列字符。它们可以被各种操作符使用，比如比较操作符，并且有各种方法可以用来操作它们。有不同的方法来操作字符串，在不同的位置查找字符串的各个部分，以及修剪和替换字符串。随着 JavaScript 规范的每一次发布，都会添加更多的方法来使字符串搜索和操作变得更加容易。

# 实例方法(续)

字符串的实例方法在字符串文字或字符串对象上调用。他们对被调用的字符串的实例做一些事情。我们继续第 1 部分的列表。

## String.normalize()

`normalize`方法返回给定字符串的 Unicode 范式。它会在转换前将非字符串参数转换为字符串。例如，如果我们有:

```
const first = '\u00e5'; // "å"
const second = '\u0061\u030A'; // "å"
```

然后我们在每个字符串中调用`normalize`方法，将它们规范化为相同的字符代码:

```
first.normalize('NFC') === second.normalize('NFC')
```

上面的代码将评估为`true`，因为我们将字符串规范化为相同的字符代码。但是，如果我们没有调用`normalize`:

```
first === second
```

然后上面的代码会返回`false`。如果我们在两个字符串上运行`console.log`来获得它们的字符代码，如下面的代码所示:

```
console.log(first.normalize('NFC').charCodeAt(0));
console.log(second.normalize('NFC').charCodeAt(0));
```

我们得到两个字符串的第一个字符为 229，因为它们在规范化后是相同的。另一方面，如果我们在用`normalize`规范化之前运行它，如下面的代码所示:

```
console.log(first.charCodeAt(0));
console.log(second.charCodeAt(0));
```

我们得到第一串日志 229 和第二串日志 97。

## String.padStart()

`padStart()`函数用另一个字符串填充当前字符串(如果需要，可以多次填充)，直到结果字符串达到给定的长度。从当前字符串的开头应用填充。

该函数有两个参数。第一个是字符串的目标长度。如果目标长度小于字符串的长度，则字符串按原样返回。第二个参数是要添加填充的字符串。这是一个可选参数，如果没有指定，它默认为`' '`。

例如，我们可以这样写:

```
'def'.padStart(10);         // "       def"
'def'.padStart(10, "123");  // "1231231def"
'def'.padStart(6,"123465"); // "abcdef"
'def'.padStart(8, "0");     // "00000def"
'def'.padStart(1);          // "def"
```

注意，每个字符串都用第二个参数中的字符串填充到目标长度。第二个参数中的整个字符串可能并不总是包含在内。只包括让函数将字符串填充到目标长度的部分。

## String.padEnd()

`padEnd()`函数用另一个字符串填充当前字符串(如果需要，可以多次填充)，直到结果字符串达到给定的长度。填充从当前字符串的末尾开始应用。

该函数有两个参数。第一个是字符串的目标长度。如果目标长度小于字符串的长度，则字符串按原样返回。第二个参数是要添加填充的字符串。这是一个可选参数，如果没有指定，它默认为`' '`。

例如，我们可以这样写:

```
'def'.padEnd(10);         // "def       "
'def'.padEnd(10, "123");  // "def1231231"
'def'.padEnd(6,"123465"); // "defabc"
'def'.padEnd(8, "0");     // "def00000"
'def'.padEnd(1);          // "def"
```

## 字符串.重复()

要使用`repeat`函数，您需要将字符串重复的次数作为参数传递。它返回一个新的字符串。

例如:

```
const hello = "hello";
const hello5 = A.repeat(5);
console.log(hello5); // "hellohellohellohellohello"
```

## 字符串.替换()

包含在字符串中的`replace()`函数对于用另一个字符串替换一部分字符串很有用。它返回一个新字符串，其中包含替换子字符串后的字符串。

示例:

```
const str = "Hello Bob";
const res = str.replace("Bob", "James"); // 'Hello James'
```

`replace()`也可以用来替换所有出现的子串。

例如:

```
const str = "There are 2 chickens in fridge. I will eat chickens today.";
const res = str.replace(/chickens/g, "ducks"); // "There are 2 chickens in fridge. I will eat chickens today."
```

如果第一个参数是全局搜索的正则表达式，它将替换子字符串的所有匹配项。

## String.search()

`search`获取传递给函数的子字符串的位置。如果在字符串中找不到子字符串，则返回-1。

示例:

```
const str = "Hello";
const res = str.search('H'); // 0
```

## String.slice()

`slice`方法提取字符串的一部分并返回一个新的字符串。我们可以传入正数和负数。如果范围是正数，那么返回的子字符串将以传入的索引开始和结束。如果它们是负的，那么起始索引从索引为-1 的字符串的最后一个字符开始，然后倒计数回到字符串的开头，当一个字符在另一个字符的左边时，递减 1。如果省略第二个参数，则假定它是字符串最后一个字符的索引。

例如，我们可以写:

```
const str = 'Try not to become a man of success. Rather become a man of value.';
console.log(str.slice(31));
// output: "ess. Rather become a man of value."console.log(str.slice(4, 19));
// output: "not to become a"console.log(str.slice(-4));
// output: "lue."console.log(str.slice(-9, -5));
// output: "of v"
```

## String.startsWith()

`startsWith`检查一个字符串是否以你传入的子字符串开始。

例如:

```
const str = "Hello world.";
const hasHello = str.startsWith("Hello"); // trueconst str2 = "Hello world.";
const hasHello2 = str.startsWith("abc"); // false
```

## String.split()

`split`方法将一个字符串分割成一个子字符串数组。`split`方法可以使用正则表达式作为分隔符来拆分字符串。例如，我们可以写:

```
const str = 'The 1 quick 2 brown fox jump.';
const splitStr = str.split(/(\d)/);console.log(splitStr); 
// gets ["The ", "1", " quick ", "2", " brown fox jump."]
```

这将一个句子分割成一个字符串数组，用数字分隔单词。它还可以将字符串作为分隔符来分隔字符串，如下例所示:

```
const str = 'The 1 quick 2 brown fox jump.';
const splitStr = str.split(' ');console.log(splitStr);
// gets ["The", "1", "quick", "2", "brown", "fox", "jump."]
```

它还可以将字符串数组作为给定字符串实例的分隔符。例如:

```
const str = 'The 1 quick 2 brown fox jump.';
const splitStr = str.split([' ']);
console.log(splitStr);
// gets ["The", "1", "quick", "2", "brown", "fox", "jump."]
```

## String.substr()

JavaScript 字符串有一个`substr`函数来获取字符串的子串。

它将起始索引作为第一个参数，将起始索引中的多个字符作为第二个参数，依此类推。

它可以这样使用:

```
const str = "Hello Bob";
const res = str.substr(1, 4); // ello
```

## String.substring()

JavaScript strings 还有一个`substring`函数，它将起始和结束索引作为两个参数，并返回一个介于起始和结束索引之间的字符串。

包括开始索引，但不包括结束索引。

示例:

```
const str = "Hello Bob";
const res = str.substring(1, 3); // el
```

![](img/0b10528d9b25e615e998b518f9015611.png)

照片由 [Allef Vinicius](https://unsplash.com/@seteales?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

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

## String.trimStart()

现在，字符串对象有了一个`trimStart()`函数来修剪字符串开头的空白。还有一个`trimLeft()`方法，它是这个方法的别名。

例如，我们可以写:

```
let message = '   Hi! How\'s it going?   ';console.log(message);// We get '   Hi! How's it going?   'let message = '   Hi! How\'s it going?   ';
console.log(message.trimStart());// We get 'Hi! How's it going?   '
```

正如我们所看到的，左边的空白消失了。我们可以对`trimLeft()`做同样的事情:

```
let message = '   Hi! How\'s it going?   ';
console.log(message.trimLeft());// We get 'Hi! How's it going?   '
```

注意，用`trimStart`或`trimLeft`返回一个新字符串，因此原始字符串保持不变。这可以防止我们改变原始字符串，这对于防止意外改变对象的错误是很方便的。

## String.trimEnd()

`trimEnd`方法删除字符串右端的空白。`trimRight`是`trimEnd`方法的别名。例如，我们写道:

```
let message = '   Hi! How\'s it going?   ';
console.log(message);
// We get '   Hi! How's it going?   'let message = '   Hi! How\'s it going?';
console.log(message.trimEnd());
// We get '   Hi! How\'s it going?'
```

注意，一个新的字符串用`trimEnd`或`trimRight`返回，所以原来的字符串保持不变。这可以防止我们改变原始字符串，这对于防止意外改变对象的错误是很方便的。

## String.valueOf()

使用`valueOf`方法，我们可以将一个字符串对象转换成一个原始字符串。例如，我们可以写:

```
const sPrim = 'foo';
const sObj = new String(sPrim);
console.log(typeof sPrim);
console.log(typeof sObj.valueOf());
```

然后我们得到两个`console.log`语句都显示‘string’。

## 结论

字符串是 JavaScript 中一个重要的全局对象。它代表一系列字符。它有操作字符串的方法，在不同的位置查找字符串的各个部分，以及修剪和替换字符串。随着 JavaScript 规范的每一次发布，都会添加更多的方法来使字符串搜索和操作变得更加容易。
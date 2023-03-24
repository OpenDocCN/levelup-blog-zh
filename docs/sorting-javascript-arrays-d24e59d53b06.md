# 对 JavaScript 数组排序

> 原文：<https://levelup.gitconnected.com/sorting-javascript-arrays-d24e59d53b06>

![](img/c23c9b597bc771e2d7ffc1819e976da0.png)

[Alex Block](https://unsplash.com/@alexblock?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

对数组进行排序是许多应用程序中的常见操作。JavaScript 数组附带的`sort`方法使这变得容易。然而，在对数组进行排序时，有很多复杂的事情。

在本文中，我们将看看如何用`sort`方法对数组进行排序。此外，我们将了解如何比较对象，以便按照我们期望的方式对它们进行排序。

# 基本排序

最基本的排序操作大概就是对数字进行排序。有了`sort`方法，我们可以轻松做到这一点。例如，如果我们有以下数组:

```
let arr = [3, 4, 5, 2, 5, 8, 9];
```

那么我们可以用如下的`sort`方法对 is 进行排序:

```
arr.sort();
```

`arr`数组已经排序到位，所以我们不需要把它赋给一个新的变量。

调用`sort`后，我们得到数组是:

```
[2, 3, 4, 5, 5, 8, 9]
```

我们可以看到，排序是通过按升序排列数字来完成的。

按升序对数字数组进行排序是最简单的情况。我们不必修改`sort`方法的默认功能来做到这一点。

我们还可以按照 Unicode 码位的升序对字符串进行排序。`sort`方法将根据每个字符串的总代码点进行排序。Unicode 字符的完整码位列表在[https://en.wikipedia.org/wiki/List_of_Unicode_characters](https://en.wikipedia.org/wiki/List_of_Unicode_characters)中列出。

例如，给定以下数组:

```
let strings = ['foo', 'bar', 'baz'];
```

我们可以把`sort`写成:

```
strings.sort();
```

按 Unicode 码位总数升序排序。

# 排序前复制数组

正如我们所见，`sort`将对它所调用的数组进行排序。我们可以通过在排序前将数组复制到一个新数组来避免这种情况。

为此，我们可以使用如下的`slice`方法:

```
let sortedStrings = strings.slice().sort();
```

或者更方便的是，我们可以使用 spread 操作符来做同样的事情:

```
let sortedStrings = [...strings].sort();
```

# 使用比较功能进行排序

`sort`方法可以采用一个可选的回调函数，让我们比较数组的内容，让我们按照自己想要的方式进行排序。

回调函数有两个参数。第一个是第一个数组条目，第二个是第一个参数中第一个条目之后的条目。

我们比较它们，然后根据我们的偏好返回正数、负数或 0。

假设我们有两个条目，分别用于回调的第一个和第二个参数的`a`和`b`，我们返回一个负数，使`a`出现在`b`之前。为了让`b`在`a`之前到来，我们可以返回一个正数。

为了保持它们之间的顺序不变，我们返回 0。

例如，我们可以使用它对数字进行反向排序，如下所示:

```
arr.sort((a, b) => b - a);
```

我们希望让较大的条目出现在较小的条目之前，所以如果`b`大于`a`，我们返回`b — a`以使`b`出现在`a`之前，因为`b`大于`a`意味着`b — a`将为正。

否则，`b`小于`a`则为负，相同则为零。

对于按对象数组中的字段进行排序也很方便。如果我们想按对象的字段排序，那么我们必须编写一个比较函数来完成。例如，给定以下数组:

```
let arr = [{
    name: 'Joe',
    age: 20
  },
  {
    name: 'Mary',
    age: 16
  },
  {
    name: 'Jane',
    age: 22
  },
];
```

我们可以按照`age`字段按年龄升序排序如下:

```
arr.sort((a, b) => a.age - b.age);
```

它的工作原理和其他数字的排序一样。如果`a.age`小于`b.age`，那么我们返回一个负数，所以`a`在`b`之前。如果相反，那么`b`在`a`之前，其中`a`和`b`是数组的相邻条目。

否则，如果它们相同，那么`a`和`b`的顺序保持不变。

如果我们记录该阵列，我们会得到以下结果:

```
{name: "Mary", age: 16}
{name: "Joe", age: 20}
{name: "Jane", age: 22}
```

我们可以使用像`>`、`<`、`<=`或`>=`这样的比较运算符来比较字符串。

例如，如果我们想按名称对上面的数组进行排序，我们可以写:

```
arr.sort((a, b) => {
  if (a.name.toLowerCase() < b.name.toLowerCase()) {
    return -1;
  } else if (a.name.toLowerCase() > b.name.toLowerCase()) {
    return 1;
  }
  return 0;
});
```

然后我们得到以下结果:

```
{name: "Jane", age: 22}
{name: "Joe", age: 20}
{name: "Mary", age: 16}
```

![](img/327c4841fc5a0e0c9011ffe5bc1c2b12.png)

[伊尔努尔·卡里穆林](https://unsplash.com/@kalimullin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 比较非英语字符串

上面排序字符串的回调函数可以很好地排序英文字符串。然而，它对非英语字符串不太适用，因为它没有考虑标点或重音等因素。

为了比较这些类型的字符串，我们可以使用字符串自带的`localeCompare`方法。

它有很多选项，我们可以调整来改变字符串的比较，比如是否检查重音，大小写和字母的其他变体。也可以调整用于比较的区域设置。

选项的完整列表位于[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/String/locale compare](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare)

我们可以如下使用它:

```
let arr = ['réservé', 'Premier', 'Cliché', 'communiqué', 'café', 'Adieu'];
arr.sort((a, b) => a.localeCompare(b, 'fr', {
  ignorePunctuation: true,
  sensitivity: 'base'
}));
```

在上面的代码中，我们在`localeCompare`方法的比较函数中传递了一个字符串`b`来与`a`进行比较，作为第一个参数。然后我们将第二个参数设置为`'fr'`地区，以表明我们正在比较法语字符串。最后，我们传入了一些基本选项，包括忽略标点符号和调整字符串比较的敏感度。

`sensitivity`设置为`'base'`表示字母不同的字符串不相等。

在上面的`sort`方法调用之后，我们得到`arr`的值是:

```
["Adieu", "café", "Cliché", "communiqué", "Premier", "réservé"]
```

对 JavaScript 数组进行排序有其棘手的地方。最简单的情况是按升序对数字和字符串码位进行排序，这不需要比较函数。

对于更复杂的比较，我们必须传递一个比较函数。在其中，返回负数将使第一个值在第二个值之前。正值将使第二个值出现在第一个值之前。返回 0 将保留现有顺序。

比较非英语字符串需要更加小心。我们可以使用字符串附带的`localeCompare`方法，通过传入各种选项来进行比较。
# 用值填充 JavaScript 数组的几种方法

> 原文：<https://levelup.gitconnected.com/several-ways-to-fill-a-javascript-array-with-values-91f52f09669>

![](img/a244ce5ce8ff4bf7ec616d83b9dfb063.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[Louis Hansel @ shotsoflouis](https://unsplash.com/@louishansel?utm_source=medium&utm_medium=referral)拍摄

JavaScript 数组可以通过多种方式创建。有一些用值填充数组的构造和方法。

在本文中，我们将看看如何用我们选择的内容填充一个新数组。

# `Array.prototype.fill()`

我们可以用 array 实例的`fill`方法用值填充一个现有的数组。

它有以下签名:

```
Array.prototype.fill(value, start=0, end=this.length)
```

`fill`方法有以下参数:

*   `value` —用于填充数组的值。
*   `start` —可选参数，指示填充数组的起始索引。默认值为 0
*   `end` —可选参数，带有结束索引以填充值。默认为数组实例的`length`。不包括指数本身

它返回一个用`value`填充的修改后的数组。

例如，我们可以如下使用它:

```
const arr = [1, 2, 3].fill(6, 1, 3);
```

那么`arr`就是`[1, 6, 6]`，因为我们指定要从索引 1 开始填充值 6，直到 2。

`fill`总是填充到结束索引减 1。

如果我们跳过可选参数，那么我们用条目覆盖`value`:

```
const arr = [1, 2, 3].fill(6);
```

然后我们得到`[6, 6, 6]`,因为我们忽略了可选参数，用 6 覆盖了所有条目。

# 用升序数字填充

通过将 spread 操作符与数组实例的`keys`方法结合起来，我们可以用从 0 开始的升序数字填充数组。

例如，我们可以这样写:

```
const arr = [...new Array(5).keys()]
```

然后`arr`的值为`[0, 1, 2, 3, 4]`，因为我们创建了一个有 5 个条目的新数组，对其调用`keys`，然后将其扩展到一个新数组中。

# 用计算值填充

要用计算出的值填充数组，我们可以使用`Array.from`方法，然后传入一个对第二个参数的回调，以将值映射到每个条目中我们想要的内容。

例如，如果我们想创建一个包含 5 个奇数的数组，我们可以写如下:

```
const arr = Array.from(new Array(5), (x,i) => i*2 + 1)
```

那么`arr`的值为`[1, 3, 5, 7, 9]`，因为我们在第一个参数中调用了`Array`构造函数，创建了一个新数组。然后在第二个参数中，我们传入一个函数来映射我们在第一个参数中创建的数组的索引`i`，并返回`i*2 + 1`。

因此，我们在数组中得到 5 个奇数。

# 用`undefined`填充

要用`undefined`填充，我们只需要用一个参数调用`Array`构造函数，这个参数是 0 或更大的整数。

然后，我们将新构造的数组扩展到一个新数组中，以转换从数组构造函数调用`undefined`中创建的空值。

例如，我们可以写:

```
const arr = [...new Array(3)]
```

`arr`有值`[undefined, undefined, undefined]`，因为我们传播了这个值。

![](img/b1c7e218fa78e2154de222af86181dcc.png)

[Alev Takil](https://unsplash.com/@alevtakil?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 使用字符串`repeat()`

我们可以调用`repeat`来重复一个字符串，然后调用`split`来将字符串拆分成数组条目。

例如，如果我们想用`'foo'`填充长度为 5 的数组，那么我们可以写:

```
const arr = 'foo|'.repeat(5).split('|').filter(f => !!f);
```

在上面的代码中，我们使用了`|`符号作为分隔符，在我们完成调用`repeat`以重复`'foo|'`之后，我们用它来调用`split`。

然后我们调用`filter`删除`split`返回的数组末尾的空字符串值。

因此，`arr`就有了`[“foo”, “foo”, “foo”, “foo”, “foo”]`的价值。

# 结论

用值填充数组有几种方法。

我们可以使用`Array.from`方法创建一个新的数组。通过传入一个映射函数，这些值可以映射到我们想要的东西。

另外，`Array`有`fill`静态方法来用值填充给定的数组。

与 spread 操作符结合使用的`Array`构造函数也可以用来在数组中填充值。

最后，我们可以在一个字符串上调用`repeat`来重复它，然后调用`split` 来拆分成数组条目。

当我们调用`repeat`时，我们可能需要调用`filter`来删除不需要的值。
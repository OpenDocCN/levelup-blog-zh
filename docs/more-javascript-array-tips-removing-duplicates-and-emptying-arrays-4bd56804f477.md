# 删除重复项和清空数组:JavaScript 数组提示

> 原文：<https://levelup.gitconnected.com/more-javascript-array-tips-removing-duplicates-and-emptying-arrays-4bd56804f477>

![](img/cb73af5548e842b6d3fcbdfe9be8641c.png)

照片由 [Zbynek Burival](https://unsplash.com/@zburival?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

JavaScript 和其他编程语言一样，有许多方便的技巧，让我们可以更容易地编写程序。在本文中，我们将看看如何做涉及数组的不同事情，比如删除数组中的重复项和清空数组。

# 移除数组中的重复项

## 使用集合

从数组中删除重复的条目是我们经常要解决的问题，有几种方法可以做到。对于 ES6 或更高版本，最简单的方法之一是将其转换为集合，然后再转换回数组。

我们可以通过使用`Set`构造函数将数组转换成集合。构造函数接受一个参数，可以是任何可迭代的对象。这意味着我们可以传入一个数组，或其他类似数组的对象，如`arguments`对象、字符串、节点列表、类型数组，如`Uinit8Array`、`Map`、其他`Sets`和任何其他具有`Symbol.iterator`方法的对象。

我们也可以传入`null`，做一个空集。然后，我们用 spread 操作符将集合转换回数组。例如，如果我们有一个数组，我们可以编写以下代码将它转换为一个集合，然后再转换回数组，以删除重复的条目:

```
let arr = [1, 1, 2, 2, 3, 4, 5, 5, 6, 6];
arr = [...new Set(arr)];
console.log(arr);
```

在上面的代码中，我们从拥有重复条目的`arr`开始，到最后，我们应该记录了以下内容:

```
[1, 2, 3, 4, 5, 6]
```

注意，我们在数组声明中使用了`let`。我们需要使用`let`,这样我们就可以在第二秒把新数组赋回给我们的`arr`变量。每个条目的第一次出现被保留，其他重复出现被丢弃。

我们也可以使用`Array.from`方法将`Set`转换成一个数组。`Array.from`方法的第一个参数是一个类似数组的对象，如`arguments`对象、字符串、节点列表、类型数组如`Uinit8Array`、`Map`、其他`Sets`和任何其他具有`Symbol.iterator`方法的对象。这意味着我们可以在下面的代码中使用它来替代数组中的 spread 运算符:

```
let arr = [1, 1, 2, 2, 3, 4, 5, 5, 6, 6];
arr = Array.from(new Set(arr));
console.log(arr);
```

使用上面的代码，我们应该得到与第一个例子相同的输出。

## 使用 Array.filter 方法

另一种从数组中删除重复项的方法是使用数组的`filter`方法。`filter`方法让我们过滤掉已经存在的数组条目。它采用一个有两个参数的回调函数，第一个是数组入口，第二个是被`filter`方法迭代的数组的索引。

`filter`方法过滤器让我们定义我们想要保留的内容，并返回一个包含保留条目的新数组。我们可以将它与`indexOf`方法一起使用，后者返回数组条目的第一次出现。

`indexOf`方法接受我们想要查找索引的任何对象。我们可以使用它来过滤掉重复出现的数组条目，如下面的代码所示:

```
let arr = [1, 1, 2, 2, 3, 4, 5, 5, 6, 6];
arr = arr.filter((entry, index) => arr.indexOf(entry) === index);
console.log(arr);
```

`filter`中的回调函数返回我们想要保留的数组项的状态。在上面的代码中，我们想返回`arr.indexOf(entry) === index`，因为我们只想返回每个条目的第一次出现。`arr.indexOf(entry)`返回条目第一次出现的索引，`index`是我们想要检查的数组条目，因为`filter`方法正在遍历它。

`filter`方法的第一次迭代应该使`entry`为 1，而`index`为 0，因此我们得到:

```
arr.indexOf(1) === 0
```

因为`indexOf`获得了第一个条目的索引，所以它返回 0，所以`filter`方法将保留它。在下一次迭代中，我们再次将`entry`设为 1，将`index`设为 1，因此我们得到:

```
arr.indexOf(1) === 1
```

我们已经知道`arr.indexOf(1)`是 0 但是右边是 1。这意味着表达式将被求值为`false`，因此`filter`方法将丢弃它。对于第三个条目，我们有值为 2 的`entry`和值为 2 的`index`。然后我们得到:

```
arr.indexOf(2) === 2
```

`arr.indexOf(2)`将返回 2，因为 2 的第一次出现是 2，并且我们在右侧有 2，因为`index`是 2，所以表达式计算为`true`，所以它将被保存在返回的数组中。这种情况会一直持续下去，直到所有的条目都被遍历并检查完毕。因此，我们应该得到与上面其他例子相同的条目。

## 使用 Array.reduce 方法

从数组中删除重复条目的第三种方法是使用`reduce`方法。该方法根据我们在传入的回调函数中指定的内容，将数组的条目组合成一个实体，并返回组合后的实体。

`reduce`方法接受一个有两个参数的函数，第一个参数包含一个数组，该数组包含到目前为止由`reduce`方法合并的条目，第二个参数包含要合并到第一个参数的第一个值中的条目。

第二个参数是回调函数的第一个参数的初始值。例如，我们可以使用它来过滤掉重复的数组，如下面的代码所示:

```
let arr = [1, 1, 2, 2, 3, 4, 5, 5, 6, 6];
arr = arr.reduce((noDupArr, entry) => {
  if (noDupArr.includes(entry)) {
    return noDupArr;
  } else {
    return [...noDupArr, entry];
  }
}, [])
console.log(arr);
```

在上面的代码中，我们传递了一个回调函数，该函数通过使用数组附带的`includes`方法来检查没有任何重复值的数组`noDupArr`是否在第二个参数中包含了`entry`。`includes`方法接受任何对象，并让我们检查它是否已经在`noDupArr`中。因此，如果`noDupArr.includes(entry)`是`true`，那么我们返回到目前为止组合的所有内容。否则，如果该值还没有包含在数组中，那么我们可以添加`entry`，通过使用 spread 操作符将`noDupArr`的现有条目扩展到一个新的数组中，然后我们添加`entry`作为数组的最后一个条目。

按照这种逻辑，第一个值之后的后续值不会包含在新组合的数组中。因此，我们不会在数组中包含重复的条目。最初，`noDupArr`将是一个由`reduce`方法的第二个参数指定的条目。

![](img/3ad5e8e0c953d5675f97e001e428a2c2.png)

照片由[freestocks.org](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 清空数组

有几种方法可以清空数组。我们可以将它设置为空数组，或者将数组的长度设置为 0。例如，我们可以将其设置为一个空数组，如下面的代码所示:

```
let arr = [1, 1, 2, 2, 3, 4, 5, 5, 6, 6];
arr = [];
console.log(arr);
```

上面的代码是最明显的方法。一种不太为人所知的清空数组的方法是将其长度设置为 0，如下所示:

```
let arr = [1, 1, 2, 2, 3, 4, 5, 5, 6, 6];
arr.length = 0;
console.log(arr);
```

那么我们也应该为`arr`得到一个空数组。

# 结论

有许多方法可以从数组中删除重复的条目，每种方法都保持第一次出现的值不变，并丢弃后续出现的值。我们可以用 spread 操作符将它转换成一个集合，然后再转换回一个数组。

此外，我们可以使用`filter`和`indexOf`方法返回一个新数组，只保留第一次出现的值，因为`indexOf`返回第一次出现的值和回调函数的第二个参数，如果我们可以检查原始数组的索引，我们会将该参数传递给`filter`。

我们可以一起使用`reduce`和`includes`方法来做类似于`filter`和`indexOf`方法的事情。

要清空数组，我们可以将其`length`设置为 0 或者直接设置为空数组。
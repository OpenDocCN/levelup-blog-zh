# JavaScript —数组可变方法

> 原文：<https://levelup.gitconnected.com/javascript-array-mutation-methods-e42143713a4e>

## 编程；编排

## 数组方法 push、shift、unshift、pop、reverse、splice、sort、copyWithin、fill——简单的例子

![](img/b3bf0aeeebd536b53d94b5d9a6c8e2be.png)

照片由 [Devin Avery](https://unsplash.com/@devintavery?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

只概述了可以**改变**原始数组的数组方法。其中一些已经广为人知，一些并不常用。让我们把它们都过一遍，不要有困难的解释。

> *注:这也适用于* ***打字稿***

# 添加项目

## 1 —推送

最广为人知的方法是`push`。在下面的例子中，我们有一个包含两个名字(字符串)的数组。使用`push`，您**将一个项目添加到数组**的末尾。

```
const array = ['John', 'Jeroen'];
array.push('Peter');console.log(array);// ['John', 'Jeroen', 'Peter']// You can also add more arguments by separating them by a comma. E.g. array.push('Bob', 'Alice').
```

## 2-不移位

使用`unshift`，你可以**在数组**的开头添加一个条目。之所以称之为 unshift，是因为它与`shift`(我稍后会解释)相反。

```
const array = ['John', 'Jeroen'];
array.unshift('Peter');console.log(array);// ['Peter', 'John', 'Jeroen']// You can also add more arguments by separating them by a comma. E.g. array.unshift('Bob', Alice').
```

# 移除项目

## 3 —流行音乐

`pop`方法也是最常用的数组方法之一。它用于**移除数组**末尾的一个项目。你不能传递任何参数。

```
const array = ['John', 'Jeroen', 'Peter'];
array.pop();console.log(array);// ['John', 'Jeroen']
```

## 4-移位

我们已经解释过`unshift`，但是相反的叫做`shift`。它**删除数组开头的一个项目。**

```
const array = ['John', 'Jeroen', 'Peter'];
array.shift();console.log(array);// ['Jeroen', 'Peter']
```

# 更改项目

## 5 —填充

`fill`方法用于**覆盖数组**中的现有项。它最多可以有三个参数；第一个参数用于新值，第二个用于 *startIndex* (可选)，第三个用于 *endIndex* (也是可选的)。

```
const array = ['John', 'Jeroen', 'Peter'];
array.fill('Bob', 1, 2);console.log(array);// ['John', 'Bob', 'Bob']
```

# 添加、删除或更改项目

## 6 —拼接

更高级的方法是`splice`。这可用于**移除、更改或添加项目**。第一个参数是 *startIndex* ，第二个参数是 *deleteCount* ，后面的参数是项目。

**添加:**

```
const array = ['John', 'Jeroen', 'Peter'];
array.splice(3, 0, 'Bob', 'Alice');console.log(array);// ['John', 'Jeroen', 'Peter', 'Bob', 'Alice']
```

**移除:**

```
const array = ['John', 'Jeroen', 'Peter'];
array.splice(2, 1);console.log(array);// ['John', 'Jeroen']
```

**改变:**

```
const array = ['John', 'Jeroen', 'Peter'];
array.splice(2, 0, 'Bob');console.log(array);// ['Jeroen', 'Peter', 'Bob']
```

# 重新订购项目

## 7-反向

用`reverse`，我们**反转数组**的顺序。听起来很简单，因为事实如此。

```
const array = ['John', 'Jeroen', 'Peter'];
array.reverse();console.log(array);// ['Peter', 'Jeroen', 'John']
```

## 8-排序

以不同的顺序排列。我们需要指定两个参数，因为它将比较第一项和第二项的值。它可以用于**以多种方式对数组进行排序**。

```
const array = ['John', 'Jeroen', 'Peter'];
array.sort((name1, name2) => name1 < name2);console.log(array);// ['Jeroen', 'John', 'Peter']
```

## 9-复制范围内

最后一个数组方法叫做`copyWithin`。这用于**将数组中的项目从一个地方移动到另一个地方**。最多允许三个参数。第一个参数用于 *targetIndex* ，第二个用于 *startIndex* (可选)，第三个用于 *endIndex* (也可选)。下面的示例复制索引 1 到 3，并将其从索引 0 开始放置。

```
const array = ['John', 'Jeroen', 'Peter'];
array.copyWithin(0, 1, 3);console.log(array);// ['Jeroen', 'Peter', 'Peter']
```

# 结论

这些都是改变原始数组的方法。我希望你学到了新的东西。如果你喜欢这篇文章，请点击 50 次👏按钮，跟我来。我也推荐阅读' [JavaScript —数组循环方法](https://itnext.io/javascript-array-loop-methods-46ad0d7a5a7c)'。
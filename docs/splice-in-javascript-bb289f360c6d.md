# JavaScript 中的拼接

> 原文：<https://levelup.gitconnected.com/splice-in-javascript-bb289f360c6d>

![](img/2ce59634ebb737d567100f50e87eba6c.png)

javascript 中的拼接

splice 方法就地更改数组的内容，可用于在数组中添加或移除项目。

```
const arr = ["🌼", "🌴", "🌹", "🌵", "🍄"];
arr.splice(2,3); // ["🌹", "🌵", "🍄"]
console.log(myArr); // ["🌼", "🌴"]
```

语法:

```
let arrDeletedItems = array.splice(start[, deleteCount[, item1[, item2[, ...]]]])
```

`start`指定开始改变数组的索引。

如果`start`大于数组的长度，那么`start`将被设置为数组的长度。即不会删除任何元素。

如果`start`为负，它将从数组的末尾开始计算。

在`deleteCount`中，您想要移除的项目数量。

在`item`中，您要添加的数字(*如果您要删除，您可以将此留空)。*

注意:Splice 总是返回一个包含被删除元素的数组。

🌚当只提供一个参数时，从数组中移除所提供的起始索引之后的所有项:

```
const arr = ["🌼", "🌴", "🌹", "🌵", "🍄"];
arr.splice(2); // ["🌹", "🌵", "🍄"]
console.log(myArr); // ["🌼", "🌴"]
```

🌚移除索引 3 处的 1 个元素:

```
const arr = ["🌼", "🌴", "🌹", "🌵", "🍄"];
arr.splice(3, 1); // ["🌵"]
console.log(myArr); // ["🌼", "🌴", "🌹", "🍄"]
```

🌚可以传入任意数量的附加参数，并将它们添加到数组中:

```
const arr = ["🌼", "🌴", "🌹", "🌵", "🍄"];
arr.splice(2, 1, "⭐️", "💥"); // ["🌹"]
console.log(myArr); // ["🌼", "🌴", "⭐️", "💥", "🌵", "🍄"]
```

🌚从索引-2 中删除 1 个元素:

```
const arr = ["🌼", "🌴", "🌹", "🌵", "🍄"];
arr.splice(-2, 1); // ["🌵"]
console.log(myArr); // ["🌼", "🌴", "🌹", "🍄"]
```

🌚您可以将 0 指定为要移除的项目数，以便在数组中的指定位置添加新项目:

```
const arr = ["🌼", "🌴", "🌹", "🌵", "🍄"];
arr.splice(2, 0, "⭐️", "💥"); // []
console.log(myArr); // ["🌼", "🌴", "⭐️", "💥", "🌹", "🌵", "🍄"]
```

🌚在数组末尾添加几个项目:

```
const arr = ["🌼", "🌴", "🌹", "🌵", "🍄"];
arr.splice(arr.length, 0, "🌕", "🌞", "🌦"); // []
console.log(myArr); // ["🌼", "🌴", "🌹", "🌵", "🍄", "🌕", "🌞", "🌦"]
```

# 参考🧐

[拼接 MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)

🌟[推特](https://twitter.com/suprabhasupi) |👩🏻‍💻 [Suprabha.me](https://www.suprabha.me/) |🌟 [Instagram](https://www.instagram.com/suprabhasupi/)
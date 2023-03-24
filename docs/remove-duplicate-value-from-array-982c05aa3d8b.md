# 从数组中删除重复值

> 原文：<https://levelup.gitconnected.com/remove-duplicate-value-from-array-982c05aa3d8b>

![](img/36d0998ac5c77d628447b4d5f5991ac5.png)

从数组中删除重复值

有多种方法可以从数组中过滤出重复的值并只返回唯一的值。

# 1️⃣ .使用集合🔥

## 什么是集合？

Set 是 ES6 中引入的新数据对象。集合是唯一值的集合。

*   数组被转换为`Set`，所有重复的元素被自动删除。
*   扩展语法`...`用于将`Set`的所有元素包含到一个新数组中。

```
const arr = ["🌼", "🌴", "🌹", "🌵", "🍄", "🌹", "🌴"];const filteredArr = [...new Set(arr)];
console.log(filteredArr); //["🌼", "🌴", "🌹", "🌵", "🍄"]
```

**使用** `**Array.from**`将集合转换为数组

你也可以使用`Array.from`将一个`Set`转换成一个数组:

```
const arr = ["🌼", "🌴", "🌹", "🌵", "🍄", "🌹", "🌴"];const filteredArr = Array.from(new Set(arr));
console.log(filteredArr); //["🌼", "🌴", "🌹", "🌵", "🍄"]
```

# 2️⃣ .使用🕸滤波器

如果元素通过并返回 true，它将被包括在过滤后的数组中，而任何失败或返回 false 的元素，它将不在过滤后的数组中。

```
const arr = ["🌼", "🌴", "🌹", "🌵", "🍄", "🌹", "🌴"];const filteredArr = arr.filter((item, index) => {
    return arr.indexOf(item) === index;
})
console.log(filteredArr); //["🌼", "🌴", "🌹", "🌵", "🍄"]
```

# 3️⃣ .使用 forEach 方法🚀

使用`forEach`，您可以迭代数组中的元素，如果数组中不存在新的元素，就将其推入新的数组。

```
const arr = ["🌼", "🌴", "🌹", "🌵", "🍄", "🌹", "🌴"];const filteredArr = (arr) => {
    let uniqueVal = [];
    arr.forEach(el => {
        if(!uniqueVal.includes(el)) {
            uniqueVal.push(el);
        }
    })
    return uniqueVal;
}
console.log(filteredArr(arr)); //["🌼", "🌴", "🌹", "🌵", "🍄"]
```

# 使用 Reduce 方法的 4️⃣😎

`reduce`方法用于减少数组的元素，并根据您传递的一些 reducer 函数将它们组合成一个最终的数组。

```
const arr = ["🌼", "🌴", "🌹", "🌵", "🍄", "🌹", "🌴"];const filteredArr = arr.reduce((acc, curr) => {
    return acc.includes(curr) ? acc : [...acc, curr];
}, [])
console.log(filteredArr(arr)); //["🌼", "🌴", "🌹", "🌵", "🍄"]
```

# 5️⃣ .阵列原型的独特方法📔

在 Javascript 中，数组原型构造函数允许您向数组对象添加新的属性和方法。

```
const arr = ["🌼", "🌴", "🌹", "🌵", "🍄", "🌹", "🌴"];Array.prototype.filteredArr = function (){
    let arr = [];
    for(let i = 0; i < this.length; i++) {
        let current = this[i];
        if(arr.indexOf(current) < 0 ) arr.push(current);
    }
    return arr;
}
console.log(arr.filteredArr()); //["🌼", "🌴", "🌹", "🌵", "🍄"]
```

# 参考🧐

*   [MDN 文档—集合](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)
*   [MDN 文档—过滤器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
*   [MDN 单据—减少](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce)

🌟[推特](https://twitter.com/suprabhasupi)👩🏻‍💻 [suprabha.me](https://www.suprabha.me/) 🌟Instagram
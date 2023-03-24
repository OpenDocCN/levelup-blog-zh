# JavaScript 算法:记录收集

> 原文：<https://levelup.gitconnected.com/javascript-algorithm-record-collection-fc107c1eb612>

## 我们编写一个函数来修改一个包含多个音乐记录的 JSON 对象。

![](img/bb44cca0b8a57b28bf01b2a70b87d89f.png)

今天我们将编写一个名为`updateRecords`的函数，它将接受一个整数(`id`)和两个字符串(`prop`和`value`)作为参数。

给你一个 JSON 对象，代表你的音乐唱片集。每个相册都有一个唯一的 ID 作为其关键字。每个键都有另一个包含多个属性的对象，如 album、tracks 和 artist。并非所有的相册都有这些属性。

```
var collection = {
    2548: {
        album: "Slippery When Wet",
        artist: "Bon Jovi",
        tracks: [
            "Let It Rock",
            "You Give Love a Bad Name"
        ]
    },
    2468: {
        album: "1999",
        artist: "Prince",
        tracks: [
            "1999",
            "Little Red Corvette"
        ]
    },
    1245: {
        artist: "Robert Palmer",
        tracks: []
    },
    5439: {
        album: "ABBA Gold"
    }
};
```

该函数的目标是使用给定的函数输入修改上面的 JSON 数据，并返回整个集合对象。`ID`是指每条记录的 ID 号，`prop`是该 ID 内的对象属性，`value`是与该`prop`相关联的值。

以下是处理不存在的属性或具有空值的对象属性时要遵循的一些规则。

如果`prop` = `"tracks"`但特定`ID`中的相册没有`tracks`属性，则在将新的`value`添加到数组中之前创建一个空数组。

如果`prop` = `"tracks"`并且相册已经有一个`tracks`属性和一个现有的值数组，则将`value`推到现有数组的末尾。

如果`value`为空(`""`，则从相册中删除给定的`prop`属性。

如果`prop`是另一个不是`tracks`的属性，则更新或设置该唱片集属性的`value`。

让我们开始写代码吧。

因为我们的对象被赋给了一个变量，所以我们将使用括号符号来访问对象属性。

我们要写一系列 if-else 语句。使用上面的规则，我们编写第一个 if 语句。这将检查给定的`prop`是否等于`tracks`，并检查我们的`collections`对象中给定的`ID`内的相册是否没有`tracks`属性。

```
if(prop === "tracks" && !collection[id].hasOwnProperty([prop])){
    collection[id][prop] = [];
    collection[id][prop].push(value);
}
```

我们使用`hasOwnProperty()`方法检查一个对象是否有属性。如果属性存在，则返回`true`或`false`。

如果这个语句为真，意味着`tracks`属性不存在，我们创建一个新的属性并给它一个空数组。之后，我们把给定的`value`放入数组。

在我们的 else-if 语句中，我们检查给定的`prop`是否等于`tracks`，并检查`collections`对象是否已经有一个现有的`tracks`属性。如果这是真的，我们只需将给定的`value`推到现有数组的末尾。

```
else if(prop === "tracks" && collection[id].hasOwnProperty([prop])){
    collection[id][prop].push(value);
}
```

在我们最后的 else 语句中，对于所有其他属性，更新或设置唱片集属性的`value`。

```
else{
    collection[id][prop] = value;
}
```

下面是完整的 if-else 语句块:

```
if (prop === "tracks" && !collection[id].hasOwnProperty([prop])) {
    collection[id][prop] = [];
    collection[id][prop].push(value);
} else if (prop === "tracks" && collection[id].hasOwnProperty([prop])) {
    collection[id][prop].push(value);
} else {
    collection[id][prop] = value;
}
```

我们编写一个最终的 if 语句，检查给定的`value`是否为空字符串。如果这是真的，那么我们从相册的对象中删除给定的`prop`属性。

```
if(!value){
    delete collection[id][prop];
}
```

在函数完成对集合对象的修改后，我们返回它。

```
return collection;
```

以下是完整的代码:

```
function updateRecords(id, prop, value) {

  if(prop === "tracks" && !collection[id].hasOwnProperty([prop])){
    collection[id][prop] = [];
    collection[id][prop].push(value);
  }else if(prop === "tracks" && collection[id].hasOwnProperty([prop])){
    collection[id][prop].push(value);
  }else{
    collection[id][prop] = value;
  }

  if(!value){
    delete collection[id][prop];
  }

  return collection;
}
```

如果你觉得这个算法有帮助，看看我的其他 JavaScript 算法解决方案:

[](https://medium.com/javascript-in-plain-english/javascript-algorithm-calculate-body-mass-index-6f14dce4075d) [## JavaScript 算法:计算身体质量指数

### 我们写了一个函数，计算你的身体质量指数，并确定你是否超重，体重不足，肥胖或正常。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-calculate-body-mass-index-6f14dce4075d) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-es6-string-addition-d9022d8f189c) [## JavaScript 算法:ES6 字符串添加

### 我们将在不使用任何 JavaScript 内置方法和+号的情况下连接两个字符串。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-es6-string-addition-d9022d8f189c) [](https://medium.com/javascript-in-plain-english/javascript-algorithm-funny-string-adabc8eac4d7) [## JavaScript 算法:有趣的字符串

### 对于今天的算法，我们要写一个叫做 funnyString 的函数，它接受一个输入:一个字符串，s。

medium.com](https://medium.com/javascript-in-plain-english/javascript-algorithm-funny-string-adabc8eac4d7)
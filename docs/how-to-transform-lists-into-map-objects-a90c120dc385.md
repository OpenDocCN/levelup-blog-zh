# 如何将列表转换为地图对象

> 原文：<https://levelup.gitconnected.com/how-to-transform-lists-into-map-objects-a90c120dc385>

您可能需要将对象数组转换为地图对象，反之亦然。在这篇文章中，我们将看看如何做到这一点。

![](img/10c9278f6d32de79b0410d4932b66d5a.png)

作者照片

# 将列表转换为地图对象

假设我们有一个游戏对象数组。每个游戏对象都有一个`id`和一个`name`。

```
const games = [
  {id:1, name: 'Fornite'},
  {id:2, name: 'RDR2'},
  {id:3, name: 'Cyberpunk 2077'}
];
```

我们希望将游戏数据的数组转换成一个对象，将游戏的`id`映射到`name`。

```
{
  1: "Fornite", 
  2: "RDR2", 
  3: "Cyberpunk 2077"
}
```

## 为每一个

一种解决方案是使用`forEach`方法循环遍历数组并构建新的映射对象。对于输入数组中的每个游戏元素，我们在`map`对象中创建一个新属性。属性键是游戏的`id`，属性值是游戏的`name`。

```
const map = {};
games.forEach(({id, name})=>{
  map[id] = name;
});console.log(map);
//{1: "Fornite", 
// 2: "RDR2", 
// 3: "Cyberpunk 2077"}
```

## 减少

我们可以通过使用`reduce`数组方法实现类似的解决方案。

`reduce`将一系列值聚合成一个值。在我们的例子中，这个聚合值是一个对象文字。

```
const map = games.reduce((map, {id, name}) => {
  map[id] = name;
  return map;
}, {});
```

它从一个空映射开始，然后在每次迭代中添加一个新的键。

```
//initial -> {}
//game 1  -> {1: "Fornite"}
//game 2  -> {1: "Fornite", 2: "RDR2"}
//game 3\. -> {1: "Fornite", 2: "RDR2", 3: "Cyberpunk 2077"}
```

我们可以通过提取出一个带有意图揭示名称的函数的缩减器来提高可读性。

```
function toMappingObject(map, {id, name}) {
  map[id] = name;
  return map;
}const map = games.reduce(toMappingObject, {});
```

## 对象. fromEntries

另一种选择是使用`map`数组方法将数组转换成一个由`[key, value]`对组成的列表

```
const keyValuePairs = games.map(({id, name}) => [id, name]);
console.log(keyValuePairs);
//[
//["1", "Fornite"],
//["2", "RDR2"],
//["3", "Cyberpunk 2077"]
//]
```

`map`使用映射函数将值列表转换为新的值列表。在我们的例子中，映射函数将一个游戏对象转换成一个`[key, value]`对。

```
const mapToKeyValuePair = ({id, name}) => [id, name];
```

然后我们可以使用`Object.fromEntries`实用程序将该对列表转换成一个对象文字。

```
const map = Object.fromEntries(keyValuePairs);
console.log(map);
//{1: "Fornite", 2: "RDR2", 3: "Cyberpunk 2077"}
```

# 将地图对象转换为列表

现在让我们看看如何将地图对象转换成对象列表。

考虑以下将游戏 id 映射到游戏名称的对象。

```
const gamesMap = {
  1: "Fornite", 
  2: "RDR2", 
  3: "Cyberpunk 2077"
};
```

我们想把它转换成一个游戏对象列表，其中属性键成为游戏的`id`，属性值成为游戏对象的`name`。

```
[
  {id:1, name: 'Fornite'},
  {id:2, name: 'RDR2'},
  {id:3, name: 'Cyberpunk 2077'}
]
```

## Object.entries()

`Object.entries()`从一个对象文本返回一个`[key, value]`对列表。

让我们首先做那件事。

```
const keyValuePairs = Object.entries(gamesMap);
console.log(keyValuePairs);
//[
//["1", "Fornite"],
//["2", "RDR2"],
//["3", "Cyberpunk 2077"]
//]
```

## 为每一个

一旦我们有了`[key, value]`对的列表，我们就可以使用`forEach`数组方法循环遍历对象中的所有道具并构建对象列表。

我们从一个空列表`[]`开始，并在每次迭代中添加一个新对象。

```
const gamesArr = [];Object
  .entries(gamesMap)
  .forEach(([key, value]) => {
    gamesArr.push({
      id: key, 
      name: value
    });
 });console.log(gamesArr);
//[
//{id:1, 'Fornite'},
//{id:2: 'RDR2'},
//{id:3: 'Cyberpunk 2077'}
//];
```

## 地图

当你想到我们实际上正在做的是将一个`[key, value]`对映射到一个游戏对象。映射事物的一个更好的选择是`map()`数组方法。下面是我们如何重写前面的转换。

```
const gamesArr = Object
  .entries(gamesMap)
  .map(([key, value]) =>({
    id: key, 
    name: value
  }));
```

通过提取映射函数并给出一个揭示意图的名称，可以提高可读性。

```
function toGameObject([key, value]){
  return {
    id: key, 
    name: value
  };
}const gamesArr = Object
  .entries(gamesMap)
  .map(toGameObject);console.log(gamesArr);
```

# 关键注意事项

对象可以转换成`[key, value]`对，也可以从【】对创建。

使用映射函数，`map` array 方法可以将一个对象数组转换成一个新的`[key, value]`对数组。

同样的`map`方法可以用来将一个`[key, value]`对的数组转换成一个`{key, value}`对象的数组。

感谢阅读。
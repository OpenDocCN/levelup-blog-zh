# 我赖以生存的 9 个 JavaScript 技巧

> 原文：<https://levelup.gitconnected.com/9-javascript-tricks-i-live-by-549165129ca3>

使我的编程生活变得更加容易，它们在编辑器中看起来很酷。

![](img/997c9ade526bb1b4d8e2a500664bcfa5.png)

# 1.解构

```
let weekday = "work", weekend = "roadtrip";[weekday, weekend] = [weekend, weekday]console.log(weekday) // -> "road trip"
console.log(weekend) // -> "work"
```

```
let tests = {
   "science": "pass",
   "math": "fail"
}let {science, math} = tests;console.log(science) // -> "pass"
console.log(math) // -> "fail"
```

# 2.动态属性名称

```
let unit = "celcius";let temperature = {
   [unit]: 24
}console.log(temperature[unit]) // -> 24
```

# 3.地图()

*返回数组中每一项的函数运行结果。*

```
let numbers = [1, 2, 3, 4, 5, 6];let newNumbers = numbers.map(number => number * 2);console.log(newNumbers) // -> [2, 4, 6, 8, 10, 12]
```

# 4.过滤器()

*返回一个数组，该数组只包含根据条件筛选出的项目。*

```
let animals = [
    {type: "dog", age: 6, colour: "black"},
    {type: "cat", age: 10, colour: "gray"},
    {type: "dog", age: 13, colour: "golden brown"},
    {type: "hamster", age: 4, colour: "white and brown"}
];let filteredAnimals = animals.filter(animal => animal.type === "dog");console.log(filteredAnimals) // -> 
[ 
    {type: "dog", age: 6, colour: "black"},
    {type: "dog", age: 13, colour: "golden brown"}
]
```

# 5.模板文字

```
let whyIsItAwesome = "Because you can do "+ this + "and " + thatBecomes:let whyIsItAwesome = `Because you can do ${this} and ${that};`console.log(whyIsItAwesome) // -> Because you can do this and that
```

# 6.短路条件

```
sunny && goToTheBeach()The equivalent of:if(sunny) {
  goToTheBeach()
}
```

# 7.扩展运算符

*将一个(或多个)数组中的值扩展到另一个数组中，形成一个数组。*

```
let list = ["Pizza", "Milk", "Cheese", "Avocados"]let additional = ["Yoghurt"]let finalList = [...list, ...additional, "Wine"] console.log(finalList) // -> ["Pizza", "Milk", "Cheese", "Avocados", "Yoghurt", "Wine"]
```

# 8.一些()

如果数组中至少有一个**元素满足传入的条件，则*返回 true。否则为假。***

```
*let numbers = [1, 3, 6, 9, 12, 15, 25, 36, 43, 56, 62, 70];let over20 = numbers.some(number => number > 20);console.log(over20) // -> true*
```

# *9.包括()*

```
*let colours = ["blue", "red", "yellow", "green"];let hasBlue = colours.includes("blue");console.log(hasBlue) // -> true*
```

*还有其他你离不开的人吗？欢迎在评论中发表！*
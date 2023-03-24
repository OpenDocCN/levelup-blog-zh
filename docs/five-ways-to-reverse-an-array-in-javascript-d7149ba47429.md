# Javascript 中反转数组的五种方法

> 原文：<https://levelup.gitconnected.com/five-ways-to-reverse-an-array-in-javascript-d7149ba47429>

![](img/df5c40250d6cc42eef024b5b9d27ca35.png)

我们可以通过修改源数组或不修改源数组来反转数组。我们将研究实现每个目标的不同方法。

# 通过修改源数组来反转数组

我们可以使用 JavaScript 数组可用的`reverse`方法。

```
var numbers = [ '1️⃣' , '2️⃣', '3️⃣', '4️⃣', '5️⃣' ];

numbers.reverse();

console.log(numbers); // [5️⃣, 4️⃣, 3️⃣, 2️⃣, 1️⃣] 
```

# 在不修改原始数组的情况下反转

1.使用普通 for 循环

```
var numbers = [ '1️⃣' , '2️⃣', '3️⃣', '4️⃣', '5️⃣' ];var reversedNum = [];for(let i =  numbers.length -1; i >= 0; i--) { reversedNum.push(numbers[i]);

}
```

2.使用`map`方法和`unshift`阵列方法。`unshift`将值推到数组的第`0`个位置。

```
var numbers = [ '1️⃣' , '2️⃣', '3️⃣', '4️⃣', '5️⃣' ];var reversedNum = [];

numbers.map((val) => { reversedNum.unshift(val);});
```

3.使用`slice`和`reverse`

```
var numbers = [ '1️⃣' , '2️⃣', '3️⃣', '4️⃣', '5️⃣' ];

var reversedNumbers = numbers.slice().reverse();
```

4.使用扩展运算符和`reverse`

```
var numbers = [ '1️⃣' , '2️⃣', '3️⃣', '4️⃣', '5️⃣' ];

var reversedNumbers = [...numbers].reverse();
```

5.使用扩展运算符和`reduce`

```
var numbers = [ '1️⃣' , '2️⃣', '3️⃣', '4️⃣', '5️⃣' ];

let reversedArray = [];numbers.reduce( (reversedArray, value) => { return [value , ...reversedArray];}, reversedArray);
```

在 JS 中还有很多其他的方法来反转一个数组。请在评论中分享你最喜欢或最感兴趣的方法。

请跟随我 [Javascript Jeep🚙💨](https://medium.com/u/f9ffc26e7e69?source=post_page-----98efbae5e8aa----------------------)。

请在这里捐款[。你捐款的 80%捐给了需要食物的人🥘。提前感谢。](https://www.paypal.com/paypalme2/jagathishSaravanan)
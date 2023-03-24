# 一些 JavaScript 技巧

> 原文：<https://levelup.gitconnected.com/a-few-javascript-hacks-cf2b11df84ca>

![](img/64c60875b78965cf773892459e70879a.png)

JavaScript 是最流行的编程语言，与 HTML 和 CSS 一样，是网络的核心技术之一。它简单易学，可用于客户端和服务器端开发。

这里有一些 JavaScript (JS)技巧，可以提高代码的效率。

1.  **从数组中获取唯一值**

```
const numbers = [ 1, 1, 2, 3, 4, 4, 5]

const uniqueNumbers = [ ...new Set(numbers)]

console.log(uniqueNumbers) // [1, 2, 3, 4, 5]
```

使用 [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set) 对象，我们可以从数组中删除重复项。在上面的例子中，我们使用了 [Spread](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) 操作符(…)来包含数组中的所有元素(在我们的例子中是`numbers`)。

2.**将数字转换为字符串**

```
let a = 2

let s1 = a.toString()
let s2 = String(a)
let s3 = '' + a
let s4 = `${a}`

console.log(s1, typeof s1) //2 string
console.log(s2, typeof s2) //2 string
console.log(s3, typeof s3) //2 string
console.log(s4, typeof s4) //2 string
```

您可以选择上述四种方法之一将整数或浮点数转换为字符串类型。第三种方法是连接一个空字符串，这是最简单的方法之一。

3.**转换为数字**

```
const toNumber = (str) => ( +str)

console.log(toNumber('test')) //NaN
console.log(toNumber('3')) //3
console.log(toNumber(new Date())) //1671598549947

/*------------------------*/

console.log(~~('test')) //0
console.log(~~('3')) //3
```

您可以像上面的例子一样使用+运算符将字符串数字转换成数字。否则会返回`NaN` (不是数字)。它也可以转换`Date`，但是会返回时间戳。

您还可以使用操作符`~~`将字符串数字转换成数字。在这种情况下，字符串文本将作为`0`返回。

4.**条件句快捷键**

```
//normal way
if(someCondition) {
  console.log("I'm in")
}

//easier and shorter
someCondition && console.log("I'm in") // I'm in

/*------------------------*/

//normal way
if(data) {
  return data
}
else{
  return 'no data'
}

//easier and shorter
return (data || 'no data')
```

您可以使用逻辑 AND ( `&&`)和 OR ( `||`)运算符来缩短`if else`条件。

4.**默认值使用 OR (** `**||**` **)运算符**

```
function Car(make, year){
  this.make = make || 'Toyota'
  this.year = year || 2000
}

let carOne= new Car('Ford', 1998)
console.log(carOne.make, carOne.year)  //Ford 1998

let carTwo = new Car()
console.log(carTwo.make, carTwo.year)  //Toyota 2000
```

您可以使用 OR ( `||`)运算符，并将默认值设置为要使用的第二个参数。如果第一个参数返回 false 或未定义，则第二个参数将用作默认值。

5.**使用**T10 清空数组或调整数组大小

```
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9]
console.log(numbers.length) //9

//Resize
numbers.length = 4
console.log(numbers, numbers.length) //[1, 2, 3, 4] 4

//Empty
numbers.length = 0
console.log(numbers, numbers.length) //[] 0
```

您可以使用数组实例的`[length](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/length)`数据属性来调整数组大小或清空数组。

6.**将大型数组合并在一起**

```
//small data sets
let first = [ 1, 2, 3, 4, 5]
let second= ['a','e','i', 'o', 'u']

console.log(first.concat(second)) //[1, 2, 3, 4, 5, 'a', 'e', 'i', 'o', 'u']

//for large data sets
console.log(first.push.apply(first, second)) //10
console.log(first) //[1, 2, 3, 4, 5, 'a', 'e', 'i', 'o', 'u']
```

您可以使用`Array.concat()`函数或`Array.push.apply()`来合并数组。但是对于大型数据集，使用`Array.push.apply()`(例如:`arr1.push.apply(arr1, arr2)`)，它将第二个数组合并到第一个数组中，而无需创建新的数组，也不会像`Array.concat`那样消耗大量内存。

7.**转换为布尔型**

```
const thanks = "Thanks for reading!"

console.log(!!thanks) //true
console.log(Boolean(thanks)) //true
```

您可以在变量前使用双重否定运算符(`!!`)将任何类型的数据转换为布尔值。只有当它有这些值`0, null, " ", undefined or NaN`时，它才会返回 false，否则，它将返回 true。

您还可以使用`Boolean()` 函数将传递的值转换成布尔值。

8.**将二维数组转换为一维数组**

```
let doubleD = [1, [2, 3, 4, 5], [6, 7], 8, 9]
console.log([].concat(...doubleD)) //[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

您可以使用 spread 运算符将二维数组转换为一维数组。

9.**动态键/属性名**

```
const dynamicKey = 'year'
let Car = {
  make: 'Toyota',
  [dynamicKey]: 1998
}

console.log(Car) //{ make: "Toyota", year: 1998 }
```

上面的例子显示了我们如何为对象设置动态键名。

10. **Foreach 循环**

```
//normal way
for(let i = 0; i< data.length; i++)

//easier way
for(let i in data)
for(let i of data)
```

编码快乐！

我们感谢您阅读这篇文章。如果你喜欢我的文章，但不是 Medium 的会员，你可以注册一个 Medium 会员资格，可以无限制地访问所有内容，并支持我们作为作家。

[](https://sagar-shrestha.medium.com/membership) [## 加入我的推荐链接-萨加尔 Shrestha 媒体

### 阅读 Sagar Shrestha(以及媒体上成千上万的其他作家)的每一个故事。您的会员费直接支持…

sagar-shrestha.medium.com](https://sagar-shrestha.medium.com/membership)
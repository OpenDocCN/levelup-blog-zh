# JavaScript 中的主析构值

> 原文：<https://levelup.gitconnected.com/mastering-destructing-values-in-javascript-68a4e3b7b79>

## 了解如何在 JavaScript 中将数组和对象属性析构为变量。

![](img/c88cba37912289a904f942190fd5e064.png)

图片来自 Unsplash

析构语法允许我们根据属性名从对象或数组中提取多个值。

```
 **// Destructing array**
var num = [1,2,3,4,5];var [a, b] = num;console.log(a, b); //1,2 **// Destructing Object** 

var obj = {  name : "JavaScript Jeep", work : "Blogging"};

var {name, work} = obj;console.log(name, work); // "Javascript Jeep", "Blogging"
```

# 数组析构

数组中的值根据它们的索引进行分解。该变量可以被命名为您想要的任何名称。与索引相关联的变量名是如何分配的。

```
var fruits = ["🍎", "🍌", "🍍"];**var [apple, banana, pineapple] = fruits;**console.log(apple, banana, pineapple); // "🍎", "🍌", "🍍"
```

我们也可以给已经声明的变量赋值。

```
var apple = "apple";var banana = "banana";

**[apple, banana] = ["🍎", "🍌"]**console.log(apple, banana); // "🍎", "🍌"
```

如果变量多于数组长度，那么剩余变量的值变成`undefined`。

```
var arr = [1] **var [a, b] = arr;** console.log(a, b); 1, undefined
```

**在析构中使用 rest 运算符:**

```
var numbers = [1,2,3,4,5,6]; **var [a, b, ...rest] = numbers;** console.log(a, b, rest);  // 1, 2, [3,4,5,6]
```

rest `...`操作符允许您累积未被析构的剩余项。

使用 rest 运算符时，`rest`元素应该是最后一个元素，否则会抛出`Syntax Error`。

```
**var [a, ...b, c] = [1, 2, 3]; // error**

**var [a, ...b,] = [1, 2, 3]; // error because trailing comma after rest element**
```

**为变量设置默认值:**

```
var fruits = ["🍎", "🍍"];

                      **//without default value****var [apple, pineapple, banana ] = fruits;**console.log(apple, pineapple, banana); // "🍎", "🍍", undefined                       **// with default value**var [apple, pineapple, **banana= "🍌"** ] **=** fruits**;**console.log(apple, pineapple, banana); // "🍎", "🍍", ** "🍌"**
```

**跳过数组的值**

```
var num = [1,2,3];var [**one,,three**] = num;console.log(one, three); 1, 3
```

**提示:通过数组析构，我们可以很容易地交换值**

```
var a = 10,b = 20;

**[b, a] = [a, b]**

console.log(a, b); 20, 10
```

# 对象析构

我们可以使用对象析构从对象的属性中提取数据。这里，变量的名称与对象的属性相匹配，相应的属性值从对象中检索并赋给变量。

```
var user = {name : "Ram", age : 20};

**var {name, age} = user;**console.log(name, age); // "Ram", 20
```

示例 2:

```
**var {name, age} = {name: "Ram", age : 20};**console.log(name, age); // "Ram", 20
```

如果变量名与对象的属性不匹配，那么不匹配变量的值将是`undefined`。

```
var {name, age, **salary**} = {name: "Ram", age : 20};**salary**; //undefined
```

**使用 rest 运算符**

```
var user = {name: "Mike", age : 30, weight : 50}; var {name, **...details**} = user; console.log(name, details);  // **"Mike" , {age: 30, weight : 30}**
```

**析构一个已经声明的变量**

```
var name , age; var user = {name: "Ram", age : 20}; 
```

我们不能像`{name, age} = user;`那样做，因为“{”是第一个标记，所以 JavaScript 认为它是块的开始。

为了解决这个问题

```
**({name, age} = user);**
```

**给新变量名赋值**

```
var user = {name : "Ram"};var {**name: userName**} = user;

userName; // "Ram"
```

上面的代码从`user`中获取`name`属性，创建一个名为`userName`的新变量，并将值赋给`userName`变量。

提示:当对象的属性是无效的标识符名称时，我们可以使用新的变量名

```
var user = {**"user-name"** : "Mike"}; var {**"user-name": userName**} = user;userName; //Mike
```

**分配默认值**

如果属性不在对象中，则可以将默认值赋给变量。

```
var user1 = {name : "Mike", nickName : "mic"}; var {name1, **nickName1="Noob Master"**} = user1;console.log(**nickName1**); // "mic"
 **//If the property not present** var user2 = {name : "Ram"}; var {name2, **nickName2="Noob Master"**} = user2; console.log(**nickName2**); //**"Noob Master"**
```

**使用默认值和新变量名**

```
var user = {}; var { **name: userName = "Default Name"** } = user; console.log(userName); // "**Default Name**"
```

**使用对象析构作为函数参数**

*不析构*

```
function logUser(user) 
{   
    var name = user.name;   
    var age = user.age;   
    console.log(name, age); 
} var user = {name : "Mike", age : 20}; logUser(user); // "Mike", 20
```

上面的代码可以简化为

```
function logUser( **{name, age}** ) 
{   
    console.log(name, age); 
} var user = {name : "Mike", age : 20}; logUser(user); // "Mike", 20
```

**在函数参数析构中使用默认值和新变量名**

```
function logUser( **{name:userName, age: userAge = 30}** ) 
{   console.log(**userName**, **userAge**); } var user = {name : "Mike"}; logUser(user); // "Mike" , 30
```

如果我们在没有任何参数的情况下调用方法，上面的方法`logUser`将会抛出一个错误。如果我们不传递任何参数，它试图析构`undefined`,因此抛出错误。要解决这个问题:

```
function logUser({name: Name="Jack", age: Age = 30} **= {}**) 
{

    console.log(Name, Age);} logUser(); // Jack 30
```

谢谢😊 🙏为了阅读📖。

关注我 [JavaScript 吉普🚙💨](https://medium.com/u/f9ffc26e7e69?source=post_page-----98efbae5e8aa----------------------)。

请在这里捐款[。你捐款的 80%捐给了需要食物的人🥘。提前感谢。](https://www.paypal.com/paypalme2/jagathishSaravanan)
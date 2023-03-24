# JavaScript 中的 30 个必读主题

> 原文：<https://levelup.gitconnected.com/30-must-know-topics-in-javascript-2b6a998f92de>

你知道如何在 JavaScript 中使用 if-else、for 循环和函数吗？你知道吗？太好了。因为下面的 30 个话题会让你成为 JavaScript 忍者！

![](img/c6726448200e999c66d731cc27d9575e.png)

由[克莱门特 Photo】在](https://unsplash.com/@clemhlrdt?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) [Unsplash](https://unsplash.com/s/photos/web-development?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

# 1. **Array.splice()**

Array.splice()方法可用于通过添加新元素或替换现有元素来修改现有数组。它需要三个参数。第一个是起始索引。第二个参数决定应该删除多少个元素。如果我们只想在某个位置插入一个元素，它就保持为 0。最后一个参数是新元素。

```
const iftar = ['Chola', 'Piyaju', 'Beguni', 'Muri'];
iftar.splice(1, 0, 'Shorbot');
console.log(iftar);
iftar.splice(4, 1, 'Halim');
console.log(iftar);
```

# 2. **Array.every()**

Array.every()方法用于检查数组中的所有元素是否都通过了回调函数中实现的测试。如果所有元素都返回 True 值，则该方法返回 True。否则它将返回 false。

```
const isAdult = curAge => curAge > 18;
const ageList = [19, 30, 39, 29, 100, 23];
console.log(ageList.every(isAdult));        // true
```

# 3. **String.replace()**

string.replace()方法有两个参数。第一个是要替换的模式，第二个是替换。模式可以是字符串或正则表达式，替换可以是新字符串或函数。RegExp 可以替换多个匹配项，但 string pattern 只会替换第一个匹配项。

```
const text = 'To be, or not  to be';
const regex = /be/gi;console.log(text.replace(regex, 'eat'));    // To eat, or not to eat
console.log(text.replace('be', 'eat'));     // To eat, or not to be
```

# 4.String.indexOf()

String.indexOf(searchValue [，fromIndex])返回 searchValue 的第一个匹配项的索引或-1。
但是当我们向 searchValue 传递一个空字符串时，它会奇怪地工作。它总是返回我们从 Index 作为**传递的任何东西，只要它不大于字符串的长度。**

```
'hello world'.indexOf('')          // returns 0
'hello world'.indexOf('', 8)      // returns 8
```

如果 **fromIndex** 的值大于返回值将等于字符串的长度。

```
'hello world'.indexOf('', 15)    // returns 11
```

# 5.**伊斯南()**

在 JS 中，最奇特的一个值是**楠**。等式运算符很不可靠，因为 **NaN == NaN** 和 **NaN===NaN** 返回 **false** ！

看起来 JS 有问题，但是再想想。发生这种情况是因为 **NaN** 没有引用特定的值。**(0/0)****(无穷大/无穷大)**都会返回 **NaN** 。但我们可以清楚地看到，它们并不相同。

由于这个原因，不可能说两个 nan 是否相等。
这使得 **isNaN()** 开始发挥作用，因为它使得识别一个值是否为 NaN 成为可能。

```
isNaN('this is a string')    // true
isNaN(234324)                // false
```

# 6.休息参数

rest 参数运算符用于函数参数列表，格式为:**…变量**，它将在该变量中包含调用函数时使用的未捕获参数的完整列表。

```
function avg(...args) {
  var sum = 0;
  for (let value of args) {
    sum += value;
  }
  return sum / args.length;
}
avg(2, 3, 4, 5);    // 3.5
```

# 7.符号()

ECMAScript 2015 中引入的符号是 JavaScript 的原始数据类型。与其他原始数据类型不同，符号没有文字形式。符号是用 **Symbol()** 调用可选描述(名称)创建的。符号保证是唯一的。即使我们创造了许多描述相同的符号，它们也是不同的值。描述只是用于调试目的的标签。

```
let id1 = Symbol("id")
let id2 = Symbol("id")
console.log(id1 == id2)      // false
```

# 8.客户端存储

客户端存储允许我们在客户端存储数据，并在需要时检索数据。这有许多不同的用途，例如:

*   个性化站点首选项
*   持续以前的站点活动
*   在本地保存数据和资产
*   在本地保存 web 应用程序生成的文档

# 9.客户端存储的类型

*   Cookies(过时、不安全、存储简单数据)
*   Web 存储 API(存储简单的数据，数据存储在两个类似对象的结构中，称为 sessionStorage 和 localStorage)
*   IndexedDB API(用于存储复杂数据的完整数据库)
*   缓存 API(离线资产存储)

# 10.服务工作者和缓存

**服务人员**充当网络应用、浏览器和网络(如果可用的话)之间的代理服务器。它们用于以下任务:

*   有效的线下体验
*   拦截网络请求
*   更新驻留在服务器上的资产

**缓存**接口为被缓存的请求/响应对象对提供了一种存储机制。它们通常被用作 ServiceWorker 生命周期的一部分。

# 11.**DOM 的节点类型**

在文档对象中，总共有十二个节点。但是其中只有四种在实践中使用。它们是:

*   **文档:**它是 DOM 的入口点
*   **元素节点:**这些 HTML 元素是 DOM 树的构建块
*   **文本节点:**这些是文本
*   **注释:**这些是 JS 可以从 DOM 中读取的隐藏信息

# 12.**文档片段**

DocumentFragment 是一种特殊的 DOM 节点，它作为包装器传递节点列表。每当我们插入 DocumentFragment 时，只有它的内容被插入。

```
<ul id="ul"></ul>
<script>
  let fragment = new DocumentFragment()
  for(let i = 1; i <= 3; i++) {
    let li = document.createElement('li')
    li.append(i)
    fragment.append(li)
  }
  ul.append(fragment)
</script>
```

# 13. **getComputedStyle**

getComputedStyle()方法获取指定元素的所有实际的(解析的)CSS 属性和值。因为不可能读取来自 CSS 类的任何内容，所以 getComputedStyle()对于获取样式信息是必要的。

```
<head>
 <style> body { margin: 10px } </style>
</head>
<body>
 <script>
   let computedStyle = getComputedStyle(document.body); alert( computedStyle.marginTop ); // 10px
 </script>
</body>
```

# 14.**相等运算符**

在 JS 中，有两种方法可以检查两个值是否相等。

**相等运算符(==):** 相等运算符在比较操作数之前进行类型强制，这会导致奇怪的输出和性能问题。

```
'1'  ==  1         // true
1    == '1'        // true
0    == false      // true
0    == null       // false
```

**严格相等运算符(= = =):
严格相等运算符在没有任何类型强制的情况下比较操作数。**

```
3 === 3   // true
3 === '3' // false
```

# 15.**类型**

typeof 运算符返回指示操作数类型的字符串。这可以说是 javascript 语言最大的设计缺陷，应该不惜一切代价避免。不管怎样，检查一个变量是否被声明是有用的。

```
typeof foo !== 'undefined'
```

# 16.**评估**

**eval()** 是 JavaScript 中的一个全局函数，将指定的字符串作为 JavaScript 代码进行求值并执行。如果直接调用，它将在局部范围内执行代码。否则，它将在全局上下文中执行代码，这可能会导致一些错误。它也有安全问题，因为它执行任何给它的代码。应避免使用 eval()。

```
function test() {
  var x = 2, y = 4;
  console.log(eval('x + y'));  // uses local scope, 6
  var geval = eval;
  console.log(geval('x + y')); // uses global scope, throws an error
}
```

# 17.**未定义**

undefined 是具有未定义初始值的全局对象的属性。在现代浏览器中，它是一个不可配置和不可写的属性。对于以下情况，我们可以得到未定义:

*   访问未定义的全局变量
*   访问已声明的*但未初始化的*变量
*   调用没有返回语句的函数
*   无所回报地返回
*   不存在的对象属性
*   没有任何显式值的函数参数

# 18.**删除操作员**

**删除**操作符用于从一个对象中删除一个属性。但是它不适用于全局变量或函数。如果一个变量被指定为全局对象的属性，它仍然可以被删除。

```
const a = 1;       // DontDelete is set
delete a;          // false
a;                 // 1
var obj = {x: 1};
delete obj.x;      // true
obj.x;             // undefined
this.a = 4;
delete a;          // true
a;                 // undefined
```

# 19.**吊装**

提升是一种 JavaScript 机制，在代码执行之前，变量和函数声明被移动到它们作用域的顶部。这意味着无论在哪里声明函数和变量，无论它们的作用域是全局的还是局部的，它们都被移动到作用域的顶部。

```
a = 5;
console.log(a);  // Error

b = 5;
console.log(b);  // 5
var b;
```

# 20.**自动插入分号**

JavaScript 不是一种没有分号的语言，但是一个叫做自动插入分号的特性允许它像一个语言一样工作。每当由于缺少分号而出现解析器错误时，它会自动插入一个分号。这可能会由于不正确的分号插入而导致一些错误。因此，我们应该在代码中明确使用分号。

```
console.log(3)
(console.log(4))  // TypeError: console.log(...) is not a function
```

# 21.**类声明**

**类声明**是 JS 中定义类的两种方法之一。它以 class 关键字开始，后跟类名和一对花括号。

```
class Phone {
   constructor(name){
       this.name = name
   }

   getModel(){
       console.log(this.name)
   }
}
let motorola = new Phone('razr')
motorola.getModel()
```

# 22.**课堂用语**

类表达式是 JS 中定义类的第二种方式。有两种类型的类表达式，命名的和未命名的。

```
//unnamed
let Circle = class {
   constructor(radius){
       this.radius = radius
   }

   getArea(){
       return Math.PI * this.radius * this.radius;
   }
}
//named
let Circle = class Circle2 {
   constructor(radius){
       this.radius = radius
   }

   getArea(){
       return Math.PI * this.radius * this.radius;
   }
}
```

# 23.析构赋值

在 JS 的对象析构中，可以将其用作赋值。但是有一点我们应该记住，析构赋值语句必须用括号括起来。这是因为 JS 默认将花括号视为 block 语句，block 语句不能在赋值操作符的左边。

```
let person = {
   name: 'john',
   age: 100
}
let name = 'doe';
let age = 50;
({name,age} = person);
console.log(name,age);    // john, 100
```

# 24.**克隆阵列**

在引入 ES6 之前，concat()是 JS 开发人员中克隆数组最流行的选择。虽然 concat()是用来连接两个数组的，但是向它传递一个空参数会创建一个新的数组克隆。

```
var numbers = [1,2,3];
var newNumbers = numbers.concat();
console.log(newNumbers);              // [1,2,3]
```

自从 ES6 问世以来，使用 **Rest 项目**，克隆阵列变得更加直观。

```
let numbers = [1,2,3];
let [...newNumbers] = numbers;
console.log(newNumbers);              // [1,2,3]
```

# 25.**太阳穴死区**

临时死区是 JS 中的一种行为，它发生在用 let 和 const 关键字声明变量时。在 JS 中，let 和 const 不提升。在声明之前在变量的作用域内调用这些变量将会引发错误。他们范围内的这个区域被称为时间死区。在它们的作用域之外调用这样的变量不会抛出任何错误，即使在它们被声明之前。

```
// TDZ
if (true) {
   console.log(typeof value);  // ReferenceError!
   let value = "blue";
}
//NON TDZ
console.log(typeof value);      // undefined
if (true) {
   let value = "blue";
}
```

# 26.**面向对象编程**

面向对象编程，通常称为 OOP，是一种编程风格，其中一个程序被分成几个可以相互通信的对象段。每个对象可能有自己的一组属性和方法。JavaScript 中有 3 个核心概念。

1.  包装
2.  多态性
3.  遗产

# 27.**事件循环**

尽管 JavaScript 被认为是单线程语言，但事件循环使它能够单独处理阻塞代码，如超时和数据获取。它使用事件表和事件队列以及现有的调用堆栈来实现这一点。这是一个不断运行的过程，它检查调用堆栈是否为空。当调用堆栈为空时，它会检查事件队列中是否有内容，并将其移动到调用堆栈中。如果没有，它什么也不做。

# 28.**调用堆栈**

在 JavaScript 中，调用堆栈是一种数据结构，它使用后进先出(LIFO)原则来临时存储和管理函数调用(call)。

```
function firstFunction(){
   throw new Error('Stack Trace Error');
}
function secondFunction(){
   firstFunction();
}
function thirdFunction(){
   secondFunction();
}
thirdFunction();
//Stack Trace Error! But it also represents the corresponding call //stack
//    at firstFunction (<anonymous>:2:11)
//    at secondFunction (<anonymous>:5:5)
//    at thirdFunction (<anonymous>:8:5)
//    at <anonymous>:10:1
```

# 29.**高阶函数**

在 JavaScript 中，接受回调函数作为参数的函数称为高阶函数。例如，map、filter 和 reduce 就是 JS 中内置的高阶函数的例子。

```
const numbers = [1,2,3,4,5];
const total = numbers.reduce((a,b)=>a+b,0);
console.log(total)    // 15
```

# 30.**阿谀奉承**

Currying 是将一个具有多个参数的函数分解成一系列具有单个参数的函数的过程。

```
//without currying
function add (a, b) {
 return a + b;
}
add(2+3)    // 5
//with currying
function add (a) {
 return function (b) {
   return a + b;
 }
}
add(2)(3)   // 5
```
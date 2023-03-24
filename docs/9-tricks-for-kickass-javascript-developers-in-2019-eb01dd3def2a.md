# 2019 年 Kickass JavaScript 开发者的 9 招

> 原文：<https://levelup.gitconnected.com/9-tricks-for-kickass-javascript-developers-in-2019-eb01dd3def2a>

![](img/9b6be81abc7b2c4c367c6f3195723145.png)

安德鲁·沃利在 [Unsplash](https://unsplash.com/search/photos/nerd?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

又一年过去了，JavaScript 一直在变化。然而，有一些技巧可以帮助你编写干净有效的代码，甚至(或者可能特别？)2019 年。下面列出了 9 个实用的技巧，可以让你成为更好的开发者。

# 1.异步/等待

如果你仍然被困在回调地狱，2014 年想要回它的代码。只是不要使用回调，除非它是绝对必要的，例如库所要求的或者性能原因。承诺是好的，但是如果你的代码库变大了，使用起来就有点尴尬了。我现在最常用的解决方案是 async / await，这使得阅读和改进我的代码更加简洁。事实上，你可以在 JavaScript 中`await`每一个承诺，如果你正在使用的库返回一个承诺，只需`await`它。事实上，async/await 只是承诺的语法糖。为了让你的代码工作，你只需要在一个函数前面加上`async`关键字。这里有一个简单的例子:

注意顶层的`await`是不可能的，你只能调用一个`async`函数。

*async / await 是 ES2017 中引入的，所以请确保传输您的代码。*

# 2.异步控制流

通常，在所有异步调用都返回值之后，有必要获取多个数据集，并为每个数据集做一些事情，或者完成一个任务。

## 为了…的

假设我们的页面上有几个口袋妖怪，我们必须获取它们的详细信息。我们不希望等待所有的调用都结束，尤其是当我们不知道有多少个调用时，但是我们希望在得到一些回报时立即更新我们的数据集。我们可以使用`for...of`循环遍历一个数组，并在块内执行`async`代码，执行将被暂停，直到每个调用成功。需要注意的是，如果您在代码中做了类似的事情，可能会出现性能瓶颈，但是将其保留在工具集中是非常有用的。这里有一个例子:

> 这些例子实际上是可行的，请随意复制并粘贴到您最喜欢的代码沙箱中

## 承诺。所有

如果你想并行获取所有的口袋妖怪呢？因为你可以`await`所有的承诺，简单地使用`Promise.all`:

`*for...of*` *和* `*Promise.all*` *是在 ES6+中引入的，所以一定要移植你的代码。*

# 3.析构&默认值

让我们回到上一个示例，执行以下操作:

```
const result = axios.get(`https://ironhack-pokeapi.herokuapp.com/pokemon/${entry.id}`)const data = result.data
```

有一种更简单的方法，我们可以使用析构来从一个对象或数组中取出一个或几个值。我们会这样做:

```
const { data } = await axios.get(...)
```

我们保存了一行代码！耶！您也可以重命名变量:

```
const { data: newData } = await axios.get(...)
```

另一个好的技巧是在析构时给出默认值。这确保了你永远不会以`undefined`结束，你也不必手动检查变量。

```
const { id = 5 } = {}console.log(id) // 5
```

这些技巧也可以用于函数参数，例如:

这个例子一开始可能看起来有点混乱，但是不要着急，慢慢来。当我们没有将任何值作为参数传递给函数时，将使用默认值。一旦我们开始传递值，就只使用不存在的参数的默认值。

*ES6 引入了析构和缺省值，所以一定要转换你的代码。*

# 4.真假价值观

使用默认值时，对现有值的一些检查将成为过去。然而，很高兴知道你可以用真值和假值工作。这将改进您的代码，并为您节省一些指令，使它更有说服力。我经常看到人们做这样的事情:

```
if(myBool === true) {
  console.log(...)
}// ORif(myString.length > 0) {
  console.log(...)
}// ORif(isNaN(myNumber)) {
  console.log(...)
}
```

所有这些都可以简化为:

```
if(myBool) {
  console.log(...)
}// ORif(myString) {
  console.log(...)
}// ORif(!myNumber) {
  console.log(...)
}
```

要真正利用这些陈述，你必须理解什么是真值和假值。这里有一个小的概述:

## 福尔西

*   长度为 0 的字符串
*   数量`0`
*   `false`
*   `undefined`
*   `null`
*   `NaN`

## 真理

*   空数组
*   空对象
*   其他一切

注意，当检查 true/falsy 值时，没有明确的比较，这相当于用两个等号`==`而不是三个`===`进行检查。一般来说，它的行为是一样的，但是在某些情况下，你会遇到一个 bug。对我来说，它大多发生在数字`0`上。

![](img/080dbc029f2895452721126be7fffed2.png)

菲利普·莱昂在 [Unsplash](https://unsplash.com/search/photos/contrast?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 5.逻辑和三元运算符

这些都是用来缩短你的代码，节省你宝贵的代码行。通常它们是很好的工具，有助于保持代码的整洁，但是它们也会造成一些混乱，尤其是在链接它们的时候。

## 逻辑运算符

逻辑运算符基本上组合了两个表达式，将返回`true`、`false`或匹配值，并由表示“与”的`&&`和表示“或”的`||`表示。让我们来看看:

```
console.log(true && true) // trueconsole.log(false && true) // falseconsole.log(true && false) // falseconsole.log(false && false) // falseconsole.log(true || true) // trueconsole.log(true || false) // trueconsole.log(false || true) // trueconsole.log(false || false) // false
```

我们可以将逻辑运算符与我们从最后一点，truthy 和 falsy 值中获得的知识结合起来。使用逻辑运算符时，以下规则适用:

*   `&&`:返回第一个 falsy 值，如果没有，则返回最后一个 true 值。
*   `||`:返回第一个真值，如果没有，则等于最后一个假值。

```
console.log(0 && {a: 1}) // 0console.log(false && 'a') // falseconsole.log('2' && 5) // 5console.log([] || false) // []console.log(NaN || null) // nullconsole.log(true || 'a') // true
```

## 三元运算符

三元运算符非常类似于逻辑运算符，但有三个部分:

1.  这种比较要么是虚假的，要么是真实的
2.  第一个返回值，以防比较结果为真
3.  第二个返回值，以防比较结果为假

这里有一个例子:

```
const lang = 'German'console.log(lang === 'German' ? 'Hallo' : 'Hello') // Halloconsole.log(lang ? 'Ja' : 'Yes') // Jaconsole.log(lang === 'French' ? 'Bon soir' : 'Good evening') // Good evening
```

# 6.可选链接

您是否遇到过这样的问题:在不知道对象或子属性是否存在的情况下，访问嵌套的对象属性？您可能会得到这样的结果:

```
let data
if(myObj && myObj.firstProp && myObj.firstProp.secondProp && myObj.firstProp.secondProp.actualData) data = myObj.firstProp.secondProp.actualData
```

这很繁琐，有一个更好的方法，至少是一个建议的方法(继续阅读如何启用它)。它被称为可选链接，工作方式如下:

```
const data = myObj?.firstProp?.secondProp?.actualData
```

我认为这是检查嵌套属性的一种很好的方式，并且使代码更干净。

*目前，可选链接不是官方规范的一部分，而是作为一项实验性功能处于第一阶段。你得在你的 babelrc 中添加*[*@ babel/plugin-proposal-optional-chaining*](https://babeljs.io/docs/en/babel-plugin-proposal-optional-chaining)*才能使用。*

# 7.类属性和绑定

在 JavaScript 中绑定函数是一项常见的任务。随着 ES6 规范中箭头函数的引入，我们现在有了一种自动将函数绑定到声明上下文的方法——非常有用，并且在 JavaScript 开发人员中普遍使用。当类第一次被引入时，你不能再真正使用箭头函数，因为类方法必须以特定的方式声明。我们需要在其他地方绑定函数，例如在构造函数中(React.js 的最佳实践)。我总是被先定义类方法，然后绑定它们的工作流所困扰，过一段时间后，这看起来很可笑。使用 class property 语法，我们可以再次使用箭头函数，并获得自动绑定的优势。箭头函数现在可以在类内部使用。下面是一个绑定了`_increaseCount`的例子:

目前，职业属性并不是官方规范的一部分，而是作为一个实验性的特性处于第三阶段。你得在你的 babelrc 中添加[*【babel/plugin-proposal-class-properties*](https://babeljs.io/docs/en/next/babel-plugin-proposal-class-properties.html)*才能使用。*

# 8.使用包裹

作为一名前端开发人员，您几乎肯定遇到过捆绑和传输代码。很长一段时间以来，事实上的标准是 webpack。我第一次使用 webpack 是在版本 1 中，那时候很痛苦。摆弄所有不同的配置选项，我花了无数个小时试图让捆绑运行起来。如果我能做到的话，为了不打碎任何东西，我不会再碰它了。几个月前，我偶然发现了[包裹](https://parceljs.org/)，这让我立即松了一口气。它几乎为你做了开箱即用的所有事情，同时如果有必要的话，你还可以改变它。它还支持一个插件系统，类似于 webpack 或 babel，速度非常快。如果你不知道包裹，我绝对建议你去看看！

# 9.自己多写代码

这是一个很好的话题。对此我有过很多不同的讨论。即使对于 CSS，很多人倾向于使用像 bootstrap 这样的组件库。对于 JavaScript，我仍然看到有人使用 jQuery 和小型库进行验证、滑动条等等。虽然使用库是有意义的，但我强烈建议自己编写更多的代码，而不是盲目安装 npm 包。当有大型库(甚至框架)时，整个团队都在构建它，例如 [moment.js](https://momentjs.com/) 或 [react-datepicker](https://reactdatepicker.com/) ，尝试自己编码是没有意义的。但是，您可以自己编写正在使用的大部分代码。这将给你带来三大优势:

1.  您确切地知道代码中发生了什么
2.  在某个时候，你会开始真正理解****编程以及幕后的工作原理****
3.  ****您可以防止代码库变得臃肿****

****一开始，使用 npm 包更容易。自己实现某些功能需要更多的时间。但是如果这个包不能像预期的那样工作，你不得不换另一个包，花更多的时间阅读他们的 API 是如何设置的，那该怎么办呢？当你自己实现它时，你可以根据你的用例百分之百地定制它。****

*****关于作者:Lukas Gisder-Dubé作为 CTO 共同创立并领导了一家初创公司 1 年半，建立了技术团队和架构。离开创业公司后，他在*[*iron hack*](https://medium.com/u/1ff093a3da32?source=post_page-----eb01dd3def2a--------------------------------)*担任首席讲师教授编码，现在正在柏林建立一家创业机构&咨询公司。查看*[*Dube . io*](https://dube.io)*了解更多。*****

****![](img/840b0ca008c4fa8e0a1d00791a6c9f9f.png)********[![](img/ff5028ba5a0041d2d76d2a155f00f05e.png)](https://levelup.gitconnected.com/)********[](https://gitconnected.com/learn/javascript) [## 学习 JavaScript -最佳 JavaScript 教程(2019) | gitconnected

### 排名前 64 的 JavaScript 教程。课程由开发者提交并投票，使您能够找到最好的…

gitconnected.com](https://gitconnected.com/learn/javascript)****
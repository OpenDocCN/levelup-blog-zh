# 使用 Monty Python 解释 JavaScript 构造函数

> 原文：<https://levelup.gitconnected.com/using-monty-python-to-explain-javascript-constructor-functions-b0926908c6a7>

在我们开始之前，让我先说一下房间里的大象。

![](img/9a9289c0e11f3a9d7b7abbad9f8398a4.png)

照片由[卡斯霍尔姆斯](https://unsplash.com/@cas1111?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

是的，我正在用 Monty Python 来解释一个编码概念。不，我不是在用 Monty Python 解释 Python 概念。

这是对双关语和编码世界的一种可怕的不公正吗？对于一个以制造荒谬的编码双关语而闻名的作家来说，这是不是太出格了？

是啊是啊。对不起

继续前进。

先说圣杯里的[死亡之桥场景。](https://www.youtube.com/watch?v=0D7hFHfLEyk)

在这个场景中，我们有三个骑士:亚瑟爵士、罗宾爵士和加拉哈德爵士。为了过桥，每个人都有一系列必须回答的问题。

亚瑟先走。巨魔问他三个问题:他叫什么名字(“亚瑟”)，他最喜欢什么颜色(“蓝色”)，什么是 quest(“寻找圣杯”)。

罗宾纠结于第二个问题:“你最喜欢的颜色是什么？”被扔进了下面的深处。

加拉哈德得到了最难的问题:“一只空载燕子的空速是多少？”

“什么样的燕子？”他问道。巨魔不知道——也被扔进了下面的深处。电影史上最精彩的时刻就这样结束了。

# 让我们把这个场景变成 JavaScript。

## 步骤 1:创建我们的构造函数

把 JavaScript 构造函数想象成一个切饼刀，或者一个蓝图。这是我们用来创造物体的工具。

![](img/f86d1548898f72a8eb2dc08f187e7189.png)

由[在](https://unsplash.com/@halacious?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的

例如:在桥的场景中，我们有三个骑士。

每个骑士都有一套相同的属性——他们都有一个**名字**，一个**最喜欢的颜色**，并且被问**第三个问题**——但是这些属性的值对于每个骑士都是不同的。

构造函数(即蓝图)创建对象的原型；`new`操作符创建对象本身，它从构造函数继承属性。

## 让我们使用构造函数创建一个名为 Knight 的对象。

```
**// 1) Create the Knight object**
function Knight(name, favoriteColor, thirdQuestion) { **this**.name = name;
    **this**.favoriteColor = favoriteColor;
    **this**.thirdQuestion = thirdQuestion;}
```

这里，构造函数有三个参数:`name`、`favoriteColor`和`question`。

函数内部有三个表达式:

```
**this**.name = name;
**this**.favoriteColor = favoriteColor;
**this**.question = question;
```

这些表达式创建并初始化对象的属性。

例如，`this.name = name`创建并初始化对象`Knight`的属性`name`。关键字`this`允许我们访问该对象的任何实例的`name`属性。一旦初始化了`name`属性，[我们就可以像访问 JavaScript 方法一样访问它(使用‘点’符号)。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Property_accessors)

## 关于“这个”的一个简短说明

`this`是 JavaScript 中众所周知的棘手话题。我们在这里只触及皮毛，但是如果你想了解更多，请查看 MDN 的[这个资源，或者教程老师](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)的[这篇文章。](https://www.tutorialsteacher.com/javascript/this-keyword-in-javascript)

就我们的目的而言，`this`指的是物体本身(即，被讨论的单个骑士)。(关于`this`的棘手之处在于准确理解它所指的对象——这可能会很快变得复杂！)

通过 Giphy 和 Trafalgar 释放

让我们以罗宾爵士为例。我们将创建一个名为`robin`的`Knight`对象。

我们可以使用运算符`new`来实现这一点:

```
**// Creating a Knight object**var robin = **new** Knight('Sir Robin', 'Yellow', 'What is the capital of Assyria?');
```

上面的语句创建了一个名为`robin`的骑士对象，并为其属性赋值‘罗宾爵士’、‘黄色’和‘亚述的首都是哪里？’。

如果我们想打印罗宾最喜欢的颜色呢？我们可以使用下面的表达式，使用点标记法访问“color”属性(就像“this.color”，但是用“Robin”替换“this”):

```
console.log(robin.favoriteColor);
**Output -->** "Yellow"
```

现在，让我们也为亚瑟爵士和加拉哈德爵士创建对象。(你知道，这样我们就不会遗漏它们。)

```
**// Creating Sir Arthur and Sir Galahad instances using Knight object** var arthur = new Knight('Sir Arthur', 'Blue', 'What is your quest?');var galahad = new Knight('Sir Galahad', 'Green', 'What is the airspeed velocity of an unladen swallow?');
```

现在，我们有三个独立的对象。现在，是时候写一个程序来播放桥段了。

我们走吧，朋友们。

通过 Giphy 和 Trafalgar 释放

# 把所有的放在一起

给你，朋友们。同样的场景——但是用的是 JavaScript。

## 步骤 1)用以下元素创建一个基本的 HTML 文档:

*   一个`**h1**` (我们的标题)
*   一个`**h2**` (为我们字幕)
*   `**Three buttons**`，每个都有自己的 id

```
**// HTML body**
<body>
  <h1>Monty Python and the Holy Grail - the Bridge of Death Quiz</h1>
  <p>(using JavaScript)</p>
  <h2 id="crossMessage">Select Your Knight</h2>
  <p></p>
  <button id="robin">Sir Robin</button>
  <button id="arthur">Sir Arthur</button>
  <button id="galahad">Sir Galahad</button>
</body>
```

## 步骤 2)在一个新文件中，为每个按钮创建一个 JavaScript 对象(每个骑士一个)。

这里，我们使用之前创建的 HTML ids 为每个骑士的按钮创建 JS 对象。

```
const robinButton = document.querySelector('#robin');
const arthurButton = document.querySelector('#arthur');
const galahadButton = document.querySelector('#galahad');
```

## 步骤 3)为“骑士”对象创建构造函数

```
function Knight(name, favoriteColor, thirdQuestion) {

  this.name = name;
  this.favoriteColor = favoriteColor;
  this.thirdQuestion = thirdQuestion;

}
```

## 步骤 4)使用“新建”操作符为每个骑士创建对象

```
const robin = new Knight('Sir Robin', 'Yellow', 'What is the capital of Assyria?');
const arthur = new Knight('Sir Arthur', 'Blue', 'What is your quest?');
const galahad = new Knight('Sir Galahad', 'Green', 'What is the airspeed velocity of an unladen swallow?');
```

## 步骤 5)让用户选择一个骑士。

`addEventListener('click', function () {})` →这句话是节目的*主厨之吻*。是它让我们的按钮做我们想让它们做的事情。

在这个函数中，根据用户选择的骑士，我们迭代问题，并显示一个弹出提示，祝贺用户成功过桥，或者将用户“送入深渊”。

```
**// If user clicks 'Sir Robin', iterate through questions**
robinButton.addEventListener('click', function () {
  let nameQuestion = prompt('What is your name?');
  if (nameQuestion != robin.name) {
    alert("Wrong! Into the abyss with ye.");
  } else {
    let colorQuestion = prompt('What is your favorite color?');
    if (colorQuestion != robin.favoriteColor) {
      alert('Wrong! Into the abyss with ye.');
    } else {
      let thirdAnswer = prompt(robin.thirdQuestion);
      if (thirdAnswer != 'Gondar') {
        alert('Wrong! Into the abyss with ye.');
      } else {
        alert("You may cross the bridge!")
      }
    }
  }
})**// If user clicks 'Sir Arthur', iterate through questions**
arthurButton.addEventListener('click', function () {
  let nameQuestion = prompt('What is your name?');
  if (nameQuestion != arthur.name) {
    alert("Wrong! Into the abyss with ye.");
  } else {
    let colorQuestion = prompt('What is your favorite color?');
    if (colorQuestion != arthur.favoriteColor) {
      alert('Wrong! Into the abyss with ye.');
    } else {
      let thirdAnswer = prompt(arthur.thirdQuestion);
      if (thirdAnswer != 'To seek the grail') {
        alert('Wrong! Into the abyss with ye.');
      } else {
        alert("You may cross the bridge!")
      }
    }
  }
}) **// If user clicks 'Sir Galahad', iterate through questions** galahadButton.addEventListener('click', function () {
  let nameQuestion = prompt('What is your name?');
  if (nameQuestion != galahad.name) {
    alert("Wrong! Into the abyss with ye.");
  } else {
    let colorQuestion = prompt('What is your favorite color?');
    if (colorQuestion != galahad.favoriteColor) {
      alert('Wrong! Into the abyss with ye.');
    } else {
      let thirdAnswer = prompt(galahad.thirdQuestion);
      if (thirdAnswer != 'What kind of swallow?') {
        alert('Wrong! Into the abyss with ye.');
      } else {
        alert("I don't know that! Aaargh!")
      }
    }
  }
})
```

扔一点 CSS 进去，就大功告成了！你可以看看我下面的程序。

和往常一样，我强烈推荐尝试这些概念。勇往直前，犯错误——即使是你，完美主义者。毕竟，学习编码的最好方法是写代码——即使它并不完美，或者有一点点错误。你能行的，朋友们。编码快乐！
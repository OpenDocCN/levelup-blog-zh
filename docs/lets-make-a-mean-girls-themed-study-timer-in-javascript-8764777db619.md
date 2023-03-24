# 让我们用 JavaScript 做一个“坏女孩”主题的学习计时器

> 原文：<https://levelup.gitconnected.com/lets-make-a-mean-girls-themed-study-timer-in-javascript-8764777db619>

没人要求这样。实际上没有人。但我们还是要去。

via Giphy，Hollywood Suite 和 Paramount Pictures

起初，它开始的时候很天真——我只是想做一个 JavaScript 计时器来帮助我学习。

但是，在一个小时的编码后，它感觉好像缺少了什么。

然后我突然想到:它需要刻薄女孩的 gif。

没错——2004 年的千禧年热门经典，有标志性的引用，甚至更多的标志性人物。

我很酷。

让我们开始吧。

# 首先，我创建了一个基本的 HTML 文档。

没有什么特别的——只有一个标题、一个 gif 和一个按钮，包含在一个标准的、基本的 HTML 文件中。我喜欢尽早创建我的 HTML 元素(和它们的类)，因为我们很快就会创建引用它们的 JavaScript 对象。

这里需要记住的重要事情是:**为您想要用 JavaScript 操作的元素创建描述性的类名**。

例如，如果你有一个显示`“Four for you, Glen Coco! You go Glen Coco!”`的按钮，你可以合理地将这个按钮分配给一个名为`“glen”`、`“coco”`、`“glen-coco”`或`“four-for-you-glen-coco-you-go-glen-coco”`的类。

你明白了。这样，如果你在六个月后重新选择这个项目，你仍然可以准确地记得那个类指的是什么。

(我只是把我的叫做“开始”。)

via Giphy 和派拉蒙电影公司

无论如何，这是我的 HTML 标记的主体看起来像什么。

> *注意*:把你的贱女生 gif 放在正文里！使用`img`标签链接你想要的任意多的与莉贾娜·乔治相关的迷因或 gif 的 URL。自由吧。跟随你的心。四个给你，格伦·可可。你去吧，格伦可可。

```
<body>
  <header class="headingContainer">
    <h1>Mean Girls Timer</h1>
  </header>
  <div class = "gif">
  <img src=[https://media.giphy.com/media/3otPoGoDvNReGKyclG/giphy.gif?cid=ecf05e47vnhn1kfqit990bl8orm8q6ov6s5f94ag3fw54krh&rid=giphy.gif&ct=g](https://media.giphy.com/media/3otPoGoDvNReGKyclG/giphy.gif?cid=ecf05e47vnhn1kfqit990bl8orm8q6ov6s5f94ag3fw54krh&rid=giphy.gif&ct=g) /> 
 </div><button class="start">START TIMER</button>
  <script src="pomodoroTimer.js"></script>
 </body>
```

# 接下来，我写了我的 JavaScript 伪代码。

它看起来像这样:

```
**// 1) Create JS object for the "start" button****// 2) Initialize variables for number of minutes and number of seconds to 0****// 3) Create global constants for timer length****// 4) Create a function that runs the timer****// 5) Use the 'setInterval' function to increment seconds****// 6) Use conditional statements to display the clock as it counts****// 7) Display message at the end of the set time**
```

随着我的计划到位，我的浏览器标签已经充满了刻薄女孩的 gif(我很兴奋，好吗？)，我遍历了伪代码的每一步来构建我的程序。

经由吉菲、泰勒和派拉蒙电影公司

# 接下来，我为“开始”按钮创建了一个 JavaScript 对象。

当你在 HTML 中创建一个元素时，你可以使用一个叫做 DOM 的[接口来操作它](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction)。以下是用 JavaScript 创建对象的格式:

```
**// General Format**
const myObjectName = document.querySelector(".myClassName");
```

因为我们的按钮的类叫做“start”，这是我们将用来创建它的 JS 对象的表达式:

```
**// Our expression**
const startButton = document.querySelector(".start");
```

# 然后，我初始化我的变量。

程序运行时，**秒数**T0 和**分钟数**的值将加 1。由于这些变量的值会改变，我们将使用`let`语句来创建它们。

让我们将我们的`numSeconds`变量初始化为 0，并用这个值除以 60 来初始化我们的`numMinutes`变量。

```
**// Mutable variables**
let numSeconds = 0;
let numMinutes = numSeconds / 60;
```

这些变量将记录经过的秒数和分钟数，并让我们知道时间到了。

## 现在是不可变变量(其值不会改变的变量)。

我们知道，当`numSeconds`或 `numMinutes` 变量到达`9`时，我们会想要移除它们左边的占位符零。让我们将`9`指定为常量`extraZero.`的值

接下来，我们将创建一个名为`addMinute`的常量，其值为`59`。当`numSeconds === 59`时，我们希望程序给`numMinutes`变量加 1。

```
**// Immutable variables** const extraZero = 9;const addMinute = 59;
```

# 之后我从用户那里得到了定时器长度。

在 JavaScript 中，您可以使用`prompt`方法来获取用户输入。

***重要的事情*** →默认情况下，用户通过该方法输入的任何值都以字符串的形式返回。因为我们希望这个方法返回一个整数，所以我们将在提示语句中添加“Number ”:

```
**// Use prompt method to get timer length from user (in minutes)**let timerFinished = **Number**(prompt("How long do you want to study? (Enter the number of minutes): "));
```

via Giphy 和派拉蒙电影公司

# 接下来，我创建了“运行”计时器的控制流。

让我们先写条件语句的伪代码:

1.  直到`09:09`，在`numMinutes`和`numMinutes`前添加占位符零
2.  直到`09:59`，在`numMinutes`前添加一个占位符零。之后，将`numSeconds`的值重置为 0。
3.  直到`10:09`，在`numSeconds`前加一个占位符零。

# 然后，我使用了。`textContent`在按钮对象中显示定时器的方法。

这是更改对象内文本的一般格式:

```
objectName.textContent = "New Text Content";
```

我们希望该按钮显示实时计时器，因为它计数秒。下面是我们的代码看起来的样子，大概有 0 到 2 个占位符:

```
startButton.textContent = `0${numMinutes} : 0${numSeconds}`
```

到目前为止，我们的控制流(条件语句)是这样的:

```
**// Create a function that counts the seconds** function studyTimer () {

if (numSeconds < extraZero && numMinutes < extraZero) {  numSeconds ++;  startButton.textContent = `0${numMinutes} : 0${numSeconds}`;

 } else if (numSeconds > extraZero && numSeconds < addMinute && numMinutes < extraZero) {
  numSeconds ++;  
  startButton.textContent = `0${numMinutes} : ${numSeconds}`;

 } else if (numSeconds < extraZero && numMinutes >= extraZero) {
  numSeconds++;
  startButton.textContent = `${numMinutes} : 0${numSeconds}`;

 } else if (numSeconds < addMinute && numMinutes < extraZero) {
  numSeconds ++;
  startButton.textContent = `0${numMinutes} : ${numSeconds}`;

 } else if (numSeconds >= addMinute && numMinutes < extraZero){  numSeconds = 0;
  numMinutes ++;
  startButton.textContent = `0${numMinutes} : 0${numSeconds}`;
 } else {
    numSeconds = 0;
  numMinutes ++;
  startButton.textContent = `${numMinutes} : ${numSeconds}`;
 }
 if (numMinutes === timerFinished) {  startButton.textContent = "Time to take a break!";
 }
}
```

# 我们在最后冲刺阶段，朋友们！

是时候把所有的东西放在一起了。

我们的最后一步是实际制作我们的计时器——像计时器一样工作。

换句话说，我们需要让它以秒计算。这就是`setInterval`方法的用武之地。默认情况下，`setInterval`方法以毫秒为单位递增——因此，为了以秒为单位递增，我们输入 1000 毫秒。

下面是使用`setInterval`方法的一般格式:

```
**// General format**
let variableName = setInterval(functionName, numberOfMilliseconds)
```

当我们插入变量时，我们的实际语句是这样的:

```
**// Our statement**
let secondsCounter = setInterval(studyTimer, 1000);
```

现在，让我们添加一些 CSS 样式，瞧！

点击此处查看最终结果:
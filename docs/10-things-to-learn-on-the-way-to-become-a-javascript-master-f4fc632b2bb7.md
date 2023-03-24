# 成为 JavaScript 大师的 10 件事

> 原文：<https://levelup.gitconnected.com/10-things-to-learn-on-the-way-to-become-a-javascript-master-f4fc632b2bb7>

![](img/9738cbee210bd9e48b7709706d958866.png)

照片由 [Nathz Guardia](https://unsplash.com/photos/dBnrs5XYTrs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/boxing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

我猜你是一个网络开发人员。希望你过得很好，有一份很棒的工作，也许你是个体经营者或者自由职业者。正如我在上一篇文章中描述的那样，这个领域的未来看起来很美好。也许你刚开始是一名网站开发人员，也许你已经做了很长时间的程序员了。无论您对 JavaScript 有多熟悉，重温一些主题或者首先关注它们总是好的。在你称自己为 JavaScript 大师之前，这里有 10 件事是你必须学会的。

## 1.控制流

可能是列表中最基本的主题。最重要的，也许是最重要的。如果你不知道如何继续你的代码，你会有一段艰难的时间。了解基本控制流的来龙去脉绝对是**必须的**。

*   `if else` —如果你不知道这些，你之前是怎么写代码的？
*   `switch`——基本上是`if else`用一种更雄辩的方式，一旦你有多个不同的案例就使用它。
*   `for` —不要重复自己，这就是循环的作用。除了普通的`for`之外，循环“for of”和`for in`也非常方便。`for`循环的最大优点是它们是阻塞的，所以你可以在其中使用`async await`。
*   高级条件句——使用三元运算符和逻辑运算符可以让您的生活变得更加轻松，尤其是当您试图以内联方式处理事情时，这意味着您不想保存值以便以后使用。示例:

```
// ternaryconsole.log(new Date().getHours() < 12 ? 'Good Morning!' : 'Time for a siesta') // logical operatorsconst isJsMaster = prompt('Are you a JavaScript master?') === 'true'
console.log(isJsMaster && 'proficient coder')
```

## 2.错误处理

这花了我一段时间。不管你是在前端还是后端工作，第一年左右，你可能会默认使用`console.log`或者`console.error`来‘处理’错误。要编写好的应用程序，您必须改变这种情况，用处理得很好的错误来替换您的懒惰日志。您可能希望了解如何构建自己的错误构造函数以及如何正确地捕捉它们，并向用户展示实际问题是什么。

更新:查看关于如何优雅地处理错误的[文章](https://medium.com/@gisderdube_55369/the-definite-guide-to-handling-errors-gracefully-in-javascript-58424d9c60e6)！

## 3.数据模型

类似于在应用程序中不断地移动，您必须决定在哪里将特定的信息块分组，在哪里将它们分开。这不仅适用于构建数据库模型，也适用于函数参数和对象或变量。示例:

```
const calcShape = (width, height, depth, color, angle) => {...}const calcShape = ({width, height, depth, color, angle}) => {...}
```

## **4。异步**

这是 JavaScript 的一个非常重要的方面，要么从后端获取数据，要么在后端异步处理请求。在几乎所有用例中，您都会遇到异步及其警告。如果您不知道那是什么，您可能会得到一个奇怪的错误，您会尝试修复几个小时。如果你知道它是什么，但你真的不知道该怎么办，你会在回调-地狱结束。更好的方法是在你的应用中使用承诺和/或`async await`。

## 5.DOM 操作

这是一个有趣的话题。通常，作为一名开发人员，它在今天的生活中被忽略了。也许你学过 jQuery，但从未觉得有必要学习一些原生 DOM 操作技能，也许你只是在使用一个前端框架，这里很少需要定制 DOM 操作。然而，我认为这是理解 JavaScript 的关键部分，至少在前端是如此。了解 DOM 如何工作以及如何访问元素会让你对网站如何工作有更深的理解。此外，**和**将是您必须进行一些定制 DOM 操作的地方，即使您使用现代前端框架，您肯定不希望将 jQuery 放在您的`package.json`中只是为了访问一个元素。

![](img/cd221291adca4071c62759d859f33e3d.png)

[LinkedIn 销售导航员](https://unsplash.com/photos/0QvTyp0gH3A?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/professional?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

## 6.Node.js /快递

即使作为一名前端开发人员，您也应该了解 node.js 的基础知识。理想情况下，您还应该知道如何启动一个简单的 express 服务器并添加一些路由或更改现有路由。JavaScript 非常适合编写脚本来帮助您自动化许多任务。因此，知道如何读取文件、使用文件路径或缓冲区会给你一个很好的工具集来构建任何东西。

## 7.功能方法

关于函数式编程和面向对象编程有一个永恒的争论。用这两种方法，你可能可以达到同样的效果。在 JavaScript 中，甚至更简单，您可以使用这两种方法。像 [lodash](https://lodash.com/) 这样的库给了你一个非常好的工具集，可以用函数方法构建应用程序。如今，甚至不再需要使用外部库。许多最重要的功能已经在官方的 JavaScript 规范中实现了。你肯定应该知道如何使用`map` `reduce` `filter` `forEach `和` find `。

## 8.面向对象的方法

类似于函数式方法，如果你想掌握它，你也必须熟悉面向对象的 JavaScript。在我的职业生涯中，有很长一段时间我忽略了这一部分，只是用一种变通方法来解决，但有时使用对象/类和实例来实现特定的功能肯定更好。类广泛用于 React、MobX 或自定义构造函数中。

## **9。前端框架**

三大是 React.js，Angular 和 Vue.js，如果你现在找工作，你几乎总会把其中一个列为先决条件。即使它们变化得相当快，掌握它们的一般概念对于理解应用程序如何工作也是很重要的。此外，用这种方式编写应用程序更容易。如果你还没有决定你想跳上哪一列火车，我的建议是 React.js。我已经用它工作了几年，并不后悔我的决定。

## 10.捆绑/运输

不幸的是，这是 web 开发的一大部分。一方面，我不应该说不幸，因为能够用所有最新的特性编写代码是很棒的。另一方面，我这么说的原因是，我们必须始终记住，周围有旧浏览器可能不支持这些功能，因此我们必须将代码转换成旧浏览器可以理解的其他内容。如果您使用 node.js，那么您可能很少需要编译代码。transpilation 事实上的标准是 [babel.js](https://babeljs.io/) ，所以熟悉一下吧。至于捆绑你的代码和把所有东西绑在一起，你有几个选择。Webpack 在很长一段时间内都是主导者。不久前，[package](https://parceljs.org/)不知从哪里冒出来，现在成了我的首选解决方案，因为它性能很好，易于配置，尽管并不完美。

## 额外收获:正则表达式

这不是 JavaScript 特有的，但在很多用例中非常有用。同样令人困惑。了解正则表达式的语法肯定需要一些时间，记住所有不同的选项是不可能的。

## 更新:测试

正如 Paul Kamma 指出的，测试是软件开发中非常重要的一部分，JavaScript 也不例外。当编写代码时，您(希望)在推送特性之前测试它，即使它可能是手动的。更好的方法是使用自动化测试，不同的测试类型有单元测试、端到端测试、负载测试、安全测试或前端测试(例如，是否安装了组件)。有很多不同的测试环境，酵素，茉莉，摩卡，柴等。目前我最喜欢的解决方案是 [ava.js](https://github.com/avajs/ava) ，所以如果你到目前为止还没有使用过自动化测试，就去看看吧。

希望你已经知道上面列出的所有主题。如果没有，投入工作，努力成为 JavaScript 高手！绝对值得。记住，对于编码来说，实践就是一切，所以即使你不熟悉这些概念，或者知道它们但不知道如何应用它们，它也会在未来出现。

你觉得这份名单怎么样？是不是少了什么？你认为编码时其他主题更重要吗？请在评论中告诉我！

*关于作者:Lukas Gisder-Dubé作为首席技术官共同创立并领导了一家初创公司 1 年半，建立了技术团队和架构。离开创业公司后，他在*[*iron hack*](https://medium.com/u/1ff093a3da32?source=post_page-----f4fc632b2bb7--------------------------------)*担任首席讲师教授编码，现在正在柏林建立一家创业机构&咨询公司。查看*[*Dube . io*](https://dube.io)*了解更多。*

![](img/840b0ca008c4fa8e0a1d00791a6c9f9f.png)[![](img/ff5028ba5a0041d2d76d2a155f00f05e.png)](https://levelup.gitconnected.com/)[](https://gitconnected.com/learn/javascript) [## 学习 JavaScript -最佳 JavaScript 教程(2019) | gitconnected

### 排名前 64 的 JavaScript 教程。课程由开发者提交并投票，使您能够找到最好的…

gitconnected.com](https://gitconnected.com/learn/javascript)
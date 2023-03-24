# 60 岁学习 Web 开发:第 2 部分

> 原文：<https://levelup.gitconnected.com/learning-web-development-at-60-part-2-fa5f85ef5c84>

*准备好仔细看看 JavaScript 了吗*

![](img/65c7abaa0d120368c3c9db204fa544a0.png)

经过三个月的前端开发学习，我已经掌握了 HTML、CSS 和 JavaScript 的基础知识。在我的[上一篇文章](https://medium.com/@jeffrey.amen/web-development-at-60-years-old-aaceb22db421)中，我描述了我学习 web 开发的决定和最初的方向。我完成了 App Brewery 的安吉拉胡[精品课程的前端部分。虽然我现在可以制作一个简单的网页，但我觉得我需要深入学习，尤其是 JavaScript。是时候重新评估我的道路并寻找新的学习资源了。](https://www.udemy.com/course/the-complete-web-development-bootcamp/)

**作为一名自学成才的学生找到自己的路**

作为一个自考的学生，我可以使用多种学习资源，并根据自己的需要进行混合和搭配。随着我学得越来越多，我发现我需要专注于不同的科目，这意味着我在不断调整我的课程，但我不必致力于完成任何特定的课程或教科书。我找到我需要学习的东西，然后我寻找最好的资源来指导我的学习。

【JavaScript 入门

JavaScript 是前端 web 开发中使用的主要语言，它也越来越多地用于后端。JavaScript 提供了 web 应用程序的交互性。一个简单的网页可以只用 HTML 和 CSS 来制作，但要真正使应用程序动态化，还需要 JavaScript。

App Brewery 课程很好地介绍了 JavaScript。因为我知道一些 HTML 和基本的 CSS 编码，所以我相对容易地完成了这些部分。开始 JavaScript 是一个不同的故事。我不得不增加我的学习时间和注意力来完成这一部分。安吉拉·胡，我的老师，非常擅长解释这些概念。她还让我做了很多解决问题的工作，包括自己研究一些东西来完成一些小问题。安吉拉几乎没有给出如何解决这个问题的信息，而是让每个学生自己去解决。我的妻子是一名程序员，她告诉我很多编程都是在 Google 和其他文档中查找。

早期的一个 JavaScript 程序非常难。这是斐波那契计算器。我在设计课上对斐波那契数列有所了解。面临的挑战是要制作一个程序来生成指定长度的数字序列。

```

//斐波那契生成器

var fib sequence =[]；

函数 fibonacciGenerator ( n ){

for(var I = 0；i < n ; i++){

if ( i <= 1 ) {

fibSequence.push ( i );

}

else if ( i > = 2 ) {

fib sequence . push(fib sequence[I-2]+fib sequence[I-1])；

}

}

console . log(fib sequence)；

}

斐波那契生成器(1)

```

该类中的最后一个 JavaScript 项目使用 JavaScript 代码，通过文档对象模型(DOM)操作使其与声音和视觉变化进行交互。

例如，这一行代码通过 Id 查找 HTML 元素，添加事件侦听器，并操作 CSS 属性:

```

document . getelementbyid(" top-container ")。addEventListener("mouseover "，function(){

(" top-container ")style . background color = " gold "；

(" top-container ")style . border color = " red "；

});

```

在课程的这一点上，我可以选择继续学习后端开发，从 node.js 开始，或者学习前端框架 React.js。

但是在我完成了 App Brewery 课程的前端部分后，我觉得虽然我理解了大多数 JavaScript 概念，但我并没有像我希望的那样很好地掌握这些概念。当我写程序的时候，我可以按照指示去做，并且弄清楚代码，但是我不能完全理解我到底在做什么。虽然该课程很好地解释了基础知识，但课程缺乏完整性。我觉得我需要比现在更深入地学习 JavaScript。我寻找额外的信息来源，我发现了一些好的。

*   Jonas Schmedtmann 编写的 Udemy 上的完整 JavaScript 课程是 Udemy 上的一门评价很高的课程。它包含了更多关于 JavaScript 的信息以及更多的编码项目。乔纳斯对编码的解释非常好，有助教可以解答问题。我的花费是 9.99 美元。

[学习现代 JavaScript(构建和测试应用程序)—完整课程](https://www.udemy.com/course/the-complete-javascript-course/)

*   对于印刷信息，JavaScript.info 的在线教程非常全面。它教授现代 JavaScript。当我需要比 W3schools 提供的更多的信息时，我现在用这个资源作为在线教材来查找东西。

[现代 JavaScript 教程](https://javascript.info/)

*   W3schools 有一个简单易懂的 JavaScript 教程。因为我发现这些解释比 JavaScript.info 更容易理解，所以如果我有不明白的地方，它就成了我的首选。

[JavaScript 教程](https://www.w3schools.com/js/DEFAULT.asp)

因此，在我学习三个月后，我转到了完整的 JavaScript 课程，并通过了基础 JavaScript 部分。这一部分有五个小项目。第一个相对容易，因为我已经学了一些。接下来的四个要求我查找 App Brewery 课程中已经涉及的主题，但我显然没有吸收所有内容。每个项目都建立在最后一个项目的基础上，最后一个项目非常复杂。

通过做编码项目，我发现我必须把我学到的东西放在一起做一些东西。我必须从几个来源查找信息，在学生论坛上提问，并构建自己的代码，而不是复制教师的代码。我发现边做边学远比仅仅看视频或阅读说明书更有效。

**项目导向学习**

然后我决定从其他来源寻找更多的编码项目。虽然我可以想出自己的项目来尝试，但我觉得使用发布在网络上的学习项目可以让我提出问题并与其他学生互动，并了解其他人如何处理相同的问题。这里有一篇金奎大写的关于如何开始基于项目的学习之路的文章。

[如何从阅读编码教程转向构建自己的项目](https://medium.com/better-programming/how-to-shift-from-reading-coding-tutorials-to-building-your-own-projects-a05e3d6e270c)

首先，我找到了 Avic Ndugu 的这篇文章。它始于来自[自由代码营(FCC)](https://www.freecodecamp.org/learn/) 响应式网页设计课程的五个项目。每个项目都越来越难。但是，在这个过程中，我不得不学习如何使用媒体查询，以及 flexbox 和 CSS grid，这些都是我以前没有学过的。我还必须学会如何在 FCC 论坛上展示我的项目并征求反馈。

[7 个练习 HTML 的项目&初学 CSS 的技巧](https://medium.com/better-programming/7-projects-to-practice-html-css-skills-for-beginners-cba7521a45b)

当我在网上寻找更多的项目时，我偶然发现了 Odin 项目，这是一个为自学的 web 开发人员设计的课程。他们有一个以逻辑顺序呈现的资源集合和几个项目。有一个学生论坛，有自己的不和谐频道。我在他们的 Web 开发 101 课程中关注 JavaScript 项目。我学到了大量关于使用 DOM 以及用 CSS 设计网页和样式的知识。

[网络开发 101](https://www.theodinproject.com/courses/web-development-101)

学习 JavaScript 对于 web 开发的重要性怎么强调都不为过。它是前端的主要编程语言，越来越多地用于后端。大部分前端框架，比如 React.js 都是基于它的。一个好的 web 开发课程或训练营会给你一个坚实的基础 JavaScript 介绍。但更有可能的是，你将需要寻找其他来源来真正深入地学习它。构建项目将允许您使用编码知识，并提供学好它所需的重复。学习编码是令人着迷的，但有时也会令人沮丧。耐心和不断前进是至关重要的。

查看我的学习笔记，我在 30 天内完成了 30 个项目。

[](https://jeffrey-amen.medium.com/javascript30-day-2-study-notes-3ad65d210e3f) [## JavaScript30 —第 2 天学习笔记

### Clock 项目涉及处理 CSS 转换和使用 JavaScript Date 对象。

jeffrey-amen.medium.com](https://jeffrey-amen.medium.com/javascript30-day-2-study-notes-3ad65d210e3f) [](https://jeffrey-amen.medium.com/javascript-30-01-drum-kit-2987437d32c5) [## JavaScript 30–01 架子鼓

### 2020 年 11 月 1 日——我报名参加了韦斯·博斯的免费课程 JavaScript 30，为期 30 天的普通 JavaScript 编码挑战…

jeffrey-amen.medium.com](https://jeffrey-amen.medium.com/javascript-30-01-drum-kit-2987437d32c5)
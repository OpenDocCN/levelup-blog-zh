# Node.js:让我们超越' console.log '！

> 原文：<https://levelup.gitconnected.com/node-js-move-beyond-console-log-4259234386f1>

## 你还在用 console.log 吗？

![](img/33ce21a09d98459b65c5d05523d9e14b.png)

Paul Esch-Laurent 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

`console`是由浏览器(如`Window.console`)或 Node.js(如`global.console`)提供的全局对象。

无需使用`Window`或`global`即可访问。

让我们了解一下与`console`对象相关的不同方法。

# console.log()

挺标准的一个！

这会将输出记录到屏幕上。

```
console.log("Hey there");
// Output : Hey there
```

# console.assert()

如果断言为假，这会向控制台写入一条消息。

```
console.assert(1+1 !== 2 , "Wrong");
//Output : Wrong
```

# console.clear()

此方法清除控制台。

```
console.clear();
//Output : 
```

![](img/3c24da3977db5790ebd32f89746bb0af.png)

Paul Esch-Laurent 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# console.warn()

此方法将警告消息写入控制台。

```
console.warn("This is a warning message");
//Output: This is a warning message
```

# console.error()

此方法将错误写入控制台。

请注意，它写入`stderr`，而`console.log()`写入`stdout`。

```
console.error("This is the error message");
//Output: This is the error message
```

# console.count()

这个方法计算它被调用的次数。

```
for (let i = 0; i < 3; i++) {
  console.count("count");
};

//Output: 
//count:1
//count:2
//count:3
```

![](img/d2b5354b9267ba05c5c8a5fd1e106354.png)

洛伦佐·埃雷拉在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# console.time() & console.timeEnd()

这个方法允许我们计时一个函数的执行。

```
console.time("testResult"); //Starts the timer

for (let i=0; i<=1000; i++){
    console.log("Hey!");
};

console.timeEnd("testResult"); //Ends the timer

//Output:
//testResult: 94.724853515625 ms
```

# console.table()

此方法将数组/对象作为表格打印到控制台。

```
console.table({name:"Ashish", surname:"Bamania"});

console.table(["Red", "Green", "Blue"]);
```

# console.trace()

此方法将堆栈跟踪打印到代码中的指定位置。

```
function f3(){
    f2();
};

function f2(){
    f1();
};

function f1(){
    console.trace();
};

f3();

//Output: 
//f1
//f2
//f3
//anonymous
```

![](img/079437e2b94a65e8b3163f89ed791a07.png)

照片由[丹尼尔·苏扎](https://unsplash.com/@callahans?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

非常感谢你的阅读！

*如果你是 Python 或机器学习的初学者，可以看看我下面的书:*

[](https://bamaniaashish.gumroad.com/l/python-book) [## 学习 Python 的无牛指南

### 你是一个正在考虑学习编程却不知道从哪里开始的人吗？

bamaniaashish.gumroad.com](https://bamaniaashish.gumroad.com/l/python-book) [](https://bamaniaashish.gumroad.com/l/ai_book) [## 人工智能学习指南

### 你好。你是想学习人工智能却不知道从哪里开始的人吗？

bamaniaashish.gumroad.com](https://bamaniaashish.gumroad.com/l/ai_book) [](https://bamania-ashish.medium.com/membership) [## 通过我的推荐链接加入 Medium——Ashish Bama nia 博士

### 阅读 Ashish Bamania 博士(以及 Medium 上成千上万的其他作家)的每一个故事。您的会员费直接…

bamania-ashish.medium.com](https://bamania-ashish.medium.com/membership) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   💰免费编码面试课程[查看课程](https://skilled.dev/?utm_source=luc&utm_medium=article)
*   🔔关注我们:[推特](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected) | [时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)
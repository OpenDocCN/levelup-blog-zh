# 用 10 行代码用 JavaScript 创建一个番茄定时器

> 原文：<https://levelup.gitconnected.com/creating-a-pomodoro-timer-in-javascript-in-10-lines-of-code-868f577be63c>

![](img/8b92e6c661b30669d11c520e0bc407ca.png)

算法:

1.  使用`setInterval`创建一个每秒执行一次的函数。
2.  获取总分钟数。
3.  将其转换为秒并存储在一个全局变量中。
4.  每秒钟将秒数减 1。
5.  检查秒数是否达到 0。如果为真，则警告用户并清除定时器。

编码时间！准备→预备→开始。开始编码🤩

```
<script>var seconds = 0;var interval ;function pomodoro(mins) { seconds = mins*60 || 0; **  interval = setInterval(function() {

        seconds--;** **if(!seconds){** **clearInterval(interval);** **alert("🚨 It is Cool 😎. I wish you could share ");** **}** **},1000)**
}</script> 
```

创建一个躯体是如此简单😎超级独家番茄钟。跟随 [Javascript 吉普🚙](https://medium.com/u/f9ffc26e7e69?source=post_page-----868f577be63c--------------------------------)。

如果你发现这个有用的惊喜🎁我[在这里](https://www.paypal.me/jagathishSaravanan?source=post_page---------------------------)。

开心就分享😃 😆 🙂。
# 代码气味 166 —用户界面上的低级错误

> 原文：<https://levelup.gitconnected.com/code-smell-166-low-level-errors-on-user-interface-ed9b57ee7951>

## *致命错误:未捕获的错误:在/var/www/html/query-line.php:78 堆栈跟踪中找不到类“logs _ queries _ web:# 0 { main }在第 718 行的/var/www/html/query-line . PHP 中抛出*

![](img/61bb08413c1ad7a9a417530efd05a4f9.png)

> *TL；大卫:抓住你的错误。即使是你意想不到的。*

# 问题

*   安全性
*   错误处理
*   错误记录
*   糟糕的 UX 经历

# 解决方法

1.  使用顶级处理程序
2.  避免偏爱[返回码](https://blog.devgenius.io/code-smell-72-return-codes-1b869fb53f54)的语言
3.  预期数据库和低级错误

# 语境

即使在 2022 年，我们也可以看到“严肃”的网站向临时用户展示堆栈或调试消息。

# 示例代码

## 错误的

```
<?Fatal error: Uncaught Error: Class 'MyClass' 
  not found in /nstest/src/Container.php:9
```

## 对吧

```
<?// A user-defined exception handler function
function myException($exception) {
    logError($exception->description())
    // We don't show Exception to final users      
}// Set user-defined exception handler function
set_exception_handler("myException");
```

# 侦查

[X]自动

我们可以用突变测试来模拟问题，看看是否处理正确。

# 标签

*   安全性

# 结论

我们需要不断成熟。

我们的解决方案不应该草率。

我们需要提高我们作为严肃软件工程师的声誉。

# 关系

[](https://blog.devgenius.io/code-smell-72-return-codes-1b869fb53f54) [## 代码气味 72 —返回代码

blog.devgenius.io](https://blog.devgenius.io/code-smell-72-return-codes-1b869fb53f54) 

# 更多信息

[](https://codeburst.io/fail-fast-3f3f036032b0) [## 快速失败

### 失败是时尚。制造比思考容易得多，失败不是耻辱，让我们把这个想法带到我们的…

codeburst.io](https://codeburst.io/fail-fast-3f3f036032b0) 

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

杰西·奥里科在 [Unsplash](https://unsplash.com/s/photos/dirty) 上的照片

> 我 80%的问题都是简单的逻辑错误。其余 80%的问题是指针错误。剩下的问题很难。

马克·唐纳

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)
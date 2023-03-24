# 代码气味 165 —空异常块

> 原文：<https://levelup.gitconnected.com/code-smell-165-empty-exception-blocks-ad13df47ffc9>

## *接下来是我在第一份工作中学到的第一件事*

![](img/8fdf7aa6a79d3d6e5aa62fd52d564738.png)

> *TL；DR:不要回避例外。搞定他们。*

# 问题

*   [废掉快](https://codeburst.io/fail-fast-3f3f036032b0)违反原则

# 解决方法

1.  捕捉异常并显式处理它

# 语境

在编程的早期，我们在错误处理之前给系统运行特权。

我们已经进化了。

# 示例代码

## 错误的

```
# bad
import loggingdef send_email(): 
  print("Sending email") 
  raise ConnectionError("Oops")try:
  send_email() 
except: 
  # AVOID THIS
pass
```

## 对吧

```
import logginglogger logging.getLogger(__name___)
try:
  send_email()
except ConnectionError as exc:
  logger.error(f"Cannot send email {exc}")
```

# 侦查

[X]自动

许多 linters 警告我们空的异常块

# 例外

如果我们需要跳过和忽略异常，我们应该明确地记录它。

# 标签

*   例外

# 结论

准备处理错误。

即使你决定什么都不做，你也应该明确这个决定。

# 关系

[](/code-smell-132-exception-try-too-broad-6e245f910f9b) [## 代码气味 132 —异常尝试范围太广

### 例外很方便。但是应该尽可能窄

levelup.gitconnected.com](/code-smell-132-exception-try-too-broad-6e245f910f9b) 

# 更多信息

[](https://codeburst.io/fail-fast-3f3f036032b0) [## 快速失败

### 失败是时尚。制造比思考容易得多，失败不是耻辱，让我们把这个想法带到我们的…

codeburst.io](https://codeburst.io/fail-fast-3f3f036032b0) 

[出错时恢复下一个包](https://www.npmjs.com/package/on-error-resume-next)

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

照片由[詹姆斯·贝斯特](https://unsplash.com/@jim_at_jibba)在 [Unsplash](https://unsplash.com/) 上拍摄

谢谢@ [简·贾科姆利](@jangia)

[](https://twitter.com/1571126817322602496) [## JavaScript 不可用。

### 编辑描述

twitter.com](https://twitter.com/1571126817322602496) 

> *优化阻碍进化。除了第一次，所有的东西都应该自上而下的构建。简单不先于复杂，而是跟随复杂。*

艾伦·珀利斯

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)
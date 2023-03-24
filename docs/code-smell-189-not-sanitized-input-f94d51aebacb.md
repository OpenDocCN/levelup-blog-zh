# 代码气味 189 -未净化的输入

> 原文：<https://levelup.gitconnected.com/code-smell-189-not-sanitized-input-f94d51aebacb>

## 糟糕的演员在那里。我们需要非常小心他们的投入。

![](img/1f13971497d115ba27f79b2a14b8f750.png)

> *TL；对来自你控制之外的一切进行消毒。*

# 问题

*   安全性

# 解决方法

1.  使用净化和输入过滤技术。

# 语境

每当您从外部资源获取输入时，安全原则会要求您验证并检查潜在的有害输入。

[SQL 注入](https://en.wikipedia.org/wiki/SQL_injection)是威胁的一个显著例子。

我们还可以在输入中添加[断言](https://blog.devgenius.io/code-smell-15-missed-preconditions-84349973e003)和不变量。

更好的是，我们可以使用[域受限对象](/code-smell-178-subsets-violation-3b3b9cb15d85)。

# 示例代码

## 错误的

```
user_input = "abc123!@#"
# This content might not be very safe if we expect just alphanumeric characters
```

## 对吧

```
import re

def sanitize(string):
  # Remove any characters that are not letters or numbers
  sanitized_string = re.sub(r'[^a-zA-Z0-9]', '', string)

  return sanitized_string

user_input = "abc123!@#"
print(sanitize(user_input))  # Output: "abc123"
```

# 侦查

[X]半自动

我们可以静态检查所有输入，也可以使用渗透测试工具。

# 标签

*   安全性

# 结论

对于超出我们控制范围的输入，我们需要非常谨慎。

# 关系

[](https://mcsee.medium.com/code-smell-121-string-validations-ecc7d9d04b73) [## 代码气味 121 —字符串验证

### 您需要验证字符串。所以你根本不需要绳子

mcsee.medium.com](https://mcsee.medium.com/code-smell-121-string-validations-ecc7d9d04b73) [](/code-smell-178-subsets-violation-3b3b9cb15d85) [## 代码气味 178 —违反子集

### 不可见的物体有规则，我们需要在一个点上执行

levelup.gitconnected.com](/code-smell-178-subsets-violation-3b3b9cb15d85) [](https://blog.devgenius.io/code-smell-15-missed-preconditions-84349973e003) [## 代码气味 15 —错过的前提条件

### 断言、前置条件、后置条件和不变量是我们避免无效对象的盟友。避开他们会导致…

blog.devgenius.io](https://blog.devgenius.io/code-smell-15-missed-preconditions-84349973e003) 

# 更多信息

*   [维基百科](https://en.wikipedia.org/wiki/SQL_injection)

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

Jess Zoerb 在 [Unsplash](https://unsplash.com/photos/UGCgoVmFZC0) 上拍摄的照片

> 公司应该像网络安全公司应该制造他们自己的阿司匹林一样经常制造他们自己的企业系统。

菲尔·西蒙

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)
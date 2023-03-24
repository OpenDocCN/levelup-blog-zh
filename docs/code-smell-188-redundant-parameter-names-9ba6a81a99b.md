# 代码气味 188 —冗余参数名

> 原文：<https://levelup.gitconnected.com/code-smell-188-redundant-parameter-names-9ba6a81a99b>

## *使用上下文和本地名称*

![](img/1eaf29e3d489be37ff6d5a88e9b4aebd.png)

> *TL；DR:不要重复你的参数的名字。名字应该是上下文相关的。*

# 问题

*   复制
*   可读性

# 解决方法

1.  从名称中删除重复的部分

# 语境

在使用名字的时候，我们经常会忽略单词是有上下文关系的，需要作为一个完整的句子来阅读。

# 示例代码

## 错误的

```
class Employee
  def initialize(@employee_first_name : String, @employee_last_name : String, @employee_birthdate : Time)
  end
end
```

## 对吧

```
class Employee
  def initialize(@first_name : String, @last_name : String, @birthdate : Time)
  end
end
```

# 侦查

[X]半自动

我们可以检查我们的参数名，并试图找到重复。

# 标签

*   命名

# 结论

为参数使用简短的上下文名称。

# 关系

[](https://blog.devgenius.io/code-smell-87-inconsistent-parameters-sorting-46e91d48bae0) [## 代码气味 87 —不一致的参数排序

### 与您使用的参数保持一致。代码是散文。

blog.devgenius.io](https://blog.devgenius.io/code-smell-87-inconsistent-parameters-sorting-46e91d48bae0) 

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

照片由[沃尔夫冈·哈塞尔曼](https://unsplash.com/@wolfgang_hasselmann)在 [Unsplash](https://unsplash.com/photos/Y3RVsHBeK7c) 拍摄

> *一般来说，软件系统只有在实际应用中被使用过，并且多次失败后，才能很好地工作。*

大卫·帕纳斯

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)
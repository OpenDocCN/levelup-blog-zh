# 代码气味 159 —混合 _ 案例

> 原文：<https://levelup.gitconnected.com/code-smell-159-mixed-case-acb9f2b8616>

## 严肃的开发是由许多不同的人完成的。我们必须开始达成一致。

![](img/d2fbd9e60c291e0799d54fc6e9f4be12.png)

> *TL；DR:不要混淆不同的案例转换*

# 问题

*   可读性
*   可维护性

# 解决方法

1.  选择案例标准
2.  抓住它

# 语境

当不同的人一起开发软件时，他们可能会有个人或文化差异。

有些人更喜欢[茶包](https://en.wikipedia.org/wiki/Camel_case)🐫，其他[蛇 _ 案](https://en.wikipedia.org/wiki/Snake_case)🐍、MACRO_CASE🗣️和许多其他人。

代码应该简单易懂。

# 示例代码

## 错误的

```
{
    "id": 2,
    "userId": 666, 
    "accountNumber": "12345-12345-12345",
    "UPDATED_AT": "2022-01-07T02:23:41.305Z",
    "created_at": "2019-01-07T02:23:41.305Z",
    "deleted at": "2022-01-07T02:23:41.305Z"
}
```

## 对吧

```
{
    "id": 2,
    "userId": 666, 
    "accountNumber": "12345-12345-12345",
    "updatedAt": "2022-01-07T02:23:41.305Z",
    "createdAt": "2019-01-07T02:23:41.305Z",
    "deletedAt": "2022-01-07T02:23:41.305Z"
  // This doesn't mean THIS standard is the right one
}
```

# 侦查

[X]自动

我们可以告诉我们的同事我们公司宽泛的命名标准并强制执行。

每当新人来到组织，自动化测试应该礼貌地问他/她/..来改变代码。

# 例外

每当我们需要与我们范围之外的代码进行交互时，我们应该使用客户的标准，而不是我们的标准。

# 标签

*   命名

# 结论

处理标准很容易。

我们需要强制执行。

# 关系

[](https://blog.devgenius.io/code-smell-48-code-without-standards-60c9e0905627) [## 代码气味 48 —没有标准的代码

### 从事个人项目很容易。除非你几个月后再去找它。与许多其他开发人员合作…

blog.devgenius.io](https://blog.devgenius.io/code-smell-48-code-without-standards-60c9e0905627) 

# 更多信息

[](https://blog.devgenius.io/what-exactly-is-a-name-part-i-the-quest-b812a4b1e0bf) [## 名字到底是什么？—第一部分:探索

### 我们都同意:好名声永远是最重要的。让我们找到他们。

blog.devgenius.io](https://blog.devgenius.io/what-exactly-is-a-name-part-i-the-quest-b812a4b1e0bf) 

[所有命名约定](https://en.wikipedia.org/wiki/Naming_convention_(programming)#Multiple-word_identifiers)

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

沃尔夫冈·哈塞尔曼在 [Unsplash](https://unsplash.com/s/photos/camel) 上拍摄的照片

> *如果你有太多的特例，那你就是做错了。*

克雷格·泽鲁尼

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)
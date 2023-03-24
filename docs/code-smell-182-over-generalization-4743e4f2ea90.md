# 代码气味 182 —过度概括

> 原文：<https://levelup.gitconnected.com/code-smell-182-over-generalization-4743e4f2ea90>

## *我们是重构迷。有时候我们需要停下来思考。*

![](img/02fdc9943cee89738418e365dcecc1c5.png)

> *TL；博士:不要做出超出真实知识的概括。*

# 问题

*   过分概括
*   [双射](https://codeburst.io/the-one-and-only-software-design-principle-5328420712af)违例

# 解决方法

1.  在进行结构概括之前，先思考一下

# 语境

重构不仅仅是看结构化代码。

我们需要重构行为，并检查它是否需要抽象。

# 示例代码

## 错误的

```
fn validate_size(value: i32) {
    validate_integer(value);
}

fn validate_years(value: i32) {
    validate_integer(value);
}

fn validate_integer(value: i32) {
    validate_type(value, :integer);
    validate_min_integer(value, 0);
}
```

## 对吧

```
fn validate_size(value: i32) {
    validate_type(value, Type::Integer);
    validate_min_integer(value, 0);
}

fn validate_years(value: i32) {
    validate_type(value, Type::Integer);
    validate_min_integer(value, 0);
}

// Duplication is accidental, therefore we should not abstract it
```

# 侦查

[X]手册

这是一种语义气味。

# 标签

*   复制

# 结论

软件开发是一种思维活动。

我们有自动化的工具来帮助我们。我们需要掌控一切。

# 关系

[](https://blog.devgenius.io/code-smell-46-repeated-code-433d3feb516d) [## 代码气味 46 —重复代码

### 干是我们的口头禅。老师告诉我们要消除重复。我们需要超越。

blog.devgenius.io](https://blog.devgenius.io/code-smell-46-repeated-code-433d3feb516d) 

# 更多信息

*   [干——复制的罪恶](https://en.wikipedia.org/wiki/The_Pragmatic_Programmer)

# 放弃

代码气味只是我的[观点](https://mcsee.medium.com/i-wrote-more-than-90-articles-on-2021-here-is-what-i-learned-76c238f9936f)。

# 信用

照片由[马太·亨利](https://unsplash.com/@matthewhenry)在 [Unsplash](https://unsplash.com/s/photos/duplicate) 上拍摄

> 复制比错误的抽象要便宜得多。

*桑迪·梅斯*

[](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) [## 软件工程名言

### 有时一个简短的想法可以带来惊人的想法。

blog.devgenius.io](https://blog.devgenius.io/software-engineering-great-quotes-3af63cea6782) 

本文是 CodeSmell 系列的一部分。

[](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c) [## 如何找到你的代码中有问题的部分

### 代码很难闻。让我们看看如何改变香味。

blog.devgenius.io](https://blog.devgenius.io/how-to-find-the-stinky-parts-of-your-code-fa8df47fc39c)
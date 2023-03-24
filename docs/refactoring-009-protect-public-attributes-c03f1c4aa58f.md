# 重构 009 —保护公共属性

> 原文：<https://levelup.gitconnected.com/refactoring-009-protect-public-attributes-c03f1c4aa58f>

## 忘掉数据结构、dto、POJOs 和贫血对象。

![](img/6871d147db45d9748f43334d58a5d41b.png)

> *TL；博士:避免外部操纵*

# 解决的问题

*   封装违规
*   贫血模型

# 相关代码气味

[](https://blog.devgenius.io/code-smell-01-anemic-models-f9fb5a1323b3) [## 代码气味 01——贫血模型

### 你的对象是一堆没有行为的公共属性。

blog.devgenius.io](https://blog.devgenius.io/code-smell-01-anemic-models-f9fb5a1323b3) [](https://blog.devgenius.io/code-smell-40-dtos-ca35f5d8f7c9) [## 代码气味 40—dto

### dto 被广泛使用并“解决”了实际问题，是吗？

blog.devgenius.io](https://blog.devgenius.io/code-smell-40-dtos-ca35f5d8f7c9) 

# 步伐

1.  将属性的可见性从公共更改为私有。

# 示例代码

## 以前

```
public class Song {
   String artistName;
   String AlbumName;
}
```

# 在...之后

```
public class Song {
   // 1- Change the visibility of your attributes from public to private
   private String artistName;
   private String AlbumName; // We cannot access attributes until we add methods
}
```

# 类型

[X]半自动

我们可以用 IDE 或文本编辑器来改变可见性。

# 安全

这不是一个安全的重构。

现有的依赖关系可能会中断。

# 为什么代码更好？

我们可以很容易地改变封装的代码。

代码不重复。

# 限制

有些语言没有可见性选项。

# 标签

*   贫血的

# 相关重构

[](https://blog.devgenius.io/refactoring-001-remove-setters-e5acdf60462c) [## 重构 001 —移除设置器

### Setters 违反了不变性并增加了意外耦合

blog.devgenius.io](https://blog.devgenius.io/refactoring-001-remove-setters-e5acdf60462c) 

# 信用

图片由 [Couleur](https://pixabay.com/users/couleur-1195798/) 在 [Pixabay](https://pixabay.com/) 上拍摄

本文是重构系列的一部分。

[](https://mcsee.medium.com/how-to-improve-your-code-with-easy-refactorings-fe80b60e6a8e) [## 如何通过简单的重构来改进代码

### 重构对于增长和改进我们的代码是惊人的

mcsee.medium.com](https://mcsee.medium.com/how-to-improve-your-code-with-easy-refactorings-fe80b60e6a8e)
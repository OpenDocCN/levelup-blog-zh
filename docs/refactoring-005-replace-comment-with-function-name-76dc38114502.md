# 重构 005 —用函数名替换注释

> 原文：<https://levelup.gitconnected.com/refactoring-005-replace-comment-with-function-name-76dc38114502>

## *评论应该增值。还有函数名。*

![](img/326aa18c5493593469c8e0ace150bae6.png)

> *TL；大卫:不要评论你正在做的事情。说出你在做什么。*

# 解决的问题

*   糟糕的名字
*   评论

# 相关代码气味

[](https://blog.devgenius.io/code-smell-05-comment-abusers-feec3aeb926) [## 代码气味 05 —评论滥用者

### 代码有很多注释。注释与实现联系在一起，很难维护。

blog.devgenius.io](https://blog.devgenius.io/code-smell-05-comment-abusers-feec3aeb926) [](https://blog.devgenius.io/code-smell-75-comments-inside-a-method-c911f8729ee1) [## 代码味道 75 —方法内部的注释

### 注释通常是一种代码味道。将它们插入到方法中需要紧急重构。

blog.devgenius.io](https://blog.devgenius.io/code-smell-75-comments-inside-a-method-c911f8729ee1) [](https://blog.devgenius.io/code-smell-06-too-clever-programmer-bffec35daf0b) [## 代码气味 06——太聪明的程序员

### 难以阅读的代码。没有语义的名字很棘手。有时使用语言的意外复杂性。

blog.devgenius.io](https://blog.devgenius.io/code-smell-06-too-clever-programmer-bffec35daf0b) 

# 步伐

1.  用前面的注释命名函数
2.  删除注释

# 示例代码

## 以前

```
<?function repl($str) {
  // Replaces with spaces the braces   $str = str_replace(array("\{","\}")," ",$str);
  return $str;}
```

## 在...之后

```
<?// 1\. Name the function with the previous comment
// 2\. Remove the Commentfunction replaceBracesWithSpaces($input) { return str_replace(array("\{","\}")," ", $input);}
```

# 类型

[X]半自动

一些 ide 有这种重构，尽管命名不是完全自动的。

# 为什么代码更好？

评论总是骗人的。

很难维持评论。

相反，函数是活的，不言自明的。

# 限制

和往常一样，非常重要的设计决策是有效的注释。

# 标签

*   评论

# 请参见

[名字里有什么？](https://medium.com/dev-genius/what-exactly-is-a-name-part-i-the-quest-b812a4b1e0bf)

# 信用

图片来自 [Pixabay](https://pixabay.com/) 的 [Jannik Texler](https://pixabay.com/users/texler-3778340/)

本文是重构系列的一部分。
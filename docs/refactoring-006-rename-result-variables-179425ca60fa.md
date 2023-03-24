# 重构 006 —重命名结果变量

> 原文：<https://levelup.gitconnected.com/refactoring-006-rename-result-variables-179425ca60fa>

## “结果”是一个非常糟糕的通用名。修好它

![](img/36b108656a680acc744196e7ba4d5673.png)

> *TL；DR:用最后一次通话作为语义指导。*

# 解决的问题

变量的错误命名

# 相关代码气味

[](https://blog.devgenius.io/code-smell-81-result-eef3b7c24e61) [## 代码气味 81 —结果

### 结果=？？？

blog.devgenius.io](https://blog.devgenius.io/code-smell-81-result-eef3b7c24e61) [](https://blog.devgenius.io/code-smell-79-theresult-3c536d926f06) [## 气味代码 79 —结果

### 如果一个名字已经被使用，我们总是可以在它前面加上“the”。

blog.devgenius.io](https://blog.devgenius.io/code-smell-79-theresult-3c536d926f06) 

# 步伐

使用与最后一次函数调用相同的名称命名变量。

# 示例代码

## 以前

```
function doubleFavoriteNumber(n) {
    return this.favoriteNumber * n;
}var result = doubleFavoriteNumber(2);// Many lines after we have no idea what does 
// result holds// var result ???
```

## 在...之后

```
function doubleFavoriteNumber(n) {
    return this.favoriteNumber * n;
}const favoriteNumberDoubled = doubleFavoriteNumber(2);// Many instructions after// We can use favoriteNumberDoubled knowing its semantics
```

# 类型

[X]半自动

和许多名字启发法一样，我们可以用另一个重构*替换变量，重命名变量*

# 为什么代码更好？

可变作用域可以持续很长时间。

赋值和使用可能相距甚远。

# 请参见

名字里有什么？

# 信用

由[香颂](https://pixabay.com/users/heungsoon-4523762/)在 [Pixabay](https://pixabay.com/) 上拍摄的图像

本文是重构系列的一部分。
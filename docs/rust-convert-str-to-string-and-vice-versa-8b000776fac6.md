# Rust:字符串和字符串类型之间的转换

> 原文：<https://levelup.gitconnected.com/rust-convert-str-to-string-and-vice-versa-8b000776fac6>

使用`to_string()`和`as_str()`方法或`&`操作器在铁锈的`&str`和`String`类型之间转换。

![](img/27f985263bba3747eabc6a34e4ea75cd.png)

## &字符串与字符串

在 Rust 中，`&str`是一个字符串片段，指的是字符串中特定的字符序列。它是一个不可变的引用，意味着一旦绑定到一个特定的字符串，它就不能被改变。

另一方面，`String`是存储在堆上的可增长的可变字符串类型。它可以根据需要在内存中修改和重新分配。

## &字符串到字符串

要将 Rust 中的`&str`转换为`String`，可以使用`to_string()`方法:

```
// Declare a string slice
let s = "Hello, world!";

// Convert the string slice to a String
let string = s.to_string();
```

`to_string()`方法从字符串片`s`创建一个新的`String`对象，它包含字符串数据的副本。

## 字符串到&str

要将`String`转换为`&str`，可以使用`as_str()`方法:

```
// Declare a String
let string = String::from("Hello, world!");

// Convert the String to a string slice
let s = string.as_str();
```

`as_str()`方法返回一个引用`String`对象中数据的`&str`片。如果您需要将字符串数据传递给需要一个`&str`参数的函数或方法，这个方法会很有帮助。

或者，您可以使用`&`操作符将对`String`的引用作为`&str`:

```
// Declare a String
let string = String::from("Hello, world!");

// Get a reference to the String as a string slice
let s = &string;
```

这个语法相当于调用`as_str()`方法，但是在某些情况下可能更简洁。

## 你想联系吗？

如果你想联系我，请通过 LinkedIn 联系我。

另外，请随意查看我的书籍推荐📚。

[](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [## 我的书籍推荐

### 在接下来的章节中，你可以找到我对所有日常生活话题的书籍推荐，它们对我帮助很大。

mr-pascal.medium.com](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [](https://mr-pascal.medium.com/membership) [## 通过我的推荐链接加入 Medium—Pascal Zwikirsch

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

mr-pascal.medium.com](https://mr-pascal.medium.com/membership)
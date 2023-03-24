# Rust:字符串和&str 类型的字符串连接

> 原文：<https://levelup.gitconnected.com/rust-string-concatenation-of-string-and-str-types-59720c2f7182>

了解如何使用`+` 操作符或`format!`宏连接 Rust 中的&str 和 String 值，以及如何使用`format!`宏或`push_str()`方法连接两个字符串值。

![](img/27f985263bba3747eabc6a34e4ea75cd.png)

在 Rust 中，您可以使用`+`操作符或`format!`宏连接`&str`和`String`值。

## &str + &str

要连接两个`&str`值而不首先将一个转换为`String`，您必须使用`format!`宏。即使这将产生一个`String`而不是第三个`&str`值。

```
let s1 = "Hello, ";
let s2 = "world!";

let s3: String = format!("{}{}", s1, s2);

println!("{}", s3); // Outputs "Hello, world!"
```

## 字符串+&字符串

要连接一个`&str`值和一个`String`值，您可以使用`+`运算符或`format!`宏:

```
let s1 = "Hello, ";
let string = String::from("world!");
let string2 = String::from("world!");

// Using the + operator
let s2: String = string + s1;

// Using the format! macro
let s3: String = format!("{}{}", s1, string2);

println!("{}", s2); // Outputs "Hello, world!"
println!("{}", s3); // Outputs "Hello, world!"
```

在这两种情况下，`+`操作符或`format!`宏将创建一个新的`String`对象，其中包含连接的字符串数据。

请记住，`+`运算符只能用于连接两个`&str`值或一个`&str`值和一个`String`值。

## 字符串+字符串

要连接两个`String`值，必须对其中一个`String`对象使用`format!`宏或`push_str()`方法:

```
let string1 = String::from("Hello, ");
let string2 = String::from("world!");

// Using the format! macro
let string3 = format!("{}{}", string1, string2);

// Using the push_str() method
let mut string4 = string1;
string4.push_str(&string2);

println!("{}", string3); // Outputs "Hello, world!"
println!("{}", string4); // Outputs "Hello, world!"
```

在这两种情况下，`format!`宏或`push_str()`方法将创建一个新的`String`对象，其中包含连接的字符串数据。

## 你想联系吗？

如果你想联系我，请在 LinkedIn 上给我打电话。

另外，请随意查看[我的书籍推荐](https://medium.com/@mr-pascal/my-book-recommendations-4b9f73bf961b)📚。

[](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [## 我的书籍推荐

### 在接下来的章节中，你可以找到我对所有日常生活话题的书籍推荐，它们对我帮助很大。

mr-pascal.medium.com](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [](https://mr-pascal.medium.com/membership) [## 通过我的推荐链接加入 Medium—Pascal Zwikirsch

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

mr-pascal.medium.com](https://mr-pascal.medium.com/membership)
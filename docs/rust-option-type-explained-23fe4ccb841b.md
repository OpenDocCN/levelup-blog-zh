# 生锈:选项类型说明

> 原文：<https://levelup.gitconnected.com/rust-option-type-explained-23fe4ccb841b>

了解 Rust 的`Option`类型，它表示值的存在或不存在，以及如何使用它来处理可能存在或可能不存在的值。

![](img/27f985263bba3747eabc6a34e4ea75cd.png)

`Option`类型是 Rust 编程语言中的一种类型，表示值的存在或不存在。它是一种枚举类型，这意味着它有一组可以接受的固定值。`Option`的两个变种是`Some`和`None`。

下面是使用`Option`的一般语法:

```
let option: Option<T> = some_function_that_returns_an_option();
```

`Option`类型有一个类型参数:`T`。`T`表示可能存在也可能不存在的值的类型。

下面是一个例子，说明如何使用`Option`来处理一个函数的结果，这个函数可能返回值，也可能不返回值:

```
fn find(haystack: &str, needle: char) -> Option<usize> {
    for (index, c) in haystack.char_indices() {
        if c == needle {
            return Some(index);
        }
    }
    None
}

fn main() {
    let result = find("hello", 'l');
    match result {
        Some(index) => println!("Found at index {}", index),
        None => println!("Not found"),
    }
}
```

在本例中，`find`函数接受一个字符串和一个字符，并返回该字符在字符串中第一次出现的索引(如果存在)。如果在字符串中没有找到该字符，则返回`None`。

在`main`函数中，我们使用一个`match`表达式来处理`find`函数返回的`Option`值。如果值是`Some`，我们打印字符的索引。如果值是`None`，我们打印一条消息，指出没有找到该字符。

您还可以使用`map`和`map_or`分别转换`Some`变量中的值或为`None`提供默认值。下面是一个使用这些方法的示例:

```
fn find(haystack: &str, needle: char) -> Option<usize> {
    for (index, c) in haystack.char_indices() {
        if c == needle {
            return Some(index);
        }
    }
    None
}

fn main() {
    let result = find("hello", 'l').map(|index| index + 1);
    let index = result.map_or(0, |index| index);
    println!("Index: {}", index);
}
```

在这个例子中，我们使用`map`方法将`find`函数返回的`Option`值的`Some`变量中的值加 1。然后，如果`Option`值为`None`，我们使用`map_or`方法提供默认值 0。`index`的最终值将是`Some`变量内的转换值或默认值 0。

也可以使用`and_then`将`Option`值链接在一起。下面是一个使用`and_then`的例子:

```
fn parse_int(input: &str) -> Option<i32> {
    match input.parse() {
        Ok(number) => Some(number),
        Err(_) => None,
    }
}

fn divide(numerator: i32, denominator: i32) -> Option<i32> {
    if denominator == 0 {
        None
    } else {
        Some(numerator / denominator)
    }
}

fn main() {
    let result = parse_int("10").and_then(|numerator| {
        parse_int("0").and_then(|denominator| {
            divide(numerator, denominator)
        })
    });
    match result {
        Some(result) => println!("Result: {}", result),
        None => println!("Error"),
    }
}
```

在这个例子中，我们有三个函数:`parse_int`，它将一个字符串解析成一个`i32`值，并返回一个`Option`；`divide`，将一个`i32`值除以另一个值，返回一个`Option`；和`main`，它使用`and_then`将这两个函数链接在一起。

`parse_int`函数接受一个字符串作为输入，并将其解析为一个`i32`值。如果解析成功，它将返回一个包含`i32`值的`Some`变量。如果解析失败，它返回一个`None`变量。

`divide`函数将两个`i32`值作为输入，如果分母不为零，则返回一个包含除法结果的`Some`变量。如果分母为零，则返回一个`None`变量。

在`main`函数中，我们使用`and_then`将`parse_int`和`divide`函数的结果链接在一起。如果这些函数中的任何一个返回一个`None`变量，整个链将短路并返回一个`None`变量。如果两个函数都返回一个`Some`变量，最终结果将是由`divide`函数返回的`Some`变量。

## 你想联系吗？

如果你想联系我，请在 LinkedIn 上给我打电话。

另外，请随意查看[我的书籍推荐](https://medium.com/@mr-pascal/my-book-recommendations-4b9f73bf961b)📚。

[](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [## 我的书籍推荐

### 在接下来的章节中，你可以找到我对所有日常生活话题的书籍推荐，它们对我帮助很大。

mr-pascal.medium.com](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [](https://mr-pascal.medium.com/membership) [## 通过我的推荐链接加入 Medium—Pascal Zwikirsch

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

mr-pascal.medium.com](https://mr-pascal.medium.com/membership)
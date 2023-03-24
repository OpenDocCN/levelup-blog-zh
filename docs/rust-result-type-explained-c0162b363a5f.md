# Rust:解释的结果类型

> 原文：<https://levelup.gitconnected.com/rust-result-type-explained-c0162b363a5f>

理解 Rust 结果类型，用于处理计算中的成功和错误情况，并使用其方法来处理和操作值。

![](img/27f985263bba3747eabc6a34e4ea75cd.png)

## 定义

`Result`类型是 Rust 标准库提供的通用枚举。它表示计算的结果，可以是成功的(`Ok`变量)，也可以是不成功的(`Err`变量)。

`Result`类型定义如下:

```
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

`T`和`E`类型参数分别代表成功和不成功结果的类型。例如，您可以使用`Result<T, E>`来表示一个计算，如果成功，则返回一个类型为`T`的值，如果失败，则返回一个类型为`E`的错误。

## 用法示例

下面是一个使用`Result`包装可能失败的函数的结果的例子:

```
fn parse_int(s: &str) -> Result<i32, std::num::ParseIntError> {
    s.parse::<i32>().map_err(|e| e.into())
}

fn main() {
    let result = parse_int("5");
    match result {
        Ok(n) => println!("Parsed integer: {}", n),
        Err(e) => println!("Error parsing integer: {}", e),
    }
}
```

在这个例子中，`parse_int`函数试图从一个字符串中解析一个整数。如果解析成功，该函数将返回一个包含已解析整数的`Ok`变量。如果解析失败，函数返回一个包含发生的`ParseIntError`的`Err`变量。

## 处理结果

您可以使用模式匹配来处理`Result`值。在上面的例子中，我们使用一个`match`表达式来处理`Ok`和`Err`变量。您还可以使用`unwrap`方法从`Ok`变量中提取值，但是如果值是`Err`变量，这将会出错:

```
let result = parse_int("5");
let n = result.unwrap(); // Will panic if result is an Err variant
println!("Parsed integer: {}", n);
```

由于可能会引起恐慌，所以非常不鼓励在生产代码中使用`unwrap()`。

处理`Result`值的另一种方式是使用`map`和`map_err`方法。这些方法允许您通过对其应用函数来转换`Ok`或`Err`变量内部的值。

例如，假设您有一个函数`divide`，它将两个整数相除，并返回一个`Result`来指示相除是否成功:

```
fn divide(x: i32, y: i32) -> Result<i32, DivisionError> {
    if y == 0 {
        return Err(DivisionError::DivideByZero);
    }
    Ok(x / y)
}
```

## `map()`和`map_err()`

您可以使用`map`方法将除法的成功结果转换成不同的类型，比如浮点数:

```
let result = divide(10, 2);
let f: Result<f32, DivisionError> = result.map(|n| n as f32);
```

您还可以使用`map_err`方法将误差值转换成不同的类型:

```
let result = divide(10, 0);
let f: Result<i32, &str> = result.map_err(|e| match e {
    DivisionError::DivideByZero => "Divide by zero",
    DivisionError::Negative => "Cannot divide by negative number",
});
```

## 更多结果处理

除了`map`和`map_err`方法之外，`Result`类型还提供了其他几种处理和操作值的方法。这里有几个例子:

```
fn add_one(x: i32) -> Result<i32, &'static str> {
    if x > 100 {
        return Err("Number too large");
    }
    Ok(x + 1)
}

fn add_two(x: i32) -> Result<i32, &'static str> {
    if x > 50 {
        return Err("Number too large");
    }
    Ok(x + 2)
}

fn add_three(x: i32) -> Result<i32, &'static str> {
    if x > 30 {
        return Err("Number too large");
    }
    Ok(x + 3)
}

// Using and_then
let result = add_one(5).and_then(|x| add_two(x)).and_then(|x| add_three(x));
assert_eq!(result, Ok(11));

// Using or_else
let result = add_one(105).or_else(|e| add_two(5)).or_else(|e| add_three(5));
assert_eq!(result, Ok(7));

// Using unwrap_or
let result = add_one(105).unwrap_or(100);
assert_eq!(result, 100);

// Using unwrap_or_else
let result = add_one(105).unwrap_or_else(|e| -1);
assert_eq!(result, -1);
```

在这些例子中，我们有三个函数，每个函数执行一个操作并返回一个`Result`值。`and_then`方法允许我们将这些函数链接在一起，如果所有的计算都成功，就返回最终的操作结果。如果任何计算失败，立即返回`Err`变量。

`or_else`方法与`and_then`相似，但它应用于`Err`变体，而不是`Ok`变体。它允许我们在原始计算失败时指定一个后备计算。

`unwrap_or`方法返回`Ok`变量内部的值，如果`Result`是`Err`变量，则返回默认值。当您希望在出现错误时提供默认值，而不是显式处理错误时，这很有用。

`unwrap_or_else`方法类似于`unwrap_or`，但是它将一个闭包作为参数应用于`Err`变量内部的错误值，以产生一个默认值。当您需要根据误差值计算默认值时，这很有用。

我希望这些例子有助于阐明 Rust 中处理和操作`Result`值的不同可用方法的用途和功能。

## 你想联系吗？

如果你想联系我，请通过 LinkedIn 联系我。

另外，请随意查看我的书籍推荐📚。

[](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [## 我的书籍推荐

### 在接下来的章节中，你可以找到我对所有日常生活话题的书籍推荐，它们对我帮助很大。

mr-pascal.medium.com](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [](https://mr-pascal.medium.com/membership) [## 通过我的推荐链接加入 Medium—Pascal Zwikirsch

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

mr-pascal.medium.com](https://mr-pascal.medium.com/membership)
# 铁锈:特征解释

> 原文：<https://levelup.gitconnected.com/rust-traits-explained-e098532dc8ca>

理解 Rust 的特质系统:定义和实现不同类型的共同行为指南

![](img/27f985263bba3747eabc6a34e4ea75cd.png)

Rust 中的特征是一种指定一个类型可以实现的一组行为的机制。特征类似于其他编程语言中的接口，允许您定义一组方法，类型必须实现这些方法才能符合接口。

然而，Rust 的 trait 系统比许多其他语言的界面系统更加强大和灵活。例如，在 Rust 中，traits 可以有泛型类型，并为它们定义的一些或所有方法提供默认实现。trait 系统允许您定义许多类型都可以实现的更加灵活和可重用的抽象。

在 Rust 中使用特征的一个主要优点是，它们允许您用相同的行为抽象不同的类型。当处理需要对各种类型进行操作的数据结构或算法时，Traits 是很有优势的，因为它允许您编写与所使用的特定类型无关的代码。

特征的另一个主要好处是它们是 Rust 类型系统的核心部分。Rust 编译器可以使用特征来加强类型安全，并在编译时防止常见的编程错误。例如，Rust 编译器会阻止你调用一个在 trait 中定义的方法，除非你能保证你调用它的类型实现了它。

总的来说，Rust 的 trait 系统提供了一种强大而灵活的方式来定义和实现不同类型的通用行为。它是 Rust 类型系统的重要组成部分，支持创建可重用和类型安全的抽象。

## 示例实现

下面是如何在 Rust 中定义和使用特征的一个简单示例:

```
// Define a trait called `Summarize` with a single method called `summarize`
trait Summarize {
    fn summarize(&self) -> String;
}

// Define a struct called `NewsArticle` with a `headline` and a `location` field
struct NewsArticle {
    headline: String,
    location: String,
}

// Implement the `Summarize` trait for `NewsArticle`
impl Summarize for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, {}", self.headline, self.location)
    }
}

// Define a struct called `Tweet` with a `username` and a `message` field
struct Tweet {
    username: String,
    message: String,
}

// Implement the `Summarize` trait for `Tweet`
impl Summarize for Tweet {
    fn summarize(&self) -> String {
        format!("{}: {}", self.username, self.message)
    }
}

// Define a function that takes an object that implements the `Summarize` trait
fn summarize<T: Summarize>(item: T) -> String {
    item.summarize()
}

fn main() {
 // Create some instances of `NewsArticle` and `Tweet`
 let article = NewsArticle {
     headline: String::from("Penguins win the Stanley Cup Championship!"),
     location: String::from("Pittsburgh, PA"),
 };

 let tweet = Tweet {
     username: String::from("ice_hockey_fan123"),
     message: String::from("Can't believe the Penguins actually won the Stanley Cup!"),
 };

 // Use the `summarize` function with both a `NewsArticle` and a `Tweet`
 println!("New article available! {}", summarize(article));
 println!("Tweet: {}", summarize(tweet));
}
```

该代码将输出:

```
New article available! Penguins win the Stanley Cup Championship!, Pittsburgh, PA
Tweet: ice_hockey_fan123: Can't believe the Penguins actually won the Stanley Cup!
```

在这个例子中，我们用一个名为`summarize`的方法定义了一个名为`Summarize`的特征，该方法引用`self`并返回一个`String`。然后我们为`NewsArticle`和`Tweet`结构实现了`Summarize`特征，每个都实现了`summarize`方法。

最后，我们定义了一个名为`summarize`的函数，它接受一个实现了`Summarize`特征的对象，并通过调用该对象上的`summarize`方法返回一个`String`。我们可以用一个`NewsArticle`和一个`Tweet`对象来使用这个函数，因为它们都实现了`Summarize`特征。

## 泛型类型

Traits 也可以有泛型类型和默认实现。以下是如何使用这些功能的示例:

```
// Define a trait called `Summary` with a generic type `T` and a method called `summarize`
trait Summary<T> {
    fn summarize(&self) -> T;
}

// Define a struct called `NewsArticle` with a `headline` and a `location` field
struct NewsArticle {
    headline: String,
    location: String,
}

// Implement the `Summary` trait for `NewsArticle`
impl Summary<String> for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, {}", self.headline, self.location)
    }
}

// Define a struct called `Tweet` with a `username` and a `message` field
struct Tweet {
    username: String,
    message: String,
}

// Implement the `Summary` trait for `Tweet`
impl Summary<usize> for Tweet {
    fn summarize(&self) -> usize {
        self.message.len()
    }
}

// Define a function that takes an object that implements the `Summary` trait
fn summarize<T, U: Summary<T>>(item: U) -> T {
    item.summarize()
}

fn main() {
 // Create some instances of `NewsArticle` and `Tweet`
 let article = NewsArticle {
     headline: String::from("Penguins win the Stanley Cup Championship!"),
     location: String::from("Pittsburgh, PA"),
 };

 let tweet = Tweet {
     username: String::from("ice_hockey_fan123"),
     message: String::from("Can't believe the Penguins actually won the Stanley Cup!"),
 };

 // Use the `summarize` function with both a `NewsArticle` and a `Tweet`
 println!("New article available! {}", summarize(article));
 println!("Tweet length: {}", summarize(tweet));
}
```

该代码将输出:

```
New article available! Penguins win the Stanley Cup Championship!, Pittsburgh, PA
Tweet length: 56
```

在这个例子中，我们用一个通用类型`T`和一个名为`summarize`的方法定义了`Summary`特征，该方法引用`self`并返回一个类型为`T`的值。然后，我们为`NewsArticle`和`Tweet`结构实现了`Summary`特征，每个结构都有自己的`summarize`方法实现。`NewsArticle`实现返回一个`String`，而`Tweet`实现返回一个`usize`。

我们还定义了一个名为`summarize`的函数，它接受一个类型为`U`的对象，该对象为某个类型`T`实现了`Summary`特征，并返回一个类型为`T`的值。这允许我们对一个`NewsArticle`和一个`Tweet`对象使用`summarize`函数，即使它们对`summarize`方法有不同的实现并返回不同的类型。

我希望这阐明了特征在 Rust 中是如何工作的，以及如何使用它们来定义和实现不同类型的常见行为。

## 你想联系吗？

如果你想联系我，请通过 LinkedIn 联系我。

另外，请随意查看我的书籍推荐📚。

[](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [## 我的书籍推荐

### 在接下来的章节中，你可以找到我对所有日常生活话题的书籍推荐，它们对我帮助很大。

mr-pascal.medium.com](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [](https://mr-pascal.medium.com/membership) [## 通过我的推荐链接加入 Medium—Pascal Zwikirsch

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

mr-pascal.medium.com](https://mr-pascal.medium.com/membership)
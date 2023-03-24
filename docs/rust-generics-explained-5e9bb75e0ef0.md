# Rust:泛型解释

> 原文：<https://levelup.gitconnected.com/rust-generics-explained-5e9bb75e0ef0>

探索 Rust 中的泛型:使用类型占位符编写灵活且可重用的代码

![](img/27f985263bba3747eabc6a34e4ea75cd.png)

泛型是一种编写灵活且可重用的代码的方式，它允许您指定占位符类型，这些类型可以在以后使用代码时填充。这类似于模板在 C++中的工作方式，或者类型参数在 Java 和其他语言中的工作方式。

使用泛型，您可以编写适用于任何类型的代码，而不是特定于特定类型的代码。这使得编写开发人员可以在各种情况下使用的代码变得更加容易，而不必为每个特定类型重写代码。

例如，编写一个函数，它接受两个值并返回它们的和。如果没有泛型，您需要为想要支持的每种类型编写一个单独的函数，如下所示:

```
fn add_i32(x: i32, y: i32) -> i32 {
    x + y
}

fn add_f64(x: f64, y: f64) -> f64 {
    x + y
}
```

## 函数中的泛型

要在 Rust 中使用泛型，必须首先使用`<t>'语法在函数或结构定义中声明一个泛型类型参数。例如，下面是一个简单的函数，它采用泛型类型“t”并返回它:</t>

```
fn identity<T>(x: T) -> T {
    x
}
```

然后，通过在调用该函数时指定类型参数，可以将该函数用于任何类型。例如:

```
let x = identity(5); // x has type i32
let y = identity("hello"); // y has type &str
```

您也可以通过用逗号分隔来指定多个泛型类型参数:

```
fn pair<T, U>(x: T, y: U) -> (T, U) {
    (x, y)
}

let x = pair(5, "hello"); // x has type (i32, &str)
```

## 结构中的泛型

除了在函数定义中使用泛型，还可以在结构定义中使用泛型。例如，下面是一个使用泛型的简单链表实现:

```
struct Node<T> {
    value: T,
    next: Option<Box<Node<T>>>,
}
```

在本例中,“Node”结构有一个通用类型参数“t ”,它表示该节点持有的值的类型。这意味着您可以对任何类型使用“Node”结构，如下所示:

```
let node1 = Node { value: 5, next: None }; // node1 has type Node<i32>
let node2 = Node { value: "hello", next: Some(Box::new(node1)) }; // node2 has type Node<&str>
```

## 性状的类属

您还可以在 trait 定义中使用泛型来指定开发人员可以实现 trait 的类型。例如，下面是一个简单的特征，它定义了一个比较两个值的方法:

```
trait Comparable<T> {
    fn cmp(&self, other: &T) -> Ordering;
}
```

这一特征可应用于任何类型的“t ”,这些类型可以用“cmp”方法进行比较。例如，您可以为“i32”类型实现这个特征，如下所示:

```
impl Comparable<i32> for i32 {
    fn cmp(&self, other: &i32) -> Ordering {
        self.cmp(other)
    }
}
```

然后，您可以将此特征用于“i32”类型，如下所示:

```
let x = 5;
let y = 6;

if x.cmp(&y) == Ordering::Less {
    println!("x is less than y");
}
```

## 通用约束

泛型还可以有约束，这些约束指定泛型类型为了与函数或结构一起使用而必须满足的要求。例如，您可能希望编写一个函数，它可以处理任何实现“Add”特征的类型，这允许您将同一类型的两个值相加。您可以使用“where”关键字来指定此约束:

```
fn add<T>(x: T, y: T) -> T
where T: Add<Output=T>
{
    x + y
}
```

该函数接受两个类型为“t”的值，并返回一个类型为“t”的值，并且它有一个约束条件，即“t”必须实现“Add”特征。这意味着您只能对实现“Add”的类型(如整数或浮点数)使用此函数。

除了使用“where”关键字之外，您还可以使用特征界限。特征界限是一种指定泛型类型必须实现特定特征的方式。例如:

```
fn add<T: Add<Output=T>>(x: T, y: T) -> T {
    x + y
}
```

以下是如何使用该函数的示例:

```
let x = 5;
let y = 6;

let sum = add(x, y); // sum has type i32

println!("The sum is {}", sum);
```

## 你想联系吗？

如果你想联系我，请在 LinkedIn 上联系我。

另外，可以随意查看[我的书籍推荐](https://medium.com/@mr-pascal/my-book-recommendations-4b9f73bf961b)📚。

[](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [## 我的书籍推荐

### 在接下来的章节中，你可以找到我对所有日常生活话题的书籍推荐，它们对我帮助很大。

mr-pascal.medium.com](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [](https://mr-pascal.medium.com/membership) [## 通过我的推荐链接加入 Medium—Pascal Zwikirsch

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

mr-pascal.medium.com](https://mr-pascal.medium.com/membership)
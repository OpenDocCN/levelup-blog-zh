# Rust:克隆结构解释

> 原文：<https://levelup.gitconnected.com/rust-cloning-structs-explained-d633713d5de0>

了解 Rust `Clone`特征以及如何为定制结构实现它，包括定制`clone`方法以及处理引用和资源。

![](img/27f985263bba3747eabc6a34e4ea75cd.png)

`Clone`特征是 Rust 标准库提供的一个特征，允许你创建一个对象的副本。在 struct 上实现`Clone`特征将使您能够使用`clone`方法创建一个新实例，它的所有字段都用原始实例的值初始化。

其定义如下:

```
trait Clone {
    fn clone(&self) -> Self;
}
```

要使用`clone`特征，可以在实现它的对象上调用`clone`方法。例如:

```
let x = 5;
let y = x.clone();
```

这将创建一个新的整数`y`，其值与`x`相同。

## 导出它

下面是如何在 Rust 中的结构上实现`Clone`特征:

1.  首先，您需要从`std::clone`模块导入`Clone`特征。您可以通过在文件顶部添加以下行来实现这一点:

```
use std::clone::Clone;
```

2.您必须添加`Clone`特征作为您的结构的超级特征。您可以通过将`Clone`添加到您的结构的`impl`块中的超特征列表中来实现这一点。例如:

```
#[derive(Clone)]
struct MyStruct {
    field1: i32,
    field2: String,
}
```

这将使用 Rust 标准库提供的默认实现自动为您的结构实现`Clone`特征。

## 定制它

1.如果您想要为您的结构定制`clone`方法的行为，您可以在您的结构的`impl`块中手动实现`clone`方法。例如:

```
impl Clone for MyStruct {
    fn clone(&self) -> MyStruct {
        MyStruct {
            field1: self.field1,
            field2: self.field2.clone(),
        }
    }
}
```

在这个例子中，我们使用由`String`类型提供的`clone`方法来创建一个`field2`字段的新实例，然后使用原始`MyStruct`实例的值来初始化新实例的其他字段。

注意，如果您手动实现了`clone`方法，您不需要将`#[derive(Clone)]`属性添加到您的结构中。此外，不再需要导入它。

2.一旦为结构实现了`Clone`特征，就可以使用`clone`方法来创建结构的新实例。例如:

```
let original = MyStruct { field1: 42, field2: "hello".to_string() };
let copy = original.clone();
```

`copy`变量将包含一个与`original`变量具有相同值的`MyStruct`的新实例。

## 小心点

在您的结构上实现`Clone`特征时，有一些事情要记住:

*   如果您的结构中有包含引用的字段，您需要避免创建对同一数据的多个可变引用。这可以通过在字段类型上使用`Clone`特征或通过使用分别由`std::rc`和`std::sync`模块提供的`Rc`(引用计数)或`Arc`(原子引用计数)类型来完成。
*   如果你的结构包含本身就是结构的字段，你需要确保那些结构也实现了`Clone`特征。这将允许您创建结构的深层副本，而不仅仅是浅层副本。
*   如果您的类型包含像文件句柄或网络套接字这样的资源，您可能需要实现一个定制版本的`clone`来正确处理这些资源的克隆。
*   如果您的类型是更大的数据结构的一部分，请考虑克隆该类型是否会导致数据结构的其余部分出现问题。例如，如果您有一个树结构，其中每个节点都包含对其父节点的引用，则克隆节点将创建对原始父节点的引用，这可能与您想要的不同。

总的来说，仔细考虑为您的类型实现`clone`特征的含义是很重要的。

## 你想联系吗？

如果你想联系我，请在 LinkedIn 上给我打电话。

另外，请随意查看我的书籍推荐📚。

[](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [## 我的书籍推荐

### 在接下来的章节中，你可以找到我对所有日常生活话题的书籍推荐，它们对我帮助很大。

mr-pascal.medium.com](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [](https://mr-pascal.medium.com/membership) [## 通过我的推荐链接加入 Medium—Pascal Zwikirsch

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

mr-pascal.medium.com](https://mr-pascal.medium.com/membership)
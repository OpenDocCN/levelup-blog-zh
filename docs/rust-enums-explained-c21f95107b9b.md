# Rust: Enums 解释道

> 原文：<https://levelup.gitconnected.com/rust-enums-explained-c21f95107b9b>

Rust 枚举:用不同的变量和数据表示一组命名的常量

![](img/27f985263bba3747eabc6a34e4ea75cd.png)

一个`enum`(枚举的缩写)是一种 Rust 编程语言类型，代表一组命名的常量。枚举是一种对一组相关值进行分组并给它们一个通用名称的方法。

例如，您可以使用枚举来表示一副牌中的不同花色:

```
enum Suit {
    Spades,
    Hearts,
    Clubs,
    Diamonds,
}
```

`Suit`枚举的每个变体代表一副牌中不同的花色。然后，您可以使用此枚举来定义扑克牌的类型:

```
struct Card {
    suit: Suit,
    value: u8,
}
```

现在，您可以使用`Card`结构创建一张有特定花色和值的新卡:

```
let ace_of_spades = Card { suit: Suit::Spades, value: 1 };
```

您还可以使用一个`enum`来定义一个具有几个不同变量的类型，每个变量都有自己的数据。例如，您可以使用一个`enum`来表示可以通过网络发送的不同类型的消息:

```
enum Message {
    Text(String),
    Image(Vec<u8>),
    File(PathBuf),
}
```

在这个例子中，`Message` `enum`有三个变体:`Text`、`Image`和`File`。`Text`变量有一个类型为`String`的字段，表示消息的文本。`Image`变量有一个类型为`Vec<u8>`的单一字段，代表图像的二进制数据。而`File`变量有一个类型为`PathBuf`的字段，表示文件系统上文件的路径。

您可以使用此`enum`来定义网络消息的类型:

```
struct NetworkMessage {
    sender: String,
    recipient: String,
    message: Message,
}
```

现在，您可以使用`NetworkMessage`结构创建一个具有特定类型和数据的新消息:

```
use std::path::PathBuf;

// ...

let text_message = NetworkMessage {
    sender: "Alice".to_string(),
    recipient: "Bob".to_string(),
    message: Message::Text("Hello, Bob!".to_string()),
};

let image_message = NetworkMessage {
    sender: "Alice".to_string(),
    recipient: "Bob".to_string(),
    message: Message::Image(vec![0, 1, 2, 3]),
};

let file_message = NetworkMessage {
    sender: "Alice".to_string(),
    recipient: "Bob".to_string(),
    message: Message::File(PathBuf::from("/path/to/file.txt")),
};
```

在 Rust 中，枚举是一个强大的工具，允许您用一组固定的值定义一个类型，每个值都有自己的数据。它们通常用于表示有限选项集之间的选择，或者用一组固定的可能类型来表示数据。

枚举上也可以定义方法，就像结构一样。例如，您可以在`Suit`枚举上定义一个方法，将其转换为字符串表示:

```
impl Suit {
    fn to_string(&self) -> &str {
        match self {
            Suit::Spades => "Spades",
            Suit::Hearts => "Hearts",
            Suit::Clubs => "Clubs",
            Suit::Diamonds => "Diamonds",
        }
    }
}
```

这个方法引用它所调用的`Suit`枚举值(`&Self`)，并返回一个对字符串片(`&str`)的引用，该字符串片表示服装的字符串表示。

然后您可以使用这个方法将一个`Suit`值转换成一个字符串:

```
let suit = Suit::Spades;
println!("{}", suit.to_string()); // prints "Spades"
```

枚举也有几个内置的方法，可以用来操作和比较值。例如，`PartialEq`特征允许您比较两个枚举值是否相等:

```
assert!(Suit::Spades == Suit::Spades);
assert!(Suit::Spades != Suit::Hearts);
```

尽管如此，为了实现这一点，`PartialEq`特征必须在`Suit`结构上实现。显式或通过使用`derive`宏:

```
#[derive(PartialEq)]
enum Suit {
    Spades,
    Hearts,
    Clubs,
    Diamonds,
}
```

`Copy`特征允许您复制枚举的值，而不是移动它:

```
let suit = Suit::Spades;
let copied_suit = suit;
```

## 你想联系吗？

如果你想联系我，请通过 LinkedIn 联系我。

另外，请随意查看我的书籍推荐[📚。](https://medium.com/@mr-pascal/my-book-recommendations-4b9f73bf961b)

[](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [## 我的书籍推荐

### 在接下来的章节中，你可以找到我对所有日常生活话题的书籍推荐，它们对我帮助很大。

mr-pascal.medium.com](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [](https://mr-pascal.medium.com/membership) [## 通过我的推荐链接加入 Medium—Pascal Zwikirsch

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

mr-pascal.medium.com](https://mr-pascal.medium.com/membership) 

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)
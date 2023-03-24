# Rust 中的“ref”关键字

> 原文：<https://levelup.gitconnected.com/the-ref-keyword-in-rust-a81e64cda3af>

![](img/4209f0acee7dd4a332e3ab977e3b843c.png)

贝丝·麦克唐纳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

Rust 有一个非常强大的模式匹配系统。您可以匹配文字、结构、枚举、切片(不同长度)、结构中的特定字段(通过解构造)、嵌套字段、引用、范围等。

假设我们对 Rust 中的模式匹配有一个相当好的理解，让我们探索一下`ref`关键字以及`ref`和`&`之间的关系，当涉及到模式匹配的时候。

Eg1:

```
let x:i32 = 1;
match x {
  ref y => println!("{}", *y),
  _ => ()
}
```

上面的代码片段是一个非常简单的例子(*很明显,“默认”条件永远不会被满足。但是这里我们可以忽略这一点，为了这个话题*。基本上，`ref`在比赛结束后开始发挥作用。即“匹配，然后将绑定变量视为引用”。这里，匹配后，变量`y`被绑定到`x`的引用。

让我们看下一个例子:

Eg2:

```
let x:i32 = 1;
match &x {
  y => println!("{}", *y),
  _ => ()
}
```

这里，`match`的操作数是一个引用，这意味着变量`y`将被绑定到整个操作数。这是另一个例子，

Eg3:

```
let x:i32 = 1;
match &x {
  &y => println!("{}", y),
  _ => ()
}
```

操作数 is `&x`在 case ( `&y`)中被解构造，只有值部分`y`被提取(复制)。注意区别；`&`参与匹配过程，而`ref`不参与。如果操作数不是引用，此示例将给出编译错误。

从可读性的角度来看，Eg1 更好。实际上没有必要把引用传递给`match`块，如果类型不是`Copy`呢？Eg3 不会编译，因为当一个值在一个引用后面时，你不能将它移出。

让我们再看一些例子。

这里有一个例子，我们去构造一个自定义类型(枚举)并引用它的变体的字段。

```
**enum Shape {
  Circle(u8),
  Rectangle(u8, u8),
  Triangle(u8, u8)
}***// where `shape` is an instance of `Shape`* 
**match shape {** *// `radius` is of type &u8, due to ref
* **Circle(ref radius) => circle_area(radius),***// `length` and `width` are of type &u8, due to ref
*  ** Rectangle(ref length, ref width) => rect_area(length, width),**  *// `base` and `height` are of type &u8, due to ref*
   **Triangle(ref base, ref height) => triangle_area(base, height)** 
**}**
```

这里有一个例子，我们解构造了`struct`，然后匹配它的一个字段，并在另一个字段上引用。

```
enum FoodType {
    Soda,
    Pizza
}struct Details {
    name : String,
    calories : u16,
}struct Food {
    category : FoodType,
    details : Details
}fn consume(p: Food) {
    match p {
        Food {
            category: Soda,
            details: ref d,
        } => sip(d),
        Food {
            category: Pizza,
            details: ref d,
        } => bite(d),
    }
}
```

如果我们将`&p`传递给`match`块会怎么样？那能编译吗？

是啊！它可以编译。注意案例中的模式。它们都是值，所以为了匹配，编译器首先自动解引用(`*p`)。如果它是`&&p`，那么它将自动去友两次，以此类推...

这意味着`d`是一个引用，因为操作数是一个引用。

那么我们还需要保留关键字`ref`吗？虽然代码仍然可以编译，但是这个场景中的`ref`是多余的，我们在这里不需要它。

```
**fn consume(p: Food) {**
    **match &p {**  // could very well be &&p, &&&p ...
        **Food {
            category: Soda,
            details: d,** // `ref d` or `d`,both are same i.e &Details
        **} => sip(d),
        Food {
            category: Pizza,
            details: d,** // `ref d` or `d`,both are same i.e &Details
        **} => bite(d),
    }
}**
```

另一个鲜为人知的用法(模式匹配之外)是引用变量。

基本上是下面的片段:

```
let y = &mut x;
```

可以写成

```
let ref mut y = x;
```

就是这样！希望你会发现这篇简短的文章信息丰富。
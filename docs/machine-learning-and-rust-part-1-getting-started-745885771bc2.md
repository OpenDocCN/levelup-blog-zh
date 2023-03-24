# 机器学习与 Rust(第 1 部分):入门！

> 原文：<https://levelup.gitconnected.com/machine-learning-and-rust-part-1-getting-started-745885771bc2>

这是 Rust 和 ML 系列教程的第一部分。今天，让我们来了解关于生锈的 5 个基本问题！

![](img/5325a9ab89c6c8c85edec6ffed86f506.png)

[不飞溅](https://unsplash.com/photos/e9-oSbS-gCE)产生的铁锈和真菌。图片来自[桑德拉·弗雷](https://unsplash.com/@schoeneheimat)

[](https://medium.com/@stefanobosisio1/membership) [## 通过我的推荐链接加入 Medium-Stefano Bosisio

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

medium.com](https://medium.com/@stefanobosisio1/membership) 

这个教程想和经典的不一样。我们不会从 Rust 命令的冗长命名开始，也不会从如何声明变量或如何编写 hello world 开始。相反，我们将在这里开始查看 Rust 的 5 个重要概念，然后，在下一个教程中，我们将直接开始学习 Rust 在机器学习中的应用。一开始可能会很难，因为有很高的障碍要克服，但我相信你会得到最多，而且会非常有回报！所以让我们跳进铁锈吧！

*   [第二教程:](/machine-learning-and-rust-part-2-linear-regression-d3b820ed28f9/) `[rusty_machine](/machine-learning-and-rust-part-2-linear-regression-d3b820ed28f9/)` [和线性回归](/machine-learning-and-rust-part-2-linear-regression-d3b820ed28f9/)

## 1.安装铁锈

首先要知道如何安装 Rust。Rust 在实际安装周期中派上了用场(如果您使用的是 Mac 或 Linux):

```
curl --proto '=https' --tlsv1.2 [https://sh.rustup.rs](https://sh.rustup.rs) -sSf | sh
```

上面的命令将下载 Rust 并询问您:

*   继续安装(默认):如果你想让 Rust 处理所有的事情，只需按回车键
*   自定义安装:如果您想在安装过程中修改某些内容，请按 2。
*   取消安装:不是我们的情况，但如果你按 3 Rust 将不会安装

安装完成后，重新启动当前 shell 并键入:

```
rustc --version
```

如果某个版本被退回，Rust 安装正常:)

## 2.货物指令

Rust 为什么是这么有前途的语言？一个原因是`cargo`。Cargo 是 Rust 的构建系统和包管理器。事实上，`cargo`能够指导我们从构建代码到库管理，再到解决依赖，保持所有的跟踪和易于利用。

需要记住的命令很少:

*   创建新项目:

```
cargo **new** myNewApp
```

该命令将设置所有文件，特别是一个`toml`文件和一个`src`文件夹，其中有一个已经在`main.rs`中编写的“hello world”程序

*   构建项目:

```
cd myNewApp/
cargo **build**
```

命令构建我们当前的代码。首先`cd`到你的应用程序文件夹，这样 cargo 可以读取`toml`文件，然后运行构建命令。重要的是，这个阶段的构建是一个“轻量级”的构建，也就是说，Rust 不会执行任何优化。构建命令创建一个`target`文件夹和`Cargo.lock`文件。要运行构建的代码，只需输入`./target/debug/myNewApp`

*   在旅途中构建项目:

```
cd myNewApp
cargo **run**
```

`run`就像 cargo `build`一样，但它在开发应用程序时很有帮助，并且在构建时自动执行您的代码，因此您不必键入`./target/debug/myNewApp`

*   检查一切正常:

```
cd myNewApp
cargo **check**
```

测试代码是否良好，是否准备好构建，而不编译它

## 3.借贷，所有权:什么？

当我们写代码时，经常会重复使用同一个变量，比如说`var`，多次赋予它不同的值。然而这被认为是不安全的，所以在 Rust 中我们需要创建一个对变量的引用，并通过传递这个引用。这就是借力的概念。

```
fn main() {
    let name = "stefano"; 
    let name_surname = add_surname(**&**name);
    println!("{:?}", name_surname); let nickname = add_nickname(**&**name);
    println!{"{:?}", nickname);
}fn add_surname(s: &str) -> String {
    s.to_string() + " Bosisio"
}fn add_nicknams(s: &str) -> String { 
    s.to_string() + " Bosi"
}
```

## 4.关于函数的两件事

函数是这样声明的:

```
**fn** function_name(**var: type) -> returnType** {
    something;
    return_something
}
```

没有显式的`return`语句，从函数返回值只是**省略了分号；**

## 5.结构和特征

Rust 支持用`struct`创建自定义类型

```
struct Person {
    name: String, // NB there's a comma here 
    surname: String, 
    age: u32
}
```

对于每种类型，我们都可以通过引用我们想要使用的`struct`类型的**固有实现** `impl`来实现功能。例如，对于`struct Person`，我们可以有这样一个`impl Person`:

与《铁锈》中的`impl`相似，我们也有**特征。特征就像面向对象编程中的接口，它允许有一个更通用的实现，通用性强，并且我们可以随时修改。特征可以通过*固有实现或独立*实现。这里有一个`struct Person`的前一种实现类型的例子:**

虽然这里是一个 trait 的独立实现，但是如果您熟悉 C 或 Java，应该会回到接口:

所以现在大体上你可以写类似的东西:

暂时就这些吧！看起来不是很多，但是你学会 Rust，申请 ML，已经是很了不起的进步了！

请随时给我发电子邮件询问问题或评论，地址:stefanobosisio1@gmail.com

或者，你可以在 Instagram 上联系我:[https://www.instagram.com/a_pic_of_science/](https://www.instagram.com/a_pic_of_science/)
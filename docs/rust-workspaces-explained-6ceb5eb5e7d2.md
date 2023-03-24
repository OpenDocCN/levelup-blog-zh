# Rust:工作空间解释

> 原文：<https://levelup.gitconnected.com/rust-workspaces-explained-6ceb5eb5e7d2>

了解 Rust workspaces:一个用多个板条箱组织和构建复杂项目的工具，支持代码共享和简单的构建/测试。

![](img/27f985263bba3747eabc6a34e4ea75cd.png)

## 什么是 Rust 工作区？

Rust workspaces 是一种在单个项目中组织多个 Rust 板条箱的方法。它们允许开发人员将多个机箱作为一个单元来管理，使得构建、测试和分发复杂的项目变得更加容易。Rust 工作区是一个包含 Cargo.toml 文件的目录，该文件用于定义组成工作区的板条箱及其依赖关系。

Cargo 是 Rust 的包管理器和构建工具，它用于管理和构建 Rust 工作区。当您在工作区中运行类似于`cargo build`或`cargo test`的命令时，Cargo 将自动在工作区中构建所有的板条箱，并考虑它们的依赖关系。这意味着您可以在一个命令中构建和测试所有的板条箱，而不必为每个板条箱运行单独的命令。

使用 Rust 工作空间的一个主要好处是，它们允许您在机箱之间共享代码。例如，如果您有一个要在多个板条箱中使用的公共库，您可以将它放在一个单独的板条箱中，然后将其作为从属项包含在其他每个板条箱中。这有助于减少重复，并使维护和更新代码变得更加容易。

要在特定的包上运行`cargo`命令，可以使用`--package PACKAGE_NAME`参数。例如:

```
cargo build --package my_package
```

这将在工作区中构建`my_package`箱。

## 如何使用 Rust 工作区

要创建 Rust 工作区，首先需要创建一个新目录，然后在其中创建 Cargo.toml 文件。您可以手动完成这项工作，也可以使用`cargo new`命令，这将使用一个基本的 Cargo.toml 文件创建一个新的工作区。

一旦你创建了你的工作空间，你可以通过在工作空间内创建新的目录并使用`cargo new`命令在每个目录中创建一个新的板条箱来添加额外的板条箱。每个板条箱都有自己的 Cargo.toml 文件，您可以使用该文件来定义它的依赖项并指定它的构建配置。

要构建和测试您的工作空间，您可以分别使用`cargo build`和`cargo test`命令。这些命令将构建和测试工作空间中的所有板条箱，并考虑它们的依赖关系。

如果要在工作区中使用另一个板条箱中的一个板条箱，可以将其作为依赖项添加到依赖它的板条箱的 Cargo.toml 文件中。例如，如果您的工作区有一个名为“common”的机箱和另一个名为“app”的机箱，您可以将“common”作为依赖项添加到“app”的 Cargo.toml 文件中，如下所示:

```
[dependencies]
common = { path = "../common" }
```

这将告诉 Cargo 在构建和测试“app”板条箱时构建和链接“common”板条箱。

现在，您可以在代码中使用“公共”箱中的函数和类型，方法是用关键字`use`导入它们，后跟库的路径。例如，如果您的“通用”机箱有一个名为“foo”的函数，您可以像这样在代码中使用它:

```
use common::foo;

fn main() {
    foo();
}
```

这将从“公共”箱中调用“foo”函数。

## 结论

总之，Rust workspaces 是一个强大的工具，用于组织和构建由多个箱子组成的复杂项目。它们允许您将多个板条箱作为一个单元来管理，在板条箱之间共享代码，并在一个命令中构建和测试您的所有板条箱。通过使用 Rust workspaces，您可以提高项目的可维护性和可伸缩性，并使与其他开发人员的协作变得更加容易。

## 你想联系吗？

如果你想联系我，请在 [LinkedIn](https://www.linkedin.com/in/pascal-zwikirsch-3a95a1177/) 联系我。

另外，可以随时查看[我的书籍推荐](https://medium.com/@mr-pascal/my-book-recommendations-4b9f73bf961b)📚。

[](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [## 我的书籍推荐

### 在接下来的章节中，你可以找到我对所有日常生活话题的书籍推荐，它们对我帮助很大。

mr-pascal.medium.com](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [](https://mr-pascal.medium.com/membership) [## 通过我的推荐链接加入 Medium—Pascal Zwikirsch

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

mr-pascal.medium.com](https://mr-pascal.medium.com/membership)
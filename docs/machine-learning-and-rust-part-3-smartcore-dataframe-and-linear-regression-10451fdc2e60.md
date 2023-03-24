# 机器学习和 Rust(第三部分):智能核心、数据框架和线性回归

> 原文：<https://levelup.gitconnected.com/machine-learning-and-rust-part-3-smartcore-dataframe-and-linear-regression-10451fdc2e60>

在本教程中:我们能拥有铁锈中的熊猫吗？什么是 smartcore？

![](img/7338fcc1de8821d5aa54820a56988d87.png)

图片由 [Cloris Ying](https://unsplash.com/@clorisyy) 在 [Unsplash](https://unsplash.com/photos/SckmWxP8ImQ)

[](https://medium.com/@stefanobosisio1/membership) [## 通过我的推荐链接加入 Medium-Stefano Bosisio

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

medium.com](https://medium.com/@stefanobosisio1/membership) 

欢迎回到 Rust 及其在 ML 中的应用的第三个教程！今天我们将学习 [Polars](https://docs.rs/polars/0.14.2/polars/index.html) 一个处理数据帧和系列的神奇 Rust 软件包和 [smartcore](https://docs.rs/smartcore/0.2.0/smartcore/) ，它将是我们最好的 ML Rust 朋友之一:)

*   **今日教程参考 Github repo 在这里:**[https://Github . com/ste boss/ML _ and _ Rust/tree/master/tutorial _ 1](https://github.com/Steboss/ML_and_Rust/tree/master/tutorial_2)
*   在这里你可以找到我对 Rus 的简介
*   [之前的教程—第二部分—关于](https://stefanobosisio1.medium.com) `[rusty_machine](https://stefanobosisio1.medium.com)` [和线性回归](https://stefanobosisio1.medium.com)
*   今天我们将处理[线性回归](https://en.wikipedia.org/wiki/Linear_regression)和著名的[波士顿房屋数据集](https://www.cs.toronto.edu/~delve/data/boston/bostonDetail.html)。
*   为了测试 Rust online 的功能，我可以向你推荐这个神奇的应用程序[https://play.rust-lang.org](https://play.rust-lang.org)。

本教程结束时，您将学到什么？

*   在 Rust 中处理数据帧和序列及其操作
*   使用 smartcore 实施线性回归
*   在同一代码中混合使用 smartcore 和 polars

## 铁锈色数据框

一个最常用的数据科学-d 对象是`dataframe`。如果你是一名数据科学家或者是一名 Pythonist 爱好者，你肯定玩过`Pandas`和数据帧。顾名思义，这些计算对象是“插入”在表格框架中的数据，这允许简单的数据可视化和数据管理操作。Rust 有自己的 dataframe 管理包，其中一个是 [Polars](https://docs.rs/polars/0.14.4/polars/index.html) 。

Polars 是一个完全并行的数据处理器，基于由 [Ritchie Vink](https://www.ritchievink.com/blog/2021/02/28/i-wrote-one-of-the-fastest-dataframe-libraries/) 编写的 [Apache Arrow](https://github.com/apache/arrow) 。这个软件包已经记录了相对于流行的数据帧软件包如 R 中的`data.table`和`Spark`的快速性能。Polars 的目标是处理对熊猫来说太大而对 Spark 来说太小的数据。Polars 存在于两个 API 中:`eager`，在这里操作被立即执行，就像在 pandas 中一样，还有`lazy,` ，它针对查询和数据连接进行了优化。深入研究 Polars 超出了本教程的范围，但是我肯定很快会写一些关于这方面的东西，并提供关于复杂数据操作的很好的基准。

看看机器学习的人能用这个包做什么，所以`cargo new polars_learning`！而数据集可以在这里找到:[https://github . com/ste boss/ML _ and _ Rust/tree/master/tutorial _ 2/datasets](https://github.com/Steboss/ML_and_Rust/tree/master/tutorial_2/datasets)和代码在这里:[https://github . com/ste boss/ML _ and _ Rust/tree/master/tutorial _ 2/polars _ learning](https://github.com/Steboss/ML_and_Rust/tree/master/tutorial_2/polars_learning)

第一件事是:如何处理`Cargo.toml`？[让我们添加第一个必需的依赖项:](https://github.com/Steboss/ML_and_Rust/blob/master/tutorial_2/smartcore_linear_regression/Cargo.toml)

目前 polars 的最新版本是`0.14.2`

> 我们如何读取一个 csv 文件，并把它作为一个数据帧？

好的，这里有很多信息。首先让我们来看看进口:

*   我们需要处理`polars`的所有东西都在`prelude`(或多或少)，所以我们可以从那里`use polars::prelude::*;`导入所有东西，或者我们可以只导入我们需要的东西(例如`CsvReader, DataType, DataFrame`……)
*   然后`use std::fs::File`和`use std::path::{Path}`用于读取给定的文件，如`iris.csv`
*   最后，`use polars::prelude::SerReader`包含了我们需要让`CsvReader`与方法`::new`一起工作的所有特征。记住，我们从`CsvReader`返回一个数据帧，所以没有`;`

要读取 csv 文件，我们可以利用`CsvReader`:

*   用`let file = File::open(path).expect("Cannot open file.");`定义输入文件
*   检查是否存在一些割台`.has_header`
*   并收集所有内容`.finish()`。最终输出是`PolarResult<DataFrame>`类型，即一个数据帧

最后`Some`类似于 Haskell 的`Just`和`Nothing`，它是一个`variant enum`，允许读取前 5 行。

读取数据帧后:

> 我们想知道更多关于它的大小和形状。

这里没有什么太复杂的，步骤类似于 Python 的 Pandas，如在这个函数中:

我们没有从这个函数返回任何东西，所以`-> ()`。一旦创建了数据帧，我们就可以得到`df.shape()`并将其打印为`{:#?}`以允许正确的解析。此外，我们可以检查每一列的`schema`和`dtype`，`width` —即列数，以及`height` —即行数。

> 我们可以检查柱子，看看它们的样子:

使用`get_columns()`我们可以读取数据帧的所有列，`get_column_names()`检索所有标题。此外，我们可以遍历列值，像 Python 一样打印列名及其值。

现在更性感的东西:

> 将数据帧堆叠在一起。

我们经常需要堆叠不同的数据帧，尤其是当我们读取一个巨大的文件时(Polars 也会这样吗？我们很快就会回来):

那么我们有什么？

*   使用`vstack`的垂直堆栈，我们可以将两个数据帧连接在一起(在 Pandas 中这是`pd.concat([df1, df2])`)
*   我们可以从列`df3.column("sepal.length").unwrap()`中提取一个`&series`类型
*   更重要的是我们可以考虑提取一个序列来执行一些操作，这样我们就可以得到一个`series`类型:

`let sepal_length = df3.drop_in_place("sepal.length").unwrap();`

*   然后，我们可以对`sepal_length`进行一些操作，并将其重新添加回`df`，如下所示:

`let _df4 = df3.insert_at_idx(0, sepal_length).unwrap()`

(这里的下划线在`df4`之前，因为在 Rust 中——为了最佳实践——未使用的变量必须有`_`)

现在你知道如何处理级数了，

> 让我们来看看如何对序列进行实际操作。

例如，我们希望对 dataframe 列进行对数转换:

*   首先，我们可以直接对带有`apply_at_idx`——第 22 行的列应用闭包。这是一个 dataframe 方法，我们需要给出我们想要修改的列的索引，以及映射操作，例如`|s| s+1`给列加 1
*   否则我们可以直接对该系列采取行动。在这种情况下，我们可以考虑使用一个可变的数据帧和一个函数`numb_to_log` : **a)** 在第 4 行我们将一个序列转换成一个*分块数组* — *即一个类型化数组，它允许对数据应用闭包并收集类型为* `T`的结果。 **b)** 操作`drop_in_place.unwrap().rename()`从`sepal.length`创建一个系列，并将其重命名为`log10.sepal.length`。然后，`.f64().unwrap()`指定该系列的类型，`unwrap`将该系列转换为`ChunkedArray`。 **c)** 最后，我们可以将这个数组转换为 log10-array: `cast::<Float64Type>()`转换为正确的数字类型并返回一个类似于`Result<>`的对象，因此我们必须`unwrap`得到一个 f64 数组，然后`apply(|s| s.log10())`对数字进行对数转换。`log10`是一个 f64 号式的锈法。d)重要的是，我们可以用`into_series()`将一个`ChunkedArray`转换回一个系列，并返回到主函数，用`df.with_column()`将新系列添加为一列
*   上面是一个关于直接处理序列和分块数组的很好的例子，但是我们能直接在数据帧上做同样的事情吗？当然我们可以-第 31 行-:首先`apply_at_idx`告诉 dataframe 我们想要对一列应用操作，然后我们可以用`|s|`映射这个列，将它转换成一个分块数组，`s.f64().unwrap()`，最后应用操作 so `apply(|t| t.log10()))`，其中`|t|`是指列元素。

我想给你看的关于 polars 的最后一样东西是与`Cargo.toml`文件相关的东西，即`features`的概念。在 Rust 中，任何包都有额外的特性，比如说方法，可以在需要的时候使用。例如，`polars`能够将一个数字数据帧转换成一个名为`to_ndarray`的数组:

**如果没有明确添加到** `Cargo.toml`中，特性不会立即生效。这是为了确保最终编译的 Rust 包没有过大的尺寸，并且有助于减少编译时间。我们可以看到`polars`有大量可以使用的附加功能:[https://github . com/pola-RS/polars/blob/master/polars/cargo . toml](https://github.com/pola-rs/polars/blob/master/polars/Cargo.toml)

要添加包含在`polars-core`中的`to_ndarray`——所有数据帧操作的核心——我们需要将其显式添加到 Cargo 文件中:

只有这样，我们才能使用`to_ndarray`作为数据框架。

## Smartcore + Polars:用数据帧实现线性回归！

[Smartcore](https://docs.rs/smartcore/0.2.0/smartcore/) 是一个相当新的 Rust 包，有很多用于机器学习的应用程序，并且有一个非常活跃的社区。smartcore 中的机器学习算法从分类到聚类，再到模型评估的指标——关于`rusty machine`还有几个

此外，smartcore 与不同的 Rust 代数库进行了优化集成，如`ndarray`或`nalgebra`，这使得该软件包在处理不同的数据类型时更加灵活。与 smartcore 的积极贡献者 Lorenzo 讨论，在实现更通用的数据解析方面仍有差距，因此我们将很快看到在 smartcore 中集成 polars 数据帧的更快方法，但现在我们利用这个机会了解 Rust 中的更多数据！

现在，让我们用 Smartcore 来弄脏我们的手:`cargo new smartcore_linear_regression`代码可以在这里找到:[https://github . com/ste boss/ML _ and _ Rust/tree/master/tutorial _ 2/smart core _ linear _ regression](https://github.com/Steboss/ML_and_Rust/tree/master/tutorial_2/smartcore_linear_regression)

首先，让我们准备好`Cargo.toml`所需的依赖项和特性:

第二，让我们跳到`main.rs`上，想出实施线性回归的 4 个步骤:

*   读取输入`boston_dataset.csv`并解析成 Polars 数据帧
*   提取相关的培训特征和目标
*   将功能和目标转换为 smartcore `DenseMatrix`格式
*   运行`LinearRegression`:)

[在所有常用的导入中，我们要导入](https://github.com/Steboss/ML_and_Rust/blob/2cdff2152cfa7337571114ee980b857395a0aaf0/tutorial_2/smartcore_linear_regression/src/main.rs#L2) `[smartcore](https://github.com/Steboss/ML_and_Rust/blob/2cdff2152cfa7337571114ee980b857395a0aaf0/tutorial_2/smartcore_linear_regression/src/main.rs#L2)` [功能](https://github.com/Steboss/ML_and_Rust/blob/2cdff2152cfa7337571114ee980b857395a0aaf0/tutorial_2/smartcore_linear_regression/src/main.rs#L2)，即`LinearRegression`、`DenseMatrix`、`BaseMatrix`、`train_test_split`和`mean_squared_error`。正如我们将看到的,`smartcore`需要一个基于`nalgebra`包的矩阵类型`DenseMatrix`或`BaseMatrix`作为输入。

前两步是从 Rust 学习更多新知识的好机会:

*   在`read_csv`中，我留下了可能有用的方法`with_delimiter`。然而，对于这个数据集来说，情况并非如此，`with_delimiter`只需要字节作为输入。例如，考虑一个散列作为列之间的分隔符:`with_delimiter(b'#')`——注意我们使用的是`'`而不是引号`"`，因为引号会引起错误
*   从`feature_and_target`开始，我们第一次同时返回两个变量。简单地将变量及其类型括在括号中`()`
*   在这里你可以看到`polars`比[定制 csv 阅读器](/machine-learning-and-rust-part-2-linear-regression-d3b820ed28f9)更方便，在那里我们可以`select`我们想要的列。要选择多列，我们需要通过一个 Rust `vec`，因此`vec![col1, col2, ...]`

步骤 3:将特征数据框和目标列转换为所需的 smartcore 格式`DenseMatrix`

*   如您所见，在第 7 行，我们使用`ndarray`将数据帧转换为数组。从那里我们可以初始化一个零矩阵`xmatrix`。
*   这个矩阵属于带有`<f64>`个数字的`DenseMatrix`类型。[要创建零矩阵，我们可以简单地使用](https://docs.rs/smartcore/0.2.0/smartcore/linalg/trait.BaseMatrix.html) `[BaseMatrix::zeros](https://docs.rs/smartcore/0.2.0/smartcore/linalg/trait.BaseMatrix.html)`。
*   然后稍微注意一下 Rust 类型，我们初始化两个计数器，一个用于行`row`，一个用于列`col`，我们遍历数组值。
*   迭代过程是`features_res`是一个 1D 数组。在每次迭代中，我们通过使用`*val`*解引用借用的*值，将一个值`set`到`xmatrix`中，这样就不会有错误:`expected f64 not &f64`。最后，我们可以用`Ok`返回`DenseMatrix`

类似的方法也被用于将`target`数组转换为`vector`——注意在 Rust 中的向量中插入一个值看起来与 C++ `push`相似

最后，强调所有这些变量的`mut`很重要，因为我们已经将它们初始化为零，然后填充它们。在 github 代码中，我还保留了——注释——不使用函数在`main`中做同样事情的过程。

最后在 smartcore 中进行线性回归和拟合！这超级简单，它记得我的方法，使`smartcore`成为一个很棒的库——至于`rusty_machine`,我们不必担心`train_test_split`:

就这么简单！

# 准备，稳住，开始！

现在我们已经为运行代码做好了一切准备。像往常一样，你可以在 Rust 文件夹中运行`cargo run`。如果一切顺利，您应该会看到一个`Cargo.lock`文件和`target`文件夹，其中包含我们编译的代码。此外`cargo run`将运行我们的`main.rs`

如果你足够高兴，你可以用`cargo build`构建整个包，它将对我们的代码运行进一步的优化，瞧！

🎊🎊🎊

暂时就这些吧！绝对是今天学习 Rust 的一个显著进步！敬请期待下一期教程！

请随时给我发电子邮件询问问题或评论，地址:stefanobosisio1@gmail.com

或者，你可以在 Instagram 上联系我:[https://www.instagram.com/a_pic_of_science/](https://www.instagram.com/a_pic_of_science/)
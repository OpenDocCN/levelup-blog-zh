# 在 Go 中轻松读写 CSV 文件

> 原文：<https://levelup.gitconnected.com/easy-reading-and-writing-of-csv-files-in-go-7e5b15a73c79>

浅谈如何在 Go 中读写 CSV 文件？

![](img/cf489f9e8e220fc3382c4752824bf659.png)

照片由 [Aaron Burden](https://unsplash.com/@aaronburden) 在 [Unsplash](https://www.unsplash.com) 拍摄

读写 CSV 文件是编程中非常常见的用例。此外，由于 CSV 文件的直接格式，它非常容易理解和实现。

# 代码

一如既往，我为您准备了一个小的 GitHub 库，您可以在那里查看完整的代码:

[](https://github.com/Abszissex/medium-csv-read-write) [## abszisex/medium-CSV-读写

### 通过在 GitHub 上创建一个帐户，为 abszisex/medium-CSV-read-write 开发做出贡献。

github.com](https://github.com/Abszissex/medium-csv-read-write) 

所以我们先从定义一些基本结构开始。在下一个代码片段中，您可以看到实现了一个小的 helper 方法，该方法只记录可用的错误。这个助手在实际实现中被调用。当然，对于生产程序来说，这不是正确的错误处理，但是目前有助于避免重复太多。

除此之外，我们正在定义我们的`Person`结构，它应该表示稍后从 CSV 文件中读取和写入的数据格式。

# 阅读 CSV

要读取 CSV 文件，首先需要定义两件事。

1.  CSV 文件位于哪里？→ `filePath string`
2.  CSV 数据应该转换成什么结构(`struct`)？→ `Person`

因此，让我们从创建一个`readCSVFile`方法开始，该方法将文件路径作为输入，并返回一部分`Person`

实际代码非常简单。开始时，程序试图打开文件并在其上创建一个 CSV reader 对象，然后该对象在无限循环中逐行读取文件。

定义的唯一退出标准是在到达文件结尾(EOF)时通过读取错误检查。

如果不是这种情况，则检查读取器当前是否在第一行。因为 CSV 文件通常有一个标题行，所以以不同的方式处理该行是有意义的。在这种情况下，当 CSV 单元的值(→列标题)是键**和索引(→列索引)是映射的值**时，映射被填充。

在接下来的行中，这部分被跳过，一个新的人被创建并附加到`persons`切片。这里你可以看到`record[headerMap["XYZ"]]`正在被访问。这样，CSV 文件的列顺序可以完全随机，程序不会在意，因为它已经创建了一个从列名到索引的映射——就像处理第一行一样，因此可以很容易地访问它。

尽管这种方法不是必需的，并且可以简单地使用类似于`record[i]`的索引直接访问记录中的值，但是我建议使用这种方法来防止由于改变列顺序而导致的错误，这在实际情况中很容易发生。这里唯一需要注意的是列名必须匹配通过`headerMap`访问的字符串。

# 编写 CSV

编写 CSV 文件和读取它们一样容易，如果不是更容易的话。类似于阅读的情况，也有一些东西必须被定义。

1.  新文件的输出路径→ `outputPath string`
2.  应该写入 CSV 的输入格式→ `Person`
3.  CSV 文件的标题行→ `headerRow []string`

首先定义`headerRow`，包含 CSV 文件的标题行，并添加到一般的`data`数组中，该数组将在最后逐行写入 CSV 文件。

接下来，`Person`对象的数组需要被转换成字符串数组——考虑到`headerRow`内部的顺序——并且也被添加到`data`数组中。

现在唯一剩下的事情就是

*   创建新文件
*   创建新的编写器
*   将数据(`data`)逐行写入文件

超级直白吧？😉

# 把所有东西放在一起

现在让我们把所有东西放在一起。我们想读取一个文件，修改读取的内容，然后写入其他文件。这是一个简单的管道工作。

首先，我们定义包含`Person`数据的`input.csv`，它将被 Go 应用程序读取:

在下面的`main.go`文件中`input.csv`将被读取，所有被读取的`Person`的`Country`属性将被修改为`AnotherCountry`，然后修改后的`Person`将被写回`output.csv`。

# 最后的话

我希望我可以给你一个小的但可以理解的例子，告诉你如何使用 Go 处理 CSV 文件，并帮助你在下一个项目中开始使用 CSV 文件。

感谢您花时间阅读我的文章。

## 你想联系吗？

如果你想联系我，请通过 LinkedIn 联系我。

另外，请随意查看我的书籍推荐📚。

[](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [## 我的书籍推荐

### 在接下来的章节中，你可以找到我对所有日常生活话题的书籍推荐，它们对我帮助很大。

mr-pascal.medium.com](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [](https://mr-pascal.medium.com/membership) [## 通过我的推荐链接加入 Medium—Pascal Zwikirsch

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

mr-pascal.medium.com](https://mr-pascal.medium.com/membership)
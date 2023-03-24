# 如何在 Rust 中高效的逐行读取一个文本文件？

> 原文：<https://levelup.gitconnected.com/how-to-read-a-text-file-line-by-line-efficiently-in-rust-9f9a68afbf35>

![](img/15e380b7f9e57cc53357de354c5854d5.png)

# 如何打开文件进行阅读？

在 Rust 中，对文件系统上打开文件的引用由 struct `std::fs::File`表示。我们可以使用`File::open`方法打开一个已存在的文件进行读取:

```
pub fn open<P: AsRef<Path>>(path: P) -> Result<File>
```

该函数将一个路径(确切地说，它可以从其中借用一个`&Path`的任何东西，即`AsRef<Path>`)作为参数，并返回一个`io::Result<File>`。

# 如何高效的逐行读取一个文件？

为了提高效率，阅读器可以被*缓冲*，这仅仅意味着它们有一块内存(一个缓冲区)来保存内存中的一些输入数据。这节省了系统调用。在 Rust 中，`BufRead`是`Read`的一种，它有一个内部缓冲器，允许它执行额外的读取方式。注意`File`不是自动缓冲的，因为`File`只实现`Read`而不是`BufRead`。然而，很容易为`File`创建一个缓冲读取器:

```
BufReader::new(file);
```

最后，我们可以使用`std::io::BufRead::lines()`方法来返回这个缓冲阅读器的行上的迭代器:

```
BufReader::new(file).lines();
```

# 把所有东西放在一起

现在，我们可以轻松地编写一个函数，高效地逐行读取文本文件:

```
use std::fs::File;
use std::io::{self, BufRead, BufReader, Lines};
use std::path::Path;fn read_lines<P>(path: P) -> io::Result<Lines<BufReader<File>>>
where P: AnyRef<Path>,
{
    let file = File::open(path)?;
    Ok(BufReader::new(file).lines())
}
```

# 分级编码

感谢您成为我们社区的一员！更多内容见[升级编码出版物](https://levelup.gitconnected.com/)。
跟随:[推特](https://twitter.com/gitconnected)，[领英](https://www.linkedin.com/company/gitconnected)，[通迅](https://newsletter.levelup.dev/)
**升一级正在转型理工大招聘➡️** [**加入我们的人才集体**](https://jobs.levelup.dev/talent/welcome?referral=true)
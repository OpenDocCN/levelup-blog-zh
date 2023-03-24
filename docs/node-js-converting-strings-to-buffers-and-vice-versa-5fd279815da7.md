# Node.js —将字符串转换为缓冲区，反之亦然

> 原文：<https://levelup.gitconnected.com/node-js-converting-strings-to-buffers-and-vice-versa-5fd279815da7>

在 Node.js 中使用一行程序轻松地将字符串转换为缓冲区，将缓冲区转换为字符串。

![](img/7ac00ae084e41c6b7febc6566bd2bf5e.png)

幸运的是，Node.js 自带了现成的工具，可以使用全局`Buffer`对象来处理缓冲区。所以你不需要任何外部库。

## 将字符串转换为缓冲区

要从字符串创建缓冲区，可以使用`Buffer.from(...)`方法。您不必显式地导入/要求它，`Buffer`对象在全局范围内是可用的。

如果由于某种原因，您的 Node.js 版本因为您没有导入它而抛出错误，您仍然可以使用以下命令显式导入它:

```
const { Buffer } = require('buffer');
// OR
import { Buffer } from 'buffer';
```

下面的代码片段显示了字符串的初始化及其到缓冲区的转换。

作为第一个参数，`Buffer.from(...)`方法接受一个字符串。即使静态方法被重载，其他输入类型也是可能的。当您决定输入字符串时，第二个参数需要一个可选的编码。在大多数情况下，当使用字符串时，你会想要使用`utf-8`。尽管你不必显式地将`utf-8`设置为编码，因为这是默认设置。

最终的方法签名如下:`Buffer.from(string[, encoding])`

## 把黄油变成细绳

当你想把一个缓冲区转换回一个字符串的时候，它是简单明了的，并且是受支持的。

创建的 buffer 对象有一个`toString(...)`方法，将缓冲区转换回字符串。

例如，在下面的代码片段中，您可以看到我们如何从一个字符串创建一个新的缓冲区，同上，然后使用`toString(...)`方法将缓冲区转换回我们的初始字符串。

类似于`Buffer.from(...)`方法，`toString(...)`方法也有一个可选的`encoding`参数，此外还有一些其他可选的参数。

该方法的默认编码也是`utf-8`。

所以`toString()`和`toString("utf-8")`一样。

## 你想联系吗？

如果你想联系我，请在 LinkedIn 上给我打电话。

另外，请随意查看我的书籍推荐📚。

[](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [## 我的书籍推荐

### 在接下来的章节中，你可以找到我对所有日常生活话题的书籍推荐，它们对我帮助很大。

mr-pascal.medium.com](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [](https://mr-pascal.medium.com/membership) [## 通过我的推荐链接加入 Medium—Pascal Zwikirsch

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

mr-pascal.medium.com](https://mr-pascal.medium.com/membership)
# 用 R 进行批量图像编辑

> 原文：<https://levelup.gitconnected.com/batch-editing-images-with-r-3d4aef08bc62>

使用函数式编程编辑和文件转换任意多的图像。

![](img/14b842273f239d1f88ce6782b0e674bb.png)

照片:Unsplash 上的 Josh Hild

大多数程序员可能使用 R 作为统计或“常规”数据分析的工具。当然，作为一名研究人员，这也是我经常使用它的原因。

但是，借助于一系列极大地扩展了其功能的软件包，R 的能力扩展得更远。将这一点与函数式编程的能力结合起来，可以解决更多的问题。

magick 包就是一个很好的例子。作为 C++库 ImageMagick 的包装器，这个包提供了各种简单的方法来编辑和操作图像。就其本身而言，它可以处理文件转换、多层图像编辑，甚至以一种即使对 R 新手也相当直观的方式处理动画。

# 但是这有什么关系呢？

图像处理工具无处不在。几乎任何设备都自带图像编辑软件，裁剪和调整照片是大多数科技用户的第二天性。什么时候需要加载一个 R 项目来做这些呢？

编程图像处理的真正强大之处在于能够一次处理大量图像，同时创建一个可共享的源文件。尽管像 Photoshop 这样的编辑器可以进行批量操作，但这些解决方案的可重复性较差。更不用说，他们是以购买软件的成本来的。

相比之下，一个 R 脚本可以上传到一个公共存储库中，然后任何人、任何地方都可以访问，用户或创建者无需支付任何费用。这创造了一定程度的透明度，这在许多用例中都是需要的。就我个人而言，它帮助我的科学变得更容易被其他人复制。因为我追求简洁的代码，所以用 magick 批量编辑我的图像是显而易见的。

# 一个真实的例子，解释说

这是我作为研究助理编写的一些代码，用来编辑数百个。实验中使用的 pdf 图像。

```
library(magick)
library(tidyverse)

imgnames <- list.files(path=paste0(getwd(), "/input_folder/"))

images <- map(imglist, ~ image_read_pdf(paste0(getwd(), "/input_folder/", .x))) %>%  
    map(~ image_scale(.x, "500")) %>%  
    map(~ image_modulate(.x, saturation = 0))

output_names <- paste0(1:length(imglist), ".jpg")

map2(images, output_names, ~ image_write(.x, path=paste0(getwd(), "/output_folder/", .y), format = "jpeg")
```

这个脚本需要完成三件事:

1.  将每个图像缩放到统一的大小(500 像素宽)
2.  降低每个图像的饱和度
3.  将每个图像从。pdf 格式转换为。jpeg 格式

为此，我使用了 magick 和 [tidyverse](https://tidyverse.tidyverse.org/articles/paper.html) 包中的函数。虽然我将一步一步地解释这个脚本，但是我将假设对 tidyverse 的关键原理有一些预先的理解，特别是[管道操作符](https://towardsdatascience.com/an-introduction-to-the-pipe-in-r-823090760d64) ( `%>%`)。在编写各种简洁的 R 代码时，这一点将会发挥作用，值得单独阅读。

首先，我开始读入我所有未编辑的图像，并将它们存储在一个列表中( *images* )。我可以通过将未编辑图像文件夹中的所有文件名存储到一个矢量( *imgnames* )中来实现这一点。

```
imgnames <- list.files(path=paste0(getwd(), "/input_folder/"))
```

然后，我使用这个向量通过函数 image_read_pdf 将每个图像读入我的图像列表。

```
images <- map(imglist, ~ image_read_pdf(paste0(getwd(), "/input_folder/", .x))) %>%
```

在另一种编程语言中，我可能会使用 for 循环来读取每个未编辑的图像。相反，我可以在这里发挥 R 的优势，使用`map`函数在一行代码中实现这一点。tidyverse 中我最喜欢的部分之一是，`map`在每次迭代中使用向量或列表中的一项作为输入来重复应用一个函数。这个过程的结果被输出到一个列表中。

我使用波形符(~)指定要“映射”的函数，然后使用`.x`表示给定迭代中的输入。使用`map`的表达式不仅看起来非常整洁，而且通常比 for 循环更快，这使得它们更加有用。

[](https://towardsdatascience.com/how-to-use-map-functions-for-data-science-in-r-8180c2ed6d79) [## 如何在 R 中使用地图函数进行数据科学

### 从 tidyverse 学习强大的函数式编程工具

towardsdatascience.com](https://towardsdatascience.com/how-to-use-map-functions-for-data-science-in-r-8180c2ed6d79) 

通过在我们的图像列表中映射`image_read_pdf`来读取我的图像后，我可以开始操作它们了。我想在这里应用一些编辑(缩放和去饱和)。为了节省每一步定义新变量的时间，我将前一个函数的结果通过管道传递给下一个函数。这是常见的 tidyverse 语法，使每一步都更具可读性。

我应用函数`image_scale`，指定我想要的结果图像的大小(500 px 宽)。为了将它应用于列表中的每一张图片，我映射了这个函数，就像我在前面一行中用 image_read_pdf 所做的那样。

```
map(~ image_scale(.x, "500")) %>%
```

然后我用`image_modulate`做同样的事情来调整图像饱和度，指定一个完全的去饱和。同样，我在 map 中应用了这个函数，使我列表中的所有图片去饱和。

```
map(~ image_modulate(.x, saturation = 0))
```

这段代码运行后，我们所有的图像都被编辑了——全部在四行中，没有使用任何循环。现在，剩下要做的就是将图像以 jpeg 格式写入另一个文件夹。

为了编写编辑过的图像，我需要给它们命名。我使用`paste0`函数创建了一些简单的新名称(“1.jpg”、“2.jpg”、“3.jpg”等等)，但是任何其他想要的输出名称在这里也可以。

```
output_names <- paste0(1:length(imglist), ".jpg")
```

为了将图像写入所需的文件，我使用了`map2`中的 image_write 函数。我使用`map2`,因为它向函数传递两个输入，而不是像`map`那样传递一个输入。这是必要的，因为对`image_write`的每个调用都需要两个输入(一个图像和它的名称)。我还确保指定我的图像应该保存在。jpeg 格式。

```
map2(images, output_names, ~ image_write(.x, path=paste0(getwd(), "/output_folder/", .y), format = "jpeg")
```

一旦这段代码块执行完毕，脚本就完成了。我们已经阅读，重新调整，去饱和，并把每一个。用几行代码将 pdf 图像转换成. jpeg 格式。所有这些都不依赖于 R 的缓慢循环——非常简洁。

这只是表面现象。更进一步，这里使用的每个图像编辑功能都可以用 magick 提供的许多其他功能来替换。由于管道操作符，可以应用更多的编辑功能，脚本仍然是可读的。使用条件语句和检索图像数据可以进一步扩展功能。

即使对于简单的用途，在 R 中批量处理图像也是值得的。正如我们所看到的，它可以生成一些非常简洁但可读性很强的脚本，效果非常好。如果你是一个远离 Photoshop 的 R 用户，试试看，给你的工作流程添加一些 magick。

想阅读我所有关于编程、数据科学等方面的文章吗？在[这个链接](https://medium.com/@roryspanton/membership)注册一个媒体会员，就可以完全访问我所有的作品和媒体上的所有其他故事。这也直接帮助了我，因为我从你的会员费中得到一小部分，而不需要你额外付费。

你也可以通过[订阅这里](https://roryspanton.medium.com/subscribe)让我的所有新文章直接发送到你的收件箱。感谢阅读！
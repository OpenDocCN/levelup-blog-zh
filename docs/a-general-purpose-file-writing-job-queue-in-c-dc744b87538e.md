# C++中的通用文件写作业队列

> 原文：<https://levelup.gitconnected.com/a-general-purpose-file-writing-job-queue-in-c-dc744b87538e>

**注**:该项目的代码可以在[项目的 GitHub 页面](https://github.com/anthonymorast/mols-research/tree/square_hash_dev/src/FileManager)找到。

![](img/d4890788608473e6f110be2ef5b1954f.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [Geri Forsaith](https://unsplash.com/@geriforsaith?utm_source=medium&utm_medium=referral) 拍摄的照片

# 灵感

在最近的一系列帖子中，我写了一个叫做拉丁方的[组合对象，一个用 C++编写的用于处理这些对象](https://medium.com/star-gazers/a-primer-on-latin-squares-with-some-research-objectives-5f3ecb560544)的[类，以及一些我开发的用于确定这些对象](https://www.anthonymorast.com/blog/2019/11/25/a-c-latin-square-class/)的某些属性的 [GPGPU(通用 GPU)算法。尽管通过将该项目的代码基础移植到 C++并使用多线程(MPI、pthreads)和 GPGPU 计算重新实现算法，性能得到了提高，但由于需要处理的方块](https://www.anthonymorast.com/blog/2020/01/13/using-gpgpu-computing-to-generate-normalized-latin-squares/)数量的[组合增长，随着拉丁方块顺序的增加，内存效率问题已经出现并将持续存在。即使现在，当运行相对较小订单的算法时，我也在使用机器上所有可用的 32 GB 内存，导致程序崩溃。](https://en.wikipedia.org/wiki/Latin_square#Number)

这个问题的一个解决方案是将拉丁方写入许多文件，这些文件可以在分析结果之前进行后期处理。这允许对象存储拉丁方块(向量、集合、无序集合等。)被清除，释放一些内存。当实现一个文件管理器在继续处理拉丁方块的同时处理这些文件时，我认为这种队列可能有更广泛的应用，并创建了一个简单的框架，可以很容易地集成到任何项目中。在本文中，我将讨论我为管理这个研究项目的文件写入而编写的作业队列的实现。

# 履行

# 我们打印的对象必须是可打印的

为了使文件管理器具有通用性，使用了一个抽象类来确保写入文件的对象包含一个将它们转换为字符串的方法。

这个抽象类确保被写入文件的对象实现了 *getPrintString()* 方法。此方法的返回值是写入文件的内容，后跟一个换行符。一个非常简单的示例实现可能看起来像这个用于测试文件管理器的 *PrintableInt* 类

可打印对象(printable object)的示例实现。

这是一个非常基本的例子。 *PrintableInt* 类存储一个整数值，在打印时，使用标准库的 *to_string(…)* 函数返回该值的字符串表示。

# 写文件发生在主线程之外

为了保持计算效率，文件写作业不应该占用主线程的计算时间(在我的例子中应该是处理拉丁方块)。因此，文件管理器有一个*管理器*线程，由处理所有文件写入和作业出队的类维护。为了实现这种控制，FileManager 类定义如下所示

首先， *JobVector* 类型被定义为 PrintableObjects 的 std::vector。在开发这个类时，在尝试管理<和*和>的*之后，我认为类型定义是合适的(加上它需要大量的输入)。接下来，定义一个 *FileManagerException* 类，它扩展了 *runtime_error* 类。这是为了方便起见，因为运行时错误有时很难发现。当 *FileMangerException* 的一个实例在运行时被抛出时，通常更容易找到错误来自哪里。

最后，定义了*文件管理器*类。这个类有启动管理器线程、停止管理器线程(它也完成排队的作业)、初始化文件(或多或少地将文件添加到用于管理作业的映射/向量中)和排队作业的方法。有两个将文件名存储到特定对象的映射:一个将文件名映射到指向输出文件句柄的 *std::ofstream* 对象的指针，另一个将文件名映射到 JobVectors 的向量(*STD::vector<STD::vector<STD::shared _ ptr<printable object>>>*)。

正在存储的其他变量是附加到文件的选项( *bool _append)* 而不是覆盖它们，以及管理器线程的 pthread 句柄，以便当文件管理器停止时可以停止它。

下面提供了这些方法的实现，可以在项目的 [GitHub](https://github.com/anthonymorast/mols-research/tree/square_hash_dev/src/FileManager) 中找到。

FileManger 类方法的实现。

给帖子添加完整代码会让帖子变得更长(尤其是 C++代码)。正因为如此，我将放弃对这个实现的解释，而是在代码中添加了大量的注释来解释所有的事情。

# 小测验

使用前面提到的 *PrintableInt* 对象，创建了一个对 *FileManger* 的简短测试。

在该测试中，生成 1 到 100 的随机数(20 个作业中每个作业 100 个)。这 20 个作业平均分布在 4 个文件中。当作业生成时，它们在主动处理作业的 FileManger 中排队。一旦所有作业排队，FileManager 就会停止，这需要一些时间，因为剩余的作业正在处理中。同样，代码注释中给出了更多的细节。该代码可以通过以下方式编译

```
g++ -O3 --std=c++14 -I. -Wall -lpthread FileManagerTest.cpp -o fmt
```

跑过了

```
./fmt
```

# 未来的工作

# 进一步穿线

目前，文件写入发生在管理器的线程上。为了提高效率，文件写入本身可以在另一个线程上开始，并且可以在写入完成时向主线程发出指示，以便可以在另一个新线程上创建新的作业(以防止多个线程写入同一个文件)。我创建了一个类似这样的实现，但是遇到了一些问题，所以我选择了一个效率稍低的实现来继续这个项目的其他部分。

# 进一步抽象

为什么它必须是一个文件写入作业队列，而不仅仅是一个脱离主线程的作业队列？文件管理器中的原则几乎适用于任何作业队列。可以实现进一步的抽象，允许实现者定义、启动和停止管理线程，对任何类型的作业进行排队，以及定义在处理作业时做什么。我怀疑，这个实现只要再增加几个抽象类就会非常简单。

*原载于 2022 年 4 月 5 日 https://www.anthonymorast.com*[](https://www.anthonymorast.com/blog/2022/04/05/a-general-purpose-file-writing-job-queue-in-c/)**。**
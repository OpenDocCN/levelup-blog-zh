# 接口编程:缓存移除练习

> 原文：<https://levelup.gitconnected.com/program-to-an-interface-an-exercise-in-cache-removal-de6b68b737cc>

## Vimscript 中基于接口的编程示例

![](img/4655137df98b8460ffe6fce6c4e7da26.png)

在软件工程中，有一个简单而强大的接口编程思想。虽然具体的实施方式会因特定项目的具体情况而有所不同，但这一想法背后的核心原则是相同的。当实现得好的时候，利用[基于接口的编程](https://en.wikipedia.org/wiki/Interface-based_programming)的软件系统将倾向于展现出期望的特性，例如松耦合、高内聚和更好的模块化，从而提高代码的可维护性和可扩展性。

在这篇文章中，我将通过回顾我最近对 [Vim-CtrlSpace](https://github.com/vim-ctrlspace/vim-ctrlspace) (一个工作流管理/模糊查找插件)所做的贡献来解释如何编程一个界面，它增加了禁用默认文件缓存的能力。我希望至少一些中级程序员，他们可能想改进他们程序的设计，会发现这是有用的。虽然我的目标是保留实现细节，尤其是 Vimscript light 的细节，但喜欢用这种古怪语言编程的同道中人可能也会在这里发现一两个有趣的东西。

# 问题背景

像其他模糊查找器一样，CtrlSpace 有一个文件模式，用户可以在其中搜索和打开他们项目中的文件。为了做到这一点，在遇到一个新项目时(由 VCS 目录标记，如`.git/`和`.hg/`)，CtrlSpace 将通过 globbing 索引所有相关文件，然后将它们的路径存储在一个纯文本文件中。对于大型项目(例如包含 70k+文件的 Linux 内核)，这个索引过程可能需要一段时间；但是一旦完成，后续的文件查找变得即时，因为所有的文件路径都在那时被缓存。

虽然这个工作流工作得很好，特别是在具有静态文件结构的成熟项目上，但它伴随着使用缓存的问题，即如何以及何时使其无效。事实上，无论何时创建或删除文件，甚至切换到包含不同文件集的分支，缓存都会过时，如果希望通过 CtrlSpace 访问新文件(等等)，就需要通过重新索引来手动刷新。因此，有一个选项来禁用这个文本文件缓存可能会更方便，特别是对于文件结构经历高流量的项目，以及那些较小的项目。

# 初步尝试

一个天真但绝不简单的方法是编写新的过程逻辑，在不执行缓存操作的情况下连接插件的必要功能。因此，当进入文件模式时，当前总是读取缓存的文本文件的内容，新的逻辑可能在适当的条件下跳过这一步。我不会费心展示这种方法的代码示例，因为它很可能相当于无数分散的 if 块执行不相关的函数，我相信我们大多数人对这种编程都有足够的第一手经验。不用说，这种方法增加了代码库中的复杂性、耦合性和重复，随着时间的推移，这往往会导致可怕的分类。不推荐。

更好的方法是将条件分支限制在实现实际文本文件缓存的函数范围内。这样做允许它们的调用站点保持不变，只要它们仍然返回正确的数据或执行适当的过程。

让我们看看如何将它应用于加载文本文件缓存内容的函数`s:loadFilesFromCache`:

所以`s:loadFilesFromCache`首先获取当前项目的文本文件缓存的路径，确保它非空并且可读，然后将其内容读入脚本本地列表`s:files`。

要实现更好的方法，类似下面这样的方法应该有效:

启用文本文件缓存时执行的代码是完全相同的，只是现在被包装在 if 块中；而在 else 块中，可以添加用于禁用的高速缓存的逻辑。只要该函数仍然可以用项目的文件路径填充`s:files`，例如通过调用 else-branch 中的文件索引函数，那么`s:loadFilesFromCache`的所有调用者都应该感到满意。

上述方法中包含的基本思想在精神上与接口编程并无二致。关键的见解是，如果缓存系统的 API 是一个牢不可破的契约，那么它的功能实际上就是黑盒，从而使它们的调用者不必关心缓存的实现细节。这种方法的不足之处在于，它没有把这个想法带得足够远。

除了功能`s:loadFilesFromCache`，还有`s:saveFilesInCache`、`ctrlspace#files#RefreshFiles`和`ctrlspace#files#CollectFiles`；所以同样的条件检查和分支逻辑也需要添加到这些函数中。这肯定会导致大量的代码重复。此外，如果您后来想要添加一个新的系统来检索和处理项目文件，这些函数的内部实现可能会变得难以处理，从而使代码容易出错并且脆弱。

# 编程到接口

## 设计

敏锐的读者可能已经猜到了这种趋势。与其在实现文本文件缓存的现有函数中添加禁用缓存的逻辑，为什么不通过将实现正常运行的文本文件缓存和禁用空缓存所需的行为集划分为两个不重叠的组件来争取更大的模块化呢？在一些 Vimscript 风格的 OOP 的帮助下，这正是我所做的。

![](img/99186e38020b238cab4102a6c01ffcca.png)

实际上，这两个缓存对象都包含通用的数据属性，比如一个`files`列表，以及一些共享的帮助器方法。但最重要的是，它们都实现了定义统一接口的 4 个关键方法:

* `cache.load`，替换`s:loadFilesFromCache`
* `cache.save`，替换`s:saveFilesInCache`*`cache.refresh`，替换
* `cache.collect`中的`ctrlspace#files#RefreshFiles`，替换`ctrlspace#files#CollectFiles`的内部，并被`ctrlspace#files#CollectFiles`包裹

原始缓存系统函数的调用者现在将调用缓存对象上的等效方法，该对象可以是`file_cache`或`null_cache`，这取决于用户如何配置它。关键是，缓存系统既不知道也不关心这些函数是如何工作的。

这个定义明确的接口及其清晰的边界还简化了缓存系统的维护和扩展。关于维护，因为两个缓存没有交互，所以一个缓存中出现的错误一定是在它自己的代码中。至于扩展，我一直想添加一个新的混合缓存，能够获得两个现有缓存的好处。这个想法是，当处理包含少于 *N* 个文件的项目时，它可以表现得像`null_cache`一样，以获得永远不需要刷新缓存的便利；作为项目的`file_cache`,超过这个阈值的项目可以享受对数万甚至数十万个文件的快速文件路径查找。添加这个功能并不容易，但是新的基于界面的设计应该会使它非常易于管理。

这是我在本文中能提供给你的所有工程原理，所以你应该准备好将理论付诸实践。虽然如果您喜欢钻研本质细节，但剩余的小节确实提供了一些关于这个特定接口的实现和用法的一瞥，到目前为止，我们只是在高层次上讨论了这个接口。

## 履行

我将只展示实现上面设计的接口所需的 4 个方法中的 2 个，因为另外两个只是这些方法的包装器，增加了一些小的便利。但是如果你感兴趣的话，插件的缓存系统的完整实现(仅仅如此)驻留在源代码`[cache.vim](https://github.com/vim-ctrlspace/vim-ctrlspace/blob/master/autoload/ctrlspace/cache.vim)`中。

下面的代码片段显示了两种缓存的`load`方法:

`s:file_cache.load`方法几乎是原始`s:loadFilesFromCache`函数的逐行复制，唯一的不同是最后一行，它读取文本文件中的内容并将其分配给实例变量`self.files`(下面的 Vimscript 中将详细介绍这一点)，而不是原始实现中的脚本本地变量`s:files`。另一方面，`null_cache.load`方法每次都调用`s:glob_project_files`助手，这个函数也是`file_cache`用来索引和填充其文本文件缓存的函数。在这两种情况下，它们各自的`self.files`属性将包含项目的文件路径，并被传递给任何需要它们的函数。

比较两种`save`方法，我们有:

我没有展示最初的`s:saveFilesInCache`函数的实现，但是`s:file_cache.save`也只是重新实现了它并做了完全相同的事情，即执行与`s:file_cache.load`相反的操作，即通过写入文本文件将存储在`self.files`中的内存文件路径数据移动到磁盘上。`s:null_cache.save`方法改为执行空操作

**vim script 中的 OOP**

对于 Vimscript 爱好者来说，除了`s:null_cache.save`(因为 no-op 不需要访问自己的数据)之外的所有缓存方法都是使用 Vim 的字典函数实现的(在 Vim 中运行`:help dictionary-function`)。函数定义后面的`dict`属性有助于实现这一点，它允许函数访问指向调用它的字典实例的局部变量`self`，从而模拟 OOP 中的对象。

函数定义中使用的点符号是语法糖。在本质上，这些对象仍然是普通的 Vimscript 字典，这意味着下面是定义`s:null_cache.load`方法的一个同样有效的方法:

方法名`load`是字典中的字符串键，其对应的值是实际实现它的函数引用(见`:help Funcref`)。由于两个缓存都需要多个方法定义，更简洁的方法是像下面这样创建一次字典对象，然后直接附加它们各自的方法，如前所示。

```
let s:file_cache = {}let s:null_cache = {}
```

作为 Vimscript 中 OOP 的最后一句话，我想指出它更像 JavaScript 的基于原型的 OOP，而不是大多数 OO 语言使用的基于类的 OOP。因此，如果你熟悉 JS 的 OO 语义，那么 Vimscript 应该很容易掌握(或者如果你像我一样，用 Vimscript 编写 OOP 可以帮助你学习 JavaScript)。例如，如果想要进行原型继承，可以使用 Vim 的内置`deepcopy`函数来克隆现有的 dictionary 对象，然后根据需要修改新的实例。

## 使用

Vimscript 中没有类也意味着没有构造函数。但是我们可以模仿一个粗略的功能等价体，如下所示:

在`ctrlspace#cache#Init`“构造函数”中，通过检查`s:config.EnableFilesCache`的值来选择正确的缓存类型，这实际上是一个 Vim 全局变量(参见`:help g:`)，可以由用户在他们的`.vimrc`中设置。请注意，这是整个代码库中进行条件检查的唯一位置。然后用`call s:cache_common(cache)`初始化一些常用的数据和方法。最后，返回这个`cache`对象。

现在，在实际使用缓存对象的`[files.vim](https://github.com/vim-ctrlspace/vim-ctrlspace/blob/master/autoload/ctrlspace/files.vim)`源内部，所需要的是:

```
let s:Cache = ctrlspace#cache#Init()
```

再次强调,`files.vim`内部的函数完全不知道它的缓存实际上是如何工作的。他们只知道可以调用它的`load()`、`save()`、`collect()`和`refresh()`方法；并且由于接口的保证，可以指望这些方法正确地工作。

# 超越基于 OOP 的接口

正如在介绍中提到的，编程到一个接口最终是一个简单的想法，一旦内化，你会自然地开始在设计你的程序时考虑它。但是尽管它的概念简单，或者正因为如此，它也非常强大。

简单的想法往往是一般化的。因此，尽管接口编程似乎很自然地有助于 OOP 范例，甚至在某些语言中`interface`甚至是一个内置的构造，如 [Java](https://docs.oracle.com/javase/tutorial/java/IandI/createinterface.html) 和 [PHP](https://www.php.net/manual/en/language.oop5.interfaces.php) (在其他语言中也称为[协议](https://en.wikipedia.org/wiki/Protocol_(object-oriented_programming)))，但它的基本原理与面向对象的概念是正交的。

这种关于如何编程到一个接口的更通用的方法是我想在后续文章中展示的。我不指望它会给读者带来任何额外的见解，但肯定会有很多非常酷的技巧，当然都是在 Vimscript 中！
# Golang:深入理解 GORM 框架中的 ErrRecordNotFound 异常

> 原文：<https://levelup.gitconnected.com/golang-deep-understanding-of-errrecordnotfound-exception-in-gorm-framework-a1f44e21135a>

GORM 和 ErrRecordNotFound

![](img/f2456dbd113b4e6fd25a1e3877dda997.png)

在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Marcin Jozwiak](https://unsplash.com/@marcinjozwiak?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

最近做代码复查的时候，发现了很多关于 ErrRecordNotFound 的错误。

你对框架的细节了解得越多，就越容易使用它。

这篇博文将通过实例和源代码解读来探究`GORM`中的`ErrRecordNotFound`，其中会涉及到一些查询相关的内容，比如`Find`、`First`、`Last`等方法的应用。

注:本文为基础篇，适合入门级人群。

**`**ErrRecordNotFound**`**什么时候出现？****

**对于上面的示例代码，请按顺序执行以下步骤:**

*   **初始化数据库连接，这里是连接到本地 SQLite 数据库)。**
*   **自动创建数据库表结构，`AutoMigrate`方法可以根据传入的参数反映对象的结构，从而根据规则映射出数据库中应该创建的表结构；例如，传入`&Topic{}`将自动生成数据库中的主题表，包括`id, title, content, created_at, updated_at, deleted_at`和其他数据列。**
*   **创建主题记录。**
*   **找到 id 为`0`的话题记录。**
*   **找到 id 为`0`的主题列表。**

**这里特别说明一下，`err = db.Where(“id=?”, 0).Find(&topic).Error`这种在查询语句末尾加上`.Error`的方式，可以将 GORM 查询数据库过程中发生的错误赋给`err`，从而通过判断`err`来决定下一步的操作。**

**比如`err`为`nil`时，没有错误，代码可以正常运行。**

**当`err`不是`nil`时，表示有错误，可以直接抛出这个错误或者采取措施处理这个错误。**

**示例代码的运行结果如下，其中`record not found`是 GORM 中`ErrRecordNotFound`类错误的描述:**

```
error： record not found
id of the topic: 0
error： record not found
id of the topic: 0
error： record not found
id of the topic: 0
error： <nil>
length of topics 0
error： <nil>
length of topics 0
error： <nil>
length of topics 0
```

**既然有这种类型的错误`ErrRecordNotFound`，那么通过确定`ErrRecordNotFound`是否为`nil`，就很容易想到数据库中是否有某个检索条件的记录。那么 GORM 什么时候投`ErrRecordNotFound`？**

**从上面的例子及其运行结果可以看出，在相同的检索条件`Where('id=?', 0)`下，不同类型的参数传入接收检索结果会产生不同的结果。**

*   **传入`var topic Topic`时抛出 ErrRecordNotFound 错误。**
*   **传入`var topics[]Topic`时不会抛出 ErrRecordNotFound 错误。**

****从源代码中找答案。****

**首先，我们来看看 GORM 中的`Find, First`和`Last`方法。**

**从源代码中可以看出，在 GORM 中，`First`和`Last`比`Find`有更多的`Limit`限制和默认排序顺序，三种方法没有本质区别。**

**我们来看看 GORM 中的`queryCallback`方法。**

**从源代码可以知道，在对检索到的数据进行分析赋值时，如果检索到 0 行，并且收到的检索结果不是 Slice 类型的变量(此时必须是 Struct 类型的变量)，就会抛出 ErrRecordNotFound 错误。**

****总结一下。****

**本文用一个例子来说明 record not found 的错误，并通过分析源代码来详细阐明 GORM 抛出`ErrRecordNotFound`的具体场景。**

*   **为接收检索结果而传入的变量只能是 Struct 类型或 Slice 类型。**
*   **当传入变量为 Struc 类型时，如果检索的数据为 0，将引发 ErrRecordNotFound 错误。**
*   **当传入变量的类型为 Slice 时，在任何情况下都不会引发 ErrRecordNotFound 错误。**

**感谢阅读。**

**如果你喜欢这样的故事，想支持我，请给我鼓掌。**

**你的支持对我来说非常重要，谢谢你。**
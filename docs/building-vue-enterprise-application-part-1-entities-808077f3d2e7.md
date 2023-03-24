# 构建 Vue 企业应用程序:第 1 部分。实体

> 原文：<https://levelup.gitconnected.com/building-vue-enterprise-application-part-1-entities-808077f3d2e7>

## 清洁架构原则在企业前端应用中的应用

![](img/ec13918c5fe37bfb6498634bf2a94202.png)

# 目录

*   介绍
*   需求分析
*   测试优先(TDD)
*   改进测试
*   最后，一些生产代码！
*   结论

# 介绍

我们在[的前一篇文章](https://medium.com/@gregsolo/building-vue-enterprise-application-part-0-overture-6d41bea14236)中确定，实体是应用程序的中心。他们拥有最宝贵的财富:商业规则。他们了解应用领域的一切。但在实践中又意味着什么呢？

要明确回答这个问题是不可能的，因为每个应用都是独一无二的，都有自己的规则和逻辑。但是我们**可以**为**我们的**应用确定详尽的规则集。

它是一个博客，自然地，一篇文章是它的中心。它是一个*实体*，一个包含特定字段并具有特定验证规则的对象(例如，标题或内容可以有多长？).

我们来定义一下这个实体！如果还没有切换到“init”分支(repo 在 [github](https://github.com/soloschenko-grigoriy/vue-vuex-ts) 上可用)。在这一章中，我们将几乎完全集中在实体文件夹上。

# 需求分析

![](img/26e0fc2390511cbe052e58439e0bf561.png)

[乔·塞拉斯](https://unsplash.com/@joaosilas?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

从技术角度来看，文章应该是什么样的？它应该具备什么性质？它应该如何表现？

为了回答这些问题，我们可以选择工作演示，也可以选择在后端操作文章的 API。为了简单起见，我们不使用真正的 API，而是使用一个“data.json”文件来扮演这个角色。你可以在“服务”目录下找到它。

## 物品实体

很明显，每篇文章都有一个唯一的 id、标题、内容、称为“简短”的内容摘录、一个项目是否活跃的指示器、图片、创建日期、标签，最后是一组评论。虽然这些字段中的大部分是简单明了的，并且很明显是原始值，但是*注释*有点复杂。它们有自己的属性甚至行为:用户可以定义新的评论。因此，为他们创建一个单独的实体并封装他们的业务规则和逻辑是很自然的。

## 注释实体

很好，所以这个新的评论实体将有以下字段(根据 data.json):惟一 id、作者姓名、标题、内容、一个指示器(如果它是活动的)和创建日期。但是由于这款应用提供了一种创建新评论的方式，我们还需要更多。我们至少需要针对评论的*验证规则*,以便我们可以在用户试图创建无效数据时做出反应。我们的业务团队声明如下:

*   标题不能为空，长度不得超过 10 个字符
*   作者姓名不能为空，并且长度不得超过 10 个字符
*   内容不能为空，长度不得超过 255 个字符

漂亮！感觉我们已经有了一切可以利用的东西

# 首先测试

![](img/6f47d361c86538dcbc52adb89020068c.png)

由[托马斯·凯利](https://unsplash.com/@thkelley?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

遵循 TDD 方法，让我们首先为我们的实体编写测试。我会为每个实体专门放一个文件夹:“文章”和“评论”。每个文件夹将遵循相似的结构:它将包含实体本身的文件、实体类型的文件、实体规范的文件和桶索引文件:

```
src/entities/article/article.ts
src/entities/article/article.types.ts
src/entities/article/article.spec.ts
src/entities/article/index.ts
```

让我们创建这些文件，并用一些样板代码填充它们:

文章、文章类型和文章规格的样板代码

类似评论:

```
src/entities/comment/comment.ts
src/entities/comment/comment.types.ts
src/entities/comment/comment.spec.ts
src/entities/comment/index.ts
```

注释、注释类型和注释规范的样板代码

让我们更新桶文件

```
/src/entity/index.ts
```

并从文章和评论中导出所有内容:

文章桶文件

现在，我们可以测试什么？测试字段不会带来太多的价值，此外，TypeScript 让我们了解了这一点。但是我们可以检查注释的验证规则。

## 测试验证规则

让我们假设注释实体有一个“validate”方法，它将检查这个特定的实体是否有效，并返回 true 或 false。它应该考虑我们从业务团队收到的规则:标题最多 10，作者姓名最多 10，内容最多 255。让我们将这个方法添加到接口中。至于“最大字符数”，我们不能把它们作为实体的字段，因为它们不依赖于实体的特定实例。然而，我们可以使用静态字段，或者简单地创建独立的常量:

注释验证类型

现在，让我们编写测试:

注释验证规范

当然，TS 一直在吼我们。毕竟，IComment 和 Comment 提供的“标题”、“作者”或“内容”信息为零。但是在我们开始修改代码之前，还有一件事我想讨论一下。

## 数据与实体

我们从 API(或者在我们的例子中，data.json)接收的数据严格来说不是评论或文章实体。至少，现在还没有。这是一个普通的数据，它本身没有“验证”或任何其他功能的概念。它是纯数据，不是类实例。所以，只有评论和文章是不够的。我们需要一个专用接口 ICommentData 和 IArticleData。它们代表我们从外部资源接收到的纯粹的、精华的数据，我们可以用它们来实例化一个实际的实体。我将向前迈出一步，并预测我们也将使用这些接口来表示我们存储在 Vuex 存储中的纯数据(因为它建议不要在 Vuex 中存储带有方法的对象)。

因此，让我们定义这些接口，并根据 data.json 定义它们必须包含的字段:

纯数据和实体

以下是一些需要注意的事项:

1.  IArticle 扩展 IArticleData，IComment 扩展 ICommentData。这是有意义的，因为我们要使用的对象，实际的实体，来自于我们从 API 接收的数据。
2.  IArticleData 依赖于 ICommentData，但 IArticle 依赖于 IComment。这很自然，因为在我们将数据转换为对象之前，数据依赖于数据。但是在实例化之后，对象变得依赖于对象
3.  "但是你刚才说，实体不能依赖任何东西！"但是他们仍然可以依赖其他实体。他们经常这样做
4.  另外，您可能会注意到，我将“id”和“createdAt”字段标记为可选。不能保证它们会一直存在。一个简单的例子:用户写了一个新的评论，并给了我们一些数据。我们有一个标题，作者的名字，和内容(用户需要提供这些)。我们可以将当前时间设置为“创建时间”。但是此时我们无法找到“id”的值。该值将由 API 设置(或者更准确地说，由 API 的数据库设置)。我们自己不能自动增加它，即使不能保证我们在本地数据库中有所有的数据。类似地，我们不应该在客户端设置“createdAt ”:想象一下，如果我们为来自不同时区的用户提供服务，我们可能会面临一系列问题。因此，通常的做法是将“id”和数据生命周期时间戳设置为可选。

# 改进测试

![](img/87ddd7a5e373cf314c9f492ef9001334.png)

照片由 [Jungwoo Hong](https://unsplash.com/@oowgnuj?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

最后一个注释也给了我们一个测试应该覆盖哪些内容的想法。我们确实应该检查这样一个事实，即没有“id”或“createdAt”字段也可以创建实体。最后，我们应该检查文章是否可以正确地实例化它的子元素 Comments。

让我们更新一下我们的规格:

文章和评论规格

## **嘲笑**

我想对测试做一点小小的改进。由于实体对应用程序如此重要，我们不可避免地在其他测试中引用它们:服务、商店、UI 组件。我们可以简单地调用一个构造函数并提供必要的数据，但是这可能会在这些测试和实体*之间创建一个*结构依赖。更糟糕的是，每次我们改变一个实体时，我们将被迫更新它们。**

为了最小化这个问题，我们可以定义和使用模拟或模拟工厂，并使它们靠近实体。每次实体改变时，我们更新模拟，而不是测试。在这种情况下，我们代码的其他部分*可能不会受到影响。*

让我们为我们的实体创建一些模拟，并在其中放置模拟对象的工厂:

```
src/entities/article/article.mock.ts
src/entities/comment/comment.mock.ts
```

实体模拟

作为馅饼上的樱桃，让我们更新桶文件以导出模拟:

更新桶文件

现在我可以在我的测试中使用这些模拟:

最终确定规格

# 最后，一些生产代码！

![](img/d5a0d219619adb9577d0ec078e6649da.png)

照片由[马修·施瓦茨](https://unsplash.com/@cadop?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

这是一个漫长的旅程，但我们终于准备好编写生产代码。在这一点上，它是直截了当的，因为我们的测试告诉我们所有的故事。

我们应该实例化为构造函数提供数据的两个实体，为注释设置验证，并确保所有标题/内容/作者都存在并且不太长:

最终确定文章实体

最终确定注释实体

在这一点上，一切都应该最终编译好了，测试应该以 100%的覆盖率运行。*完整的章节源代码可以在“实体”分支下找到。*

# 结论

哦，太多了。但是我们应该为自己感到骄傲:我们为我们的企业架构打下了坚实的基础。我们指定了实体和业务规则。我们用测试覆盖它们，并确保我们的源代码是稳定的、可靠的和可维护的。下一次，我们将使用服务在应用程序中应用这些实体和规则。

这是“构建 Vue 企业应用”系列文章的第一章。其他剧集请点击此处:

*   [0 部分。序曲](https://medium.com/@gregsolo/building-vue-enterprise-application-part-0-overture-6d41bea14236)
*   [第一部分。实体](https://medium.com/@gregsolo/building-vue-enterprise-application-part-1-entities-808077f3d2e7)
*   [第二部分。服务](https://medium.com/@gregsolo/building-vue-enterprise-application-part-2-services-f7ec400190e7)
*   第三部分。Vuex
*   第四部分。UI:页面和组件
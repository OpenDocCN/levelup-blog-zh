# 当实现影响抽象时

> 原文：<https://levelup.gitconnected.com/when-implementations-affect-abstractions-1bb2adc808d1>

## 回归基础

## 关于实现的知识会影响抽象设计吗？

![](img/187f847138ea820cc8294b4553b04283.png)

照片由[UX](https://unsplash.com/@uxindo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

如果你问开发人员可用实现的知识是否会影响他的抽象设计，他很可能会说:

> 不，如果发生这种情况，那么我对系统的了解不足以设计抽象。

在我作为软件工程师的职业生涯中，我会同意这一点。事实上，如果有人问我同样的问题，我也会给出同样的答案。

然而，在不止一个场合，我遇到了一些解决方案，让我改变了主意。

长话短说，是的，您需要理解您的系统和每个模块应该提供的功能，以便能够设计您的抽象。然而，这还不够。

探索一些可能的实现可以帮助您增强抽象。

好吧，假设你对你正在设计的系统及其所有交互的了解是你工作的一部分，你确实需要它来完成你的工作。然而，创造这种知识是另一回事。

我们现在讨论的是如何建立这些知识。最好的和创造性的方法之一是，一旦你心中有了一个抽象，你就试图探索可能的实现。这会让你的思维处于一种有效的状态，帮助你联系太多松散的事情。

![](img/dae42316c04548aad197e34b378e3bf1.png)[](https://medium.com/subscribe/@eng_ahmed.tarek) [## 🔥订阅艾哈迈德的时事通讯🔥

### 订阅艾哈迈德的时事通讯📰直接获得最佳实践、教程、提示、技巧和许多其他很酷的东西…

medium.com](https://medium.com/subscribe/@eng_ahmed.tarek) ![](img/dae42316c04548aad197e34b378e3bf1.png)![](img/2abb65ce6a7bd730a9bfd17abe3d8080.png)

照片由 [Afif Kusuma](https://unsplash.com/@javaistan?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

# XML/SQL 示例

让我们假设您正在为一个应用程序设计一个解决方案，该应用程序将处理库存或类似的事情。在某些情况下，您可能需要将数据保存到数据存储中，或者从数据存储中检索数据。

现在，很明显，您将实现一个模块来处理数据的读写。当然，您需要将这个模块抽象到一个接口中，以便系统模块的其余部分与这个抽象层而不是具体的实现进行交互。

此时，您头脑中没有任何实现，您所关心的是根据系统需求设计抽象层。

因此，你想出了这样一个设计:

```
public IEnumerable<Item> GetAllItems();
public void AddItem(Item item);
```

很好。现在，其他系统模块可以使用这个抽象层来读写项目。

然而，让我们试着想象一下这个抽象层的可能实现是什么。

一种可能的实现是 XML 存储。

因此，在实现它时，我们会提供一些代码来打开一个 XML 文件，从里面读取元素，写入元素……并且在添加多个元素的情况下，很容易在项目上循环并调用`AddItem`方法。太好了…

另一个可能的实现是 SQL 数据库。

因此，在实现时，我们会提供一些代码来打开与 SQL server 的连接，从表中读取项目，将项目写入表中……如果要添加多个项目，就很容易在项目上循环并调用`AddItem`方法。太好了…

停，其实没那么好。如果你了解 SQL，你会知道每次你调用`AddItem`时，它会打开一个到服务器的连接，执行命令，获得结果，然后关闭连接。这个过程在性能上代价太大。

更广为人知的解决方案是通过抽象层启用和公开项目的批处理。在这种情况下，抽象层的设计应该是:

```
public IEnumerable<Item> GetAllItems();
public void AddItem(Item item);
public void AddItems(IEnumerable<Item> items);
```

然后，实现者可以决定如何实现批处理方法`AddItems`。对于某些实现来说，循环并调用`AddItem`方法可能会很好，对于其他实现来说，这可能是一个完全不同的实现。

有人可能会说，即使不了解 SQL，我们也应该在第一时间提出最新的设计。

也许你是对的，但是，我认为如果你深入了解自己，你可能会发现你这样说是因为你已经知道需要批处理时的类似情况…请记住，你不能撤销知识。

![](img/e728ecaac66422d8b42f2c6f19c6e51a.png)

[阿德里安·勒杜](https://unsplash.com/@adrienl?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# 运输示例

让我们假设你正在为一个管理不同交通工具的应用程序设计一个解决方案，比如汽车、公共汽车等等

您希望您的系统足够健壮，可以添加新的车辆或交通工具，并且其余的模块可以与它交互而不会出现任何问题或破坏逻辑。

因此，你想出了这样一个设计:

```
public void Move(Vehicle vehicle);
```

好的，对于许多交通工具来说，它应该是工作正常的，但是火车呢？你需要记住，火车的运动受到轨道的限制，它们不能向外移动。

因此，知道这一点后，你开始推广限制运动的概念，可能是针对火车或其他交通工具。因此，您将抽象层更新如下:

```
public void Move(Vehicle vehicle, IEnumerable<MoveRestriction> moveRestrictions);
```

这将使你的设计更加高效和健壮。

尽管如此，我们可能会认为这是一个太明显的情况，应该在第一时间考虑。

再说一次，你这么说可能是因为你已经拥有了知识，你不能撤销知识。然而，对于你来说，一个全新的领域怎么样呢？

![](img/dd20967b9af21e053e59ffe3e8388ab5.png)

哈尔·盖特伍德在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 排队或非排队示例

让我们假设您正在为一个应用程序设计一个解决方案，其中有一个数据流通过(来往于)您的应用程序。

你已经知道你的系统和它的能力。您知道您的环境处理传入请求的速度。这太棒了。

然而，您的系统将作为一个中间件，挂在其他系统之间。因此，您的系统应该预期传入的请求，并且应该转发传出的请求，并且不能保证这些传入和传出请求的频率总是相同的。

在了解这最后一条信息之前，您决定您的设计将直接处理传入的请求，处理它们，并将结果直接转发给下一个系统。那应该没问题。

然而，在了解了最后一条信息之后，现在您需要将您的系统设计得更加健壮。所以，你决定实现排队。因此，现在您的系统将对传入的请求进行排队，并行处理它们，以相同的传入顺序对结果进行排队，并将结果转发给下一个系统。

有了这种设计，即使传入请求的频率不是很高，也会使您的系统更加健壮。

![](img/804967fcc41d241d166e246515b10151.png)

照片由[扬·安东宁·科拉尔](https://unsplash.com/@jankolar?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 分页或非分页示例

让我们假设您正在为一个应用程序设计一个解决方案，这个应用程序是一个显示一些数据的仪表板。

在某些时候，您会构建 API 来检索数据。因此，您决定实现众所周知的**存储库设计模式**。太好了，它应该像魔咒一样管用。

然而，你没有使设计足够健壮来处理大量的数据。因此，现在 API 用户可以请求一次获得所有数据，这太糟糕了。

如果您将处理数据的想法概括为“我们应用节流”，那么设计中的这个问题可能会被发现得太早。

这将引导您首先应用分页和内部节流。

注意:如果您有兴趣了解更多这方面的内容，您可以查看文章 [**更好地增强存储库模式实现。NET C#**](/better-enhanced-repository-pattern-implementation-in-net-c-4e6f4bbe48a9?sk=dff8866ff91a36a314de8eb40a169955) 。

![](img/cdcd9b2f11859d91bf43eef163c15e43.png)

由[以利沙·特拉达](https://unsplash.com/@elishaterada?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

# 最后的话

不要误解我的意思，我并不是说关于你正在设计的系统的知识不重要，或者抽象应该以某种方式依赖于实现。

我真正的意思是，建立关于一个系统的知识是一个过程。这个过程本身可以用不同的方式来完成，但我确信创造性会有很大的帮助。

我推荐的方法之一是探索不同的选择、实现、交互的本质、限制等等

最后一点，知识是递增的。你从一个系统中获得的知识会在另一个系统中受益。因此，尽可能地利用知识，因为这将帮助你成长和获得经验。

![](img/dae42316c04548aad197e34b378e3bf1.png)

# 希望这些内容对你有用。如果您想支持:

如果您还不是**中的**会员，您可以使用 [**我的推荐链接**](https://medium.com/@eng_ahmed.tarek/membership) ，这样我就可以从**中**获得您的一部分费用，您无需支付任何额外费用。订阅 [**我的简讯**](https://medium.com/subscribe/@eng_ahmed.tarek) 将最佳实践、教程、提示、技巧和许多其他有趣的东西直接发送到您的收件箱。

![](img/dae42316c04548aad197e34b378e3bf1.png)

# 其他资源

这些是你可能会发现有用的其他资源。

[](/mediator-design-pattern-in-net-c-e1bfcc96789d) [## 中介设计模式。NET C#

### 中了解中介器设计模式。NET C#与代码示例。

levelup.gitconnected.com](/mediator-design-pattern-in-net-c-e1bfcc96789d) [](/builder-design-pattern-in-net-c-bbf11c891548) [## 中的生成器设计模式。NET C#

### 一步一步的指南，从零开始开发一个流畅的 API。NET C#使用生成器设计模式。

levelup.gitconnected.com](/builder-design-pattern-in-net-c-bbf11c891548) ![](img/dae42316c04548aad197e34b378e3bf1.png)
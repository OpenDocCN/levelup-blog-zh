# 使用一个命名良好的函数来代替注释

> 原文：<https://levelup.gitconnected.com/instead-of-a-comment-use-a-well-named-function-448eabb45607>

![](img/f79b696a2e8e06a700b58a1901ef231b.png)

照片由 [Clement Chai](https://unsplash.com/@clementchai?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/craftsman?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

## 发展中的开发商

## 用解释自身的代码替换解释性注释

回想一下你的第一堂编程课。你的第一门编程语言。也许是 c，也许是 Java。可能是 HTML。当您学习语法规则时，**您首先学习的事情之一是如何在代码中添加注释**。无论是`//`或`/* */`或`<!-- //-->`或`#`或者甚至是`c`或`*`，这个新工具开启了一个令人兴奋的新可能性:

> 如果(对我或其他任何人)不清楚这些代码行是做什么的，我可以用注释来解释。

有了这个闪亮的新工具，我们变得懒惰了。

当我作为[拉请求](https://www.pullrequest.com/)的评审员工作时，我偶尔会遇到类似这样的代码:

```
const prepareMemberList = async (listName, fetchOptions) => { // We need the list ID in order to get the members list,
  // so check the mapping of list names to ids.
  const mapping = require('../mappings/listToId.json')
  const id = mapping[listName] const list = await DB.getMembersForList(id)

  // Make sure that we only get members from the database who joined
  // between the startDate and the endDate
  const filteredList = list.filter(member => {
    return (member.joinDate >= fetchOptions.startDate &&
            member.joinDate <= fetchOptions.endDate)
  } return filteredList.map(member => {
    // Older member ids had spaces, but the newer format
    // uses dashes. Convert everything to the newer format.
    const formattedId = member.id.replace(' ', '-') return {
      id: formattedId,
      firstName: member.firstName,
      lastName: member.lastName,
      joinDate: member.joinDate
    }
  })
}
```

## **如此。很多。评论。**

事实上，你真的需要付出精神上的努力来将代码行从注释中分离出来。注释*对*很有帮助，因为它们解释了后面几行的一些错综复杂之处；但是它们也碍事，完全破坏了代码的可读性。

如何解决这个问题？我们很快就会谈到这一点。

# 解释性的评论会让我们变得懒惰

想出编程问题的解决方案是一项工作。想出一个可读的解决方案是很难的工作。

你可能经历了试图解决问题的第一次迭代，留下了嵌套在`while`循环中的`for`循环，你知道你已经找到了一种方法来覆盖那边的边缘情况。

你已经解决了你的问题，你退后一步看看你的代码。太丑了。但这很有效。看这段代码的其他人不可能理解它。你现在理解它*因为你刚刚写了它，但你未来的自己不会。*

> 我是不是应该在这段代码中多下点功夫，清理一下，让它更具可读性？那鸿我只会写评论来解释发生了什么。

# 不需要解释的代码非常令人愉快

让我们用一个简单重构指南来清理上面的代码示例。这条指导方针将是你从这篇文章中得到的唯一可行的东西:

> ***如果可能的话，通过提取注释所引用的代码行，将它们提取到自己的函数中，这个函数的名字很棒。***

是的，名字很棒。像这样:

```
const prepareMemberList = async (listName, fetchOptions) => { const listId = getListIdFromName(listName)
  const list = await DB.getMembersForList(listId) const filteredList = list.filter(
    joinDateIsWithin(fetchOptions.startDate, fetchOptions.endDate)
  ) return filteredList.map(member => {
    return {
      id: standardizeId(member.id),
      firstName: member.firstName,
      lastName: member.lastName,
      joinDate: member.joinDate
    }
  })
}
```

上面的代码示例中没有注释。我提取了注释和代码行，并用几个函数替换它们:

*   `getListIdFromName(listName)`
*   `joinDateIsWithin(fetchOptions.startDate, fetchOptions.endDate)`
*   `standardizeId(member.id)`

我甚至还没有提供这些函数的实现，但是你已经对这些函数可能做什么有了一个概念，这是因为它们的名字很好。

你不觉得这个版本更容易阅读吗？从一行到下一行发生了什么非常清楚。还有…没有评论。

Jeff Atwood 有一篇很棒的博客文章，题为“[代码告诉你如何做，评论告诉你为什么做](https://blog.codinghorror.com/code-tells-you-how-comments-tell-you-why/)”他在第一段写道(我完全同意):

> 你应该首先努力使你的代码尽可能简单易懂，而不是依赖注释。只有在代码无法变得更容易理解的时候，你才应该开始添加注释。

# 找个更好的方式告诉我

![](img/f7407f05a13ba8acaa7b50d9b51faeda.png)

啊，好旧的灭火器**盒** *。*

当我住在中国的时候，我看到这些灭火器箱无处不在——在商场、博物馆、学校。但是我总是很喜欢看到它们，因为它们贴着“灭火器 ***箱*** ”的标签你可能会认为明亮的白色标签是为了提醒人们，如果你需要的话，这里有一个灭火器。当然，这是事实。但是，**你为什么需要告诉我盒子的事？我需要灭火器。我不在乎那个盒子。**

第一，没必要。如果你省略“盒子”这个词，你仍然可以达到提醒我灭火器在哪里的目的。

另一方面，你*可能*有一个空盒子(灭火器被移除)，标签——这是一个“灭火器盒子”——仍然是正确的。你的标签可能在技术上是准确的，我会死于火灾。

有什么更好的方式来告诉我？也许只是一个大的灭火器图形或图标。或者盒子前面有一个透明的塑料面，这样我就可以从远处看到盒子里面有一个灭火器。你可以用很多更好的方式与我沟通*而不用*涂上白色的解释性注释。

# 必要时进行评论

如果你想让你的代码充满注释，那么你的注释需要是**必需的**。如果可以用提取到命名良好的函数中的代码来替换它们，那么它们就没有必要了。

浏览你的代码。你是否看到贯穿始终的解释性注释？每隔几行代码就有解释打断吗？清理它，使你的代码更具可读性。将这些行提取到它们自己的名字令人惊叹的函数中，你将会得到解释自身的代码。

编码快乐，朋友们！

*阿尔文·李*是亚利桑那州凤凰城的一名全栈开发人员和远程工作者，担任 [PullRequest](https://www.pullrequest.com/) 的高级评审。他专门从事 web 开发、API 构建以及为初创公司和小型企业提供服务集成。他在 Moonlight 上有空，在那里你可以[查看他的个人资料](https://www.moonlightwork.com/app/users/2862/profile)或者[请求雇佣他提供服务](https://www.moonlightwork.com/r/2862)。
# 不应该使用私有函数。以下是你应该做的

> 原文：<https://levelup.gitconnected.com/private-functions-shouldnt-be-used-here-s-what-you-should-do-instead-ceec746d5fd5>

## 友好的建议，以产生更好的代码库

![](img/9549aeb15997ff9619e8c251a494abad.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Sitraka Rakotoarivelo](https://unsplash.com/@mecdjino?utm_source=medium&utm_medium=referral) 拍摄

每一个好的编程概念都可以用一个简单的现实生活中的类比来解释。但是私有函数有相似之处吗？

事实上，没有这样的。有很多私有字段的例子，但是没有私有函数的例子。

以汽车为例。踩油门是公共功能。它的输出是轮子的运动。这个函数有一个非常复杂的实现，所以需要私有函数。

仔细一看，没有一个函数是私有的。所有的都是几个部分公开地相互作用。显然，你在车里能找到的唯一私人功能是在汽车的电脑里，在它的软件里。

同样的模式随处可见:复杂的功能是通过封装几个相连的部分来实现的。没有任何私有函数直接融合到公共接口中。

如果私有函数没有出现在任何工程分支中，它在面向对象软件中也没有一席之地。

为什么？因为编写私有函数通常是为了从公共函数中抽象出底层细节。换句话说，**私有函数使类违反了单一责任原则。**

将代码提取到函数中是很重要的。但是，在这样做的时候，最好抓住这个机会**把它们写成不同类的公共函数**。

# 例子

虽然将电子邮件验证提取给`isValidEmail()`函数是一个聪明的举动，但是`LoginPresenter`仍然忙于电子邮件验证，而不是它真正的工作:在 UI 和域逻辑之间进行协调。

# 将电子邮件验证提取到另一个类

通过向`EmailValidator`提取电子邮件验证，将自动获得以下好处:

*   没有低级细节的干扰
*   所有的类都是在同一个抽象层次上编写的
*   `EmailValidator`可重复使用
*   电子邮件验证行为可以单独测试
*   `LoginPresenter`可以通过提供嘲笑来测试`EmailValidator`

另一个问题是私有函数鼓励私有字段的修改。虽然有时这是期望的行为，但私有函数中某个字段的非故意变化往往会被忽略。因此，私有函数增加了意外副作用的可能性。

最后，私有函数会给你一种封装的错觉。具有讽刺意味的是，私有函数的存在使得打破类的封装变得容易:经常看到程序员在任何机会天真地将访问“私有”改为“公共”，而不必过多考虑它是否安全。

# 结论

通过系统地将功能提取到专门的类，整个软件架构得到了改进。走这条路首先需要一种不同的心态，但从我的经验来看，它会很快带来回报。

进一步阅读:
[https://carlosschults . net/en/are-private-methods-a-code-smeet/](https://carlosschults.net/en/are-private-methods-a-code-smell/)

# 分级编码

感谢您成为我们社区的一员！ [**订阅我们的 YouTube 频道**](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1) 或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)
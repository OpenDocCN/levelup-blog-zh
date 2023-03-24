# 如何在 Angular 中为所有用户创建一个社交媒体组件

> 原文：<https://levelup.gitconnected.com/how-to-create-a-social-media-component-in-angular-for-all-of-your-users-549fa945ac4a>

![](img/dce9fd14dcd0fe747d2a3f7d37c56b23.png)

使用 Angular 意味着你再也不用重复代码了！例如，我创建了一个帐户中立的社交媒体组件，这样即使用户有完全不同的帐户，它也可以被反复重用。我概述了以下步骤:

# 步骤 1:创建一个名为 SocialMedia 的界面

您应该利用 Typescript 强大的键入功能来指示用户需要提供什么参数。我选择包含`link`作为用户账户的超链接，包含`user`作为账户持有人的姓名。我添加了`company`作为可选参数，因为 LinkedIn 的公司 URL 与个人用户略有不同。

```
export interface SocialMedia {
  link:string,
  user:string,
  company?:boolean
}
```

# 步骤 2:创建社交媒体组件

在组件的 HTML 中使用变量，以便为用户自动创建超链接和图标。

**注意:**为了创建图标，我创建了单独的组件，这些组件使用指向我的资源库中的 SVG 的链接，而不是 HTML 文件。(我是通过 [Font Awesome](https://fontawesome.com/start) 订阅购买的 SVGs。)然而，为了便于说明，我将这些值切换为常规的图像链接。

# 步骤 3:创建角度输入以接受必要的值

我添加了一个名为“vertical”的附加布尔值，这样用户就可以指定他们是否希望图标内嵌显示。此外，我还将 URL 的开头作为变量，这样用户就可以输入他们帐户中特定于用户的部分，这样，如果将来他们的帐户发生变化，就可以很容易地更新这部分内容。

# 步骤 4:使用组件

我在我的博客网站的[页脚加入了社交媒体组件。](https://cloudengineering.studio/articles)

瞧，这就是将要渲染的内容:

![](img/90c7ea4f1491a7fc177099a0f6865dd2.png)

这里是 Github Gist 中代码的链接。
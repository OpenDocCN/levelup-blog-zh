# 反应本地奖励推荐

> 原文：<https://levelup.gitconnected.com/react-native-reward-referrals-47fb1d281229>

![](img/6ea726c27999c9f6d81081b9d4a5eff2.png)

这是系列 [*的第三篇文章 React Native Deep Linking Simplified*](https://iamshadmirza.hashnode.dev/react-native-deep-linking-simplified-cjzj6qf8900003ss1zv178dm9)在前两篇博客中，我们学习了如何在我们的应用程序中添加深度链接以及如何优雅地处理它。

在本文中，我们将建立一个推荐系统，并充分利用这个惊人的特性。

> *为成功推荐给推荐人和接收者的人提供应用内奖励是鼓励用户使用你的应用的有效方式。*

我们将经历五个简单的步骤。让我们开始吧。

# 涉及的步骤:

1.  安装 Firebase 项目和 SDK
2.  创建邀请链接
3.  发送邀请链接
4.  检索链接
5.  给予奖励

# 第一步。安装 Firebase 项目和 SDK

我们已经在本系列的[第 1 部分](https://iamshadmirza.hashnode.dev/react-native-deep-linking-simplified-cjzj6qf8900003ss1zv178dm9)和[第 2 部分](https://iamshadmirza.hashnode.dev/handling-incoming-dynamic-links-cjzkouqgq002nzts1xvo1pz11)中介绍了这一部分。请先浏览一遍，然后从步骤 2 开始继续。

# 第二步。创建邀请链接

我们已经学习了如何从 Firebase 控制台创建动态链接。这一次，我们将在发送者端生成*邀请链接*，并附上一个`payload`。这个`payload`将在接收端指定发送者的用户账户 ID。它看起来会像这样:

```
[https://www.deeplinkdemo.com?invitedby=SENDER_UID](https://www.deeplinkdemo.com?invitedby=SENDER_UID)
```

对于这篇文章，我将使用一个随机的 SENDER_UID。你可以在 Firebase 用户上调用`getUid()`或者生成你喜欢的 ID。

```
//import firebase
import firebase from 'react-native-firebase';
//Generate unique user ID here
const SENDER_UID = 'USER1234';
//build the link
const link = `https://www.deeplinkdemo.com?invitedBy=${SENDER_UID}`;
const dynamicLinkDomain = 'https://deeplinkblogdemo.page.link';
//call  DynamicLink constructor
const DynamicLink = new firebase.links.DynamicLink(link, dynamicLinkDomain);
//get the generatedLink
const generatedLink = await firebase.links().createDynamicLink(DynamicLink);
console.log('created link', generatedLink);
// console.log: https://deeplinkblogdemo.page.link?link=https%3A%2F%2Fwww.deeplinkdemo.com%3FinvitedBy%3DUSER1234
```

# 第三步。发送邀请链接

既然我们已经创建了链接，我们可以将它包含在邀请中。该邀请可以是电子邮件、SMS 消息或任何其他媒体，这取决于哪种方式最适合您的应用程序和受众。示例:

```
const INVITATION = 'Shad has invited you to try this app. Use this referral link: ' + link;
```

# 第四步。检索链接

当收件人打开带有邀请链接的应用程序时，可能会出现多种情况:

1.  如果尚未安装该应用程序，他们将被引导至 Play Store 或 App Store 安装该应用程序。
2.  如果安装了应用程序，他们将首次打开我们的应用程序，我们可以检索动态链接中包含的推荐信息。

还记得我们在邀请链接中添加了`SENDER_UID`作为有效载荷吗？我们将检索该信息来指定用户并授予奖励。我们想检查该应用程序是否已从动态链接启动。

```
//add the code to the root file of your app
    async componentDidMount() {
        let url = await firebase.links().getInitialLink();
        console.log('incoming url', url); //incoming url https://www.deeplinkdemo.com?invitedby=USER1234
        if (url) {
        const ID = this.getParameterFromUrl(url, "invitedBy");
        console.log('ID', ID); //ID USER1234
        }
    }

    getParameterFromUrl(url, parm) {
        var re = new RegExp(".*[?&]" + parm + "=([^&]+)(&|$)");
        var match = url.match(re);
        return (match ? match[1] : "");
    }
```

> `*getInitialLink()*` *返回 app 启动时的动态链接。如果应用程序不是从动态链接启动的，该值将为空。*

# 第五步。给予奖励

现在我们已经从动态链接中检索到了有效负载数据，我们可以指定共享链接的用户，并在满足我们想要的条件时向推荐人和接收者授予推荐奖励。至此我们的*奖励推荐系统*已经完成。干杯！！

![](img/ce51e88547db9f31c0888b9a252ec9db.png)

我希望你在这三篇博文系列中学习动态链接、处理动态链接和奖励推荐系统的过程中获得了乐趣。觉得有帮助？请分享。
沙德
# 向您的 Vue 应用程序添加实时更新

> 原文：<https://levelup.gitconnected.com/add-real-time-updates-to-your-vue-app-2448339e6b5b>

![](img/98338df45e9b024e4df840f31afc850d.png)

照片由[萨夫](https://unsplash.com/@saffu?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

几个月来，我一直在使用云商店作为我的主要数据库，并且对它非常满意。

> Cloud Firestore 是一个灵活、可扩展的数据库，用于 Firebase 和 Google 云平台的移动、web 和服务器开发。像 Firebase 实时数据库一样，它通过实时监听器使您的数据在客户端应用程序之间保持同步，并为移动和 web 提供离线支持，因此您可以构建响应迅速的应用程序，无论网络延迟或互联网连接如何。Cloud Firestore 还提供了与其他 Firebase 和 Google 云平台产品的无缝集成，包括云功能。

谷歌对 Firestore 的描述足够清晰，但是，出于某种原因，我忽略了关于“通过实时监听器让你的数据在客户端应用之间保持同步”的部分。我认为我忽略了它，因为(a)我认为我不需要它，(b)我不理解它。

我是在 SQL 数据库中长大的，所以我可以非常轻松地编写代码来执行所有的数据库读写操作。实时更新的概念听起来很混乱！😬

不仅这不是真的，而且从现在开始我将使用实时更新。祝您身体健康

# 传统查询

直到最近，我还只是使用传统的查询方法从 Firestore 中提取数据。这就像在 SQL 语句中使用“SELECT”一样。为了以传统方式检索数据，Firestore 提供了一种带有多个选项的`get()`方法:

*   检索集合中的所有文档

`db.collection("cities").get()`

*   检索集合组的所有文档(跨多个集合的子集合)

`db.collectionGroup("landmarks").get()`

*   检索一组中的选定文档(where 子句)

`db.collection("cities").where("capital", "==", true).get()`

*   检索单个文档

`db.collection("cities").doc("SF").get()`

# 实时更新

现在是最酷的部分。为了创建实时监听器，Firestore 提供了`onSnapshot()`方法。您所要做的就是用`onSnapshot()`方法替换上面例子中使用的`get()`方法。例如:

`db.collection("cities").onSnapshot()`

一旦被调用，您的客户将立即自动收到 Firestore 中范围内文档的所有更改。第一次看的时候真的感觉很神奇！

# 履行

这个项目的一个显著变化是，我为客户(SPA、Cordova、electronic 等)添加了 Firebase SDK。)来启用实时更新。以前，我只在服务器上使用 Firebase Admin SDK，并向客户端提供 API。我没有把这作为一个问题提出来，只是在我的典型设置中的一个改变。

因此，我们需要启动与客户的 Firebase 连接。我使用的是 Quasar 框架[,它提供了客户端启动时运行的“引导”文件。](https://quasar.dev)

这里没什么特别的。我们只是连接到 Firebase 并导出 Firestore 方法。

其他一切都在 Vuex 商店处理。

让我们更详细地看一下这个例子。

首先，我正在导入我的 Axios 和 Firestore 实例。Axios 用于对我的服务器进行 API 调用，Firestore 当然用于设置我的实时监听器。

在这个场景中，我处理用户文档。这些文档将以“收藏”状态存储。

对于突变，我只需要一种单独添加、更新和删除用户文档的方法。`SET_USER`变异将处理添加和更新，而`DELETE_USER`变异将处理删除。

我的项目中确实有 getters，但是我在这里没有包括它们，因为它们与主题无关。

我一共有四个动作。前三个，`addUser()`，`updateUser()`，`deleteUser()`在调用我的服务器 API。注意没有“提交”语句？很奇怪，对吧？？😕

最后一个动作是`bindUsers()`，我们神奇地调用 Firestore 来设置我们的监听器。正如您所看到的，发送到客户端的每个变更都有一个“添加”、“修改”或“删除”的类型。有了这个，以及文件本身，我们可以称我们的突变。

我们仍然需要调用我们的`bindUsers()`动作。我有一个列出用户的组件，所以我在这个组件的`created()`生命周期方法中调用它。监听器将保持活动状态，直到用`unsubscribe()`方法将其分离或者客户端关闭。

> 注意:当您第一次启动监听器时，它将遍历 Firestore 中所有范围内的文档。换句话说，你不需要创建一个动作来加载你的初始状态。

让我们思考一下这个问题…

1.  最终用户提交一个新用户
2.  调用`addUser()`存储动作
3.  客户端调用服务器上的 POST 端点
4.  服务器在 Firestore 中创建新的用户文档
5.  Firestore 通知我们的客户新创建的用户😮
6.  `SET_USER`突变将用户添加到用户集合状态
7.  UI 被更新以显示新创建的用户👏(缓慢鼓掌)

你还是不满意吗？？好吧，想象一下这个:

我正在用电脑浏览器上的应用程序。一名员工正在使用手机上的原生应用程序。如上所述，我添加了一个新用户，并立即在 UI 中看到了新创建的用户。我的同事也是！不需要刷新。什么？！？！⭐️ ⭐️ ⭐️ ⭐️ ⭐️

特洛伊·莫兰的更多文章

[](https://graypes.medium.com) [## 特洛伊·莫兰-中等

### 你在用 Vue，Vuex，云 Firestore 吗？即使你不使用 Firestore，你仍然应该阅读这篇文章，因为…

graypes.medium.com](https://graypes.medium.com)
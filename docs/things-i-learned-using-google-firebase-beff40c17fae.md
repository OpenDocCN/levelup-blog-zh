# 我用谷歌 Firebase 学到的东西第一部分

> 原文：<https://levelup.gitconnected.com/things-i-learned-using-google-firebase-beff40c17fae>

当我第一次开始我的编码之旅时，我总是在这里和我的程序员同事谈论认证和授权是多么的烦人。你可以花上几个小时挠头，绞尽脑汁，研究堆栈溢出，最终结果并不像一些前端 JavaScript 代码和 CSS 样式那样花哨和可见。多亏了 OAuth 和 Google Firebase 等第三方认证服务，我们再也不用编写自己的认证和授权代码了。今天，我们将深入了解 Google Firebase。

在我们开始之前，我想提一下 Google Firebase 有这么多的服务，比如认证、存储、数据库、托管和机器学习。就我个人而言，我还没有体验过他们所有的服务，但是让我们回顾一下我最近用 Google Firebase 构建和托管的项目/应用中使用的一些功能和服务。首先，为了让我们使用 firebase，我们需要一个谷歌帐户，在登录谷歌帐户后，我们可以通过简单的谷歌搜索(【https://firebase.google.com/】)来访问 firebase，一旦我们在 Firebase 网站上，我们将创建一个项目。

![](img/d78cc741eb8268df89f95b991a2675ac.png)

一旦创建了项目，我们要做的第一件事就是为项目创建一个应用程序，因为我们的项目是基于 web 的应用程序，我们将第三个选项用 web 启动我们的应用程序(如下图所示)。在 firebase 上创建我们的应用程序期间，它会立即要求我们安装 google firebase CLI，这是我们第一次使用 firebase，我们很乐意通过在我们的终端上键入以下命令来这样做`npm install -g firebase-tools`一旦 firebase 工具安装在我们的 CLI 上，我们就可以开始使用 firebase 作为工具来帮助我们验证、存储数据和托管我们的应用程序。安装 firebase 工具的一个侧面提示，如果安装时出现拒绝许可的错误，我们也可以尝试`sudo npm install -g firebase-tools`，它会立即要求用户输入密码，然后继续安装 firebase。

![](img/1317003d93e0d9bf250fcfcd3b4211ea.png)

因此，我从使用 firebase 中学到的是，他们为程序员提供数据库来构建他们的应用程序，这意味着我们不需要使用传统的后端，如 Node.js 或 Ruby on Rails (MVP)风格的代码，以及通过后端的 API 请求。Firebase 简化了我们的云功能和实时数据库来存储我们的信息。实时数据库意味着我们不必每次向我们的应用程序添加数据时都向我们的后端发出请求，firebase 会自动更新它，并在我们正确设置后自动显示在我们的应用程序上。

![](img/788c62b5bac0a26847937bc294de3537.png)

我们在 firebase 上设置数据库的第一件事是在如上图所示的云 Firestore 上创建它，总是在默认和快速设置的测试模式下启动我们的数据库。请确保使用正确的时区，因为这可能会影响从您的应用程序到 firebase 的信息传输速度。一旦数据库设置正确，我们就可以在数据库中创建一个集合。集合就像我们的 SQL 或 NoSQL 数据库中的信息，具有特定成对信息集的列和行，用于文档收集目的。

![](img/9a7f217d6bd04a17de029548391a61ba.png)

开始收集后，我们接下来要做的是手动向收集中添加信息(如下面的代码片段所示)。为了简单起见，我们通常使用 Auto-ID 作为文档 ID，然后添加字段和值来设置我们的数据库信息。值的类型可以是字符串、数字、布尔值、数组、空值等。在我们的演示中，我们简单地使用字符串，因为所有的信息，如姓名、简历和图像 URL 都与字符串格式相关。一旦我们在集合上创建了第一个文档，通过点击 add document，我们继续为我们的数据库集合创建更多的文档。

![](img/7fdee637490166346eb75b82ebb734f1.png)

接下来我们需要做的是将我们的应用程序连接到我们刚刚创建的数据库。首先，我们转到 firebase 左侧栏的项目概述，转到项目设置，我们将看到我们之前在 firebase 上创建的 web 应用程序，一直向下滚动到屏幕底部，我们将看到 Firebase SDK 片段，单击配置，我们将看到类似的代码片段，如下所示。这是我们在项目中将 firebase 数据库连接到我们的应用程序所需的神奇代码。复制 firebaseConfig 代码并转到我们的应用程序文件，创建一个名为 firebase.js 的文件，然后将这个神奇的代码粘贴到该文件中。

![](img/dd8a6931e49289ddad15677e7b7c4f54.png)

当然，魔法不会自动运行，我们需要编辑 firebase.js 中的代码，以便在我们的应用程序中最大限度地使用 firebase。在下面显示的代码片段中，我们简单地调整和编辑了一些代码，使用 firebase 作为数据库、身份验证和存储来初始化我们的应用程序。接下来我们需要做的是使用`npm install firebase`将 firebase 数据库安装到我们的终端，这将安装并识别 firebase 提供的所有功能，如 initializeApp()、firestore()、auth()等。

![](img/f036938774787ba2c63d7be8d870d7f5.png)

一旦 firebase 的配置文件设置正确，我们需要做的就是调用我们放在数据库中的信息。每当我们想将数据库中的信息调用到我们的代码中时，我们可以简单地导入 firebase.js `import firebase { db } from './firebase';`一旦导入了 db，我们就可以使用 React Hooks，这在调用数据库和操作状态方面非常有用。

![](img/82f6d04fe26eb7f7da50f44f2fca2b14.png)

第 35 行 useState 和第 43 行 useEffect 在上面的代码片段中，它在提取信息并将其存储到我们的代码状态方面非常有用。在代码片段中，db 只是表示来自 firebase.js 的数据库。第 50 行基本上意味着当我们的应用程序刷新时，我们将运行 useEffect 一次。db.collection('posts ')将访问我们命名为 posts 的 firebase 数据库集合，onSnapshot 就像一个非常强大的侦听器，每当数据库被操作(在数据库中添加或删除文档)时，它都会帮助我们拍摄集合的快照，firebase 会自动拍摄快照并再次启动我们的代码，以使用最新数据更新我们的应用程序。setPosts 是我们使用 state 来映射 Posts 集合中的文档数组，并通过文档 ID (firebase Auto ID)和文档数据(在我们的例子中是来自 firebase 数据库文档的 name、bio 和 imgUrl)输出结果的方法。一旦我们在我们的应用程序中完成了这个设置代码，如果我们在 firebase 上向我们的 posts 集合添加一个文档，代码将重新运行并添加新的文档，并使用更新的信息重新呈现我们的应用程序。

我知道我没有涵盖 Google Firebase 提供的所有功能和服务，我计划继续写更多的博客/这篇博客的第二部分，提供更多关于如何通过 Firebase 设置认证和托管的信息。与此同时，请查看以下链接，了解关于这些主题和我最近使用 React.js 和 Google Firebase 构建的项目的更多信息。感谢阅读，并很高兴通过我的 LinkedIn 与你们聊天！

[](https://github.com/haihuanchen/Grams-Project) [## 海环辰/克-项目

### Instagram 克隆应用。在 GitHub 上创建一个帐户，为 haihuan Chen/Grams-项目开发做出贡献。

github.com](https://github.com/haihuanchen/Grams-Project) [](https://medium.com/firebase-developers/what-is-firebase-the-complete-story-abridged-bcc730c5f2c0) [## 什么是 Firebase？完整的故事，节略。

### 原来，谷歌的移动和网络应用开发平台大多是股票照片，XKCD 漫画，和 dank…

medium.com](https://medium.com/firebase-developers/what-is-firebase-the-complete-story-abridged-bcc730c5f2c0)
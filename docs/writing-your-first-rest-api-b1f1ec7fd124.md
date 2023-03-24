# 编写您的第一个 REST API

> 原文：<https://levelup.gitconnected.com/writing-your-first-rest-api-b1f1ec7fd124>

![](img/3109b98b7b48145a9172f20313fe0c96.png)

图为[天马](https://unsplash.com/@tma?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 编写 API 的完整演练指南。

API(应用程序编程接口)是两个程序相互通信的一组规则。API 可用于返回数据、文件和附加信息。简而言之，这是一个抽象概念，因此其他人可以在不了解业务逻辑的情况下利用该应用程序的功能。

我们举个例子来学习一下:假设你在一家餐厅，服务员是一个 API，厨房是服务器。你给那个服务员点餐，然后那个服务员去厨房把你点的菜给厨师，然后厨师完成那个订单，服务员给你上那个订单。API 就是这样工作的，它们提供了两个应用程序之间的接口。您请求端点，该端点为您提供该请求。但是在这整个过程中，你的程序在和另一个程序进行通信，所有的代码对你都是隐藏的。

REST 代表代表性状态转移。这是一种设计 API 的架构风格，由 Roy Fielding 在 2000 年首次提出。在 REST 中，每个 URL 被称为一个**请求**，而您得到的数据被称为**响应**。这是一个设计指南，而不是工具或任何软件。

在创建 API 之前，请确保您的计算机上安装了节点。要安装 Node.js，请访问[节点网站](https://nodejs.org/)。

# 用 Express 和 Node.js 创建 REST API

首先，创建一个用于存储文件和文件夹的文件夹。就叫 test-api 吧，你想怎么叫都行。现在转到该目录并在命令行中运行`**npm init -y**`。它将初始化一个空目录用于 node.js，并创建一个名为 **package.json** 的文件。package.json 文件包含了很多关于你的项目的信息，比如应用程序的名字，关于你的项目的描述，作者的名字，依赖关系，以及依赖关系。你可以随时改变它。所以，让我们改变一些信息:

在上面的脚本中，我们创建了一个命令运行，并将它用于 npm。当你运行`**npm start**`时，它将被执行为`node server.js`，其中 server.js 是我们应用程序的主文件，你可以随意命名。

让我们创建一个 server.js 文件。但是首先，我们必须安装 express，运行给定命令:

`npm install express --save`

它将安装 express 并保存到 package.json。有一件事你已经注意到，另一个名为 **node_modules** 的文件夹被创建。在 node_modules 文件夹中，将保存所有的库和包。

现在让我们创建 server.js 文件并键入给定的代码。

在上面，我们已经导入了 express，然后通过调用 express 函数初始化了一个 app，然后使用 listen 方法，我们已经在端口 5000 上创建了一个服务器。当您运行`**npm run**`命令时，它将在端口 5000 上运行服务器。当你打开 [https://localhost:5000](https://localhost:5000/) 时，它将不会显示任何内容，因为我们还没有创建任何路由。我说的路线是指一条像 https://github.com/rajeshberwal 那样的路

但是在创建任何路由之前，首先要了解一些我们在 web 开发中使用的 HTTP 方法。

1.  **GET** : get 请求用于从 web 上检索数据，例如，当你打开我们的文章时，你发出一个 GET 请求。
2.  **POST** : post 请求用于向服务器发送数据，例如用户信息，如密码、用户名或任何个人信息。
3.  **PUT** :用于更新服务器上的数据
4.  **删除**:用于从服务器上删除一条信息

因此，让我们创建一个对主页的 get 请求。

现在，再次运行`**npm run**`命令，在浏览器中打开 [http://localhost:5000](http://localhost:5000/) 。然后你会看到 **Hello world！在主页上。**

在上面的代码中/表示我们的主主页路由，而在一个方法中，我们要先传递两个东西，一个是路由，一个是中间件函数。中间件功能用于为该路由创建功能。现在让我们创建一个用户路由。

每当有人调用我们的用户路由，就会得到用户的信息。我们将把剩余的路线存储在**路线**文件夹中。你可以把它们保存在任何你想保存的地方。但是将路径存储在 **routes** 文件夹中是一种标准的方式。

在当前目录中创建文件夹路由，并创建另一个名为 users.js 的文件。在用户文件中，我们将创建一些虚拟用户。

现在，将这条路径导入我们的主 server.js 文件。

在 users.js 文件中，我们从 express 导入路由器。然后，我们创建了两条路线，第一条是**/用户，**，第二条是**/用户/{用户 id}** 。

当有人调用用户 route 时，他们将获得所有用户的数据，当他们使用 id `user/1`指定用户时，他们将获得与该用户相关的数据。然后，我们导出了该路由器。我们已经在主 server.js 文件中导入了该路由。

在 server.js 文件中，我们通过 use 方法使用了它。它接受两个参数，第一个是我们想要显示数据的路由，第二个是模块。

当我们运行命令`**npm start**`时，我们可以在 http://localhost:5000/users route 上看到所有用户的信息，当我们指定一个 id http://localhost:5000/users/1 时，它将只返回该用户的数据，如果 id 不可用，它将显示**“User not found”**

在[上 http://localhost:5000/users](http://localhost:5000/users)

如果 id 可用，则:[http://localhost:5000/users/0](http://localhost:5000/users/0)

如果无法识别:

您已经使用 Node.js 和 express 创建了第一个 API。您可以使用这些知识来开发您的应用程序。您可以使用其他 HTTP 方法来增加它的功能。
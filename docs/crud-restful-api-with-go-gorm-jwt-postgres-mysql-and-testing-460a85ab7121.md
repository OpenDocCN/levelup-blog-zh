# 带有 Go、GORM、JWT、Postgres、Mysql 和 Testing 的 CRUD RESTful API

> 原文：<https://levelup.gitconnected.com/crud-restful-api-with-go-gorm-jwt-postgres-mysql-and-testing-460a85ab7121>

![](img/4405ddd37ae8bb77e9e019cac9000a8a.png)

Golang 是一种通用编程语言，为当今计算机的多核现实而构建( [upwork](https://www.upwork.com/hiring/development/golang-programming-language/) )。

## 在本文中:

我们将构建一个**博客**应用程序，用户可以:

*   注册(注册)
*   编辑他的账户
*   关机(删除他的账号)
*   创建一篇博客文章
*   编辑由他创建的博客帖子
*   查看所有博客文章
*   查看特定的博客文章
*   查看其他用户发布的其他博客文章
*   删除他创建的博客帖子

该 API 将使用以下内容构建:

*   去
*   戈尔姆(一种戈兰语形式)
*   JWT
*   Postgres
*   关系型数据库
*   Gorilla Mux(用于 HTTP 路由和 URL 匹配器)

你可能想看看 Postgres 和 Mysql。API 将会以一种你可以决定使用 **Mysql 或者 Postgres 驱动**的方式构建，只需要简单的改变`.env`中的配置。

所有方法和终点都将经过彻底测试。表格测试还将用于测试特定功能的每种可能情况。

## 在以后的文章中:

本文只是第一部分。将来会有文章解释如何:

*   [将 API 归档](/dockerized-crud-restful-api-with-go-gorm-jwt-postgresql-mysql-and-testing-61d731430bd8?source=friends_link&sk=f3772519771e234a5893053410d010e1)
*   [部署在 Kubernetes 上](/deploying-dockerized-golang-api-on-kubernetes-with-postgresql-mysql-d190e27ac09f?source=friends_link&sk=24aacc3d3ef2dbba4526818f27be0ec9)
*   整合特拉维斯，工作服，代码气候等
*   在 AWS、数字海洋和/或 Heroku 上托管 API。
*   使用 React/Vue 消费 API 端点

我实际上在这篇文章[这里](https://dev.to/stevensunflash/real-world-app-with-golang-gin-and-react-hooks-44ph)总结了一切。在那里我用 Gin 框架搭建了一个论坛 App。React 用作前端堆栈。

**开始吧！**

如果你只是想看代码，可以在 [github](https://github.com/victorsteven/Go-JWT-Postgres-Mysql-Restful-API) 上获得这篇文章的源代码

# 步骤 1:基本设置

创建项目所在的目录。这可以在计算机上的任何地方创建。相信我！

姑且称之为**全栈**

```
mkdir fullstack
```

接下来，启动 **go 模块**,这使得依赖版本信息更明确，更易于管理

```
go mod init github.com/{username}/{projectdir}
```

其中`{username}`是你的 **github** 用户名，`{projectdir}`是上面创建的目录。对于我的情况，我将有:

```
go mod init github.com/victorsteven/fullstack
```

我们将在这个应用程序中使用第三方包。如果您以前从未安装过它们，您可以运行以下命令:

```
go get github.com/badoux/checkmail
go get github.com/jinzhu/gorm
go get golang.org/x/crypto/bcrypt
go get github.com/dgrijalva/jwt-go
go get github.com/gorilla/mux
go get github.com/jinzhu/gorm/dialects/mysql" //If using mysql 
go get github.com/jinzhu/gorm/dialects/postgres //If using postgres
go get github.com/joho/godotenv
go get gopkg.in/go-playground/assert.v1
```

接下来，在 **fullstack** 目录中创建一个`api`和`tests`目录

```
mkdir api && mkdir tests
```

创建`.env`文件来存放我们的环境变量

```
touch .env
```

在这一点上，这是我们的结构:

```
fullstack
├── api
├── tests
├── .env
└── go.mod
```

# 步骤 2:环境细节

现在让我们来解决数据库连接的细节问题。

在你喜欢的编辑器(VSCode、Goland 等)中打开`fullstack`文件夹。然后打开`.env`文件

注意到我们在`.env`文件中有`postgres`和`mysql`细节。

默认连接是 postgres。如果您想使用 mysql，只需在`.env`中注释 postgres 并取消注释 mysql 细节。

注意，一次只能使用**一个连接**。但是可以通过使用 Postgres 进行 live 和使用 mysql 进行测试来使事情变得花哨(不推荐)。好吧，你决定怎么做。

# 步骤 3:创建模型

在`api`内创建**型号**目录

```
mkdir models
```

由于我们正在构建一个**博客**，我们现在将有两个模型文件:**用户**和**帖子**

为了避免混淆，最好是发布整个文件，而不是部分文件。

`User`型号:

在路径`api/models`中创建用户模型:

```
touch User.go
```

`Post`型号:

在路径`api/models`中创建帖子模型

```
touch Post.go
```

# 步骤 4:创建自定义响应

在我们创建将与上面定义的模型交互的控制器之前，我们将创建一个定制的响应包。这将用于 http 响应。

在`api`目录中，创建**响应**目录

```
mkdir responses
```

然后在路径:`api/responses`下创建 **json.go** 文件

```
touch json.go
```

json.go

# 步骤 5:创建 JSON Web 令牌(JWT)

请记住，用户在更新或关闭帐户、创建、更新和删除帖子之前需要进行身份验证。

让我们编写一个包，帮助我们生成一个 JWT 令牌，使用户能够执行上述操作。

在`api`目录中，创建认证包(目录)。

```
mkdir auth
```

在路径:`api/auth`中创建 **token.go** 文件

```
touch token.go
```

这是内容:

token.go

从 token.go 文件中可以看到，我们将导入:`“github.com/dgrijalva/jwt-go”`分配给了 **jwt** 。

# 第六步:创建中间件

我们将创建两个主要的中间件:

*   SetMiddlewareJSON :这将格式化对 **JSON** 的所有响应
*   **SetMiddlewareAuthentication:**这将检查所提供的认证令牌的有效性。

在`api`目录中，创建**中间件**包(目录)

```
mkdir middlewares
```

然后在`api/middlewares`路径下创建一个**middleware . go**文件

```
touch middlewares.go
```

中间件，开始

# 步骤 7:创建自定义错误处理

为了以更易读的方式格式化一些错误消息，我们需要创建一个包来帮助我们实现这一点。

记得我们之前创建了一个`utils`目录。我们将在这个目录中创建这个包。

在路径`api/utils`中创建包**格式错误**

```
mkdir formaterror
```

然后在路径:`api/utils/formaterror`下创建一个 **formaterror.go** 文件

`formaterror.go`

当我们“连接”控制器和路由包时，上述内容将对您有意义。

# 步骤 8:创建控制器

现在我们已经到了应用程序的核心部分。让我们创建将与我们的`models`包交互的`controllers`包:

在`api`目录中，创建`controllers`(目录)

```
mkdir controllers
```

在路径`api/controllers`中创建一个名为`base.go`的文件

```
touch base.go
```

该文件将包含我们的数据库连接信息，初始化我们的路线，并启动我们的服务器:

基地，开始

注意我们在导入 mysql 和 postgres 包时使用的下划线。您可以简单地导入您需要的一个，除非您想两个都尝试。

还要注意我们称之为`initializeRoutes()` 的方法。它将在 **routes.go** 文件中定义。

让我们创建一个 **home_controller.go** 文件，欢迎我们使用这个 API:

在路径:`api/controllers`

```
touch home_controller.go
```

home_controller.go

让我们创建 **users_controller.go** 文件:

在路径:`api/controllers`

```
touch users_controller.go
```

让我们创建 **posts_controller.go** 文件:

在路径:`api/controllers`

```
touch posts_controller.go
```

让我们创建 **login_controller.go** 文件:

在路径:`api/controllers`

```
touch login_controller.go
```

login_controller.go

现在，让我们通过创建 **routes.go** 文件来照亮整个地方

在路径:`api/controllers`

```
touch routes.go
```

routes.go

# 步骤 9:用虚拟数据植入数据库

如果您愿意，可以在添加真实数据之前向数据库中添加虚拟数据。

让我们创建一个**种子包**来实现这一点:

在路径`api/`中，创建种子包

```
mkdir seed
```

然后创建 **seeder.go** 文件:

```
touch seeder.go
```

# 步骤 10:创建 API 目录的入口文件

我们需要调用我们在 **base.go** 文件中定义的`initialize()`方法以及我们在上面定义的播种器。我们将创建一个名为 **server.go.** 的文件

在小路上`api/`

```
touch server.go
```

这是内容:

# 步骤 11:创建应用程序入口文件:main.go

在 **fullstack** 目录的根目录下创建 main.go 文件

```
touch main.go
```

这是实际上“启动引擎”的文件。我们将调用在 **server.go** 文件中定义的 **Run** 方法

这是内容:

main.go

在运行应用程序之前，让我们确认一下您的目录结构:

```
fullstack
├── api
│   └── auth
│   │   └── token.go
│   ├── controllers
│   │   ├── base.go 
│   │   ├── home_controller.go
│   │   ├── login_controller.go
│   │   ├── posts_controller.go
│   │   ├── users_controller.go  
│   │   └── routes.go
│   ├── middlewares 
│   │     └── middlewares.go
│   ├── models
│   │     ├── User.go
│   │     └── Post.go
│   ├── responses
│   │     └── json.go
│   ├── seed
│   │     └── seeder.go
│   ├── utils
│   │     └── formaterror
│   │         └── formaterror.go
│   │     
│   │     
│   └── server.go
├── tests
├── .env
├── go.mod
├── go.sum
└── main.go
```

还要确保您的数据库已经创建，并且您已经在`.env`文件中输入了相关数据。

现在，事不宜迟，让我们运行应用程序:

```
go run main.go
```

这将启动应用程序并运行迁移。

![](img/2aea98dabe4ff6b206d6c2e0958bae23.png)

启动应用程序

是啊！你做到了。干得好。

# 步骤 12:在 Postman 中测试端点

你可以使用 Postman 或者你喜欢的测试工具，那么在下一步，我们将**编写测试用例。**

让我们随机测试端点:

**答:GetUsers (/users):**

还记得我们植入了用户表吗

![](img/f72b31644cbf7b16add4737a7abf77e7.png)

**b. GetPosts (/posts):**

请记住，我们还植入了帖子表

![](img/32a26f8bd8038c673bf1826388772064.png)

**c .登录(/login)**

让我们登录用户 1:我们将在下一个测试中使用这个令牌来更新帖子。

![](img/c6d04838b6e10cf15fb46ea0e500aca9.png)

**d . update post(/posts/{ id }):**

要更新帖子，我们需要一个用户的身份验证令牌，在上面的示例中，我们在登录用户 1 时生成了一个令牌。该令牌看起来像这样:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdXRob3JpemVkIjp0cnVlLCJleHAiOjE1NjY5NDE3MzgsInVzZXJfaWQiOjF9.CK8rJ-3IG3xHdCj3iHKA4ihizepij5HNXz4RWrXe50
```

我将使用这个令牌，并将其放在邮递员的**授权:不记名令牌**中:

![](img/7f808a983ecf9191c45041773d209704.png)

然后我将在主体中使用 JSON 发送数据进行更新:

![](img/664afd87bb1fe3788d31b70f92b067f1.png)

如果**用户 2** 试图更新**用户 1** 的帖子，响应将被授权

![](img/a410ea489d0bbbc7a0772bd7b7ccfb06.png)

**e . DeleteUser(/users/{ id }):**

对于用户来说，要关闭他的帐户，他需要首先登录，以便生成 JWT 令牌。

让我们登录**用户 2** 并删除他。

![](img/0979f7c05457504009e4791a16eb783c.png)

抓住令牌，将其插入**授权:不记名令牌**。然后在你的 Postman 中使用一个“ **DELETE** ”方法。

![](img/ea495cea71e23db4cc47ae12a7b32a52.png)

观察**状态码**为 **204** ，表示**无内容**。而回应是 **1** ，表示成功。

**f .更新用户(/用户/{id}):**

**记得登录“要更新的用户”并使用令牌**。由于用户 2 已被删除，我们来更新用户 1:

![](img/bc8f45015e74ea2b876ca685fc1e05a6.png)

**g. CreateUser (/users):**

我们不需要被认证来创建一个用户(注册)。让我们创建第三个用户:

![](img/95b4a5f947268ce5bfe16549fa4071a9.png)

**h. CreatePost (/posts):**

让新用户创建帖子。再次记住，他必须登录并在**授权中使用他的令牌:不记名令牌**

![](img/a64083cc5f4fa7dfd776313a5f1ee16a.png)

您可以测试剩余的端点:

I .**GetUser****(/users/{ id })**—获取一个用户(不需要令牌)

j. **GetPost(/posts/{id})** —获取一个帖子(不需要令牌)

k.**删除帖子(/posts/{id})** —通过身份验证的用户删除他创建的帖子(需要令牌)

长度**主页(/**—获取主页(不需要令牌)

# 步骤 13:为端点编写测试用例

我们已经证明了端点可以工作。但这还不够，我们还需要对它们中的每一个进行真正的测试来进一步证明。

记住，我们在`.env`中定义了一个测试数据库，因此，您可以单独为测试创建一个新的数据库。

我知道我们可以使用接口和模拟数据库调用，但我决定复制真实生活中发生的完全相同的过程进行测试。

在这个项目的开始，我们创建了**测试**目录。

现在，我们将在其中创建两个包:`**modeltests**`和`**controllertests**`

**a .模特测试**

我们将测试在**步骤 3** 中定义的**模型包**中的方法

在路径`tests/`中创建`modeltests`包:

```
mkdir modeltests
```

**cd** 到包中并创建一个文件 **model_test.go**

```
cd modeltests && touch model_test.go
```

在 **model_test.go** 文件中:

`**TestMain()**`测试前做必要的设置。它加载`.env`文件并调用`**Database()**`函数来连接到测试数据库。

您还可以看到，我们编写了将在实际测试中使用的其他帮助函数，例如刷新测试数据库并播种它，这将在每个测试中完成。因此，没有一个测试是依赖于另一个测试才能通过的。

**一、user_model_test.go**

现在，让我们为用户模型中的方法创建测试。在与 **model_test.go** : `tests/modeltests`相同的路径下创建文件 **user_model_test.go**

```
touch user_model_test.go
```

这是内容:

用户模型测试

我们可以在 user_model_test.go 中运行单独的测试。

```
go test --run TestFindAllUsers
```

![](img/91d9aa8688b39a1b02edeef779210005.png)

通过附加`-v`标志获得更详细的输出:

```
go test -v --run TestFindAllUsers
```

![](img/bb380159f44479e5cbdaa8717a37217c.png)

**二。post_model_test.go**

让我们也为 Post 模型中的方法创建测试。创建文件 **post_model_test.go** ，路径同 **model_test.go** : `tests/modeltests`

这是内容:

让我们运行一个测试:

```
go test -v --run TestUpdateAPost
```

输出:

![](img/5a34a9062c5f05d4a2967c84746a4017.png)

让我们做一个测试 **FAIL** ，这样你就可以看到一个失败的测试是什么样子了。对于`TestUpdateAPost`测试，我将改变这个断言:

```
assert.Equal(t, updatedPost.ID, postUpdate.ID)toassert.Equal(t, updatedPost.ID, 543) //where 543 is invalid
```

现在，当我们运行这个测试时，它将失败:

![](img/54c8ef13b12bbb762fefa8c57454a30f.png)

你可以看到这条线:

```
post_model_test.go:101 **1** does not equal **543**
```

**运行 modeltests 包中的所有测试:**

要运行`modeltests`包中的**测试套件**，请确保在您的终端中，您位于路径:`tests/modeltests`。

然后运行:

```
go test -v
```

耶！运行`modeltests`包中的所有测试并全部通过。

**b .控制器测试**

在测试路径:`/tests`中，我们将创建一个名为`**controllertests**`的包，它与`**modeltests**` **在同一个目录路径中。**

```
mkdir controllertests
```

接下来，我们将创建 **controller_test.go** 文件，在这里我们将定义`**TestMain()**` ，它加载`.env`文件，设置数据库连接，并调用 seeder 函数。

在路径:`tests/controllertests`

```
touch controller_test.go
```

**一、login_controller_test.go**

这里我们将使用**表测试**。**表测试**背后的思想是所有可能的情况都定义在一个结构中，它使用一个循环来运行每个测试用例。这为我们节省了大量时间，因为我们倾向于用一个测试函数来测试特定功能的所有情况。

在与 controller_test.go 相同的目录路径中，我们创建一个文件

```
touch login_controller_test.go
```

内容如下:

运行 TestLogin:

```
go test -v --run TestLogin
```

![](img/366a8650b326040be1d439004ff38bcb.png)

测试登录

**二。user_controller_test.go**

这个测试文件广泛地使用**表测试**来测试所有可能的情况，而不是为每个情况创建单独的测试函数，因此节省了我们大量的时间。

在与 controller_test.go 相同的路径下，创建 **user_controller_test.go 文件**

```
touch user_controller_test.go file
```

让我们在该文件中运行一个测试函数来演示。

正在运行 **TestCreateUser**

```
go test -v --run TestCreateUser
```

![](img/fe7a51a8b1275f3fd99703ab6bcd0730.png)

TestCreateUser

观察这个:

![](img/47b298332269264871414606175245a1.png)

这是我们的表格测试中的一个案例:

```
{
  inputJSON: `{"nickname":"Pet", "email": "grand@gmail.com",  "password": "password"}`,statusCode:   500,errorMessage: "Nickname Already Taken",
},
```

由于这个测试命中数据库并识别出`Nickname already exist`并不能插入那个记录，所以返回一个错误，这正是我们在那个测试中想要的。**因此测试通过了**。当电子邮件被复制时，同样的事情也适用。

**三。post_controller_test.go**

**表格测试**在本文件中也被广泛使用。在与 controller_test.go 相同的目录下，创建**post _ controller _ test . go**文件

```
touch post_controller_test.go
```

内容如下:

在这个测试文件中，我们可以运行 **TestDeletePost** 函数

```
go test -v --run TestDeletePost
```

![](img/0b933bfb0365ef8749759415e245e56e.png)

TestDeletePost

**运行 controllertests 包中的所有测试**

我们还可以为`controllertests`包运行**测试套件**。

在`tests/controllertests`运行的路径中:

```
go test -v 
```

所有测试都通过了。感觉不错！

在应用程序中运行整个测试。

此时，您的文件夹结构应该如下所示:

![](img/62c7f7aeaaab6ce8e5374335fc242cc7.png)

现在，要运行**测试套件**，您应该在`tests/`目录中。

然后运行:

```
go test -v ./...
```

这将运行`**modeltests**`和`**controllertests**`包中的所有测试。

![](img/bb1c3b27c1a99a20ee0d40d907615bb5.png)

没有一次测试失败。多甜蜜啊。

# 结束了。

坦白地说，我做了大量的研究来整理这篇文章。由于编写测试用例，我不得不改变整个应用程序的结构。这真是令人头疼。我认为现在一切都是值得的，测试每一个功能/方法都会带来更好的软件。真的，我感觉很好。

下一步是什么？

*   将应用归档(本文第 2 部分[此处](/dockerized-crud-restful-api-with-go-gorm-jwt-postgresql-mysql-and-testing-61d731430bd8?source=friends_link&sk=f3772519771e234a5893053410d010e1))
*   在 Kubernetes 上部署(本文第 3 部分[此处](/deploying-dockerized-golang-api-on-kubernetes-with-postgresql-mysql-d190e27ac09f?source=friends_link&sk=24aacc3d3ef2dbba4526818f27be0ec9)
*   整合特拉维斯，编码气候和工作服
*   在 AWS、Digital Ocean 或 Heroku 上部署应用程序。
*   使用 React/Vue 使用 API。

我实际上在这篇文章[这里](https://dev.to/stevensunflash/real-world-app-with-golang-gin-and-react-hooks-44ph)总结了一切。在那里我用 Gin 框架搭建了一个论坛 App。React 用作前端堆栈。

你可以在 medium 上关注我以获取更新。你也可以在[推特](https://twitter.com/@stevensunflash)上关注我

## 在 [github](https://github.com/victorsteven/Go-JWT-Postgres-Mysql-Restful-API) 上获得本文的源代码

编码快乐！

[](https://gitconnected.com/learn/golang) [## 学习围棋-最佳围棋教程(2019) | gitconnected

### Go 是一种静态类型的命令式编译语言。然而，与许多编译编程语言不同，Go 是…

gitconnected.com](https://gitconnected.com/learn/golang)
# 使用 Laravel + JWT 进行 API 认证

> 原文：<https://levelup.gitconnected.com/api-authentication-with-laravel-jwt-7ee110649972>

![](img/43f45e617e20be182e40079e15e3bcda.png)

# 介绍

认证是互联网上大多数服务的基本特征。我们希望能够限制谁可以访问什么，并尽可能以最方便、最安全的方式做到这一点。本文试图介绍使用 Laravel PHP 框架和 JWT 的 API 服务中的认证。本文假设您有一些用 PHP 编写面向对象代码的经验。

## API 认证？

是的，我们将使用 Web 服务 API 认证我们的用户，特别是 RESTful API 服务。本文假设我们希望将前端和后端服务分开，在本教程系列中，我们将构建一个客户端来与我们正在构建的 API 进行交互。如果我们没有使用 REST API，我们将不得不使用会话来认证用户。这样，服务器为用户创建一个新的会话文件，并赋予它一个惟一的标识符，该标识符被发送给用户并作为 cookie 保存在用户处。这通常被称为有状态系统。

对于我们的 API 服务(它是无状态的)，我们将使用令牌，令牌通常由服务器发出，并在每个请求期间由客户端携带。在这个系统中，没有用户活动的记忆——每个请求都被认为是第一个。
对于这个应用程序，我们将使用 jwt(JSON Web 令牌)作为我们的令牌，由服务器进行发布和验证。JWT 只携带编码的 JSON 数据，通常在 API 服务中用作授权令牌。(阅读更多关于 jwt 的内容—【https://auth0.com/docs/secure/tokens/json-web-tokens )

## JWTs vs 圣所

如果您以前在 Laravel 中使用过 API，那么您一定会发现 Sanctum 或 Passport 是对您的服务上的用户进行身份验证的有效方法。那为什么是 JWTs 呢？

在遇到 jwt 之前，我在 Sanctum 工作了一段时间。我注意到的一件事是令牌存储在数据库中。因此，对于每个请求，都会执行一个数据库查询来验证令牌，然后执行另一个查询来获取用户信息。处理每个请求所增加的开销是巨大的。此外，除了作为查询数据库的随机生成的字符串之外，您很少或根本没有使用令牌。那会占用空间，但对你没什么用。在另一个实例中，如果您多次登录，您的数据库中会添加几个令牌，其中大部分是无用的，等待被删除。可以写一篇单独的文章来讨论这些限制以及它们如何影响您的生产。

相比之下，JWT 令牌是独立的，主要由客户端负责。您的服务器只是生成一个令牌并发送给客户端，而不关心令牌会发生什么。这意味着您可以多次登录，这不会以任何方式影响服务器端。此外，您还可以操作令牌上的数据。您可以将用户信息存储在令牌中，从而不再需要额外的数据库查询。从某种意义上说，这是我们应用程序上下文中访问令牌的原始概念。

# 设置和配置

首先，我们需要安装一个新的 Laravel 应用程序。在本教程中，我们将使用 Laravel 8，以便每个人都能跟上，但请记住, [Laravel 9](https://laravel.com/docs/9.x/releases) 是可用的，可以用于本教程。
要安装 Laravel 8，请使用以下命令:

```
composer create-project laravel/laravel:^8.0 jwt-laravelcd jwt-laravel
```

接下来，我们安装 tymon/jwt-auth 包:

```
composer require tymon/jwt-auth:*
```

我们使用*通配符是因为一些现有的包可能与 jwt-auth 的最新版本冲突，我们希望安装我们至少可以获得的版本。

接下来，在 config/app.php 文件的 providers 数组中，在数组末尾添加 JWT 提供者:

```
// […other providers],
Tymon\JWTAuth\Providers\LaravelServiceProvider::class,
```

在同一文件中别名数组的末尾，添加以下值:

```
‘JWTAuth’ => Tymon\JWTAuth\Facades\JWTAuth::class, 
‘JWTFactory’ => Tymon\JWTAuth\Facades\JWTFactory::class,
```

这个项目的 GitHub 资源库可以在:[https://github.com/joshuaetim/jwt-laravel-article](https://github.com/joshuaetim/jwt-laravel-article)看到

运行以下命令发布配置文件:

```
php artisan vendor:publish –provider=”Tymon\JWTAuth\Providers\LaravelServiceProvider”
```

生成密钥的命令:

```
php artisan jwt:secret
```

# 编辑您的模型

在您的模型可以使用 JWT 进行身份验证之前，您需要为此进行设置。对于我们的用户模型，这意味着从我们刚刚安装的包中实现 JWTSubject 接口。要在 PHP 中成功实现一个接口，我们必须实现接口中定义的函数。

下面是我们的用户模型的样子:

接下来，使用以下命令设置数据库并运行迁移:

```
php artisan migrate
```

# 定义路线

我们的 routes 表示 URL 到处理函数的映射。对于这一步，我们将拥有处理欢迎页面、注册、登录、仪表板和获取用户的路由。
下面是 out routes/api.php 的样子:

仪表板路由受到' *jwt.verify* '中间件的保护，我们稍后将创建该中间件。

# 定义控制器

我们创建一个名为 *AuthController* 的新控制器来处理我们的认证需求:

```
php artisan make:controller AuthController
```

AuthController 的内容如下所示:

在该文件中，我们创建了一些函数来格式化我们的成功和错误响应，以减少重复。然后，我们设置注册、登录和获取当前用户的方法。

# 定义中间件

我们将使用一个中间件来保护某些路由，比如前面定义的仪表板路由，在处理请求之前将调用中间件。

要创建中间件，请运行以下命令:

```
php artisan make:middleware JWTMiddleware
```

JWTMiddleware 的内容如下所示。它基本上试图从请求头中获取当前经过身份验证的用户。如果找不到用户或令牌有问题，将返回错误响应。

要注册这个中间件，请转到 *App/Http/Kernel.php* 并在 **$routeMiddleware** 数组的末尾添加这个值:

```
 ‘jwt.verify’ => \App\Http\Middleware\JWTMiddleware::class,
```

如果你注意到了，我们从未接触过由 [Laravel](https://laravel.com/docs/8.x/authentication) 提供的原始 auth()服务或中间件。这对我们来说很方便，因为我们可能需要扩展或实现主认证服务。如果您想使用主 auth()服务，只需在 *config/auth.php* 中将 api guard 提供者更改为‘jwt’。更多信息可以在这里找到:[https://jwt-auth.readthedocs.io/en/develop/quick-start/](https://jwt-auth.readthedocs.io/en/develop/quick-start/)

# 即将推出

在下一部分，我们将

*   实现定制的帮助器函数来帮助我们更好地进行身份验证，
*   允许用户注销服务

请继续关注，如果你对我有任何反馈，请告诉我。一定要鼓掌，这样其他人也能看到这个故事。
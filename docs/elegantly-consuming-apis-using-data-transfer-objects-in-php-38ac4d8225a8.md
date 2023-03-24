# 使用 PHP 中的数据传输对象优雅地使用 API

> 原文：<https://levelup.gitconnected.com/elegantly-consuming-apis-using-data-transfer-objects-in-php-38ac4d8225a8>

## 通过正确解析 API 响应来清理代码

![](img/d9b39a6cfc346589b4245ec1e75663be.png)

照片由[戈兰·艾沃斯](https://unsplash.com/@goran_ivos?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

API 是现代互联网的基石。您可以使用它们在前端和后端之间进行通信，从第三方资源获取信息，触发事件，验证和授权您的用户，等等。

从 PHP 使用 API 很容易。然而，由于从 API 返回的数据通常是 JSON 格式的，处理返回的数据可能会变得相当混乱。在本文中，我将展示如何以一种非混乱的方式使用从 API 返回的数据，方法是将响应自动存储在格式正确的对象中，这些对象允许以一种良好的、结构化的方式处理数据。

首先，我们将从查看一个示例 API 开始，看看如何使用它，以及我们希望用使用的数据实现什么。之后，我们将引入一些*数据传输对象*来清理我们的代码，使 API 数据的处理更加优雅。我们开始吧！

# 示例 API

对于本文，我们将使用来自 [JSONPlaceholder](http://jsonplaceholder.typicode.com) 的 API，该服务为 web 开发人员提供了一些示例 API。从可用的 API 来看，我们将关注于 [/users](http://jsonplaceholder.typicode.com/users) 端点，它以如下所示的格式为一组 10 个用户提供服务。

我们将使用这些数据打印出用户的 ID、命名的电子邮件地址(姓名和电子邮件地址的组合)和居住地址的简单概述。对于上面的 API 结果，我们的输出如下所示。

```
User 1, Leanne Graham <[Sincere@april.biz](mailto:Sincere@april.biz)> lives at:
Kulas Light, Apt. 556
92998-3874 Gwenborough
```

这是一个简单的例子，但是它需要使用嵌套数据来实现我们的结果。此外，您可以注意到，我们并没有利用从 API 返回的所有数据——请记住这一点，以后再说。首先，让我们设置一个简单的应用程序来实际使用这个 API。

# 使用 API

为了展示 API 的使用，我们将使用 Composer 包和 [PSR-4 自动加载](https://getcomposer.org/doc/04-schema.md#psr-4)构建一个简单的 PHP 应用程序，这样我们就不用担心自己需要文件了。我们将在`app/src`文件夹的`App`名称空间中添加我们的类，所以我们将下面的条目添加到我们的`composer.json`文件中:

```
"autoload": {
  "psr-4": {
    "App\\": "app/src"
  }
}
```

设置好自动加载路径后，我们可以专注于使用 API 了。为此，我们将使用使用 Composer ( `composer require guzzlehttp/guzzle`)安装的 [Guzzle](https://docs.guzzlephp.org/en/stable/) 库。我们将创建一个非常简单的存储库类，用于从 API 获取 JSON 解码的数据。这个类将包含一个静态函数，该函数将使用 Guzzle 库获取数据并解码返回的 JSON 字符串。

因为我们在这里创建了一个简单的例子，所以我们将高效的类设计和错误处理排除在范围之外。我们最终的存储库类将如下所示，并且应该在`app/src/Repository/UsersRepository.php`创建。

现在我们有了一个简单的存储库来从 API 获取数据，我们可以处理它了。我们从`UsersRepository::get()`函数接收到一个解析过的用户数组，我们可以通过迭代来显示我们想要显示的信息。这可能看起来像这样。

*呀呀*。

如果我们的 API 调用的结果是带有预定义属性的漂亮对象，那就更好了。这样，IDE 就能够自动完成属性，我们会被警告任何我们犯的错误。所以，这正是我们要做的。

# 使用数据传输对象

我们不是将用户对象解析成匿名的`stdClass`对象，而是创建一个漂亮的`User`对象，它包含我们想为用户获取的正确类型提示的属性。我们不会手动填充这些属性，但是我们将使用 Spatie 的[数据传输对象](https://github.com/spatie/data-transfer-object)包来完成这个任务(`composer require spatie/data-transfer-object`)。

这个库通过提供一组抽象类来工作，您可以使用这些抽象类来扩展您的数据类。主类被恰当地称为`DataTransferObject`，它将数组元素映射到数据类的(公共)属性。

然而，`DataTransferObject`有一个属性对我们来说是个问题——它想将数组中的所有*元素映射到对象属性。正如我们之前看到的，我们不想消耗 API 中的所有元素，因此我们不想用我们永远不会使用的属性来扩展我们的数据对象。这就是`FlexibleDataTransferObject`发挥作用的地方:这个抽象类实际上等同于`DataTransferObject`，除了它将忽略没有数据对象属性可用的数组元素。完美！*

让我们看看我们的`User`对象的数据对象可能是什么样子。这个类存在于`app/src/Model/User.php`文件中。

不要太担心这里的`Address`类——我们稍后会谈到它。我们为姓名和电子邮件地址创建了两个字符串属性，为 ID 创建了一个整数属性。这起到了类型安全的作用，因为如果接收到的数据与预期的数据类型不匹配，就会抛出一个`DataTransferObjectError`。

我们还创建了两个助手函数。一种是根据姓名和电子邮件地址格式化命名的电子邮件地址，这样我们就不必再在数据类之外格式化它了。另一个辅助函数是静态的`fromApiResult`函数，它接受一组数据，并很好地将它映射到`User`类的一个实例。请记住，如果数组中的数据不适合这个类，这个函数可能会抛出！

随着`User`类的退出，让我们来看看`Address`类。这个类存在于`app/src/Model/UserProperty/Address.php`文件中。

我们在这里做了更多相同的事情:我们使用了一个`FlexibleDataTransferObject`，因为我们没有使用 API 响应数据中的所有属性，我们只是将街道、套房、城市和邮政编码数据放在公共字符串成员中。

除此之外，我们还添加了神奇的`__toString()`函数，所以我们可以简单地将这个`Address`类的一个实例转换成一个格式正确的字符串。

很好。

正如我们在`User`对象中看到的，我们需要用一个数据数组而不是一个`stdClass`实例来调用`User::fromApiResult`函数，所以我们需要对`UsersRepository::get()`函数做一些调整。我们将把 API 响应 JSON 解码成一个关联数组，并将数组中的每个条目映射到一个`User`对象，这样我们就返回了一个`User`对象的数组。那看起来如下(看第 17 行！).

现在我们已经更新了我们的存储库，让我们看看如何使用`UsersRepository::get()`函数的结果来获得与之前相同的结果。

这个看起来干净多了。我们可以直接打印`$user->address`属性，因为我们实现了`Address::__toString()`函数，格式化指定的电子邮件地址是由数据对象处理的。

# 使用可选属性

尽管我们的 API 为所有用户返回了一个良好且一致的格式，但对于您正在使用的实际 API 来说，情况可能并非如此。假设我们的数据集中可能有没有居住地址集的用户。如果没有我们的`User`和`Address`对象，通过使用本文顶部的例子，您可能必须检查属性是否存在，并根据结果打印两种不同的格式:

我们可以称这种实现不太理想。

然而，通过我们在前面步骤中所做的设置，我们可以简单地使`User::$address`属性为空，以达到我们想要的结果:

```
*/**
 ** ***@var*** *\App\Model\UserProperty\Address|null
 */* public ?Address $address;
```

现在，我们可以更新上一步的`users.php`脚本，如下所示:

那看起来好多了。

编码快乐！

# 资源

PSR-4 自动装弹使用作曲:【https://getcomposer.org/doc/04-schema.md#psr-4 

GuzzleHTTP 库文档:[https://docs.guzzlephp.org/en/stable/](https://docs.guzzlephp.org/en/stable/)

数据传输对象库:[https://github.com/spatie/data-transfer-object](https://github.com/spatie/data-transfer-object)
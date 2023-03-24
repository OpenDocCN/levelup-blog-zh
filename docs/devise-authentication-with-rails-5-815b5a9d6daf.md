# 用 Rails 5 设计身份验证

> 原文：<https://levelup.gitconnected.com/devise-authentication-with-rails-5-815b5a9d6daf>

![](img/0d4faa7e239115e0f97c7539cb9250c7.png)

## [设计](https://github.com/plataformatec/devise)是 Ruby on Rails 认证的基石

使用 device，创建可以登录和退出应用程序的用户是如此简单，因为 device 负责用户创建(`users_controller`)和用户会话(`users_sessions_controller`)所需的所有控制器。gem 基于 [Warden](https://github.com/hassox/warden) 并用 bcrypt 处理认证，消除了手动散列和加盐密码的需要(相信我，这太有用了。当我刚开始做开发人员时，这让我陷入了很多麻烦..).

当你与 RoR 合作时，没有理由不使用认证宝石。手动实现认证很麻烦，需要多个控制器和几个小时的设置(不包括测试，您的应用程序中包括测试，对吗？).

如果你想知道 Devise 是如何工作的，我的建议是浏览一下[手册教程](https://www.sitepoint.com/rails-userpassword-authentication-from-scratch-part-i/)以了解身份验证的基础知识，构建一个使用手动身份验证的测试 Rails 应用程序，阅读相关原则，然后永远不要再这样做(除非你的公司将来让你这样做)。

这不是关于设置 Devise 的概述——在这篇文章中，我想回顾一下 gem 的一些优点、特性和一些缺点(以及如何解决它们！)

![](img/2d964953694d7bbfc49ce9650a49541a.png)

使用 Devise 在三个命令中启用用户身份验证。

## 对您的模型使用设计

Devise 使用 10 个模块来配置用户验证。您可以在您的用户模型中找到这些模块。其中六个模块默认启用(标有*)。修改`config/initializers/devise.rb` 文件中的现有配置。

*   * **数据库可认证** —散列密码并将其存储在数据库中。身份验证由 POST 请求完成。需要在数据库中保存用户/哈希密码。
*   **Omniauthable** —增加对 Omniauth 提供者的支持，允许通过第三方提供者登录，如脸书、Twitter 等
*   **可确认** —禁止访问用户帐户，除非用户已通过电子邮件确认其帐户。
*   * **可恢复** —添加“忘记密码”链接，允许用户使用电子邮件重置密码
*   ***可注册** —创建注册过程，用户现在可以编辑和删除他们的帐户。对 beta 测试/仅限邀请的网站禁用
*   * **可记忆** —创建一个令牌并存储一个带有已保存 cookie 的用户会话(添加“记住我”复选框)
*   * **可跟踪** —跟踪用户 IP 地址、登录次数、上次登录次数和时间戳
*   **Timeoutable** —在一定时间后注销用户。
*   * **Validatable** —使用内置设备验证电子邮件地址和密码(长度、字符等)。config/initializer/device . Rb 中的更改验证
*   **可锁定** —在特定时间或特定次数的登录尝试后锁定帐户。

## 设计辅助方法

Devise 还附带了一些非常有用的助手功能:

*   *before_action:验证 _ 用户！* —添加到任何控制器，以限制对动作的访问，除非用户登录

```
before_action :authenticate_user!, except: [:show, :index] before_action :authenticate_user!, only: [:show, :index]
```

*   *skip _ before _ action:authenticate _ user！* —或者，您可以将 before 操作添加到应用程序控制器，将应用程序中的每个页面列入黑名单，然后使用 skip_before_action 命令允许逐页访问

```
skip_before_action :authenticate_user!, except: [:show, :index] skip_before_action :authenticate_user!, only: [:show, :index]
```

*   *用户 _ 登录？* —验证用户是否已登录，然后才能执行某个操作。添加到控制器或视图。返回真或假。

```
if user_signed_in?
  DO SOME ACTION
else 
  DO ANOTHER ACTION
end
```

*   current_user_ —添加到控制器或视图，以返回执行操作的用户的实例。E.x .您正在创建一个属于用户的餐馆。如果当前用户是餐馆的所有者，则将`@restaurant.user`设置为`current_user`

```
@restaurant.user = current_user
@restaurant.save
```

## 路线

如果使用 Devise，您不需要设置单独的路线，只需在您的`config/routes.rb`文件中添加以下一行即可:

```
devise_for :users
devise_for :MODELNAME (if not using User as the name of the model)
```

# 当设计失败时

**默认情况下，使用 Devise 不会强制使用强密码。gem 的好处是它不存储密码——只是一个加密的散列——但仍然允许用户创建像“123456”或“password”这样的密码。我的经验是，如果用户可以创建不安全的密码，他们就会创建不安全的密码。**

*   要启用强密码，请确保添加了[设计安全扩展](https://github.com/phatworx/devise_security_extension) gem(有人要求将其包含在设计核心中，但谁知道这何时会发生)

这是一项正在进行的工作，我会不断更新。我错过了什么你想进一步了解的东西吗？下面让我知道！

*原载于* [*跳过海关*](http://www.skippingcustoms.com/how-to-use-devise-gem-rails-5/) *。*
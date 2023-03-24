# Ruby on Rails 的简单认证指南

> 原文：<https://levelup.gitconnected.com/simple-authentication-guide-with-ruby-on-rails-16a6255f0be8>

![](img/37af0ee512feeef511f4832c5685641d.png)

Jason Blackeye 的照片(Unsplash)

这是一个关于在 Rails 应用程序中实现授权/认证的简单教程。我将使用最新版本(6.0)的 Ruby on Rails。根据记录，这是实现 auth 的许多方法之一，它旨在展示一种基本方法。

# 设置

我们从生成一个全新的 Rails 应用开始。

```
rails new session_practice
cd session_practice
```

在根目录下，我们将运行一些命令来生成我们的模型和控制器。在此之前，我们先讨论一下整体结构。我们将使用一个模型`User`。我们将有两个控制器，一个用于`User`,另一个用于处理定制路由以管理会话。

# 模型

我们的用户模型将只有两个属性，`username`和`password`。

```
rails g model user username password_digest 
```

# 控制器

`UsersController`将有`new`和`create`路线。除了`new`和`create`路线外，`SessionsController`还将有`login`和`welcome`的定制路线。也将生成`create`的视图页面，但不会使用，请随意删除该文件。

```
rails g controller users new create 
rails g controller sessions new create login welcome 
```

# Bcrypt

在我们的数据库，或任何数据库中，我们不应该存储纯文本密码。相反，我们引入 Bcrypt 来加密密码并将其存储到数据库中。让我们确保已经安装了`bcrypt`宝石:

```
gem install bcrypt
bundle install
```

对于我们的`User`模型，我们有`password`字段，我们将设置一个宏来利用 Bcrypt 方法。

```
class User < ApplicationRecord has_secure_passwordend
```

# 路线

接下来，让我们在`config/routes.rb`下设置我们的路线。您现在可能已经知道，因为我们跑了`rails g controller`，所以路线已经存在。我们可以删除这些路线，并添加我们需要的路线。

```
Rails.application.routes.draw do resources :users, only: [:new, :create] get 'login', to: 'sessions#new' post 'login', to: 'sessions#create' get 'welcome', to: 'sessions#welcome'end
```

欢迎页面将包含我们的注册和登录按钮，将我们重定向到适当的形式。稍后，该页面还将检查用户是否登录，如果登录，将显示他们的姓名。

# 欢迎页面

让我们打开我们的`views/sessions/welcome.index.erb`并添加一些元素。注册按钮将重定向到用户的`new`控制器，该控制器将显示包含注册表单的用户的`new.html.erb`。登录按钮将重定向到会话新控制器，该控制器将显示包含登录表单的会话`new.html.erb`。

```
<h1>Welcome</h1><%= button_to "Login", '/login', method: :get%><%= button_to "Sign Up", '/users/new', method: :get%>
```

# 签约雇用

请记住，我们遵循的是 rails restful 约定。@ user 从`User`控制器初始化为`@user = User.new`。在**视图/用户/new.html.erb** 中，我们将拥有以下`form_for`:

```
<h1>Sign Up</h1><%= form_for @user do |f|%> <%= f.label :username%><br> <%= f.text_field :username%><br> <%= f.label :password%><br> <%= f.password_field :password%><br> <%= f.submit %><%end%>
```

在提交时，我们将被重定向到**用户**创建控制器，它将处理一些事情:

```
def create @user = User.create(params.require(:user).permit(:username,        
   :password)) session[:user_id] = @user.id redirect_to '/welcome'end
```

首先，我们创建一个用户实例，并最终重定向到欢迎页面。注意，我没有实现验证。介于两者之间，我们还需要一种方法来存储会话中的用户 id。

然后我们需要创建一个方法来处理当前用户的信息。我们将在**应用控制器**中创建这个方法，以确保我们的其他控制器可以访问这个方法。

```
def current_user    User.find_by(id: session[:user_id]) end
```

同时，我们希望在我们的**应用控制器**中有一个方法不仅可以跟踪我们的当前用户，还可以检查他们是否登录:

```
def logged_in?

    !current_user.nil? end
```

为了使这两个方法对视图可用，我们需要`helper_method`宏的帮助。仍然在**应用控制器**中，我们将添加:

```
class ApplicationController < ActionController::Basehelper_method :current_user
helper_method :logged_in?def current_user User.find_by(id: session[:user_id])enddef logged_in?

    !current_user.nil?end end
```

让我们利用这两种方法来确定用户是否登录，如果是，还显示他们的用户名。让我们回到`views/sessions/welcome.html.erb`并补充:

```
<h1>Welcome</h1><% if logged_in? %> <h1>You are Logged In, <%= current_user.username %></h1><%end%><%= button_to "Login", '/login', method: :get%><%= button_to "Sign Up", '/users/new', method: :get%>
```

首先，我们放置一个使用`logged_in?`方法的条件。如果它返回 true，这意味着用户已经登录，h1 将会呈现。在 h1 标签中，我们正在访问将返回`User`实例的`current_user`方法，我们想要显示实例的用户名。

我们现在有一种方法来注册我们的用户，并将他们存储在他们的会话中。我们可以为登录页面实现类似的方法。让我们从**会话控制器** `new`方法开始，它将呈现登录表单。在`views/sessions/new.html.erb`中，我们添加:

```
<h1>Login</h1> <%= form_tag '/login' do %> <%= label_tag :username%> <%= text_field_tag :username %> <%= label_tag :password%> <%= password_field_tag :password%> <%= submit_tag "Login"%><%end%>
```

我们有与注册表单相似的字段，但是因为我们没有创建新的用户实例，所以我们将实现一个`form_tag`。在我们的路由中，我们为`/login`定义了一个 post 路由，它将被定向到**会话控制器** `create`方法。会话的 create 方法将负责根据表单提供的用户名参数查找用户实例。

```
def create @user = User.find_by(username: params[:username]) if @user && @user.authenticate(params[:password]) sessions[:user_id] = @user.id redirect_to '/welcome' else redirect_to '/login' endend
```

如果找到匹配的用户，我们还想检查密码是否匹配。为此，我们利用 Bcrypt `authenticate`方法，该方法接受密码参数的一个参数。如果用户存在并且密码匹配，则用户实例的 id 存储在会话中，并且他们可以登录。否则，我们将被重定向回登录表单页面。(在本指南中，我们不会深入介绍如何显示闪存错误)。

# 批准

目前，我们的页面有限。当我们的应用程序变大时，我们不希望未注册的用户访问某些路线。然而，我们确实希望一些路由是可访问的，以允许用户注册或登录。为此，我们需要在多个控制器中添加方法。

为了测试这一点，我们还将添加一个额外的视图页面，只有用户登录后才能访问该页面，以及一个访问该页面的控制器方法和路由。在我们的会话控制器中，让我们添加一个方法`page_requires_login`。同时，让我们在`views/sessions` 下创建`page_requires_login.html.erb`，并在我们的路线中，添加`config/routes.rb`下的授权路线:

```
Rails.application.routes.draw do resources :users, only: [:new, :create] get 'login', to: 'sessions#new' post 'login', to: 'sessions#create' get 'welcome', to: 'sessions#welcome' get 'authorized', to: 'sessions#page_requires_login'end
```

我们希望除了`/authorized`之外的所有路线都可以被未注册用户访问。但是我们还没有定义这个动作。让我们导航到我们的**应用程序控制器，**添加另一个方法`authorized`和另一个宏`before_action`，将其设置为授权。

```
class ApplicationController < ActionController::Basebefore_action :authorized
helper_method :current_user
helper_method :logged_in?def current_user User.find_by(id: session[:user_id])enddef logged_in?

    !current_user.nil?enddef authorized redirect_to '/welcome' unless logged_in?endend
```

宏`before_action`要求授权的方法在采取任何其他动作之前运行。在这种情况下，除非用户登录，否则用户将总是被重定向到欢迎页面。然而，我们希望允许用户登录的方法可用。首先，让我们导航回**用户控制器**并添加以下宏`skip_before_action`:

```
class UsersController < ApplicationControllerskip_before_action :authorized, only: [:new, :create]def new @user = User.newenddef create @user = User.create(params.require(:user).permit(:username,        
   :password)) session[:user_id] = @user.id redirect_to '/welcome'endend
```

这个宏允许这两种方法跳过授权的方法要求。我们还将对**会话控制器**、执行宏，但是我们将在方法中添加 welcome，因为我们希望能够访问 welcome 页面。

```
class SessionsController < ApplicationControllerskip_before_action :authorized, only: [:new, :create, :welcome]def newenddef loginenddef create @user = User.find_by(username: params[:username]) if @user && @user.authenticate(params[:password]) sessions[:user_id] = @user.id redirect_to '/welcome' else redirect_to '/login' endenddef page_requires_loginendend
```

现在，让我们检查一下我们的方法是否有效。让我们回到我们之前创建的`views/sessions/page_requires_login.html.erb`。我们将添加一个 h1 标记。这个页面的目的是检查我们是否只有在登录后才能访问它，否则我们将被重定向到欢迎页面。

```
<h1>You have access to this page, <%= current_user.username %></h1>
```

如果我们试图在没有登录的情况下访问`/authorized`，我们会被重定向回欢迎页面。我们已经成功地在应用程序中实现了身份验证/授权。我们不会在本指南中介绍注销功能，但我们的想法是清除 `sessions[:user_id]`并将用户重定向回欢迎页面。

请访问我的 github 库获取源代码:

 [## reireynoso/sessions _ controller _ ruby

### Rails 中 Auth 的简单设置。在…上创建帐户，为 reirey noso/sessions _ controller _ ruby 开发做出贡献

github.com](https://github.com/reireynoso/sessions_controller_ruby) 

感谢您的阅读。有关本指南中实施的资源的更多信息，请参考:

 [## codahale/bcrypt-ruby

### 保护用户密码安全的简单方法。如果您明文存储用户密码，那么窃取…

github.com](https://github.com/codahale/bcrypt-ruby/tree/master) [](https://m.patrikonrails.com/rails-5-1s-form-with-vs-old-form-helpers-3a5f72a8c78a) [## Rails 5.1 的 form _ with vs . form _ tag vs . form _ for

### form_tag 和 form_for 已不推荐使用，将来它们将被 form_with 替换。如果你想知道…

m.patrikonrails.com](https://m.patrikonrails.com/rails-5-1s-form-with-vs-old-form-helpers-3a5f72a8c78a) [](https://guides.rubyonrails.org/action_controller_overview.html) [## 动作控制器概述- Ruby on Rails 指南

### 动作控制器概述在本指南中，您将学习控制器如何工作，以及它们如何适应…

guides.rubyonrails.org](https://guides.rubyonrails.org/action_controller_overview.html)
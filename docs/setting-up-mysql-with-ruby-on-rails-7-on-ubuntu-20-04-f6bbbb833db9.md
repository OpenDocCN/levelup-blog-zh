# 在 Ubuntu 20.04 上用 Ruby on Rails 7 设置 MySQL

> 原文：<https://levelup.gitconnected.com/setting-up-mysql-with-ruby-on-rails-7-on-ubuntu-20-04-f6bbbb833db9>

![](img/4844325393ef9725400a6cd9ec829026.png)

**MySQL** 是最流行的关系数据库系统之一。与 Postgres 不同，这是一个纯粹的关系数据库，有时 RoR 开发者更喜欢使用它。

在本教程中，我将向您展示如何安装它，创建一个角色，并将数据库与 Ruby on Rails 应用程序链接起来。

我们开始吧！

# 第一步。安装 MySQL

```
sudo apt-get update
sudo apt-get install mysql-server mysql-client libmysqlclient-dev
```

`mysql-server`是一个通用数据库包。

`mysql-client`包含额外的实用程序和功能。

`libmysqlclient-dev`用于编译 C 程序与 MySQL 通信。

# 第二步。创建用户

下面的命令旨在设置一些比默认的 Ubuntu(和 MySQL)安装更安全的初始参数。

```
sudo mysql_secure_installation
```

对于最快的设置，只需一步一步地按 Enter 键。

之后进入`mysql`环境:

```
sudo mysql
```

要创建新角色，请使用您的用户名(替换为您的用户名)和密码输入以下命令:

```
CREATE USER '<your_username>'@'localhost' IDENTIFIED BY '<your_password>';
```

将所有权限授予该用户。

```
GRANT ALL PRIVILEGES ON *.* TO '<your_username>'@'localhost' WITH GRANT OPTION;
```

然后跑

```
exit
```

现在，尝试用新用户进入`mysql`环境(并输入密码):

```
mysql -u <your_username> -p
```

如果它打开得好，再次退出 mysql 环境。

```
exit
```

# 第三步。创建 Rails 应用程序

```
rails new app -d mysql
cd app
```

现在为 Rails 安装 **MySQL 适配器**。

```
gem install mysql2
```

为了存储秘密，我使用了[凭证](https://blog.saeloun.com/2019/10/10/rails-6-adds-support-for-multi-environment-credentials.html)。

```
EDITOR=nano rails credentials:edit
```

添加数据库用户名和密码。

```
database:
  username: <your_username>
  password: <your_password>
```

`Ctrl + X` **、** `Y`和`Enter`。

更新`database.yml`

创建和迁移数据库

```
rails db:create db:migrate
```

最后一步—启动服务器。

```
rails s
```

万岁！🎉希望本教程能帮助你用 Rails 设置 MySQL。在接下来的教程中再见。
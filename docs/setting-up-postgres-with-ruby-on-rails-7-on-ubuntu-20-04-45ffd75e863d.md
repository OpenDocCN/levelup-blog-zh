# 在 Ubuntu 20.04 上用 Ruby on Rails 设置 Postgres

> 原文：<https://levelup.gitconnected.com/setting-up-postgres-with-ruby-on-rails-7-on-ubuntu-20-04-45ffd75e863d>

![](img/0c9e61a6c9dc81bbea12131b0a5b783e.png)

Postgres 是在 Ruby on Rails 开发人员中广泛使用的最强大的数据库之一。使用这个数据库有很多好处，例如，更容易部署到 Heroku。

在本教程中，我将向您展示如何安装 Postgres、创建角色以及将数据库与 Ruby on Rails 应用程序链接起来。

我们开始吧！

# 第一步。安装 Postgres

```
sudo apt-get update
sudo apt install postgresql postgresql-contrib libpq-dev
```

`postgresql`是一个通用数据库包。

`postgresql-contrib`包含额外的实用程序和功能。

`libpq-dev`用于编译 C 程序与 Postgres 通信。

# 第二步。创建用户

安装过程创建了一个名为 **postgres** 的用户帐户，该帐户与默认 postgres 角色相关联。

让我们切换到 **postgres** 账户，进入**psql**——基于终端的 postgres 前端程序。

```
sudo -i -u postgres
psql
```

现在有了`postgres=#`环境。要创建新角色，请输入以下命令。

```
CREATE ROLE <your_username> LOGIN SUPERUSER PASSWORD '<your_password>';# CREATE ROLE
```

超级用户绕过所有权限检查。

列出所有用户。

```
\du
```

使用下面的命令退出 **psql** 和 **postgres** 环境。

```
exit
```

# 第三步。C **加工** a **轨道 App**

```
rails new app -d postgresql
cd app
bundle
```

为了存储秘密，我将使用凭证。

```
EDITOR=nano rails credentials:edit
```

添加数据库用户名和密码。

```
database:
  username: <your_username>
  password: <your_password>
```

`Ctrl + X`**`Y`和`Enter`。**

**更新`database.yml`**

**创建和迁移数据库**

```
rails db:create db:migrate
```

**并启动服务器。**

```
rails s
```

# **好处:从 SQLite 转换到 Postgres**

**有时，您需要从一个数据库切换到另一个数据库。为此，请遵循以下简单步骤。**

## **更新 Gemfile**

**从 Gemfile 中删除 SQLite gem。**

```
gem 'sqlite3'
```

**并添加 Postgres 宝石。**

```
gem 'pg'
```

**运行捆绑包。**

```
bundle
```

**按照上述说明设置凭证并更换`database.yml`。**

**如果您有迁移文件，请用`t.string`到`t.text`替换列。**

**最后一步是创建一个新的数据库。**

```
rails db:setup db:migrate
```

**万岁！🎉希望本教程能帮助你用 Rails 设置 Postgres。祝你好运。**
# 柴油:一种铁锈色的形式

> 原文：<https://levelup.gitconnected.com/diesel-a-rust-y-orm-e2c247e97835>

![](img/83da5660430046c5881875942480db37.png)

[上周](https://www.mmhaskell.com/blog/2020/7/13/basic-postgres-data-in-rust)周一早上 Haskell 我们用 Rust 迈出了进入一些真实世界任务的第一步。我们探索了简单的 Rust Postgres 库来连接数据库并运行一些查询。本周我们将使用 [Diesel](https://diesel.rs/guides/getting-started/) ，这是一个具有一些很酷的 ORM 功能的库。这有点像 Haskell 库 Persistent，您可以在我们的[真实世界 Haskell 系列](https://www.mmhaskell.com/real-world/databases)中探索更多。

要更深入地了解本文的代码，您应该看看我们的 [Github 库](https://github.com/MondayMorningHaskell/RustWeb)！您可能想看看下面引用的文件，以及这里的可执行文件。

# Diesel CLI

我们的第一步是在程序中添加 Diesel 作为依赖项。我们在上周的文章中简要讨论了货物的“特性”。对于您可能使用的每个后端，Diesel 都有单独的特性。所以我们将指定“postgres”。同样，我们还将为`chrono`库使用一个特殊的特性，这样我们就可以在数据库中使用时间戳。

```
[[dependencies]]
diesel={version="1.4.4", features=["postgres", "chrono"]}
```

但还有更多！Diesel 附带了一个 CLI 来帮助我们管理数据库迁移。它还会生成一些我们的模式代码。正如我们可以使用`stack install`用 Stack 安装二进制文件一样，我们也可以用 Cargo 安装二进制文件。我们只想指定我们想要的特性。否则，如果我们没有安装其他数据库，它就会崩溃。

```
>> cargo install diesel_cli --no-default-features --features postgres
```

现在我们可以开始使用这个程序来设置我们的项目，以生成我们的迁移。我们从这个命令开始。

```
>> diesel setup
```

这在我们的项目目录中创建了几个不同的项目。首先，我们有一个“migrations”文件夹，我们将在其中放置一些 SQL 代码。然后我们还在我们的`src`目录中得到一个`schema.rs`文件。Diesel 会在这个文件中自动为我们生成代码。让我们看看如何！

# 迁移和模式

在 Haskell 中使用 Persistent 时，我们使用特殊的模板语言在一个模式文件中定义了基本类型。我们可以通过编程在整个模式上运行迁移，而不需要我们自己的 SQL。但是随着模式的发展，很难跟踪更复杂的迁移。

柴油有点不一样。不幸的是，我们必须自己编写 SQL。但是，我们会以一种更容易在我们的桌面上采取更细粒度行动的方式来这样做。然后，Diesel 将为我们生成一个模式文件。但是我们仍然需要一些额外的工作来得到我们需要的铁锈类型。首先，让我们使用 Diesel 来生成我们的第一次迁移。这个迁移将创建我们的“用户”表。

```
>> diesel migration generate create_users
```

这将在我们的“migrations”目录中为“create_users”迁移创建一个新文件夹。它有两个文件，`up.sql`和`down.sql`。我们从填充`up.sql`文件开始，指定运行迁移所需的 SQL。

```
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  name TEXT NOT NULL,
  email TEXT NOT NULL,
  age INTEGER NOT NULL
)
```

然后，我们还希望`down.sql`文件包含逆转迁移的 SQL。

```
DROP TABLE users CASCADE;
```

一旦我们写了这些，我们就可以运行我们的迁移！

```
>> diesel migration run
```

然后我们可以撤销迁移，用这个命令运行`down.sql`中的代码:

```
>> diesel migration redo
```

运行我们的迁移的结果是 Diesel 填充了`schema.rs`文件。这个文件使用了`table`宏，它为我们生成了有用的类型和特征实例。当我们将表格合并到代码中时，我们会用到它。

```
table! {
  users (id) {
    id -> Int4,
    name -> Text,
    email -> Text,
    age -> Int4,
  }
}
```

现在，让我们再进行一次迁移，添加一个 articles 表。

```
-- migrations/create_articles/up.sql
CREATE TABLE articles (
  id SERIAL PRIMARY KEY,
  title TEXT NOT NULL,
  body TEXT NOT NULL,
  published_at TIMESTAMP WITH TIME ZONE NOT NULL,
  author_id INTEGER REFERENCES users(id) NOT NULL
)-- migrations/create_articles/down.sql
DROP TABLE articles;
```

然后我们可以再次使用`diesel migration run`。

# 模型类型

现在，虽然 Diesel 将为我们生成大量有用的代码，但我们仍然需要自己做一些工作。我们必须为数据类型创建自己的结构，以利用我们获得的实例。有了 Persistent，我们可以免费得到这些。Persistent 还使用了一个包装器`Entity`类型，它将一个`Key`附加到我们的实际数据上。

Diesel 没有实体的概念。我们必须手动创建两个不同的类型，一个有数据库密钥，一个没有。对于拥有密钥的“实体”类型，我们将派生出“可查询”类。然后我们可以使用 Diesel 的函数从表中选择项目。

```
#[derive(Queryable)]
pub struct UserEntity {
  pub id: i32
  pub name: String,
  pub email: String,
  pub age: i32
}
```

然后，我们必须声明一个实现“Insertable”的单独类型。这没有数据库密钥，因为我们在插入项目之前不知道密钥。这应该是我们的实体类型的副本，但是没有关键字段。我们使用第二个宏将它绑定到`users`表。

```
#[derive(Insertable)]
#[table_name="users"]
pub struct User {
  pub name: String,
  pub email: String,
  pub age: i32
}
```

注意，对于我们的外键类型，我们将使用一个普通的整数作为列引用。在持久化中，我们会有一个特殊的`Key`类型。这样做我们就失去了这个字段的一些语义意义。但是它可以帮助我们将更多的代码从这个特定的库中分离出来。

# 进行查询

现在我们已经有了模型，我们可以开始使用它们来编写查询。首先，我们需要使用`establish`函数建立一个数据库连接。在本文中，我们将使用`.expect`来展开我们的结果，而不是使用`?`语法。这不太安全，但是更容易操作。

```
fn create_connection() -> PgConnection {
  let database_url = "postgres://postgres:postgres@localhost/rust_db";
  PgConnection::establish(&database_url)
    .expect("Error Connecting to database")
}fn main() {
  let connection: PgConnection = create_connection();
  ...
}
```

现在让我们从插入开始。当然，我们首先创建一个“Insertable”`User`项目。然后我们可以开始用 Diesel 函数`insert_into`编写一个插入查询。

Diesel 的查询函数是可组合的。我们向查询中添加不同的元素，直到查询完成。对于插入，我们使用`values`结合我们想要插入的项目。然后，我们用我们的连接调用`get_result`。插入的结果是我们的“实体”类型。

```
fn create_user(conn: &PgConnection) -> UserEntity {
  let u = User
    { name = "James".to_string()
    , email: "james@test.com".to_string()
    , age: 26}; diesel::insert_into(users::table).values(&u)
    .get_result(conn).expect("Error creating user!")
}
```

# 选择项目

选择项目有点复杂。Diesel 为我们的每种类型生成一个`dsl`模块。这允许我们在“过滤器”和排序中使用每个字段名作为一个值。假设我们想获取某个用户写的所有文章。我们将在`articles`表上开始我们的查询，并调用`filter`开始构建我们的查询。然后我们可以在`author_id`字段上添加一个约束。

```
fn fetch_articles(conn: &PgConnection, uid: i32) -> Vec<ArticleEntity> {
  use rust_web::schema::articles::dsl::*;
  articles.filter(author_id.eq(uid))
  ...
```

我们还可以在查询中添加一个排序。再次注意这些函数是如何组成的。当使用`load`函数来完成我们的选择查询时，我们还必须指定我们想要的返回类型。主要情况是返回完整的实体。这就像 SQL 行话中的`SELECT * FROM`。应用`load`将会给我们一个这些项目的向量。

```
fn fetch_articles(conn: &PgConnection, uid: i32) -> Vec<ArticleEntity> {
  use rust_web::schema::articles::dsl::*;
  articles.filter(author_id.eq(uid))
    .order(title)
    .load::<ArticleEntity>(conn)
    .expect("Error loading articles!")
}
```

但是我们也可以指定我们想要返回的特定字段。我们将在最后一个例子中看到这一点，其中我们的结果类型是一个元组向量。最后一个查询将是我们两个表之间的连接。我们从`users`开始，应用`inner_join`功能。

```
fn fetch_all_names_and_titles(conn: &PgConnection) -> Vec<(String, String)> {
  use rust_web::schema::users::dsl::*;
  use rust_web::schema::articles::dsl::*;
  users.inner_join(...
}
```

然后，我们将它加入到特定 ID 字段的 articles 表中。因为我们的两个表都有`id`字段，所以我们必须命名它来指定用户的 ID 字段。

```
fn fetch_all_names_and_titles(conn: &PgConnection) -> Vec<(String, String)> {
  use rust_web::schema::users::dsl::*;
  use rust_web::schema::articles::dsl::*; users.inner_join(
    articles.on(author_id.eq(rust_web::schema::users::dsl::id)))...
}
```

最后，我们通过查询得到结果。但是注意，我们使用`select`并且只要求用户的`name`和文章的`title`。这给了我们最终的值，所以每个元素都是两个字符串的元组。

```
fn fetch_all_names_and_titles(conn: &PgConnection) -> Vec<(String, String)> {
  use rust_web::schema::users::dsl::*;
  use rust_web::schema::articles::dsl::*; users.inner_join(
    articles.on(author_id.eq(rust_web::schema::users::dsl::id)))
    .select((name, title)).load(conn).expect("Error on join query!")
}
```

# 结论

对我来说，我更喜欢 Haskell 中 Persistent 提供的功能。但是 Diesel 提供一个单独的 CLI 来处理迁移的方法也非常酷。很高兴在这种相对较新的语言中看到更复杂的功能。

如果你还不熟悉 Rust，我们还有一些初学者相关的资料。阅读我们的 [Rust 初学者系列](https://www.mmhaskell.com/rust)或者更好，观看我们的 [Rust 视频教程](https://www.mmhaskell.com/rust-video-tutorial)！
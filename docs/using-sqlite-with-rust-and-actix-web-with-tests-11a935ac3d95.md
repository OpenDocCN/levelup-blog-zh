# 在 Rust 和 Actix Web 上使用 SQLite(带测试)

> 原文：<https://levelup.gitconnected.com/using-sqlite-with-rust-and-actix-web-with-tests-11a935ac3d95>

![](img/bb534bd1d894a8a360bd2ed0eff3d496.png)

在本文中，我们将探讨如何在文件和内存模式下将 SQLite(与 Diesel 结合)与 Rust 一起使用。

# 先决条件

除了安装 Rust & Diesel CLI 之外，还要为您的平台安装 SQLite:

```
# Linux
$ sudo apt install sqlite3 libsqlite3-0 libsqlite3-dev# OSX
$ brew install sqlite3
```

# 创建项目([提交](https://github.com/ukchukx/diesel-sqlite/commit/bd1f5209c522c9df1540cc1806821a4268870e7d))

```
$ cargo new diesel-sqlite
$ cd diesel-sqlite
```

添加以下依赖项:

*   柴油机提供 ORM 能力。
*   我们的 web 层。
*   Dotenv 用于处理环境变量。
*   Uuid 来生成 id。

```
[dependencies]
actix-rt = "1.0"
actix-web = "2.0"
chrono = { version = "0.4.11", features = ["serde"] }
diesel = { version = "1.4.4", features = ["sqlite", "uuidv07", "chrono"] }
dotenv = "0.15.0"
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
uuid = { version = "0.8", features = ["serde", "v4"] }
```

接下来，创建一个带有`DATABASE_URL`的`.env`文件:

```
DATABASE_URL=users.db
```

# 初始化 Diesel 并创建迁移脚本([提交](https://github.com/ukchukx/diesel-sqlite/commit/4c06113bb82a3ac66910f430e4274a154c3c02d2))

```
$ diesel setup
$ diesel migration generate create_users
```

将以下 SQL 语句添加到生成的向上和向下迁移文件中。

```
-- migrations/xxxx_create_users/up.sqlCREATE TABLE IF NOT EXISTS users (
  id CHARACTER(36) NOT NULL PRIMARY KEY,
  email VARCHAR(60),
  phone VARCHAR(20),
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP NOT NULL,
  updated_at DATETIME DEFAULT CURRENT_TIMESTAMP NOT NULL
);CREATE TRIGGER IF NOT EXISTS UpdateTimestamps AFTER UPDATE ON users
  FOR EACH ROW WHEN NEW.updated_at <= OLD.updated_at 
BEGIN 
  update users set updated_at=CURRENT_TIMESTAMP where id=OLD.id;  
END;-- migrations/xxxx_create_users/down.sqlDROP TRIGGER IF EXISTS UpdateTimestamps;DROP TABLE IF EXISTS users;
```

通过运行以下命令创建`users`表:

```
$ diesel migration run
```

运行之后，Diesel 应该会在`src/schema.rs`中为您创建一个模式。

让我们重新组织我们的数据库文件:

*   在`src`目录下创建一个`db`目录。
*   将`src/schema.rs`移动到`db`目录中。
*   将`diesel.toml`中的`file`变量从`src/schema.rs`更新为`src/db/schema.rs`。
*   在`db`目录下创建一个文件`models.rs`。

之后，使用以下内容创建一个文件`src/db.rs`:

```
// src/db.rs
pub mod models;
pub mod schema;
```

# 创建模型([提交](https://github.com/ukchukx/diesel-sqlite/commit/537b998b7ce67197f07ab1cd6cc82b55d170d63a))

首先，我们将嵌入我们的迁移，因为我们希望在测试中使用 SQLite 的内存模式。另一个好处是，我们的迁移被编译到我们的应用程序中，创建一个可执行文件，消除对文件系统的依赖。

将`diesel_migrations`添加到我们的依赖关系中:

```
# Cargo.toml[dependencies]
# ...
diesel_migrations = "1.4.0"
```

将这些添加到`main.rs`的顶部:

```
// src/main.rs
#[macro_use]
extern crate diesel;
#[macro_use]
extern crate diesel_migrations; mod db;
// ...
```

在`src/db.rs`中，粘贴:

```
// src/db.rs// ...
embed_migrations!();pub fn establish_connection() -> SqliteConnection {
    if cfg!(test) {
        let conn = SqliteConnection::establish(":memory:")
          .unwrap_or_else(|_| panic!("Error creating test database"));

        let _result = diesel_migrations::run_pending_migrations(&conn); conn
    } else {
        dotenv().ok();

        let database_url = env::var("DATABASE_URL").expect("DATABASE_URL must be set");

        SqliteConnection::establish(&database_url)
          .unwrap_or_else(|_| panic!("Error connecting to {}", database_url))
    }
}
```

我们利用 Rust 在`establish_connection()`中的条件编译功能，为测试返回一个内存连接，为正常运行返回一个文件连接。

将此粘贴到`src/db/models.rs`:

```
use uuid::Uuid;
use serde::{Deserialize, Serialize};
use diesel::prelude::*;use super::schema::users;
use super::schema::users::dsl::users as user_dsl; #[derive(Debug, Deserialize, Serialize, Queryable, Insertable)]
#[table_name = "users"]
pub struct User {
    pub id: String,
    pub email: Option<String>,
    pub phone: Option<String>,
    pub created_at: chrono::NaiveDateTime,
    pub updated_at: chrono::NaiveDateTime,
}impl User {
    pub fn list(conn: &SqliteConnection) -> Vec<Self> {
        user_dsl.load::<User>(conn).expect("Error loading users")
    } pub fn by_id(id: &str, conn: &SqliteConnection) -> Option<Self> {
        if let Ok(record) = user_dsl.find(id).get_result::<User>(conn) {
            Some(record)
        } else {
            None
        }
    } pub fn by_email(email_str: &str, conn: &SqliteConnection) -> Option<Self> {
        use super::schema::users::dsl::email; if let Ok(record) = user_dsl.filter(email.eq(email_str)).first::<User>(conn) {
            Some(record)
        } else {
            None
        }
    } pub fn by_phone(phone_str: &str, conn: &SqliteConnection) -> Option<Self> {
        use super::schema::users::dsl::phone; if let Ok(record) = user_dsl.filter(phone.eq(phone_str)).first::<User>(conn) {
            Some(record)
        } else {
            None
        }
    } pub fn create(email: Option<&str>, phone: Option<&str>, conn: &SqliteConnection) -> Option<Self> {
        let new_id = Uuid::new_v4().to_hyphenated().to_string();

        if email.is_none() && phone.is_none() {
            return None
        } 

        if phone.is_some() {
            if let Some(user) = Self::by_phone(&phone.unwrap(), conn) {
                return Some(user)
            } 
        }

        if email.is_some() {
            if let Some(user) = Self::by_email(&email.unwrap(), conn) {
                return Some(user)
            } 
        } let new_user = Self::new_user_struct(&new_id, phone, email); diesel::insert_into(user_dsl)
            .values(&new_user)
            .execute(conn)
            .expect("Error saving new user"); Self::by_id(&new_id, conn)
    } fn new_user_struct(id: &str, phone: Option<&str>, email: Option<&str>) -> Self {
        User {
            id: id.into(),
            email: email.map(Into::into),
            phone: phone.map(Into::into),
            created_at: chrono::Local::now().naive_local(),
            updated_at: chrono::Local::now().naive_local(),
        }
    }
} #[cfg(test)]
mod user_test;
```

首先，我们声明 import 并声明我们的`User`结构。
我们的每个`User`方法都带有一个`SqliteConnection`参数，所以我们可以传递任何我们想要的连接(测试或其他),而不必改变方法。
对于查询方法，Diesel 返回一个`Result`，我们用[将它转换成一个`Option`。ok()](https://doc.rust-lang.org/std/result/enum.Result.html#method.ok) 来符合我们的返回类型。

在`create()`中，我们首先确保在创建新的`User`记录之前，数据库中不存在提供的电子邮件和/或电话。

在文件的底部，我们声明按照惯例，测试是在位于`src/db/models/user_test.rs`的文件中声明的。

将这些测试粘贴到文件中:

```
use crate::db::{establish_connection, models::User};#[test]
fn create_user_with_phone_and_email() {
    let conn = establish_connection();
    let email = Some("test@email.com");
    let phone = Some("123456789"); let user = User::create(email, phone, &conn).unwrap(); assert_eq!(user.email.unwrap().as_str(), email.unwrap());
    assert_eq!(user.phone.unwrap().as_str(), phone.unwrap());
}#[test]
fn create_user_with_phone_only() {
    let conn = establish_connection();
    let email = None;
    let phone = Some("123456789"); let user = User::create(email, phone, &conn).unwrap(); assert!(user.email.is_none());
    assert_eq!(user.phone.unwrap().as_str(), phone.unwrap());
}#[test]
fn create_user_with_email_only() {
    let conn = establish_connection();
    let email = Some("test@email.com");
    let phone = None; let user = User::create(email, phone, &conn).unwrap(); assert_eq!(user.email.unwrap().as_str(), email.unwrap());
    assert!(user.phone.is_none());
}#[test]
fn create_user_with_existing_email() {
    let conn = establish_connection();
    let email = Some("test@email.com");
    let phone = None; let user = User::create(email, phone, &conn).unwrap();
    let existing_user = User::create(email, phone, &conn).unwrap(); assert_eq!(user.id, existing_user.id);
}#[test]
fn create_user_with_existing_phone() {
    let conn = establish_connection();
    let email = None;
    let phone = Some("123456789"); let user = User::create(email, phone, &conn).unwrap();
    let existing_user = User::create(email, phone, &conn).unwrap(); assert_eq!(user.id, existing_user.id);
}#[test]
fn list_users() {
    let conn = establish_connection();
    let email = None;
    let phone = Some("123456789"); let user = User::create(email, phone, &conn).unwrap();
    let existing_users = User::list(&conn); assert_eq!(1, existing_users.len());
    assert_eq!(user.id, existing_users[0].id);
}#[test]
fn get_user_by_phone() {
    let conn = establish_connection();
    let email = None;
    let phone = Some("123456789"); let user = User::create(email, phone, &conn).unwrap();
    let existing_user = User::by_phone(&phone.unwrap(), &conn).unwrap(); assert_eq!(user.id, existing_user.id);
}#[test]
fn get_user_by_email() {
    let conn = establish_connection();
    let email = Some("test@email.com");
    let phone = None; let user = User::create(email, phone, &conn).unwrap();
    let existing_user = User::by_email(&email.unwrap(), &conn).unwrap(); assert_eq!(user.id, existing_user.id);
}#[test]
fn get_user_by_id() {
    let conn = establish_connection();
    let email = Some("test@email.com");
    let phone = Some("123456789"); let user = User::create(email, phone, &conn).unwrap();
    let existing_user = User::by_id(&user.id, &conn).unwrap(); assert_eq!(user.id, existing_user.id);
}
```

# 添加一个 web 服务([提交](https://github.com/ukchukx/diesel-sqlite/commit/a987f138b2e9b74d818d09fafe28c7bb76e9357c))

更新我们的`actix-web`依赖项，提供一些 2.0 中没有的测试工具，并添加`r2d2`来帮助连接池:

```
[dependencies]
# ...
actix-web = "3.0.0-alpha.1"
diesel = { version = "1.4.4", features = ["sqlite", "uuidv07", "r2d2", "chrono"] }
r2d2 = "0.8.8"
r2d2-diesel = "1.0.0"
```

让我们重构`src/db.rs`来使用连接池:

```
// src/db.rs
// ...
use diesel::sqlite::SqliteConnection;
use r2d2_diesel::ConnectionManager;
use r2d2::Pool; embed_migrations!(); pub type DbPool = Pool<ConnectionManager<SqliteConnection>>; pub fn run_migrations(conn: &SqliteConnection) {
  let _ = diesel_migrations::run_pending_migrations(&*conn);
}pub fn establish_connection() -> DbPool {
    if cfg!(test) {
        let manager = ConnectionManager::<SqliteConnection>::new(":memory:");
        let pool = r2d2::Pool::builder().build(manager).expect("Failed to create DB pool.");

        run_migrations(&pool.get().unwrap()); pool
    } else {
        dotenv().ok();

        let database_url = env::var("DATABASE_URL").expect("DATABASE_URL must be set");
        let manager = ConnectionManager::<SqliteConnection>::new(&database_url);

        r2d2::Pool::builder().build(manager).expect("Failed to create DB pool.")
    }
}
```

我们还将创建测试连接池与运行迁移分开。

在我们的每个模型测试中，我们改变了获取联系的方式

```
let conn = establish_connection();
```

到

```
let conn = establish_connection().get().unwrap();
```

创建一个服务模块`src/services.rs`并粘贴:

```
// src/services.rs
pub mod user;#[cfg(test)]
mod user_test;
```

然后我们创建用户服务文件`src/services/user.rs`并粘贴:

```
// src/services/user.rs
use actix_web::{HttpResponse, web};
use serde::{Serialize, Deserialize};use crate::db::{DbPool, models::User};#[derive(Serialize, Deserialize)]
pub struct UserForm {
    email: Option<String>,
    phone: Option<String>,
}pub fn create(user_form: web::Json<UserForm>, pool: web::Data<DbPool>) -> HttpResponse {
    let conn = pool.get().unwrap(); match User::create(user_form.email.as_deref(), user_form.phone.as_deref(), &conn) {
        Some(user) => HttpResponse::Ok().json(user),
        _ => HttpResponse::InternalServerError().json("Could not create user")
    }
}pub fn index(pool: web::Data<DbPool>) -> HttpResponse {
    let conn = pool.get().unwrap(); HttpResponse::Ok().json(User::list(&conn))
}pub fn get(id: web::Path<String>, pool: web::Data<DbPool>) -> HttpResponse {
    let conn = pool.get().unwrap(); match User::by_id(&id, &conn) {
        Some(user) => HttpResponse::Ok().json(user),
        _ => HttpResponse::NotFound().json("Not Found")
    }
}pub fn init_routes(cfg: &mut web::ServiceConfig) {
    /* 
     * index: curl -i -X GET -H "Content-Type: application/json" http://localhost:5000/users
     * get: curl -i -X GET -H "Content-Type: application/json" http://localhost:5000/users/<id>
     * post: curl -i -X POST -H "Content-Type: application/json" -d '{"email":"xxx", "phone": "yyy"}' http://localhost:5000/users
     */

    cfg.service(
        web::resource("/users")
            .route(web::post().to(create))
            .route(web::get().to(index))
    )
    .service(
        web::scope("/users")
            .route("/{id}", web::get().to(get)),
    );
}
```

`create()`、`index()`和`get()`通过 id 处理用户的创建、列表和获取。
`init_routes()`将我们的路线添加到网络服务器。

然后我们更新`src/main.rs`来启动我们的 web 服务器:

```
// src/main.rs// ...
#[macro_use]
extern crate serde_json;
extern crate r2d2_diesel; // ...
mod services; #[actix_rt::main]
async fn main() -> std::io::Result<()> {
    use actix_web::{App, HttpServer, web::JsonConfig}; let conn_pool = db::establish_connection(); HttpServer::new(move || {
        App::new()
            .data(conn_pool.clone())
            .data(JsonConfig::default().limit(4096))
            .configure(services::user::init_routes)
    })
    .bind("0.0.0.0:5000")?
    .run()
    .await
}
```

有了这个更新，我们的服务就可以接收和处理请求了。

让我们添加测试，这样我们就可以有一些保证，我们的服务做了它的帐单。
在`src/services/user_test.rs`中，粘贴:

```
// src/services/user_test.rs
use actix_web::{
    App,
    test::{read_body_json, read_body, init_service, TestRequest}
};use crate::{db::{models::User, establish_connection}, services::user::init_routes};#[actix_rt::test]
async fn create_user_from_api() {
    let test_email   = "test@email.com";
    let test_phone   = "123456789";
    let request_body = json!({ "email": test_email, "phone": test_phone });
    let conn_pool    = establish_connection();
    let mut app      = init_service(App::new().data(conn_pool.clone()).configure(init_routes)).await; let resp = TestRequest::post()
      .uri("/users")
      .set_json(&request_body)
      .send_request(&mut app)
      .await; assert!(resp.status().is_success(), "Failed to create user"); let user: User = read_body_json(resp).await; assert_eq!(user.email.unwrap(), test_email);
    assert_eq!(user.phone.unwrap(), test_phone);
}#[actix_rt::test]
async fn get_user_from_api_by_id() {
    let test_email   = "test@email.com";
    let test_phone   = "123456789";
    let request_body = json!({ "email": test_email, "phone": test_phone });
    let conn_pool    = establish_connection();
    let mut app      = init_service(App::new().data(conn_pool.clone()).configure(init_routes)).await; let create_resp = TestRequest::post()
      .uri("/users")
      .set_json(&request_body)
      .send_request(&mut app)
      .await; assert!(create_resp.status().is_success(), "Failed to create user"); let created_user: User = read_body_json(create_resp).await;
    println!("/users/{}", created_user.id);

    let resp = TestRequest::get()
      .uri(format!("/users/{}", created_user.id).as_str())
      .send_request(&mut app)
      .await; assert!(resp.status().is_success(), "Failed to get user"); let retrieved_user: User = read_body_json(resp).await; assert_eq!(created_user.id, retrieved_user.id);
}#[actix_rt::test]
async fn list_users_from_api() {
    let test_email   = "test@email.com";
    let test_phone   = "123456789";
    let request_body = json!({ "email": test_email, "phone": test_phone });
    let conn_pool    = establish_connection();
    let mut app      = init_service(App::new().data(conn_pool.clone()).configure(init_routes)).await; let mut list_resp = TestRequest::get().uri("/users").send_request(&mut app).await;

    assert!(list_resp.status().is_success(), "Failed to list users"); let mut body = read_body(list_resp).await;  
    let mut retrieved_users: Vec<User> = serde_json::from_slice::<Vec<User>>(&body).unwrap(); assert_eq!(retrieved_users.len(), 0); let create_resp = TestRequest::post()
      .uri("/users")
      .set_json(&request_body)
      .send_request(&mut app)
      .await; assert!(create_resp.status().is_success(), "Failed to create user");

    list_resp = TestRequest::get().uri("/users").send_request(&mut app).await; assert!(list_resp.status().is_success(), "Failed to list users"); body = read_body(list_resp).await;    
    retrieved_users = serde_json::from_slice::<Vec<User>>(&body).unwrap(); assert_eq!(retrieved_users.len(), 1);
}
```

完整的代码可以在这里找到[。](https://github.com/ukchukx/diesel-sqlite)

# 信用

马库斯·温克勒在 Unsplash 上拍摄的照片。
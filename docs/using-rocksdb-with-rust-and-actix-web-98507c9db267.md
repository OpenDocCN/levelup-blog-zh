# 在 Rust 和 Actix-Web 上使用 RocksDB

> 原文：<https://levelup.gitconnected.com/using-rocksdb-with-rust-and-actix-web-98507c9db267>

![](img/b83720a511045c9ac2dce7522d21bf54.png)

照片由[游里·罗默](https://unsplash.com/@joeriromer?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

为了帮助我学习 Rust，我决定在 Rust 中实现 [Java RocksDB 示例](https://medium.com/@ukchukx/99cb1c43a834),因为它很简单。

由于 Rust 严格的借用和生命周期概念——几乎每个人迟早都会犯错——事情没有我希望的那么简单。本来想直接存储用 [bincode](https://crates.io/crates/bincode) 序列化的`json::value::JsonValue`enum。我遇到的主要错误是:

```
the trait `serde::ser::Serialize` is not implemented for `json::value::JsonValue`
...
the trait `serde::de::Deserialize<'_>` is not implemented for `json::value::JsonValue`
```

我确信，如果我努力的话，我本可以实现那个枚举的特征，但是鉴于这应该是一个简单的一次性例子，它很快变得超出了我的预期。我决定在存储库层处理字符串，把解序列化留给用户，因为我很懒。\_(ツ)_/

我们只需要 json、actix-web、bytes(读取 POST 主体)和 rocksdb 作为依赖项:

```
[dependencies]
actix-rt = "1.0"
actix-web = "2.0"
bytes = "0.5.2"
env_logger = "0.7.1"
json = "0.12"
rocksdb = "0.13.0"
```

然后我们创建我们的存储库模块`src/kv.rs`:

```
// src/kv.rs
use rocksdb::DB;
use std::sync::Arc;pub trait KVStore {
    fn init(file_path: &str) -> Self;
    fn save(&self, k: &str, v: &str) -> bool;
    fn find(&self, k: &str) -> Option<String>;
    fn delete(&self, k: &str) -> bool;
}#[derive(Clone)]
pub struct RocksDB {
    db: Arc<DB>,
} impl KVStore for RocksDB {
    fn init(file_path: &str) -> Self {
        RocksDB { db: Arc::new(DB::open_default(file_path).unwrap()) }
    } fn save(&self, k: &str, v: &str) -> bool {
        self.db.put(k.as_bytes(), v.as_bytes()).is_ok()
    } fn find(&self, k: &str) -> Option<String> {
        match self.db.get(k.as_bytes()) {
            Ok(Some(v)) => {
                let result = String::from_utf8(v).unwrap();
                println!("Finding '{}' returns '{}'", k, result);
                Some(result)
            },
            Ok(None) => {
                println!("Finding '{}' returns None", k);
                None
            },
            Err(e) => {
                println!("Error retrieving value for {}: {}", k, e);
                None
            }
        }
    } fn delete(&self, k: &str) -> bool {
        self.db.delete(k.as_bytes()).is_ok()
    }
}
```

我们声明我们的键值存储库接口(或者 Rust 中的特征)`KVStore`。`RocksDB`结构用于保存我们将要使用的打开的数据库。我们将`DB`包装在`Arc`中，这样它就可以跨线程安全地共享。我们希望 Actix-web 在路由处理程序被调用时将我们的数据库句柄传递给它们，但是`DB`没有实现`Clone`，所以必须包装在`Arc`中才能工作。

然后，我们继续为 RocksDB 实现我们的 trait(或接口)。RocksDB 只处理字节，所以我们必须将字符串键和值转换为字节。

接下来，我们将编写我们的路由处理器模块`src/kv_handler.rs`:

```
// src/kv_handler.rs
use actix_web::{web::{Data, Path}, HttpResponse};
use bytes::Bytes;
use json::parse;use crate::kv::{KVStore, RocksDB};// curl -i -X GET -H "Content-Type: application/json" http://localhost:8080/api/foo
pub async fn get(key: Path<String>, db: Data<RocksDB>) -> HttpResponse {
    match &db.find(&key.into_inner()) {
        Some(v) => {
            parse(v)
                .map(|obj| HttpResponse::Ok().content_type("application/json").body(obj.dump()))
                .unwrap_or(HttpResponse::InternalServerError().content_type("application/json").finish())
        }
        None    => HttpResponse::NotFound().content_type("application/json").finish()
    }
}// curl -i -X POST -H "Content-Type: application/json" -d '{"bar":"baz"}' http://localhost:8080/api/foo
pub async fn post(key:  Path<String>,
                  db:   Data<RocksDB>,
                  body: Bytes) -> HttpResponse {
    match String::from_utf8(body.to_vec()) {
        Ok(body) => match &db.save(&key.into_inner(), &body) {
            true  => {
                parse(&body)
                    .map(|obj| HttpResponse::Ok().content_type("application/json").body(obj.dump()))
                    .unwrap_or(HttpResponse::InternalServerError().content_type("application/json").finish())
            }
            false => HttpResponse::InternalServerError().content_type("application/json").finish()
        }
        Err(_) => HttpResponse::InternalServerError().content_type("application/json").finish(),
    }
}// curl -i -X DELETE -H "Content-Type: application/json" http://localhost:8080/api/foo
pub async fn delete(key: Path<String>, db: Data<RocksDB>) -> HttpResponse {
    match &db.delete(&key.into_inner()) {
        true  => HttpResponse::NoContent().content_type("application/json").finish(),
        false => HttpResponse::InternalServerError().content_type("application/json").finish()
    }
}
```

每条路由都带有`key`路径参数和我们之前打开的数据库句柄的句柄。

当我们收到 GET 请求时，我们用提供的`key`查询 RocksDB。如果返回一个值，我们将其解析为 JSON 并返回。如果一无所获，我们返回 404。

当我们收到 POST 请求时，我们将提供的主体转换为字符串并存储在 RocksDB 中。然后，我们将主体解析为 JSON，并作为响应发送回来。

对于删除请求，如果能够删除键值对，我们返回 204，否则返回 500。

剩下的工作就是初始化我们的数据库并注册我们的路由处理程序。让我们在`src/main.rs`中处理它:

```
// src/main.rs
mod kv;
mod kv_handler; #[actix_rt::main]
async fn main() -> std::io::Result<()> {
    use actix_web::{
        middleware::Logger,
        web::{scope, resource, get, post, delete},
        App,
        HttpServer
    }; let db: kv::RocksDB = kv::KVStore::init("/tmp/rocks/actix-db"); std::env::set_var("RUST_LOG", "actix_web=info,actix_server=info");
    env_logger::init(); HttpServer::new(move || {
        App::new()
            .data(db.clone())
            .wrap(Logger::default())
            .service(
                scope("/api")
                .service(
                    resource("/{key}")
                        .route(get().to(kv_handler::get))
                        .route(post().to(kv_handler::post))
                        .route(delete().to(kv_handler::delete)),
                ),
            )
    })
    .bind("0.0.0.0:8080")?
    .run()
    .await
}
```

源代码可以在[这里](https://github.com/ukchukx/rocksdb-rust)找到。
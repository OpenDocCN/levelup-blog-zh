# GraphQL 和 Cap'n Proto 的防锈微服务

> 原文：<https://levelup.gitconnected.com/microservices-in-rust-with-graphql-and-capn-proto-e125bec23eae>

![](img/dc4d9f7dca82e995e8a99ec6705c9908.png)

# 概观

我发现这个话题很有趣，因为我目前正在从事一个个人物联网项目，我一直在寻找能够提供最佳性能的技术。Rust 吸引了我的注意力，因为它在过去的几年里越来越受欢迎。在我看来，我们可以把它看作是 C++的改进版本，因为它有更好的内存管理和安全性。此外，这是一种命令式编程语言，不像 Java、Python、Ruby 等是面向对象的。我们也可以将它与 Go 进行比较，因为它也具有功能性风格，但我更喜欢 Rust，因为它的安全性和性能。

我也是一名 Go 程序员，我认为它在并发性方面非常出色，毫无疑问，Go 的创造者在设计和工程方面做了出色的工作。由于这个事实，与其他语言相比，我们可以很容易地使用 go 例程和通道来处理并发性。也许这是 Rust 的一个缺点，因为很难使用任何像 tokio 或 async-std 这样的异步机箱来处理线程。你可以查看一个 echo 服务器示例来清楚地理解我想说的话[https://github.com/seguidor777/tls_echo](https://github.com/seguidor777/tls_echo)

另一方面，我选择 GraphQL 是因为它吸引了我的注意力，而且我更喜欢它而不是 REST json。我给大家分享一个简短的故事:

脸书在 2015 年创建了 GraphQL，此后一直受到主流开发者的关注。GraphQL 不仅减少了噪音，还允许用户使用应用程序时有更好的界面。自从 GraphQL 降低了它所有的复杂性后，它就被弃用了。

最后，我们为微服务之间的服务间通信提供了 Cap'n proto。可以把 Capnp 想象成一个 RPC 框架，非常类似于 gRPC，但是要快得多，因为它不需要对消息进行编码/解码。我就不多说了，不过你可以看看它的网页【https://capnproto.org/。

# 假设

我假设你已经知道什么是微服务架构，我们不需要深入细节。

# 计算机设置

*   安装铁锈 v 1.47+【https://www.rust-lang.org/tools/install 。
*   安装 Cap'n 原型工具 v 0 . 8 . 0+[https://capnproto.org/install.html](https://capnproto.org/install.html)。

# 创建微服务

这个演示应用程序仅用于与模拟的 starwars 数据库交互，由两个微服务组成:

**元数据:**执行查询以在数据库中查找或创建一个人。

**API 网关:**分派外部 GraphQL 请求，并将它们重定向到元数据服务。

**注意:**我们没有任何外部依赖。但是如果你愿意，你可以试试蟑螂和 sqlx 箱子。

# 元数据服务

我们已经准备好开始编码元数据服务了。让我们用命令 **cargo new metadata** 创建一个新项目

我们的 **Cargo.toml** 文件应该如下所示(版本可能不同):

```
[package]
name = "metadata"
version = "0.1.0"
authors = ["Jorge Luna <[j](mailto:seguidor777@gmail.com)orge.luna@digitalonus.com>"]
edition = "2018"[dependencies]
capnp = "0.14.0"
capnp-rpc = "0.14.0"
futures = "0.3.0"
async-std = { version = "1.8.0", features = ["unstable"] }[build-dependencies]
capnpc = "0.14.1"
```

然后我们需要定义 Capnp 模式和生成新 Rust 模块的构建脚本。

**capnp/starwars.capnp**

```
@0xc1da187e3c0d97cd;

interface StarWars {
    struct Human {
      id @0 :Text;
      name @1 :Text;
      homePlanet @2 :Text;
      appearsIn @3 :AppearsIn;

      enum AppearsIn {
        newHope @0;
        empire @1;
        jedi @2;
      }
    }

    showHuman @0 (id: Text) -> Human;
    createHuman @1 Human -> Human;
}
```

**build.rs**

```
fn main() {
    ::capnpc::CompilerCommand::*new*()
        .src_prefix("capnp")
        .file("capnp/starwars.capnp")
        .run().expect("failed to compile schema");
}
```

然后我们需要编辑 **main.rs** 文件如下。

```
mod rpc;
pub mod starwars_capnp {
    include!(concat!(env!("OUT_DIR"), "/starwars_capnp.rs"));
}

fn main() {
    rpc::server::run().expect("cannot run server")
}
```

还要为 RPC 服务器创建一个新模块，我们需要这些文件:

**src/rpc/mod.rs**

```
pub mod server;
```

**src/rpc/server.rs**

```
use crate::starwars_capnp;
use capnp::capability::Promise;
use capnp_rpc::{pry, RpcSystem};
use capnp_rpc::twoparty::{VatNetwork};
use capnp_rpc::rpc_twoparty_capnp::{Side};
use futures::{AsyncReadExt, FutureExt};
use futures::task::LocalSpawnExt;

struct StarWars;

impl starwars_capnp::star_wars::Server for StarWars {
    fn show_human(&mut self, params: starwars_capnp::star_wars::ShowHumanParams, mut results: starwars_capnp::star_wars::ShowHumanResults)
        -> Promise<(), capnp::Error> {
        // get a reader object for the sent request
        let request_reader = pry!(params.get());
        // get the send ID
        let _id = request_reader.get_id();

        // set return values
        results.get().set_name("Luke");
        results.get().set_appears_in(starwars_capnp::star_wars::human::AppearsIn::NewHope);
        results.get().set_home_planet("Mars");
        Promise::*ok*(())
    }

    fn create_human(&mut self, params: starwars_capnp::star_wars::CreateHumanParams, mut results: starwars_capnp::star_wars::CreateHumanResults)
        -> Promise<(), capnp::Error> {
        // get a reader object for the sent request
        let request_reader = pry!(params.get());

        // set return values
        results.get().set_id("1234");
        results.get().set_name(request_reader.get_name().unwrap());
        results.get().set_appears_in(request_reader.get_appears_in().unwrap());
        results.get().set_home_planet(request_reader.get_home_planet().unwrap());
        Promise::*ok*(())
    }
}

pub fn run() -> Result<(), Box<dyn std::error::Error>> {
    let addr = "127.0.0.1:8001";
    let mut exec = futures::executor::LocalPool::*new*();
    let spawner = exec.spawner();

    exec.run_until(async move {
        let listener = async_std::net::TcpListener::*bind*(&addr).await.unwrap();

        println!("Metadata: tcp://{}", addr);

        let client: starwars_capnp::star_wars::Client = capnp_rpc::new_client(StarWars);

        loop {
            let (stream, _) = listener.accept().await.unwrap();

            stream.set_nodelay(true).unwrap();

            let (reader, writer) = stream.split();
            let network = VatNetwork::*new*(
                reader,
                writer,
                Side::*Server*,
                Default::*default*(),
            );

            let rpc_system =
                RpcSystem::*new*(Box::*new*(network), *Some*(client.clone().client));

            spawner.spawn_local(Box::*pin*(rpc_system.map(|_|())))?;
        }
    })
}
```

我们刚刚定义了用于服务间通信的 RPC 方法。

一旦你有了所有的文件，安装依赖项并使用 **cargo run** 运行项目

# **API 网关服务**

让我们通过使用 **cargo new api-gateway** 创建另一个项目来创建 API gateway 服务

对于这个项目，我们将重用相同的 **capnp/starwars.capnp** 和 **build.rs** 文件，因为我们实际上共享相同的 RPC 模式。

将此依赖关系添加到 **Cargo.toml** 文件中:

```
... Omitted for brevity

[dependencies]
actix-web = "3"
actix-cors = "0.4.0"
actix-rt = "1.1.0"
juniper_warp = "0.6.0"
serde = "1.0.103"
serde_json = "1.0.44"
serde_derive = "1.0.103"
juniper = "0.14.2"
capnp = "0.14.0"
capnp-rpc = "0.14.0"
futures = "0.3.0"
async-std = { version = "1.8.0", features = ["unstable"] }

[build-dependencies]
capnpc = "0.14.1"
```

并编辑 **main.rs** 文件如下:

```
mod graphql;
mod rpc;
pub mod starwars_capnp {
    include!(concat!(env!("OUT_DIR"), "/starwars_capnp.rs"));
}

use actix_cors::Cors;
use actix_web::{guard, middleware, web, App, Error, HttpResponse, HttpServer};
use juniper::http::graphiql::graphiql_source;
use juniper::http::GraphQLRequest;
use graphql::schema::{create_schema, Schema};
use std::io;
use std::sync::Arc;

async fn graphiql() -> HttpResponse {
    let html = graphiql_source("http://127.0.0.1:8000/");
    HttpResponse::*Ok*()
        .content_type("text/html; charset=utf-8")
        .body(html)
}

async fn graphql(
    st: web::Data<Arc<Schema>>,
    data: web::Json<GraphQLRequest>,
) -> Result<HttpResponse, Error> {
    let user = web::block(move || {
        let res = data.execute(&st, &());
        *Ok*::<_, serde_json::error::Error>(serde_json::to_string(&res)?)
    })
        .await?;
    *Ok*(HttpResponse::*Ok*()
        .content_type("application/json")
        .body(user))
}

#[actix_web::main]
async fn main() -> io::Result<()> {
    // Create Juniper schema
    let schema = std::sync::Arc::*new*(create_schema());
    let addr = "127.0.0.1:8000";

    println!("API Gateway: http://{}", addr);

    // Start http server
    HttpServer::*new*(move || {
        App::*new*()
            .data(schema.clone())
            .wrap(middleware::Logger::*default*())
            .wrap(
                Cors::*new*()
                    .allowed_methods(vec!["POST", "GET"])
                    .supports_credentials()
                    .max_age(3600)
                    .finish(),
            )
            .service(web::resource("/").guard(guard::Post()).to(graphql))
            .service(web::resource("/").guard(guard::Get()).to(graphiql))
    }).bind(addr)?
    .run()
    .await
}
```

基本上，我们正在运行 GraphQL 服务器，并注册了两条路由来分发来自操场的请求。

对于模式，让我们创建这些文件:

**src/graphql/mod.rs**

```
pub mod schema;
```

**src/graphql/schema.rs**

```
use crate::rpc;
use juniper::{FieldResult, RootNode};
use juniper::{GraphQLEnum, GraphQLInputObject, GraphQLObject};

#[derive(GraphQLEnum)]
pub enum Episode {
    *NewHope*,
    *Empire*,
    *Jedi*,
}

#[derive(GraphQLObject)]
#[graphql(description = "A human of any type")]
pub struct Human {
    pub id: String,
    pub name: String,
    pub appears_in: Episode,
    pub home_planet: String,
}

#[derive(GraphQLInputObject)]
#[graphql(description = "A new human of any type")]
pub struct NewHuman {
    pub name: String,
    pub appears_in: Episode,
    pub home_planet: String,
}

pub struct QueryRoot;

#[juniper::object]
impl QueryRoot {
    fn *show_human*(id: String) -> FieldResult<Human> {
        rpc::client::show_human(id)
    }
}

pub struct MutationRoot;

#[juniper::object]
impl MutationRoot {
    fn *create_human*(new_human: NewHuman) -> FieldResult<Human> {
        rpc::client::create_human(new_human)
    }
}

pub type Schema = RootNode<*'static*, QueryRoot, MutationRoot>;

pub fn create_schema() -> Schema {
    Schema::*new*(QueryRoot {}, MutationRoot {})
}
```

这里我们定义了 json 有效负载的结构以及查询和变异数据的方法的实现。

让我们定义用于将调用重定向到服务器的 RPC 客户端。

该模块由以下文件组成:

**src/rpc/mod.rs**

```
pub mod client;
```

**src/rpc/client.rs**

```
use crate::graphql::schema::{Episode, Human, NewHuman};
use crate::starwars_capnp;
use capnp_rpc::RpcSystem;
use capnp_rpc::twoparty::VatNetwork;
use capnp_rpc::rpc_twoparty_capnp::Side;
use futures::{AsyncReadExt, FutureExt};
use futures::task::{LocalSpawnExt};
use juniper::FieldResult;

pub fn show_human(id: String) -> FieldResult<Human> {
    let mut exec = futures::executor::LocalPool::*new*();
    let spawner = exec.spawner();

    exec.run_until(async move {
        let mut rpc_system: RpcSystem<Side> = get_rpc_system().await?;
        let starwars_client: starwars_capnp::star_wars::Client = rpc_system.bootstrap(Side::*Server*);

        spawner.spawn_local(Box::*pin*(rpc_system.map(|_|())))?;

        // Create get_result request object
        let mut request = starwars_client.show_human_request();

        // Set Human ID
        request.get().set_id(&id);

        // Send request, and await response
        let response = request.send().promise.await?;

        *Ok*(Human {
            id,
            name: response.get().unwrap().get_name().unwrap().to_string(),
            appears_in: appears_in_from_capnp(response.get().unwrap().get_appears_in().unwrap()),
            home_planet: response.get().unwrap().get_home_planet().unwrap().to_string(),
        })
    })
}

pub fn create_human(new_human: NewHuman) -> FieldResult<Human> {
    let mut exec = futures::executor::LocalPool::*new*();
    let spawner = exec.spawner();

    exec.run_until(async move {
        let mut rpc_system: RpcSystem<Side> = get_rpc_system().await?;
        let starwars_client: starwars_capnp::star_wars::Client = rpc_system.bootstrap(Side::*Server*);

        spawner.spawn_local(Box::*pin*(rpc_system.map(|_|())))?;

        // Create get_result request object
        let mut request = starwars_client.create_human_request();

        // Set Human fields
        request.get().set_name(&new_human.name);
        request.get().set_home_planet(&new_human.home_planet);
        request.get().set_appears_in(appears_in_to_capnp(new_human.appears_in));

        // Send request, and await response
        let response = request.send().promise.await?;

        *Ok*(Human {
            id: response.get().unwrap().get_id().unwrap().to_string(),
            name: response.get().unwrap().get_name().unwrap().to_string(),
            appears_in: appears_in_from_capnp(response.get().unwrap().get_appears_in().unwrap()),
            home_planet: response.get().unwrap().get_home_planet().unwrap().to_string(),
        })
    })
}

fn appears_in_from_capnp(appears_in: starwars_capnp::star_wars::human::AppearsIn) -> Episode {
    match appears_in {
        starwars_capnp::star_wars::human::AppearsIn::NewHope => Episode::*NewHope*,
        starwars_capnp::star_wars::human::AppearsIn::Empire => Episode::*Empire*,
        starwars_capnp::star_wars::human::AppearsIn::Jedi => Episode::*Jedi*,
    }
}

fn appears_in_to_capnp(appears_in: Episode) -> starwars_capnp::star_wars::human::AppearsIn {
    match appears_in {
        Episode::*NewHope* => starwars_capnp::star_wars::human::AppearsIn::NewHope,
        Episode::*Empire* => starwars_capnp::star_wars::human::AppearsIn::Empire,
        Episode::*Jedi* => starwars_capnp::star_wars::human::AppearsIn::Jedi,
    }
}

async fn get_rpc_system() -> Result<RpcSystem<Side>, Box<dyn std::error::Error>> {
    let stream = async_std::net::TcpStream::*connect*("127.0.0.1:8001").await?;

    stream.set_nodelay(true)?;

    let (reader, writer) = stream.split();
    let network = Box::*new*(
        VatNetwork::*new*(reader, writer, Side::*Client*, Default::*default*())
    );

    *Ok*(RpcSystem::*new*(network, *None*))
}
```

这里我们建立了与 RPC 服务器的连接，并使用来自 GraphQL 请求的相同数据执行调用。

我们需要翻译 Enum 类型，以与 Capnp 模式中定义的类型兼容。

最后，用命令 **cargo run** 在另一个 shell 中运行这个项目，并打开显示的 URL。

# **测试 API**

从 API 网关服务访问 URL[http://127 . 0 . 0 . 1:8000](http://127.0.0.1:8000)来访问 GraphQL playground。

运行下一个查询来显示人类数据:

```
{
  showHuman(id: "1235") {
    id
    name
    appearsIn
    homePlanet
  }
}
```

使用一些数据运行这个查询来创建一个新的人类，并查看结果。

(尝试更改这些值)

```
mutation {
  createHuman(newHuman: {
    name: "Luke",
    appearsIn: NEW_HOPE,
    homePlanet: "Mars",
  }) {
    id
    name
    appearsIn
    homePlanet
  }
}
```

你也可以用你选择的任何工具进行负载测试，我推荐 bombardier 或 siege。

我希望你喜欢这篇教程，作为一名软件架构师，我的目标是与他人分享我对 Rust 中微服务世界的介绍。如果这篇文章对你有帮助，请在下面留下一些掌声和评论。

# 参考

[](https://github.com/actix/examples/tree/master/juniper) [## actix/示例

### 社区展示和 Actix 生态系统使用示例。-actix/示例

github.com](https://github.com/actix/examples/tree/master/juniper) [](https://github.com/capnproto/capnproto-rust/tree/master/capnp-rpc/examples/hello-world) [## capnproto/capnproto-rust

### 铁锈船长。通过在 GitHub 上创建帐户，为 capnproto/capnproto-rust 开发做出贡献。

github.com](https://github.com/capnproto/capnproto-rust/tree/master/capnp-rpc/examples/hello-world) [](https://www.apress.com/gp/book/9781484258590) [## Rust for the IoT -使用 Rust 和 Raspberry Pi 构建物联网应用程序| Joseph Faisal…

### 开始为物联网(IoT)编写 Rust 应用程序。这本书是一个编程技能的迁移…

www.apress.com](https://www.apress.com/gp/book/9781484258590)
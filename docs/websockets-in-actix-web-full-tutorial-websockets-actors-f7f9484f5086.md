# Actix Web 完整教程中的 Web sockets—Web sockets & Actors

> 原文：<https://levelup.gitconnected.com/websockets-in-actix-web-full-tutorial-websockets-actors-f7f9484f5086>

![](img/86ecc663c1a97926cb8ec18ad5c2f2c6.png)

Actix Web 和 WebSockets

本教程将带你深入了解用 Actix Web 编写速度惊人的 WebSocket 客户端的每一个步骤，并有一个工作库作为参考。

我们将建立一个简单的聊天室，向房间里的每个人发送信息，包括私人信息。我还会解释每一步，这样你就可以扩展这个例子，用 Actix Web 编写自己的 WebSocket 服务器。

已完工项目回购:`[https://github.com/antholeole/actix-sockets](https://github.com/antholeole/actix-sockets)`

# **先决条件:**

1.  大致了解什么是 WebSockets
2.  知道一些基本的铁锈

其他的都将在本教程中讨论。

# **熟悉 Actix 架构**

在 Actix 架构中，有两个主要组件:参与者和消息。把每个演员想象成自己在内存中的对象，有一个邮箱。参与者可以读取他们的邮箱，并相应地回复他们的邮件，无论是通过向另一个参与者发送邮件、更改其状态，还是什么都不做。就是这样！这就是演员——阅读和回复邮件的简单小东西。

演员的速度如此之快，是因为他们完全独立于彼此工作。一个 actor 可以在它自己的线程上，或者在完全不同的机器上。只要演员能读它的邮件，它就能完美地工作。

需要注意的是，actor 只存在于内存中，它的地址像`Addr<Actor>`一样传递。Actor 本身可以改变它的属性(也许您有一个“messages_received”属性，并且您需要在每个消息上递增它)，但是您不能在其他任何地方这样做。相反，使用`Addr<Actor>`元素，您可以做`.send(some_message)`将消息放入演员的邮箱。

在 Actix web 中，每个套接字连接都是它自己的角色，而“大厅”(我们稍后会讲到)也是它自己的角色。

# **应用架构**

应用架构简介:

每个插座位于一个“房间”中，每个房间位于一个单独的大厅结构中。就是这样！

预先警告:我们将一次写一个文件，而不是从一个地方跳到另一个地方，所以在很多情况下你的代码无法编译。放心吧！最后，如果你按照教程正确操作，一切都会像胶水一样粘在一起并完美运行。

# **第一步:设置**

首先，运行`cargo init`或类似的东西。然后，转到您的`Cargo.toml`，确保您依赖这些软件包:

```
[dependencies]
actix-web="3.2.0" # duh
actix-web-actors="3" # actors specific to web
actix = "0.10.0" # actors
uuid = { version = "0.8", features = ["v4", "serde"] }
```

你可能想去`cargo run`hello world，去泡一杯咖啡；整理这些箱子需要一两分钟。

# **第二步:WebSocket Actor — ws.rs**

第一步是定义我们的 WebSocket 对象。创建一个名为`ws.rs`的文件。您需要在文件顶部添加以下导入和常量:

```
actix::{fut, ActorContext};
use crate::messages::{Disconnect, Connect, WsMessage, ClientActorMessage}; //We'll be writing this later
use crate::lobby::Lobby; // as well as this
use actix::{Actor, Addr, Running, StreamHandler, WrapFuture, ActorFuture, ContextFutureSpawner};
use actix::{AsyncContext, Handler};
use actix_web_actors::ws;
use actix_web_actors::ws::Message::Text;
use std::time::{Duration, Instant};
use uuid::Uuid;

const HEARTBEAT_INTERVAL: Duration = Duration::from_secs(5);
const CLIENT_TIMEOUT: Duration = Duration::from_secs(10);
```

用以下签名定义结构:

```
struct WsConn {
    room: Uuid,
    lobby_addr: Addr<Lobby>,
    hb: Instant,
    id: Uuid,
}
```

这些字段如下所示:

*   room:每个套接字都存在于一个“room”中，在这个实现中，它只是一个简单的 HashMap，将 Uuid 映射到套接字 id 列表。
*   addr:这是套接字所在的大厅的地址。这将用于向大厅发送数据。因此，向大厅发送短信可能是这样的:`self.addr.do_send('hi!')`。没有这个属性，这个演员是不可能找到大堂的。
*   hb:虽然 WebSockets 在关闭时确实会发送消息，但有时 WebSockets 会在没有任何警告的情况下关闭。我们每隔 N 秒发送一次心跳，如果没有得到响应，我们就终止套接字，而不是让这个参与者永远存在。此属性是自我们收到最后一个心跳以来的时间。在许多库中，这是自动处理的。
*   id:这是我们分配给那个插座的 ID。这对于私人信息传递非常有用，所以我们可以`/whisper <id> hello!`和客户耳语。

接下来，我们将编写一个快速的`new`特征，这样我们可以更容易地启动套接字:

```
WsConn {
    pub fn new(room: Uuid, lobby: Addr<Lobby>) -> WsConn {
        WsConn {
            id: Uuid::new_v4(),
            room,
            hb: Instant::now(),
            lobby_addr: lobby,
        }
    }
}
```

这样，我们就不必设置心跳或分配 id。

**使 WsConn 结构成为一个参与者**

请注意`WsConn`只是一个普通的老铁锈结构。要将它转换成一个 actor，我们需要在它上面实现 Actor 特征。

以下是完整的代码，然后我们将对其进行剖析:

```
Actor for WsConn {
    type Context = ws::WebsocketContext<Self>;

    fn started(&mut self, ctx: &mut Self::Context) {
        self.hb(ctx);

        let addr = ctx.address();
        self.lobby_addr
            .send(Connect {
                addr: addr.recipient(),
                lobby_id: self.room,
                self_id: self.id,
            })
            .into_actor(self)
            .then(|res, _, ctx| {
                match res {
                    Ok(_res) => (),
                    _ => ctx.stop(),
                }
                fut::ready(())
            })
            .wait(ctx);
    }

    fn stopping(&mut self, _: &mut Self::Context) -> Running {
        self.lobby_addr.do_send(Disconnect { id: self.id, room_id: self.room });
        Running::Stop
    }
}
```

首先，你会看到我们在定义(`type Context = ws::WebsocketContext<Self>;`)中定义了一个名为`Context`的类型。这是演员要求的。那就是这个演员生活的`context`；这里，我们说上下文是 WebSocket 上下文，应该允许它做 WebSocket 的事情，比如开始监听端口。每个参与者都需要一个上下文，但是只有 WebSocket 参与者需要 WebSocket 上下文。

我们还编写了`started`和`stopping`方法——这将分别创建和销毁 Actor。

在`started`中，我们开始心跳循环；它只是一个间隔触发的函数，所以在我们开始循环后，我们不必担心它——如果心跳没有回音，它会自动关闭套接字。我们一会儿再写。

我们还获取大厅的地址并向其发送消息(`Connect {}`)说“嘿！我联系上了。这是我想进入的大厅，还有我的身份证，还有我的邮箱地址，你可以通过它联系到我。”这条消息由一个游说团处理，我们稍后会写进去。

我们异步发送消息**。如果我们做了`do_send`而不是`send`，我们将会同步发送*和*。我的意思是“把信息扔进邮箱，然后开车离开。”`do_send`不关心消息是否被发送或阅读。`send`需要等待，这就是本块的目的:**

```
.then(|res, _, ctx| {
     match res {
        Ok(_res) => (),
        _ => ctx.stop(),
    }
    fut::ready(())
})
```

**如果有任何失败，我们只需用`ctx.stop`停止整个演员。这种情况可能不会发生，但如果你的`Lobby`演员出了问题，这种情况可能会发生。客户端将会看到类似于`ws handshake couldn't be completed.`的东西**

**停下来容易多了。你可以看到`do_send`在这里的作用:我们试图发送一个断开消息到大厅，但如果我们不能，没什么大不了的。阻止这个演员。**

**就是这样！我们的`WsConn`现在是演员。**

****心跳****

**这是我们之前讨论的心跳方法:**

```
WsConn {
    fn hb(&self, ctx: &mut ws::WebsocketContext<Self>) {
        ctx.run_interval(HEARTBEAT_INTERVAL, |act, ctx| {
            if Instant::now().duration_since(act.hb) > CLIENT_TIMEOUT {
                println!("Disconnecting failed heartbeat");
                act.lobby_addr.do_send(Disconnect { id: act.id, room_id: act.room });
                ctx.stop();
                return;
            }

            ctx.ping(b"PING");
        });
    }
}
```

**我们在这里所做的就是 ping 客户端，并间隔一段时间等待响应。如果响应不来，套接字就死了；发送断开连接并停止客户端。**

****处理 WS 消息****

**下一个方法有点长，但并不复杂:**

```
StreamHandler<Result<ws::Message, ws::ProtocolError>> for WsConn {
    fn handle(&mut self, msg: Result<ws::Message, ws::ProtocolError>, ctx: &mut Self::Context) {
        match msg {
            Ok(ws::Message::Ping(msg)) => {
                self.hb = Instant::now();
                ctx.pong(&msg);
            }
            Ok(ws::Message::Pong(_)) => {
                self.hb = Instant::now();
            }
            Ok(ws::Message::Binary(bin)) => ctx.binary(bin),
            Ok(ws::Message::Close(reason)) => {
                ctx.close(reason);
                ctx.stop();
            }
            Ok(ws::Message::Continuation(_)) => {
                ctx.stop();
            }
            Ok(ws::Message::Nop) => (),
            Ok(Text(s)) => self.lobby_addr.do_send(ClientActorMessage {
                id: self.id,
                msg: s,
                room_id: self.room
            }),
            Err(e) => panic!(e),
        }
    }
}
```

**对所有可能的 WebSocket 消息进行简单的模式匹配。**

*   **ping 以 pong 回应——那是客户端在敲打我们。用乒乓回应。作为副产品，由于客户端可以检测我们的心跳，我们知道它是活的，所以我们可以重置我们的心跳时钟。**
*   **pong 是对我们发送的 ping 的响应。重置我们的时钟，他们还活着。**
*   **如果消息是二进制的，我们将把它发送给 WebSocket 上下文，它将决定如何处理它。这实际上不应该被触发。**
*   **如果是关闭消息，就关闭。**
*   **对于本教程，我们不打算响应连续帧(简而言之，这些是无法放入一条消息的 WebSocket 消息)**
*   **在 nop 上让我们 nop(无操作)**
*   **发短信，(这一条我们做的最多！)送到大厅。游说团将负责把它安排到需要去的地方。**
*   **一出错，我们就慌了。您可能希望合理地实现这里要做的事情。**

****回复短信****

**这是我们第一次处理邮箱信息。当一个`WsMessage`被放入我们的邮箱时，这个方法被调用。**

```
Handler<WsMessage> for WsConn {
    type Result = ();

    fn handle(&mut self, msg: WsMessage, ctx: &mut Self::Context) {
        ctx.text(msg.0);
    }
}
```

**在这里，如果服务器将一封`WsMessage`(我们需要定义)邮件放入我们的邮箱，我们所做的就是将它直接发送给客户端。这就是从邮箱中“阅读邮件”的样子；`impl Handler<MailType> for ACTOR`。请注意，我们还需要定义对该邮件的回复可能是什么样子。如果邮件像`do_send`一样放置，回复类型并不重要。如果像`send()`一样放置，那么等待的结果类型将是`Result`的类型。也许你做`type Result = String`，或者类似的。不管你把什么类型的`T`放到那里，`handle`都需要返回`T`。**

**此外，处理程序消息的签名包括:**

1.  **信息本身。您可以完全控制该邮件传递多少数据。**
2.  **自我语境。这是你自己的语境，是自我的一个“邮箱”。你可以从 ctx 中读取 memeber 变量，也可以在这里将消息放入你自己的邮箱。**

**就是这样！那就是整个`WsClient`。**

# **第三步:为邮箱定义邮件**

**创建一个名为`messages.rs`的新文件。这个文件将保存所有放入演员邮箱的“消息”。**

**消息是具有两个特征的结构:第一个很简单，仅仅是`#[derive(Message)]`告诉我们它是一个参与者消息。第二个是`rtype`。该返回类型必须与我们在上一节末尾讨论的`T`类型相同——在消息被处理后返回的类型。因此，如果我们想定义一个返回字符串的消息，它应该是这样的:**

```
#[derive(Message)]
#[rtype(result = "String")] // result = your type T
pub struct MyMessage; // they usually carry info, but not for this example

impl Handler<MyMessage> for MyActor {
    type Result = String; // This type is T

    fn handle(&mut self, msg: MyMessage, ctx: &mut Self::Context) -> String { // Returns your type T
        ...
    }
}
```

**就是这样！定义消息实际上很简单。下面是消息文件的代码，带有相应的注释:**

```
actix::prelude::{Message, Recipient};
use uuid::Uuid;

//WsConn responds to this to pipe it through to the actual client
#[derive(Message)]
#[rtype(result = "()")]
pub struct WsMessage(pub String);

//WsConn sends this to the lobby to say "put me in please"
#[derive(Message)]
#[rtype(result = "()")]
pub struct Connect {
    pub addr: Recipient<WsMessage>,
    pub lobby_id: Uuid,
    pub self_id: Uuid,
}

//WsConn sends this to a lobby to say "take me out please"
#[derive(Message)]
#[rtype(result = "()")]
pub struct Disconnect {
    pub room_id: Uuid,
    pub id: Uuid,
}

//client sends this to the lobby for the lobby to echo out.
#[derive(Message)]
#[rtype(result = "()")]
pub struct ClientActorMessage {
    pub id: Uuid,
    pub msg: String,
    pub room_id: Uuid
}
```

**为演员定义消息非常简单。关于 actor 框架，我最喜欢的部分之一是，如果您需要从一个 Actor 获取数据到另一个 Actor，那么创建消息或向现有消息添加数据是非常容易的。**

# **第四步:定义大厅**

**第一，进口为`lobby.rs`:**

```
crate::messages::{ClientActorMessage, Connect, Disconnect, WsMessage};
use actix::prelude::{Actor, Context, Handler, Recipient};
use std::collections::{HashMap, HashSet};
use uuid::Uuid;
```

**我们快完成了！现在，我们有实际的游说写。正如我们所说，游说是一个演员，但演员是一个普通的老结构。下面是该结构(将放在`lobby.rs`中):**

```
Socket = Recipient<WsMessage>;

pub struct Lobby {
    sessions: HashMap<Uuid, Socket>,          //self id to self
    rooms: HashMap<Uuid, HashSet<Uuid>>,      //room id  to list of users id
}
```

**我们将套接字存储为一个简单的 WsMessage 接收方。通过这种设置，我们可以轻松地浏览全部、部分或找到特定的客户端。**

**作为助手，我们将为大厅实现一个默认设置:**

```
Default for Lobby {
    fn default() -> Lobby {
        Lobby {
            sessions: HashMap::new(),
            rooms: HashMap::new(),
        }
    }
}
```

**现在，让我们编写一个向客户端发送消息的助手。**

```
Lobby {
    fn send_message(&self, message: &str, id_to: &Uuid) {
        if let Some(socket_recipient) = self.sessions.get(id_to) {
            let _ = socket_recipient
                .do_send(WsMessage(message.to_owned()));
        } else {
            println!("attempting to send message but couldn't find user id.");
        }
    }
}
```

**该方法接受一个字符串和一个 id，并将该字符串发送给具有该 Id 的客户端(如果它存在；如果没有，它只是打印一些东西来安慰。您可能希望通过返回一个结果或类似的结果来相应地处理这个问题。)**

****让大厅成为演员****

**准备好学习整个教程中最短的部分了吗？要使大厅成为演员，请使用以下代码:**

```
Actor for Lobby {
    type Context = Context<Self>;
}
```

**就是这样！我们不关心大厅的任何生命周期。我们只有一个，我们只在应用程序启动时挂载它，在应用程序关闭时移除它。房间只是散列表，所以在这个简单的例子中，我们不需要让它们成为参与者。**

****处理消息****

**大厅将从客户端获得 3 种类型的消息:连接、断开和 WsMessage。两者都来自 actor 特征的 WsConn 生命周期方法。这一部分不是 actix 特有的，但是我将解释代码的主要部分。**

```
/// Handler for Disconnect message.
impl Handler<Disconnect> for Lobby {
    type Result = ();

    fn handle(&mut self, msg: Disconnect, _: &mut Context<Self>) {
        if self.sessions.remove(&msg.id).is_some() {
            self.rooms
                .get(&msg.room_id)
                .unwrap()
                .iter()
                .filter(|conn_id| *conn_id.to_owned() != msg.id)
                .for_each(|user_id| self.send_message(&format!("{} disconnected.", &msg.id), user_id));
            if let Some(lobby) = self.rooms.get_mut(&msg.room_id) {
                if lobby.len() > 1 {
                    lobby.remove(&msg.id);
                } else {
                    //only one in the lobby, remove it entirely
                    self.rooms.remove(&msg.room_id);
                }
            }
        }
    }
}
```

**我们所做的只是通过以下任一方式来响应断开消息:**

1.  **从房间中移除单个客户端。“UUID 把其他人都断开了！**
2.  **如果该客户是房间中的最后一个，则完全移除房间(这样我们就不会阻塞散列表)**

**接下来，我们需要响应连接消息。同样，几乎没有特定于参与者的逻辑:**

```
Handler<Connect> for Lobby {
    type Result = ();

    fn handle(&mut self, msg: Connect, _: &mut Context<Self>) -> Self::Result {
        // create a room if necessary, and then add the id to it
        self.rooms
            .entry(msg.lobby_id)
            .or_insert_with(HashSet::new).insert(msg.self_id);

        // send to everyone in the room that new uuid just joined
        self
            .rooms
            .get(&msg.lobby_id)
            .unwrap()
            .iter()
            .filter(|conn_id| *conn_id.to_owned() != msg.self_id)
            .for_each(|conn_id| self.send_message(&format!("{} just joined!", msg.self_id), conn_id));

        // store the address
        self.sessions.insert(
            msg.self_id,
            msg.addr,
        );

        // send self your new uuid
        self.send_message(&format!("your id is {}", msg.self_id), &msg.self_id);
    }
}
```

**这里所做的只是添加一个套接字并向它们发送消息。**

**最后，我们打开邮箱让客户给大堂发消息，让大堂转发给客户。**

```
Handler<ClientActorMessage> for Lobby {
    type Result = ();

    fn handle(&mut self, msg: ClientActorMessage, _: &mut Context<Self>) -> Self::Result {
        if msg.msg.starts_with("\\w") {
            if let Some(id_to) = msg.msg.split(' ').collect::<Vec<&str>>().get(1) {
                self.send_message(&msg.msg, &Uuid::parse_str(id_to).unwrap());
            }
        } else {

self.rooms.get(&msg.room_id).unwrap().iter().for_each(|client| self.send_message(&msg.msg, client));
        }
    }
}
```

**这将检查消息是否以\w 开头。如果是，我们知道这是一个密语，并将它发送到一个特定的客户端。如果不是，我们会将它发送给房间中的所有用户。(这是**而不是**生产就绪！如果在 w 后面有一个无效的 UUID，它将会死机，如果它后面没有任何东西，它将会自动失败。)**

**那是大厅！**

# **最后一步:设置路线/运行服务器**

**首先，我们必须打开一条让我们连接到服务器的路由。创建一个名为“start_connection.rs”的文件，并放入以下路径:**

```
crate::ws::WsConn;
use crate::lobby::Lobby;
use actix::Addr;
use actix_web::{get, web::Data, web::Path, web::Payload, Error, HttpResponse, HttpRequest};
use actix_web_actors::ws;
use uuid::Uuid;

#[get("/{group_id}")]
pub async fn start_connection(
    req: HttpRequest,
    stream: Payload,
    Path(group_id): Path<Uuid>,
    srv: Data<Addr<Lobby>>,
) -> Result<HttpResponse, Error> {
    let ws = WsConn::new(
        group_id,
        srv.get_ref().clone(),
    );

    let resp = ws::start(ws, &req, stream)?;
    Ok(resp)
}
```

**我们将一个只有组 id(应该是有效的 uuid)的路由定义为路径参数。然后，我们创建一个引用大厅的新 WsConn(在下一步中，我们向 actix web 注册大厅)。最后，我们将请求升级为 WebSocket 请求，然后砰！我们现在有了一个开放的持久连接。**

**我们的最后一步是将大厅注册为共享数据，这样我们就可以像刚才那样获取它(`srv: Data<Addr<Lobby>>`)。您的 main.rs 应该如下所示:**

```
ws;
mod lobby;
use lobby::Lobby;
mod messages;
mod start_connection;
use start_connection::start_connection as start_connection_route;
use actix::Actor;

use actix_web::{App, HttpServer};

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    let chat_server = Lobby::default().start(); //create and spin up a lobby

    HttpServer::new(move || {
        App::new()
            .service(start_connection_route) //. rename with "as" import or naming conflict
            .data(chat_server.clone()) //register the lobby
    })
    .bind("127.0.0.1:8080")?
    .run()
    .await
}
```

**然后嘣！我们的整个应用程序现在共享一个大厅。这是我们写的所有东西:**

1.  **游说聊天**
2.  **发送私人消息**
3.  **发送广播消息**
4.  **易于扩展！**

**全部在 actix web 中，使用 actors！为了测试客户端，我会使用一个简单的 websocket 来测试 chrome 或者 T2 的 firefox。打开多个选项卡，在不同的大厅发送耳语或广播！**

**快乐的网络社交！**
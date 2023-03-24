# 利用朱莉娅的电报机器人

> 原文：<https://levelup.gitconnected.com/a-telegram-bot-using-julia-c302ff2614d2>

![](img/189cb44f64dd474a8049d037365665b2.png)

[艾迪尔·华](https://unsplash.com/@aideal?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

今天我将向你展示如何使用 Julia 语言创建一个简单的机器人。

我要描述的这个机器人非常通用，它可以读取消息，回复和存储与多个用户的对话状态。

我将使用 Telegram，因为它可以方便地访问 API(【https://core.telegram.org/bots】T4)。

当与机器人交互的用户可能处于对话的不同阶段时，我们将它们称为**状态**。这些状态需要被存储，所以我们会给机器人一个交互的记忆。

我们将要描述的机器人将一些用户状态存储在一个数据库中，这个数据库可以是一个简单的文件，也可以是一个更复杂的数据库。

# 机器人眼睛:解析消息

电报 API 以 JSON 格式返回消息。幸运的是，Julia 提供了一个 [JSON 解析器](https://juliapackages.com/p/json)。当我们有消息时，我们可以简单地检索用户 id(出于隐私原因，我不会读取或存储任何更多的信息，因为在这一点上它们是不必要的)。

```
function parse_message(message)
    id = message["message"]["from"]["id"]
    update_contact_list(message)
    state = get_state(id)

    if (state == 0) | (message["message"]["text"]=="/start")
        send_welcome(message["message"]["from"])
        set_state(id, 1)
    elseif (state == 1) | (message["message"]["text"]=="/cook")
        ask_program(message["message"]["from"])
        set_state(id, 2)
    elseif state == 3
        send_bye(message["message"]["from"])
        set_state(id, 1)
    end
end
```

# bot 内存:创建一个数据库

使用[PostgreSQL](https://github.com/JuliaDatabases/PostgreSQL.jl)模块可以创建一个数据库

```
function init_db()
    conn = LibPQ.Connection("dbname=dname user=bot password=supersecret host=database")

    result = execute(conn, """
        CREATE TABLE IF NOT EXISTS dname (
            id    integer PRIMARY KEY,
            state   integer,
            program varchar(10)
        );
    """)
    close(conn)
end
```

并与它建立联系。

```
function get_connection()
    global conn
    if conn == -1
        conn = LibPQ.Connection("dbname=dname user=bot password=supersecret host=database")
    end
    return conn
end
```

# 机器人交互:存储的密钥

我们将在这里构建的机器人可以为每个用户存储一个或多个信息。为了可用性和可移植性，我将创建两个函数 *get_key* 和 *set_key* ，每个函数都能够读取和存储一些与用户相关的值。特别是，机器人可以存储**状态**键，它包含与用户对话的当前状态。

```
function get_key(id, key)
    res = execute(conn, "SELECT * from pizza_bot WHERE id=$id;")
    data = columntable(res)
    keys = Dict("id"=>data[1][1], "state"=>data[2][1], "program"=>data[3][1])
    return keys[key]
end

function set_key(id, key, value)
    res = execute(conn, "UPDATE pizza_bot SET $(key)='$value' WHERE id=$id;")
end
```

# 机器人说话:发送信息

在 Julia 中, [HTTP](https://github.com/JuliaWeb/HTTP.jl) 包提供了向 Telegram API 发送数据和发送消息的方法。

```
function send_message(params)    
    req = HTTP.request("POST",string(url,key,"/sendMessage"),["Content-Type" => "application/json"],JSON.json(params))
end
```

# 机器人生活:组装起来运行

机器人现在可以阅读、说话和记忆东西，我们只需要从消息服务中*/get updates*et voilà交互就已经设置好了！

```
function run()
    offset = -1

    println("ready!")
    while true
        if offset >= 0
            params = Dict("offset"=>offset)
        else
            params = Dict()
        end
        req = HTTP.request("GET", string(url,key,"/getUpdates"), ["Content-Type" => "application/json"],JSON.json(params))
        body = String(req.body)
        result = JSON.Parser.parse(body)

        if length(result["result"]) < 1
            continue
        end
        offset = result["result"][end]["update_id"] + 1
        for message in result["result"]
           parse_message(message)
        end
        sleep(0.1)
    end
end
```

# 密码

一个简单机器人的完整代码可以从我在 https://github.com/fvalle1/pizza_bot 的仓库中获得
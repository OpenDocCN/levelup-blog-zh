# 如何用 Rust 构建笔记命令行界面(CLI ):第 2 部分

> 原文：<https://levelup.gitconnected.com/how-to-build-a-note-taking-command-line-application-with-rust-part-2-ec738dd97c48>

![](img/51d94b184ff5a790dd798a68cd475b52.png)

温哥华下了很多雨，但是在下面的帖子里可以找到唯一的锈迹

在这个系列的第一部分[中，我们创建了一个基本的 rust CLI 程序，它允许我们**创建**笔记并将它们保存在 sqlite 数据库中。如果你还没有读过那篇文章，你应该从那里开始，因为这是在那篇文章停止的地方建立的。](/how-to-build-a-note-taking-command-line-application-with-rust-part-1-34b9cd5be6b9)

这下一部分将涵盖 CRUD 的其余部分:**读取**，**更新**，以及**删除**。

本系列中描述的 Rust 应用程序已经在[开源 repo for engram](https://github.com/adamjberg/engram/tree/main/clients/cli/ego) 中登陆。如果您想更密切地关注开发，请登录 Github，这样您就可以记得回来查看。

# 阅读

下一个合乎逻辑的步骤是添加读取刚刚创建的任何内容的能力。在命令行应用程序中，这里的选项非常有限，这使得我们可以跳过讨论所有与图形用户界面(GUI)相关的主题。但是这并不一定会使事情变得简单。

对于如何在我们的应用程序中工作，有一些考虑。即我们希望如何触发并向用户显示历史记录。

# 触发器或命令

notes 应用程序将在启动后继续运行，直到提交一个空字符串。为了查询某种类型的信息，我们需要发明一些功能，允许用户区分简单的注释和更高级的命令。

为此，我们将实现“斜杠命令”。这些已经被 Slack 和 concept 之类的公司推广开来，应该能很好地满足我们的需求。如果注释以“/”开头，后面的文本将被解释为命令。更具体地说，为了阅读笔记，我们将添加一个“/list”命令。为了简单起见，它将转储数据库中的所有注释。我们将在随后的帖子中对此进行跟进，以改进其工作方式。每一个新的功能都比你最初意识到的要多得多，所以推迟某些事情可以防止你被无关紧要的细节所困扰。

# /列表

```
let mut running = true;
    while running == true {
        let mut buffer = String::new();
        io::stdin().read_line(&mut buffer)?; let trimmed_body = buffer.trim();
        if trimmed_body == "" {
            running = false;
        } 
        else if trimmed_body == "/list" {
            let mut stmt = conn.prepare("SELECT id, body from notes")?;
            let mut rows = stmt.query(rusqlite::params![])?;
            while let Some(row) = rows.next()? {
                let id: i32 = row.get(0)?;
                let body: String = row.get(1)?;
                println!("{} {}", id, body.to_string());
            }
        }
        else {
            conn.execute("INSERT INTO notes (body) values (?1)", [trimmed_body])?;
        }
    }
```

每当提交一个注释时，我们检查是否输入了“/list”。如果是，我们进入`else if`块打印现有的笔记。

`let mut stmt = conn.prepare("SELECT id, body from notes")?;`

这将使用我们在 main 函数开始时初始化的连接准备一条 SQL 语句。`SELECT id, body from notes`指定我们要从 notes 表中返回 id 和 body 列。

`let mut rows = stmt.query(rusqlite::params![])?;`

然后，我们在准备好的语句上发出`query`方法。因为我们选择了所有的音符，所以没有参数，这就是为什么我们使用`rusqlite::params![]`传递空参数。

```
while let Some(row) = rows.next()? {
    let id: i32 = row.get(0)?;
    let body: String = row.get(1)?;
    println!("{} {}", id, body.to_string());
}
```

上面的代码遍历了所有返回的行。每一行将是一个说明，以前已经输入到我们的数据库中。`row.get(0)`和`row.get(1)`获取相关列索引的值。在我们的例子中，我们的查询指定:`SELECT id, body from notes`，这意味着 id 将位于索引 0，而 body 将位于索引 1。

Rust 无法推断这些属性的类型，这就是为什么它们必须被指定为`let id: i32 = row.get(0)?;`和`let body: String = row.get(1)?;`。用`i32`标识 id 是一个 32 位整数，用`String`标识主体是一个字符串。

最后，我们将这两者传递给`println`函数，以便将它们输出回终端。

同样，你现在可以用`cargo run`运行应用程序，如果你发出一个`/list`命令，你现在应该可以看到你提交的所有笔记都打印出来了。

## 删除

我几乎总是在更新前删除。这是一个简单的操作，可以很快实现。它也可以作为编辑项目的临时方法。在我们的 notes 示例中，如果我想编辑一个注释，我可以先删除它，然后用正确的正文创建一个新的注释。如上所述，这在像这样的小例子中不太相关，但是在更大的应用程序中，编辑 GUI 可能会非常复杂。

```
...
let trimmed_body = buffer.trim();
let cmd_split = trimmed_body.split_once(" ");let mut cmd = trimmed_body;
let mut msg = "";
if cmd_split != None {
    cmd = cmd_split.unwrap().0;
    msg = cmd_split.unwrap().1;
}if cmd == "/del" {
    let id = msg;
    conn.execute("delete from notes where id = (?1)", [id])?;
}
...
```

`/del`命令有一些`/list`命令没有的东西——一个附加参数。我们需要指定要删除的笔记。想了一下，决定还是通过`/del 1`删除 id 为 1 的笔记吧。

为了区分“命令”和“参数”，我决定使用`split_once`方法。

`let cmd_split = trimmed_body.split_once(" ");`

`split_once`方法根据传递的分隔符分割字符串。在我们的例子中，“/del 1”将作为`Some(("/del", "1"))`返回。然后，我开始解开这些值，并将它们存储在`cmd`和`msg`变量中。

`if cmd_split != None {`

这种相等性检查涵盖了没有空格的情况。在这种情况下，`split_once`方法返回 None 来标识分隔符" "不存在。

我还是新手，觉得这个有点笨重。我怀疑可能有一个更好的方式来写这个，但现在它做的工作。我已经多次学会不要纠结于小细节，因为仅仅这一点就可能导致 30 分钟的兔子洞生锈文档。如果你有任何建议，欢迎在评论区分享。

```
if cmd == "/del" {
    let id = msg;
    conn.execute("delete from notes where id = (?1)", [id])?;
}
```

我们现在检查输入文本的第一部分是不是`/del`，如果是，我们知道我们可以从`msg`变量中得到要删除的 id。

`"delete from notes where id = (?1)", [id]`

这是从 notes 表中删除与指定 id 匹配的行的 SQL 命令。

你可以再次运行`cargo run`，现在尝试`/del 1`，它会删除你创建的第一条消息。您可以通过运行`/list`来确认它是否工作，并且您应该不会再看到索引为 1 的注释。

# 更新

对于如何进行更新，有几个选项。为了继续保持简单，我决定编辑应该作为一个命令发出。`/edit 1 the new body I want to have`。与删除类似，传递一个`id`来标识要编辑哪个注释。跟随在`id`之后的所有物体都被当作新物体来覆盖现有物体。

```
else if cmd == "/edit" {
    let msg_split = msg.split_once(" ").unwrap();
    let id = msg_split.0;
    let body = msg_split.1; conn.execute("update notes set body = (?1) where id = (?2)", [body, id])?;
}
```

`/edit`命令的启动类似于`/del`。主要的区别是我们需要再次用空白分割`msg`。使用`split_once`仅在第一个空白上分割，这允许主体保持完整。

`"update notes set body = (?1) where id = (?2)", [body, id]`

这个 update 命令指定我们将把`body`列设置为我们从输入中解析的内容，该输入包含与指定的`id`相匹配的`id`。

`(?1)`和`(?2)`

这些表示位置参数。我们之前所有的 SQL 语句都只有一个，但是在这里有两个。`(?1)`将被所提供参数`[body, id]`(或本例中的`body`)中的第一个条目替换。`(?2)`将被`id`变量替换。

再次启动它，并尝试编辑您现有的笔记之一。您可以使用`/list`命令查看之前的注释，然后发出和`/edit`命令，最后发出另一个`/list`命令来确认注释被正确修改。

# 安装您的 Notes 应用程序

```
cargo install --path .
```

运行以上程序将编译 rust 应用程序，并将其添加到您的系统路径中。如果您在开始时使用了`cargo new notes`命令，现在您应该可以从终端访问`notes`命令。如果您想更新可执行文件的名称，您可以修改`Cargo.toml`文件中的`name`属性来匹配您喜欢的名称。

我已经开发了一个名为`engram`的 notes 应用程序，为了保持我的可执行文件名称简短，我将其简称为`eg`。现在，我在终端中的任何时候都可以输入`eg`并立即访问我的笔记。

# 总结

如果您遵循了整个教程，现在您应该有一个用 Rust 编写的可以从终端访问的功能性 notes 应用程序。我将在此基础上继续写文章，但当我为 engram 的 CLI 应用程序构建新功能时，它们可能会是关于更具体的主题。
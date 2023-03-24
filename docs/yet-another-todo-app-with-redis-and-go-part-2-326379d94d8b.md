# 又一个 TODO 应用！使用 Redis 和 Go[第 2 部分]

> 原文：<https://levelup.gitconnected.com/yet-another-todo-app-with-redis-and-go-part-2-326379d94d8b>

![](img/6c823752cdb04ec65c33d98f8d95cf96.png)

劳伦·索德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

这是博客系列的第二部分(也是最后一部分),在一个简单而实用的 **todo 应用**的帮助下，涵盖了一些 Redis 数据结构😉使用`[Go](https://golang.org/)` `[cobra](https://github.com/spf13/cobra)`(一个流行的 CLI 应用程序库)和`Redis`作为后端存储构建。

 [## 又一个 TODO 应用！使用 Redis 和 Go[第 1 部分]

### 通过使用 Go 构建 CLI 应用程序来学习 Redis

medium.com](https://medium.com/@abhishek1987/yet-another-todo-app-with-redis-and-go-part-1-7a1134f5752e) 

`Part 1`介绍了`todo`应用的概述、设置和试用过程。在这一部分，我们将深入了解代码

> *代码为* [*可在 Github*](https://github.com/abhirockzz/redis-todo-cli-app) 上获得

在我们开始之前，先回顾一下您可以用`todo` CLI 做些什么——好的，老的`CRUD`！来自`todo --help`:

```
Yet another TODO app. Uses Go and RedisUsage:
  todo [command]Available Commands:
  create      create a todo with description
  delete      delete a todo
  help        Help about any command
  list        list all todos
  update      update todo description, status or bothFlags:
  -h, --help      help for todo
  -v, --version   version for todoUse "todo [command] --help" for more information about a command.
```

`[Cobra](https://github.com/spf13/cobra)`用作 CLI 框架。这是一个受欢迎的项目，并支持 CLI 工具，如`docker`、`kubectl`等。

代码结构如下所示:

```
.
├── cmd
│   ├── create.go
│   ├── delete.go
│   ├── list.go
│   ├── root.go
│   └── update.go
├── db
│   └── todo-redis.go
├── go.mod
├── go.sum
└── main.go
```

每个`todo`操作(`create`、`list`等)。)由一个`cobra`命令控制。`cmd`包包含`create.go`、`list.go`等实现。

# `root`命令

在`cobra`中，根命令是最顶层的父命令。可以向其中添加其他子命令。在本例中，`todo`是根命令和`create`、`list`等。是子命令(同样，每个命令可以有一个或多个在调用期间传递的标志)

`todo`根命令在`cmd/root.go`中定义

```
var rootCmd = &cobra.Command{Use: "todo", Short: "manage your todos", Version: "0.1.0"}
```

请注意，它没有任何特定的标志—它只有子命令。让我们看看他们

# 子命令— `create`、`list`、`update`、`delete`

子命令实现逻辑遵循相同的模式:

*   使用`init()`函数引导命令-设置其标志，标记强制标志(如果需要)并将其添加到根命令
*   定义执行逻辑/功能——在我们的例子中，是与`Redis`相关的操作(我们将很快深入其中)

下面是`todo create`实现的样子(在`cmd/create.go`中)

```
...
var createCmd = &cobra.Command{Use: "create", Short: "create a todo with description", Run: Create}func init() {
	createCmd.Flags().String("description", "", "create todo with description")
	createCmd.MarkFlagRequired("description")
	rootCmd.AddCommand(createCmd)
}// Create - todo create --description <text>
func Create(cmd *cobra.Command, args []string) {
	desc := cmd.Flag("description").Value.String()
	db.CreateTodo(desc)
}
...
```

`todo create`需要`description`的强制值，并使用`func Create(cmd *cobra.Command, args []string)`执行实际的`todo`创建。调用`db.CreateTodo(desc)`进行 Redis 相关操作，将`todo`信息保存到 Redis 中。

下面是`todo update`实现的一个片段(在`cmd/update.go`中)

```
...
var updateCmd = &cobra.Command{Use: "update", Short: "update todo description, status or both", Run: Update}func init() {
	updateCmd.Flags().String("id", "", "id of the todo you want to update")
	updateCmd.MarkFlagRequired("id")
	updateCmd.Flags().String("description", "", "new description")
	updateCmd.Flags().String("status", "", "new status: completed, pending, in-progress") rootCmd.AddCommand(updateCmd)
}// Update - todo update --id <id> --status <new status> --description <new description>
func Update(cmd *cobra.Command, args []string) {
	id := cmd.Flag("id").Value.String()
	desc := cmd.Flag("description").Value.String() status := cmd.Flag("status").Value.String() if desc == "" && status == "" {
		log.Fatalf("either description or status is required")
	} if status == "completed" || status == "pending" || status == "in-progress" || status == "" {
		db.UpdateTodo(id, desc, status)
	} else {
		log.Fatalf("provide valid status - completed, pending or in-progress")
	}
}
```

`todo update`需要一个强制`--id`标志，并且需要提供`status`或`description`(可以是两者)。它进一步调用`db.UpdateTodo`进行 Redis 相关操作，以更新通过 CLI 传入的`todo`信息

# 雷迪斯

现在，让我们来探索应用程序的核心——Redis 相关的逻辑来管理`todo`信息。Redis 操作已集中在一个地方，即`db`包中的`todo-redis.go`。它有四个功能，每个功能映射到各自的`todo`子命令:

*   `CreateTodo`
*   `ListTodos`
*   `UpdateTodo`
*   `DeleteTodo`

我用过`[go-redis](https://github.com/go-redis/redis)`

> `[*redigo*](https://github.com/gomodule/redigo)` *是另一个流行的客户端*

所有的命令都是从连接到`Redis`开始的(很明显！)

```
c := redis.NewClient(&redis.Options{Addr: redisHost})
	err := c.Ping().Err()
	if err != nil {
		log.Fatal("redis connect failed", err)
	}
defer c.Close()
```

这是一个单用户`cli`应用，而不是一个长期运行的服务器组件。因此，我们可以在单独操作后连接和断开，而不是在全球层面处理 Redis 客户端`redis.Client`。现在我们来看看每个操作的相关片段

**创建待办事项**

为了在 Redis 中创建一个`todo`，我们使用一个计数器作为 todo `id`。Redis 允许您使用`INCR`(和相关命令)将`String`用作原子计数器

```
id, err := c.Incr(todoIDCounter).Result()
```

> *检查* `*INCR*` *命令为参考*[*https://redis.io/commands/incr*](https://redis.io/commands/incr)

Redis `SET`中的这个递增 ID(前置`todo:`)。Redis `Set`是`Strings`的无序集合，不允许重复。这个`id`构成了`HASH`(下一步)的名称，我们将在其中存储`todo`细节，例如，对于 id 为 42 的`todo`，细节将存储在名为`todo:42`的`HASH`中。这使得`list`、`update`和`delete`待办事项变得很容易

```
err = c.SAdd(todoIDsSet, todoid).Err()
```

> *查看* `*SADD*` *了解详情*[*https://redis.io/commands/sadd*](https://redis.io/commands/sadd)

最后，将`todo`信息(`id`、`description`、`status`)存储在`HASH`中。Redis 是字符串字段和字符串值之间的映射。在这种情况下，这使得它们适合存储对象表示，如`todo` info

```
todo := map[string]interface{}{"desc": desc, "status": statusPending}
err = c.HMSet(todoid, todo).Err()
```

> *检查* `*HMSET*` *参考*[*https://redis.io/commands/hmset*](https://redis.io/commands/hmset)

**列表待办事项**

要获取 todos，我们只需获取集合中的所有成员，即`todo:1`、`todo:2`等。

```
todoHashNames, err := c.SMembers(todoIDsSet).Result()
```

> *查* `*SMEMBERS*` *作参考*[*https://redis.io/commands/smembers*](https://redis.io/commands/smembers)

我们循环遍历这些，搜索每个`HASH`，提取信息(`id`、`description`、`status`)，创建一个`db.Todo`片段，最终以表格形式显示在 CLI 应用程序中

```
for _, todoHashName := range todoHashNames {
	id := strings.Split(todoHashName, ":")[1]
	todoMap, err := c.HGetAll(todoHashName).Result()
    ....
    todo = Todo{id, todoMap["desc"], todoMap["status"]}
    ....
    todos = append(todos, todo)
....
```

> *检查* `*HGETALL*` *参考*[*https://redis.io/commands/hgetall*](https://redis.io/commands/hgetall)

**删除待办事项**

要删除待办事项，包含信息的`HASH`将被删除

```
c.Del("todo:" + id).Result()
```

> *检查【https://redis.io/commands/del】*中的 [*中的`*DEL*`*](https://redis.io/commands/del)

*随后从`SET`中移除`todo` id 条目*

```
*err = c.SRem(todoIDsSet, "todo:"+id).Err()*
```

> **检查* `*SREM*` *参考*[*https://redis.io/commands/srem*](https://redis.io/commands/srem)*

***更新待办事项***

*为了通过 id 更新一个`todo`，我们首先需要确认它是否是一个有效的`id`。为此，我们需要做的就是检查`SET`是否包含那个`todo`*

```
*exists, err := c.SIsMember(todoIDsSet, "todo:"+id).Result()*
```

*如果是的话，我们可以继续更新它的信息。我们用用户传入的新的`status`、`description`(或两者)创建一个`map`，并调用`HMSet`函数*

```
*....
	updatedTodo := map[string]interface{}{}
	if status != "" {
		updatedTodo["status"] = status
	} if desc != "" {
		updatedTodo["desc"] = desc
	}
    c.HMSet("todo:"+id, updatedTodo).Err()
    ....*
```

> **查看* `*HMSET*` *参考*[*https://redis.io/commands/hmset*](https://redis.io/commands/hmset)*

*这个由两部分组成的博客系列到此结束。一如既往，敬请期待更多！如果觉得这有用，别忘了喜欢和分享😃很高兴通过 [Twitter](https://twitter.com/abhi_tweeter) 获得您的反馈，或者发表评论🙏🏻*
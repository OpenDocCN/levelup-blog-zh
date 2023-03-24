# 使用 Go 创建一个最小的 RabbitMQ 客户端

> 原文：<https://levelup.gitconnected.com/creating-a-minimal-rabbitmq-client-using-go-cbcec1470950>

![](img/4d4d1dbd5d85872f51e2c58447c1c8e3.png)

照片由 [Fotis Fotopoulos](https://unsplash.com/@ffstop?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Go 是一种开源编程语言，可以轻松构建简单、可靠、高效的软件。RabbitMQ 是一个开源的消息代理软件，它最初实现了高级消息队列协议，后来用一个插件架构进行了扩展，以支持面向流文本的消息协议、MQ 遥测传输和其他协议。

> *在本教程中，我们将创建一个最小的 RabbitMQ 客户端，它允许其他包使用 RabbitMQ 或向 rabbit MQ 发布消息。最终的源代码可以在我的*[***GitHub***](https://github.com/mehrdadep/rmq)*上找到。*

# 先决条件

*   我假设你已经安装了`Go`。如果不是，在这里检查[和](https://golang.org/doc/install)。
*   你需要了解一点关于[围棋模块](https://blog.golang.org/using-go-modules)的知识。我们将使用模块作为我们的依赖管理解决方案。
*   您需要对 RabbitMQ 的工作原理有所了解。我们不打算涵盖消费和发布消息的每个方面和类型。

# 第一步

首先，您需要用您的模块名创建一个目录。我准备把它命名为`rmq`。然后使用`go mod init`命令初始化 go 模块。现在，您必须使用`go get github.com/streadway/amqp.`获得主依赖项

现在在你的模块的根目录下创建一个`main.go`文件。这个文件是所有事情开始的地方(也可能是结束的地方)。您的主文件应该是这样的:

```
package rmq
```

现在让我们创建一个名为`RabbitClient`的定制类型。我们将在这个客户端中分别添加消费者和发布者通道以及连接。

```
*type* RabbitClient *struct* {
   sendConn *amqp.Connection
   recConn  *amqp.Connection
   sendChan *amqp.Channel
   recChan  *amqp.Channel
}
```

# 连接和创建频道

在这一部分中，我们将为我们的自定义类型添加两个私有方法，尝试连接到 RabbitMQ，然后基于连接类型(消费者|发布者)和重新连接(如果已经存在，尝试重新连接)创建一个通道。

`connect`方法接收两个布尔参数来了解连接类型和重新连接模式。我们假设您已经在一个名为`config`的自定义类型中拥有了关于 RabbitMQ 服务的信息(用户名、密码、主机和端口)。

```
// Create a connection to rabbitmq
func (rcl *RabbitClient) connect(isRec, reconnect bool) (*amqp.Connection, error) {
 if reconnect {
  if isRec {
   rcl.recConn = nil
  } else {
   rcl.sendConn = nil
  }
 }
 if isRec && rcl.recConn != nil {
  return rcl.recConn, nil
 } else if !isRec && rcl.sendConn != nil {
  return rcl.sendConn, nil
 }
 var c string
 if config.Username == "" {
  c = fmt.Sprintf("amqp://%s:%s/", config.Host, config.Port)
 } else {
  c = fmt.Sprintf("amqp://%s:%s@%s:%s/", config.Username, config.Password, config.Host, config.Port)
 }
 conn, err := amqp.Dial(c)
 if err != nil {
  log.Printf("\r\n--- could not create a conection ---\r\n")
  time.Sleep(1 * time.Second)
  return nil, err
 }
 if isRec {
  rcl.recConn = conn
  return rcl.recConn, nil
 } else {
  rcl.sendConn = conn
  return rcl.sendConn, nil
 }
}
```

与`connect`方法相同，`channel`方法接收两个布尔参数来了解连接类型和重新连接模式。这个方法永远尝试连接到 RabbitMQ 服务，然后根据连接类型创建一个通道。

```
*func* (rcl *RabbitClient) channel(isRec, recreate bool) (*amqp.Channel, error) {
   *if* recreate {
      *if* isRec {
         rcl.recChan = *nil* } *else* {
         rcl.sendChan = *nil* }
   }
   *if* isRec && rcl.recConn == *nil* {
      rcl.recChan = *nil* }
   *if* !isRec && rcl.sendConn == *nil* {
      rcl.recChan = *nil* }
   *if* isRec && rcl.recChan != *nil* {
      *return* rcl.recChan, *nil* } *else if* !isRec && rcl.sendChan != *nil* {
      *return* rcl.sendChan, *nil* }
   *for* {
      _, err := rcl.connect(isRec, recreate)
      *if* err == *nil* {
         *break* }
   }
   *var* err error
   *if* isRec {
      rcl.recChan, err = rcl.recConn.Channel()
   } *else* {
      rcl.sendChan, err = rcl.sendConn.Channel()
   }
   *if* err != *nil* {
      log.Println("--- could not create channel ---")
      time.Sleep(1 * time.*Second*)
      *return nil*, err
   }
   *if* isRec {
      *return* rcl.recChan, err
   } *else* {
      *return* rcl.sendChan, err
   }
}
```

既然我们能够连接和创建通道，那么让我们开始消费和发布消息。我们将声明在消费和发布模式下都持久的惰性模式队列。你可以把它改成任何适合你的问题的。

# 让我们消费一些东西

`Consume`方法接收两个参数，一个是队列的名称，另一个是处理被消费消息体的函数。我们将根据该函数的结果执行`ack|nack`。

```
// Consume based on name of the queue
func (rcl *RabbitClient) Consume(n string, f func(interface{}) error) {
 for {
  for {
   _, err := rcl.channel(true, true)
   if err == nil {
    break
   }
  }
  log.Printf("--- connected to consume '%s' ---\r\n", n)
  q, err := rcl.recChan.QueueDeclare(
   n,
   true,
   false,
   false,
   false,
   amqp.Table{"x-queue-mode": "lazy"},
  )
  if err != nil {
   log.Println("--- failed to declare a queue, trying to reconnect ---")
   continue
  }
  connClose := rcl.recConn.NotifyClose(make(chan *amqp.Error))
  connBlocked := rcl.recConn.NotifyBlocked(make(chan amqp.Blocking))
  chClose := rcl.recChan.NotifyClose(make(chan *amqp.Error))
  m, err := rcl.recChan.Consume(
   q.Name,
   uuid.NewV4().String(),
   false,
   false,
   false,
   false,
   nil,
  )
  if err != nil {
   log.Println("--- failed to consume from queue, trying again ---")
   continue
  }
  shouldBreak := false
  for {
   if shouldBreak {
    break
   }
   select {
   case _ = <-connBlocked:
    log.Println("--- connection blocked ---")
    shouldBreak = true
    break
   case err = <-connClose:
    log.Println("--- connection closed ---")
    shouldBreak = true
    break
   case err = <-chClose:
    log.Println("--- channel closed ---")
    shouldBreak = true
    break
   case d := <-m:
    err := f(d.Body)
    if err != nil {
     _ = d.Ack(false)
     break
    }
    _ = d.Ack(true)
   }
  }
 }
}
```

`Consume`方法处理来自连接和通道的`NotifyClose`、`NotifyBlocked`和`NotifyClose`，并在需要时尝试重新连接或重新创建它们。

# 我们发表点什么吧

`Publish`方法接收三个参数，一个是队列名，另一个是字节数组，包含消息体。

```
// Publish an array of bytes to a queue
func (rcl *RabbitClient) Publish(n string, b []byte) { r := false
 for {
  for {
   _, err := rcl.channel(false, r)
   if err == nil {
    break
   }
  }
  q, err := rcl.sendChan.QueueDeclare(
   n,
   true,
   false,
   false,
   false,
   amqp.Table{"x-queue-mode": "lazy"},
  )
  if err != nil {
   log.Println("--- failed to declare a queue, trying to resend ---")
   r = true
   continue
  }
  err = rcl.sendChan.Publish(
   "",
   q.Name,
   false,
   false,
   amqp.Publishing{
    MessageId:    uuid.NewV4().String(),
    DeliveryMode: amqp.Persistent,
    ContentType:  "text/plain",
    Body:         b,
   })
  if err != nil {
   log.Println("--- failed to publish to queue, trying to resend ---")
   r = true
   continue
  }
  break
 }
}
```

如果需要，该方法处理信道的重新连接或重建。

# 使用

创建一个`RabbitClient`类型的实例，并使用`Consume`或`Publish`方法。

```
*var* rc rmq.RabbitClientrc.Consume("test-queue", funcName)rc.Publish("test-queue", mBody)
```

# 我没有提到的是

*   不同类型的队列检测
*   使用交换
*   单元测试
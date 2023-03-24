# 使用 Golang 和 Logrus 登录 GCP 的 4 个技巧

> 原文：<https://levelup.gitconnected.com/4-tips-for-logging-on-gcp-using-golang-and-logrus-239baf3b1ac2>

![](img/c64cbf9a3ad6dd6c766c4c49e44c6e62.png)

日志记录是一个重要的部分，并且有着为不同类型的应用程序开发的悠久历史。软件开发从各个方面都变化很快。日志记录也不例外。过去，企业应用程序开发人员通常主要关注输出位置、旋转和翻转。如今，随着我们向云部署的转移，日志也有了新的标准。

根据十二要素应用的[，云原生应用应该以事件流的形式记录，并且从不关心路由或存储。该日志仍将由云提供商或另一个进程在其他地方处理。因此，登录云原生应用更关注遵循行业标准分发事件。](https://12factor.net/)

以下是我使用 Golang 和 [Logrus](https://github.com/sirupsen/logrus) 库登录 GCP Kubernetes 的技巧。

# 为您的消息添加上下文

如果我们正在对一个问题进行故障诊断，但是在目标部分没有找到日志，这将是非常令人沮丧的。你知道更令人沮丧的是什么吗？这是当我们有一个日志，但日志实际上并没有给你任何有价值的信息。没有比这种消息更糟糕的了:

```
Transaction failed
```

或者

```
Data validation error
```

没有适当的上下文，这些消息没有多少价值。就故障排除而言，以下消息更有意义:

```
Transaction failed: insufficient funds
```

或者

```
Data validation error: invalid email address
```

或者

```
Transaction 123456 was successfully, amount transferred: $42,394
```

我们可以稍微改进一下这些信息。日志不仅是人类的，也是机器的。在数据世界中，日志可以是自动处理的输入源(例如监控和警报)。您的日志不仅应该可读，而且应该易于解析。如果你正在实现一个解析服务，下面的日志格式会节省你很多时间:

```
2020-01-31 13:42:12,471 INFO Processing payment user_id=37162 transaction_id=38271 amount=32371 currency=USD
```

或者，您甚至可以用 JSON 格式登录:

```
{"time": "2020-01-31 13:42:12,471", "level": info, "msg": "Processing payment", "user_id": 37416, "transaction_id": 957126, "amount":3000, "currency": "USD"}
```

要对此进行存档，您应该将上下文数据与日志消息分开附加，而不是自己格式化日志。日志格式化程序将处理剩下的工作。以下是关于如何使用`logrus`实现这一点的示例:

```
package mainimport (
 log "github.com/sirupsen/logrus"
)func main() {
 ... 

 log.WithError(err).Error("Transaction failed")
 log.WithField("transaction", transaction).Debug("Transaction processed successfully")
 log.WithFields(log.Fields{
  "user_id": 2384172,
  "transaction_id": 9823183,
  "amount": 5000,
  "currency": "USD",
 }).Info("Transaction processed successfully")
 ...
}
```

上面的代码乍一看似乎非常长。但是一旦你熟悉了，附加上下文数据是非常有帮助的，而且更加灵活。您还可以用初始上下文数据创建一个 logger 对象。来自该记录器的所有日志将包含以下信息:

```
logger := log.WithFields(log.Fields{
 "user_id": 2384172,
 "transaction_id": 9823183,
 "amount": 5000,
 "currency": "USD",
})

...logger.Info("Start transaction")
logger.WithError(err).Error("Transaction failed")
```

# 记录到标准输出和标准错误

许多云提供商让他们的容器引擎捕获所包含的应用程序的日志输出管道(`stdout`、`stderr`)。容器将您的消息包装到一个新的日志条目中。新日志条目的级别取决于原始日志来自的管道。如果是从`stdout`收到的，新日志将是`INFO`。否则，新的日志将会是`ERROR`。容器日志对于故障排除非常有帮助，尤其是当您想要查看组合日志时，包括来自云提供商或其他服务的外部日志。

默认情况下，`logrus`将所有日志写入`stderr`。这意味着你所有的日志都将出现在`ERROR`级别的容器日志中。在 GCP·库伯内特网上，所有的日志都会用红色突出显示。它不仅令人困惑，而且还会禁用颜色过滤器，这有助于我们快速扫描真正的错误。迟早，我们需要将日志流式传输到正确的管道。比方说，所有等于或高于`WARN`级别的日志都将被传输到`stderr`。其他日志将转到`stdout`。一种解决方案是为`logrus`设置自定义输出:

```
import (
 "os"
 "regexp"log "github.com/sirupsen/logrus"
)type LogWriter struct{}var levelRegex *regexp.Regexpconst (
 LevelError   = "error"
 LevelWarning = "warning"
 LevelFatal   = "fatal"
 LevelPanic   = "panic"
)func init() {
 var err error
 levelRegex, err = regexp.Compile("level=([a-z]+)")
 if err != nil {
  log.WithError(err).Fatal("Cannot setup log level")
 }
}func (w *LogWriter) detectLogLevel(p []byte) (level string) {
 matches := levelRegex.FindStringSubmatch(string(p))
 if len(matches) > 1 {
  level = matches[1]
 }
 return
}func (w *LogWriter) Write(p []byte) (n int, err error) {
 level := w.detectLogLevel(p)if level == LevelError || level == LevelWarning || level == LevelFatal || level == LevelPanic {
  return os.Stderr.Write(p)
 }
 return os.Stdout.Write(p)
}...func main() {
 log.SetOutput(&LogWriter{})
}
```

# 记录全局默认字段

如果您将日志从几个服务或实例流式传输到一个目的地(例如堆栈驱动程序)，您将很快需要添加一些额外的字段用于过滤目的。对于我的项目，我需要向所有条目添加服务名称、流程 ID 和环境(登台、生产)。为了在`logrus`中存档这个，我添加了一个自定义钩子。钩子是`logrus`的一个特性，当用户写日志时触发一些动作。这个特性非常强大，尤其是在云世界中，我们有许多可能的日志用例(添加数据、流向云、报告错误、警报等。).

```
import (
 "os""github.com/sirupsen/logrus"
)type ExtraFieldHook struct {
 service string
 env     string
 pid     int
}func NewExtraFieldHook(service string, env string) *ExtraFieldHook {
 return &ExtraFieldHook{
  service: service,
  env:     env,
  pid:     os.Getpid(),
 }
}func (h *ExtraFieldHook) Levels() []logrus.Level {
 return logrus.AllLevels
}func (h *ExtraFieldHook) Fire(entry *logrus.Entry) error {
 entry.Data["service"] = h.service
 entry.Data["env"] = h.env
 entry.Data["pid"] = h.pid
 return nil
}...func main() {
 log.AddHook(NewExtraFieldHook(service, config.Env))
 ...
}
```

# 记录到堆栈驱动程序并报告错误

将所有服务的所有日志流式传输到一个位置将有助于您集中数据，以便于监控和审计，尤其是当您的服务部署在不同的位置时。

`logrus`有[几个挂钩](https://github.com/sirupsen/logrus/wiki/Hooks)用于不同的第三方。不幸的是，用于堆栈驱动程序的那个似乎不起作用。通过深入研究它的代码，我发现钩子没有使用官方文档中的栈驱动库。它看起来像一个堆栈驱动 API 的遗留版本。因此，我想出了自己的版本:

*   所有日志都将传输到堆栈驱动程序
*   所有具有`error`或更高级别的日志将被报告给堆栈驱动程序的错误报告
*   从错误的堆栈跟踪中删除`logrus`的堆栈。这是为了防止错误报告从[将你的错误组合成一个](https://cloud.google.com/error-reporting/docs/grouping-errors)

```
import (
 "context"
 "encoding/json"
 "fmt"
 "log"
 "regexp"
 "runtime""cloud.google.com/go/errorreporting"
 "cloud.google.com/go/logging"
 "github.com/sirupsen/logrus"
)type StackDriverHook struct {
 client      *logging.Client
 errorClient *errorreporting.Client
 logger      *logging.Logger
}var logLevelMappings = map[logrus.Level]logging.Severity{
 logrus.TraceLevel: logging.Default,
 logrus.DebugLevel: logging.Debug,
 logrus.InfoLevel:  logging.Info,
 logrus.WarnLevel:  logging.Warning,
 logrus.ErrorLevel: logging.Error,
 logrus.FatalLevel: logging.Critical,
 logrus.PanicLevel: logging.Critical,
}func NewStackDriverHook(logName string) (*StackDriverHook, error) {
 ctx := context.Background()client, err := logging.NewClient(ctx, config.GCPProjectID)
 if err != nil {
  return nil, err
 }errorClient, err := errorreporting.NewClient(ctx, config.GCPProjectID, errorreporting.Config{
  ServiceName: config.GCPProjectID,
  OnError: func(err error) {
   log.Printf("Could not log error: %v", err)
  },
 })
 if err != nil {
  return nil, err
 }return &StackDriverHook{
  client:      client,
  errorClient: errorClient,
  logger:      client.Logger(logName),
 }, nil
}func (sh *StackDriverHook) Close() {
 sh.client.Close()
 sh.errorClient.Close()
}func (sh *StackDriverHook) Levels() []logrus.Level {
 return logrus.AllLevels
}func (sh *StackDriverHook) Fire(entry *logrus.Entry) error {
 payload := map[string]interface{}{
  "Message": entry.Message,
  "Data":    entry.Data,
 }
 level := logLevelMappings[entry.Level]
 sh.logger.Log(logging.Entry{Payload: payload, Severity: level})
 if int(level) >= int(logging.Error) {
  err := getError(entry)
  if err == nil {
   errData, e := json.Marshal(payload)
   if e != nil {
    fmt.Printf("Error %v", e)
   }
   fmt.Print(string(errData))
   err = fmt.Errorf(string(errData))
  }
  fmt.Println(err.Error())sh.errorClient.Report(errorreporting.Entry{
   Error: err,
   Stack: sh.getStackTrace(),
  })
 }
 return nil
}func (sh *StackDriverHook) getStackTrace() []byte {
 stackSlice := make([]byte, 2048)
 length := runtime.Stack(stackSlice, false)
 stack := string(stackSlice[0:length])
 re := regexp.MustCompile("[\r\n].*logrus.*")
 res := re.ReplaceAllString(stack, "")
 return []byte(res)
}type stackDriverError struct {
 Err         interface{}
 Code        interface{}
 Description interface{}
 Message     interface{}
 Env         interface{}
}func (e stackDriverError) Error() string {
 return fmt.Sprintf("%v - %v - %v - %v - %v", e.Code, e.Description, e.Message, e.Err, e.Env)
}func getError(entry *logrus.Entry) error {
 errData := entry.Data["error"]
 env := entry.Data["env"]
 code := entry.Data["ErrCode"]
 desc := entry.Data["ErrDescription"]
 msg := entry.Messageerr := stackDriverError{
  Err:         errData,
  Code:        code,
  Message:     msg,
  Description: desc,
  Env:         env,
 }return err
}func (sh *StackDriverHook) Wait() {}
```

# 结论

以上是我在与戈朗、`logrus`和 GCP 合作过程中发现的一些记录技巧。其他库或编程语言的详细实现可能会有所不同，但我希望主要思想对您仍然有价值，尤其是当您将应用程序部署到 GCP 时:

*   日志消息应该包含上下文数据，并且应该对人和机器都友好
*   注意您的日志输出流，它可能会影响您的容器日志
*   使用钩子为每个日志条目增加额外的步骤。它的使用案例非常广泛。

*原发布于*[*https://huynvk . dev*](https://huynvk.dev/blog/4-tips-for-logging-on-gcp-using-golang-and-logrus)*。*
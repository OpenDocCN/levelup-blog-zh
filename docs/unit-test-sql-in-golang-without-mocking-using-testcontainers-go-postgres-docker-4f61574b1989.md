# 使用 TestContainers (Go，Postgres，Docker，TestContainers)在 Golang 中对 SQL 进行单元测试，没有模仿

> 原文：<https://levelup.gitconnected.com/unit-test-sql-in-golang-without-mocking-using-testcontainers-go-postgres-docker-4f61574b1989>

![](img/1270b231cbe03e34313ba808eaa07344.png)

我写了另一篇关于同一主题的文章，利用一个纯 Docker 解决方案来测试数据库代码，而没有嘲笑。这是一个很酷的解决方案，而且效果很好，但是有点笨拙。它需要特殊的脚本、特定的文件夹结构，有时很难理解。尽管如此，它给了我想要的东西，没有嘲笑我的数据库测试！你可以在这里阅读那篇文章:[用 Golang 单元测试 SQL，不带嘲讽，使用 Docker (Go，Postgres，Docker)](/unit-test-sql-in-golang-without-mocking-using-docker-go-postgres-docker-50e99b9d38a7)

有人在那篇文章上发帖，建议我研究一下 [TestContainers](https://github.com/testcontainers) 来实现同样的目标。从他们的网站:

> Testcontainers 是一个库，它提供了一个友好的 API 来运行 Docker 容器。它被设计用来创建在你的自动测试中使用的运行时环境。

嗯，这听起来很像我在上一篇文章中试图做的事情。所以在这篇文章中，我们将采用相同的代码/回购，并用 TestContainers 重新工作，看看我们是否可以测试我们所有的数据库代码，没有模仿！我们走吧！

# 为什么要去掉仿制品，它们很棒？！

首先，我想说，我并不主张完全摆脱模仿。它们绝对在测试中占有一席之地，并且在很多事情上派上用场。对我来说，对于你无法控制的资源，模仿是至关重要的。例如，以 Twilio 这样的服务为例。如果您需要调用 Twilio 并处理来自它们的 API 的响应，这是一个很好的例子，说明了模仿的重要性。您无法控制它们的 API，您只是简单地发出一个调用并处理响应。通常，如果您在 CI/CD 管道中运行，您可能无法进行这些调用，也可能不只是想进行这些调用。这里模仿是完美的，因为您并不真正关心对 API 的调用，只是为了确保您的代码能够处理响应。

说到在 Golang 代码中嘲笑数据库交互，老实说，在我看来这是一次悲惨的经历。通常，您最终会为这些代码编写大量的接口，然后可以在您的测试中模拟出来。您的测试以大量样板代码和跨多个文件重复的 SQL 查询而告终。有一些软件包可以帮助解决这个问题，甚至消除了对接口的需求，但是你仍然要写很多样板代码，现在需要训练人们如何使用另一个软件包。以下是一些例子:

*   [https://medium . com/easy read/unit-test-SQL-in-golang-5af 19075 e68e](https://medium.com/easyread/unit-test-sql-in-golang-5af19075e68e)
*   https://dev.to/pieohpah/mocking-database-in-go-55bo
*   【https://github.com/DATA-DOG/go-sqlmock 

我想要的是在真实的数据库上运行我的测试，完全消除对模拟的需求。目标是提供以下内容:

*   编写没有接口和嘲讽的常规测试
*   针对实际数据库进行测试，该数据库应用了最新的模式并植入了测试数据
*   不仅要测试我的数据库代码，还要测试任何公开该数据的 API 处理程序
*   能够在真实的 CI/CD 管道中使用该流程

# 设置

我创建了一个非常简单的 [Go API](https://github.com/atkinsonbg/go-gmux-db-testcontainers) ，它可以处理包含时区信息的数据库。这里没有什么过于复杂的，只有两个 get 和一个 POST 用于插入。数据库同样简单，一个存储时区信息的表。让我们先来看看数据库的表模式。我有一个非常简单的`./scripts/init.sql`脚本，它创建了一个表并用一些数据作为种子:

```
-- SCHEMA
CREATE TABLE timezones
(
id serial PRIMARY KEY,
created timestamptz DEFAULT now() NOT NULL,
modified timestamptz DEFAULT now() NOT NULL,
name text NOT NULL,
timeoffset smallint NOT NULL,
identifier text NOT NULL
);
-- SEED
INSERT INTO timezones
(id, name, timeoffset, identifier)
VALUES
(1, 'eastern', '-5', 'est');
INSERT INTO timezones
(id, name, timeoffset, identifier)
VALUES
(2, 'central', '-6', 'cst');
INSERT INTO timezones
(id, name, timeoffset, identifier)
VALUES
(3, 'mountain', '-7', 'mst');
INSERT INTO timezones
(id, name, timeoffset, identifier)
VALUES
(4, 'pacific', '-8', 'pst');
INSERT INTO timezones
(id, name, timeoffset, identifier)
VALUES
(5, 'alaska', '-9', 'ast');
ALTER SEQUENCE timezones_id_seq RESTART WITH 6;
```

在我的 Go API 中，我在我的`main.go`文件中定义了四条路线:

```
func main() { 
  database.InitDB()   r := mux.NewRouter()              r.HandleFunc("/healthcheck", handlers.HealthHandler).Methods("GET")     r.HandleFunc("/timezones", handlers.ListTimezonesHandler).Methods("GET")     r.HandleFunc("/timezones/{identifier}", handlers.GetTimezoneHandler).Methods("GET")     r.HandleFunc("/timezones", handlers.InsertTimezoneHandler).Methods("POST")     log.Fatal(http.ListenAndServe(":80", r))
}
```

让我们快速分析一下这些路由，以了解数据库交互的水平设置:

*   /healthcheck:简单的健康检查，以确保我的容器正在运行
*   /timezones: GET 返回数据库中的所有时区
*   /timezones/{identifier}: GET 根据标识符从数据库中返回单个时区。在这种情况下:东部时间、中部时间、太平洋标准时间等。
*   /timezones: POST 以插入新时区

我将我的处理程序和数据库代码分开，以便于测试，这就是你对这样一个简单实现的期望。

**处理程序(时区获取)**

```
// ListTimezonesHandler lists all the timezones in the databasefunc ListTimezonesHandler(w http.ResponseWriter, r *http.Request) 
{ 
  timezones, err := database.GetAllTimezones() 
  if err != nil {  
      w.WriteHeader(http.StatusInternalServerError)  
      return 
  }  
  results, _ := json.Marshal(timezones)  
  w.WriteHeader(http.StatusOK) 
  w.Write(results)
}
```

**数据库(时区获取)**

```
// GetAllTimezones returns all timezones from the DB
func GetAllTimezones() ([]Timezone, error) {
  rows, err := db.Query("SELECT * FROM timezones")
  if err != nil {
    log.Fatal(err)
    return nil, err
  }
  var timezones []Timezone
  defer rows.Close()
  for rows.Next() {
    var timezone Timezone
    err := rows.Scan(&timezone.ID, &timezone.Created,        &timezone.Modified, &timezone.Name, &timezone.Timeoffset, &timezone.Identifier)
    if err != nil {
      log.Print(err)
    }
    timezones = append(timezones, timezone)
  }
  return timezones, nil
}
```

在本地运行时，我通过 Docker Compose 将所有这些连接起来，这样我就可以将我的 API 和本地 Postgres 容器一起运行，并通过 Postman 调用轻松测试我的代码:

```
version: "3.7"
services:
  api:
    container_name: go-mux-api
    image: 
      github.com/atkinsonbg/go-gmux-db-testcontainers/api:latest
    environment:
      DBHOST: go-mux-db
      DBNAME: go-mux-db
      DBUSER: postgres
    ports:
      - 80:80
  database:
    container_name: go-mux-db
    image: postgres:11.6-alpine
    environment:
      POSTGRES_DB: go-mux-db
      volumes:
        - ./scripts/init.sql:/docker-entrypoint-initdb.d/init.sql
      ports:
        - 5432:5432
```

Docker Compose 在这种情况下非常有用，因为我可以快速旋转一个配置为满足我的需求的 DB，将其拆下，然后再旋转回来。如果我需要更复杂的测试，我可以分别启动数据库和 API，并使用[远程容器](/debugging-go-inside-docker-using-visual-studio-code-and-remote-containers-5c3724fe87b9)进行测试。Docker 在这里是一个救星，因为它对于我的测试用例非常灵活。更重要的是，当我们用真实的数据库进行测试时。

# 测试

在深入讨论之前，我应该指出这段代码中使用的以下环境变量:

```
DBHOST=localhostDBNAME=postgresTCDBUSER=postgresDBPORT=5432
```

在我的 repo 中，这些是通过 Makefile 的“test”命令注入的，并在整个代码中使用。您将在下面的测试代码中看到它们。

正如我之前所说的，我不想嘲笑我的测试。我想针对我的数据库代码和处理程序编写普通的 Golang 测试。TestContainers 包允许我非常容易地做到这一点。基本上，它允许您启动一个数据库容器，在我们的场景中是 Postgres，供您在测试中使用。完成后，您可以将其拆除，这样在测试期间您就有了一个临时数据库！

下面的代码展示了它的样子。我们将首先查看这两个测试文件，然后分析它们内部的情况:

**处理程序测试(时区获取)**

```
package handlersimport (
 "bytes"
 "context"
 "encoding/json"
 "fmt"
 "net/http"
 "net/http/httptest"
 "os"
 "strings"
 "testing" db "github.com/atkinsonbg/go-gmux-db-testcontainers/database"
 "github.com/gorilla/mux"
 "github.com/testcontainers/testcontainers-go"
 "github.com/testcontainers/testcontainers-go/wait"
)func TestMain(m *testing.M) {
 // Work out the path to the 'scripts' directory and set mount strings
 packageName := "handlers"
 workingDir, _ := os.Getwd()
 rootDir := strings.Replace(workingDir, packageName, "", 1)
 mountFrom := fmt.Sprintf("%s/scripts/init.sql", rootDir)
 mountTo := "/docker-entrypoint-initdb.d/init.sql"// Create the Postgres TestContainer
 ctx := context.Background()
 req := testcontainers.ContainerRequest{
  Image:        "postgres:11.6-alpine",
  ExposedPorts: []string{"5432/tcp"},
  BindMounts:   map[string]string{mountFrom: mountTo},
  Env: map[string]string{
   "POSTGRES_DB": os.Getenv("DBNAME"),
  },
  WaitingFor: wait.ForLog("database system is ready to accept connections"),
 }postgresC, err := testcontainers.GenericContainer(ctx, testcontainers.GenericContainerRequest{
  ContainerRequest: req,
  Started:          true,
 })
 if err != nil {
  // Panic and fail since there isn't much we can do if the container doesn't start
  panic(err)
 }defer postgresC.Terminate(ctx)// Get the port mapped to 5432 and set as ENV
 p, _ := postgresC.MappedPort(ctx, "5432")
 os.Setenv("DBPORT", p.Port())db.InitDB()exitVal := m.Run()
 os.Exit(exitVal)
}func TestListTimezonesHandler(t *testing.T) {
 req, err := http.NewRequest("GET", "/timezones", nil)
 if err != nil {
  t.Fatal(err)
 }rr := httptest.NewRecorder()
 handler := http.HandlerFunc(ListTimezonesHandler)handler.ServeHTTP(rr, req)if rr.Code != http.StatusOK {
  t.Errorf("handler returned wrong status code: got %v want %v",
   rr.Code, http.StatusOK)
 }var timezones []db.Timezone
 err = json.NewDecoder(rr.Body).Decode(&timezones)
 if err != nil {
  t.Error(err.Error())
  t.Error("Error retreiving list of timezones.")
 }if len(timezones) == 0 {
  t.Error("Error retreiving list of timezones.")
 }
}func TestGetTimezoneHandler(t *testing.T) {
 req, err := http.NewRequest("GET", "/timezones/est", nil)
 if err != nil {
  t.Fatal(err)
 }rr := httptest.NewRecorder()
 router := mux.NewRouter()
 router.HandleFunc("/timezones/{identifier}", GetTimezoneHandler)
 router.ServeHTTP(rr, req)if rr.Code != http.StatusOK {
  t.Errorf("handler returned wrong status code: got %v want %v",
   rr.Code, http.StatusOK)
 }var timezone db.Timezone
 err = json.NewDecoder(rr.Body).Decode(&timezone)
 if err != nil {
  t.Error(err.Error())
  t.Error("Error retreiving specific of timezone.")
 }if timezone.Name != "eastern" {
  t.Error("Error retreiving specific of timezone.")
 }
}func TestInsertTimezoneHandler(t *testing.T) {
 var data = []byte(`
 {
  "name": "xyz",
  "timeoffset": -10,
  "identifier": "xyz"
 }`)b := bytes.NewBuffer(data)req, err := http.NewRequest("POST", "/timezones", b)
 if err != nil {
  t.Fatal(err)
 }req.Header.Add("Content-Type", "application/json")rr := httptest.NewRecorder()
 handler := http.HandlerFunc(InsertTimezoneHandler)handler.ServeHTTP(rr, req)if rr.Code != http.StatusOK {
  t.Errorf("handler returned wrong status code: got %v want %v",
   rr.Code, http.StatusOK)
 }
}
```

这段代码利用了 Go 中的`[net/http/httptest](https://golang.org/pkg/net/http/httptest/)`包，它允许我从测试中轻松地调用我的 API 端点。

**数据库测试(时区获取)**

```
package databaseimport (
 "context"
 "fmt"
 "os"
 "strings"
 "testing""github.com/testcontainers/testcontainers-go"
 "github.com/testcontainers/testcontainers-go/wait"
)func TestMain(m *testing.M) {
 // Work out the path to the 'scripts' directory and set mount strings
 packageName := "database"
 workingDir, _ := os.Getwd()
 rootDir := strings.Replace(workingDir, packageName, "", 1)
 mountFrom := fmt.Sprintf("%s/scripts/init.sql", rootDir)
 mountTo := "/docker-entrypoint-initdb.d/init.sql"// Create the Postgres TestContainer
 ctx := context.Background()
 req := testcontainers.ContainerRequest{
  Image:        "postgres:11.6-alpine",
  ExposedPorts: []string{"5432/tcp"},
  BindMounts:   map[string]string{mountFrom: mountTo},
  Env: map[string]string{
   "POSTGRES_DB": os.Getenv("DBNAME"),
  },
  WaitingFor: wait.ForLog("database system is ready to accept connections"),
 }postgresC, err := testcontainers.GenericContainer(ctx, testcontainers.GenericContainerRequest{
  ContainerRequest: req,
  Started:          true,
 })
 if err != nil {
  // Panic and fail since there isn't much we can do if the container doesn't start
  panic(err)
 }defer postgresC.Terminate(ctx)// Get the port mapped to 5432 and set as ENV
 p, _ := postgresC.MappedPort(ctx, "5432")
 os.Setenv("DBPORT", p.Port())InitDB()exitVal := m.Run()
 os.Exit(exitVal)
}func TestGetAllTimezones(t *testing.T) {
 tzones, err := GetAllTimezones()
 if err != nil {
  t.Error("Get All Timezones failed.")
 }if len(tzones) == 0 {
  t.Error("Timezones did not return any values.")
 }
}func TestGetTimezone(t *testing.T) {
 tzone, err := GetTimezone("est")
 if err != nil {
  t.Error("Get a Timezone failed.")
 }if tzone.Name != "eastern" {
  t.Error("Timezone did not return correct values.")
 }
}func TestInsertTimezone(t *testing.T) {
 tzone := Timezone{}
 tzone.Name = "Test"
 tzone.Timeoffset = 10
 tzone.Identifier = "tst"rowid, err := InsertTimezone(tzone)
 if err != nil {
  t.Error("Insert a Timezone failed.")
 }if rowid != 6 {
  t.Error("Insert timezone failed.")
 }
}
```

在这一点上，你可以看到，我在这里没有使用任何模拟。这是我所能得到的最普通的测试代码，非常棒。它简明扼要。任何曾经编写过 Go 代码的人都可以阅读这段代码并理解它在做什么。没什么特别的，只是做些测试。然而，`TestMain`函数有一些魔力，所以让我们来分解一下:

```
// Work out the path to the 'scripts' directory and set mount strings
 packageName := "database"
 workingDir, _ := os.Getwd()
 rootDir := strings.Replace(workingDir, packageName, "", 1)
 mountFrom := fmt.Sprintf("%s/scripts/init.sql", rootDir)
 mountTo := "/docker-entrypoint-initdb.d/init.sql"
```

我需要做的第一件事是设计出我的挂载路径。如果你还记得在这篇文章的前面，我们有一个`init.sql`文件，它创建了数据库模式并用数据播种它。我们需要将它挂载到 Postgres 映像中的`docker-entrypoint-initdb.d`文件夹中。这是在一个不同的文件夹中，我们将进行绑定挂载，需要一个绝对路径。所以我们得到当前的工作目录路径，然后对它做`strings.Replace`，取出当前的目录名。最后，我们创建两个新的字符串来保存挂载路径。

```
// Create the Postgres TestContainer
 ctx := context.Background()
 req := testcontainers.ContainerRequest{
  Image:        "postgres:11.6-alpine",
  ExposedPorts: []string{"5432/tcp"},
  BindMounts:   map[string]string{mountFrom: mountTo},
  Env: map[string]string{
   "POSTGRES_DB": os.Getenv("DBNAME"),
  },
  WaitingFor: wait.ForLog("database system is ready to accept connections"),
 }postgresC, err := testcontainers.GenericContainer(ctx, testcontainers.GenericContainerRequest{
  ContainerRequest: req,
  Started:          true,
 })
 if err != nil {
  // Panic and fail since there isn't much we can do if the container doesn't start
  panic(err)
 }defer postgresC.Terminate(ctx)
```

一旦我们设计好了挂载路径，我们就创建一个测试容器。这段代码完全是他们网站上的模板，但是你可以看到我们正在做以下事情:

*   基于`postgres:11.6-alpine`图像创建新的容器
*   暴露端口 5432。这将把端口 5432 映射到容器上动态生成的端口。这将在下一节中出现。这样做是为了在工作时最大限度地减少端口冲突。
*   用我们的路径添加绑定挂载。(注意，对我来说这是一个陷阱。我最初试图在这里放入字符串，但是因为我需要当前的工作目录，`$(pwd)`在这里不适合我。更容易得到这些并将它们放入字符串中)
*   为 Postgres 数据库名称设置一个环境变量
*   设置日志消息的“等待”命令，说明数据库已准备就绪。TestContainers 有很多“等待”策略，你可以利用，这非常好！(公平地说，在我的代码中我可能不需要这样做，因为我利用了一个[指数回退](https://github.com/cenkalti/backoff)来测试数据库是否准备好了，然后再做其他事情。)

```
// Get the port mapped to 5432 and set as ENVp, _ := postgresC.MappedPort(ctx, "5432")os.Setenv("DBPORT", p.Port())
```

现在我们已经创建了容器，我们需要弄清楚它映射到哪个端口 5432。我们可以使用`MappedPort`函数做到这一点，并将它设置为我们的`DBPORT`环境变量。

这就是设置！我在每个包中都定义了一个`TestMain`函数，所有的 TestContainer 设置都在里面。之后，我就像平常一样测试，没有模仿！

# 运行测试

现在运行测试会提供以下输出:

```
Users-Air:go-gmux-db-testcontainers user$ make test
go test -v ./... -coverpkg ./...
?       github.com/atkinsonbg/go-gmux-db-testcontainers [no test files]
2020/12/30 16:27:06 Starting container id: c408f3bca264 image: quay.io/testcontainers/ryuk:0.2.3
2020/12/30 16:27:06 Waiting for container id c408f3bca264 image: quay.io/testcontainers/ryuk:0.2.3
2020/12/30 16:27:06 Container is ready id: c408f3bca264 image: quay.io/testcontainers/ryuk:0.2.3
2020/12/30 16:27:07 Starting container id: e8ccb040bfed image: postgres:11.6-alpine
2020/12/30 16:27:07 Waiting for container id e8ccb040bfed image: postgres:11.6-alpine
2020/12/30 16:27:09 Container is ready id: e8ccb040bfed image: postgres:11.6-alpine
2020/12/30 16:27:09 DB is not ready...backing off...
2020/12/30 16:27:09 DB is not ready...backing off...
2020/12/30 16:27:10 DB is ready!
=== RUN   TestConfig
--- PASS: TestConfig (0.00s)
=== RUN   TestGetAllTimezones
--- PASS: TestGetAllTimezones (0.00s)
=== RUN   TestGetTimezone
--- PASS: TestGetTimezone (0.00s)
=== RUN   TestInsertTimezone
--- PASS: TestInsertTimezone (0.00s)
PASS
coverage: 45.6% of statements in ./...
ok      github.com/atkinsonbg/go-gmux-db-testcontainers/database        (cached)        coverage: 45.6% of statements in ./...
2020/12/30 16:27:06 Starting container id: 5d99c0559dea image: quay.io/testcontainers/ryuk:0.2.3
2020/12/30 16:27:06 Waiting for container id 5d99c0559dea image: quay.io/testcontainers/ryuk:0.2.3
2020/12/30 16:27:06 Container is ready id: 5d99c0559dea image: quay.io/testcontainers/ryuk:0.2.3
2020/12/30 16:27:07 Starting container id: 0310b61386ca image: postgres:11.6-alpine
2020/12/30 16:27:07 Waiting for container id 0310b61386ca image: postgres:11.6-alpine
2020/12/30 16:27:09 Container is ready id: 0310b61386ca image: postgres:11.6-alpine
2020/12/30 16:27:09 DB is not ready...backing off...
2020/12/30 16:27:09 DB is ready!
=== RUN   TestHealthHandler
--- PASS: TestHealthHandler (0.00s)
=== RUN   TestListTimezonesHandler
--- PASS: TestListTimezonesHandler (0.00s)
=== RUN   TestGetTimezoneHandler
--- PASS: TestGetTimezoneHandler (0.00s)
=== RUN   TestInsertTimezoneHandler
--- PASS: TestInsertTimezoneHandler (0.00s)
PASS
coverage: 68.4% of statements in ./...
ok      github.com/atkinsonbg/go-gmux-db-testcontainers/handlers        (cached)        coverage: 68.4% of statements in ./...
```

如您所见，测试容器开始运转，我的所有测试都在执行，不需要模拟！

# 包扎

总而言之，我对 TestContainers 非常满意。鉴于我之前在没有模拟的情况下进行测试的尝试，这一关不那么复杂，也更容易编码。也就是说，我很高兴我能够尝试这两个，因为它们都工作得很好。有选择总是好的，所以现在我至少有两个选择！

## 资源

*   GitHub 回购:[https://github.com/atkinsonbg/go-gmux-db-testcontainers](https://github.com/atkinsonbg/go-gmux-db-testcontainers)
*   测试容器 Go:[https://github.com/testcontainers/testcontainers-go](https://github.com/testcontainers/testcontainers-go)
*   TestContainers Go 文档:[https://golang.testcontainers.org/](https://golang.testcontainers.org/)

[![](img/3515ab52cb6fb5e74c27c7a2e06d3811.png)](https://ko-fi.com/O5O63ENS7)
# 使用 Kubernetes 在 Golang 中构建 TODO API

> 原文：<https://levelup.gitconnected.com/building-a-todo-api-in-golang-with-kubernetes-1ec593f85029>

这篇文章的目标读者是 Kubernetes 的新用户，他们希望通过一个实际的例子来学习如何编写一个 Go API 来管理你的待办事项列表，以及如何将它部署到 Kubernetes。

> 最后更新于 2022 年

> 每个开发人员都喜欢好的待办事项列表，对吗？否则我们怎么能完成任何事情。

![](img/4d474f284277417c28e8290125e1bf1b.png)

每个开发人员都喜欢一个好的 TODO 应用程序，对吗？

我们将从材料清单开始，然后继续配置 Kubernetes，提供 Postgresql 数据库，然后安装一个应用程序框架，该框架可以帮助我们轻松地将 Go APIs 部署到 Kubernetes，而不会陷入混乱。

我们将在 API 中创建两个端点——一个创建新的 TODO 项，另一个选择所有 TODO 项。

本教程的完整代码示例可从 GitHub 获得:[https://github.com/alexellis/kubernetes-todo-go-app](https://github.com/alexellis/kubernetes-todo-go-app)

## 开始之前

你会从对围棋的实际理解中受益。[我的电子书《日常生活》(T3)涵盖了所有的基础知识和一些更高级的话题。](https://openfaas.gumroad.com/l/everyday-golang)

阅读人们在 Gumroad 上对它的评论:

[](https://openfaas.gumroad.com/l/everyday-golang) [## 日常 Golang

### “日常生活”是从生产中使用的真实工具中学习工具、技术和模式的快捷方式。这本书是一本…

openfaas.gumroad.com](https://openfaas.gumroad.com/l/everyday-golang) 

## 材料清单:

*   本地安装的 Docker

Kubernetes 在容器映像中运行代码，所以您需要在您的计算机上安装 Docker。

现在安装 Docker:【https://www.docker.com】T4

注册一个 Docker Hub 账户来储存你的 Docker 图片:[https://hub.docker.com/](https://hub.docker.com/)

*   库伯内特星团

您可以选择本地集群或远程集群，但是哪一个是最好的呢？像 k3d 这样的轻量级选项可以在任何可以运行 Docker 的计算机上运行，因此运行本地集群不再需要大量的 RAM。远程集群也是一种非常有效的工作方式，但是请记住，每次更改都需要上传和下载所有 Docker 映像。

安装 k3d:[https://github.com/k3d-io/k3d](https://github.com/k3d-io/k3d)

k3d 不会安装 kubectl(这是 Kubernetes 的 CLI)，所以从这里单独安装:[https://kubernetes.io/docs/tasks/tools/install-kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

*   Go 又名 Golang

你需要在电脑上安装 Go 和 IDE。Go 是免费的，您可以在此下载适用于 MacOS、Windows 或 Linux 的 go:

立即安装 Go:[https://golang.org/dl](https://golang.org/dl/)

*   IDE

我会推荐使用 Visual Studio 代码，它有一组插件，你可以为 Go 添加，而且是免费的。有些人更喜欢 Jetbrains 的 Goland，如果你是一名 Java 程序员，你可能会更喜欢为 Goland 付费，因为它会让你想起他们的其他产品。

现在安装 VSCode 或 Goland:[https://code.visualstudio.com](https://code.visualstudio.com)或[https://www.jetbrains.com/go](https://www.jetbrains.com/go/)

# 构建集群

您需要安装上一节中的所有软件。

## 使用 k3d 创建新的集群:

```
k3d cluster create
```

`kubectl`的配置现在将指向 k3d 集群，您可以用`kubectl config get-clusters`来验证它。

检查群集是否至少有一个节点，您可以在这里看到我有 Kubernetes 1.17，这是一个相对较新的版本:

```
kubectl get nodeNAME                     STATUS   ROLES    AGE   VERSION
k3d-k3s-default-server   Ready    master   48s   v1.17.0+k3s.1
```

我们将在数据库表中存储我们的 TODO 项，所以我们现在需要安装一个数据库。Postgresql 是一个流行的关系数据库，我们可以使用它的 helm chart 将它安装到集群中。

## 安装 arkade

arkade 是一个 Go CLI，类似于“brew”或“apt-get ”,但用于 Kubernetes 应用程序。它使用 Helm、kubectl 或项目的 CLI 将项目或产品安装到您的集群中。

```
curl -sLS [https://get.arkade.dev](https://dl.get-arkade.dev) | sudo sh
```

如果你愿意，你可以在没有 sudo 的情况下运行上面的程序，但是你应该在之后自己把下载的二进制文件移动到`/usr/local/bin`。

现在安装 [Postgresql](https://www.postgresql.org) 来存储我们的待办事项列表项:

```
arkade install postgresql===================================================================== = PostgreSQL has been installed.                                    =
=====================================================================
```

您还将看到打印出的连接字符串信息，以及如何通过集群内的 Docker 映像运行 Postgresql CLI。

用`arkade info postgresql`可以随时获取这些信息。

设计一个表模式

```
CREATE TABLE todo (
  id              INT GENERATED ALWAYS AS IDENTITY,
  description     text NOT NULL,
  created_date    timestamp NOT NULL,
  completed_date  timestamp NOT NULL
);
```

运行`arkade info postgresql`再次获取连接信息，应该类似于:

```
export POSTGRES_PASSWORD=$(kubectl get secret --namespace default postgresql -o jsonpath="{.data.postgresql-password}" | base64 --decode)kubectl run postgresql-client --rm --tty -i --restart='Never' --namespace default --image docker.io/bitnami/postgresql:11.6.0-debian-9-r0 --env="PGPASSWORD=$POSTGRES_PASSWORD" --command -- psql --host postgresql -U postgres -d postgres -p 5432
```

现在您有一个提示，如:`postgres=#`，您可以通过粘贴创建表格，然后运行`\dt`显示表格:

```
postgres=# \dt
List of relations
Schema | Name | Type  |  Owner
--------+------+-------+----------
public | todo | table | postgres
(1 row)
```

## 安装应用程序框架

就像 PHP 开发人员通过使用 LAMP (Linux Apache + Mysql + PHP)加速他们的工作流，Rails 开发人员通过预先构建的堆栈加速他们的工作流一样，Kubernetes 开发人员也可以利用应用程序框架。

PLONK 栈代表普罗米修斯、Linux、OpenFaaS、NATS 和 Kubernetes。

*   Prometheus 提供指标、自动扩展和可观察性来检查您的系统的健康状况，并允许它响应需求高峰，并通过提供有关零扩展决策的指标来节省成本。
*   虽然 Linux 不是在 Kubernetes 上运行工作负载的唯一选择，但它是默认的，也是最容易使用的。
*   OpenFaaS 最初开始为开发人员提供可移植的功能，但在部署 API 和微服务时也能很好地工作。它的多功能性意味着任何带有 HTTP 服务器的 Docker 容器都可以被部署和管理。
*   NATS 是一个流行的 CNCF 项目，用于消息传递和发布/订阅。在 PLONK 栈中，它提供了异步运行请求和排队的能力。
*   库伯内特是我们在这里的原因。它提供了横向扩展、自我修复和声明式基础架构。如果你不需要大部分，它的 API 通过 OpenFaaS 变得很简单。

通过 arkade 安装 PLONK 堆栈:

```
arkade install openfaas
```

阅读信息消息并运行每个命令:

*   安装 faas-cli
*   获取您的密码
*   端口转发 OpenFaaS 网关 UI(通过端口 8080)
*   并通过 CLI 登录

像以前一样，您可以通过`arkade info openfaas`获得信息消息。

## 部署您的第一个 Go API

用 PLONK 创建 Go API 有多种方法。第一种选择是使用 docker 文件，手动定义 TCP 端口、健康检查、HTTP 服务器等等。这可以通过`faas-cli new --lang dockerfile API_NAME`完成，但是有一种更简单、更少人工的方法。

第二种方法是利用函数商店提供的预建模板:

```
faas-cli template store list | grep gogo                       openfaas           Classic Golang template
golang-http              openfaas-incubator Golang HTTP template
golang-middleware        openfaas-incubator Golang Middleware template
```

由于我们想要创建一个传统的 HTTP 风格的 API，golang 中间件模板将是最合适的。

在本教程的开始，您注册了一个 Docker Hub 帐户来存储您的 Docker 图像。Kubernetes 中的每个工作负载在部署之前都需要构建到 Docker 映像中。

拉入特殊模板:

```
faas-cli template store pull golang-middleware
```

使用 golang 中间件和您的 Docker Hub 用户名搭建 API:

```
export PREFIX=alexellis2
export LANG=golang-middleware
export API_NAME=todofaas-cli new --lang $LANG --prefix $PREFIX $API_NAME
```

您将看到生成了两个文件:

*   `./todo.yml` —提供一种配置部署以及设置其模板和名称的方法
*   `./todo/handler.go` —这是您编写代码和添加您需要的任何其他文件或包的地方

让我们快速编辑一下，然后部署代码。

```
package functionimport (
    "net/http"
    "encoding/json"
)type Todo struct {
    Description string `json:"description"`
}func Handle(w http.ResponseWriter, r *http.Request) {
    todos := []Todo{}
    todos = append(todos, Todo{Description: "Run faas-cli up"}) res, _ := json.Marshal(todos) w.WriteHeader(http.StatusOK)
    w.Header().Set("Content-Type", "application/json") w.Write([]byte(res))
}
```

如果您没有使用 VScode 及其插件来编辑和格式化代码，那么在每次更改后运行它以确保文件被正确格式化。

```
gofmt -w -s ./todo/handler.go
```

现在，通过构建一个新的映像来部署代码，将其推送到 Docker Hub，并通过 OpenFaaS API 将其部署到集群:

```
Invoke your endpoint when ready:curl [http://127.0.0.1:8080/function/todo](http://127.0.0.1:8080/function/todo){
 "description": "Run faas-cli up"
}
```

## 允许用户创建新的待办事项

现在允许用户在其待办事项列表中创建新项目。首先，您需要为 Go 向 Postgresql 库添加一个引用或“依赖项”。

我们可以通过销售或 Go 1.11 中引入的 Go 模块来实现这一点，这些模块在 Go 1.13 中被设置为默认设置。

编辑`handler.go`并添加我们访问 Postgresql 所需的模块:

```
import (
    "database/sql"
    _ "github.com/lib/pq"
...
```

为了让 Go 模块检测到依赖关系，我们必须在文件中声明一些东西，我们将在后面使用，如果我们不这样做，那么 VSCode 将在保存时删除这些行。

将此添加到导入下的文件中

```
var db *sql.DB
```

总是在`handler.go`所在的`todo`文件夹中运行这些命令，而不是在有`todo.yml`的根目录下。

初始化新的 Go 模块:

```
cd todo/
ls
handler.goexport GO111MODULE=on
go mod init
```

现在用 pq 库更新文件:

```
go get
go mod tidycat go.modmodule github.com/alexellis/todo1/todogo 1.17require github.com/lib/pq v1.3.0
```

无论你在`go.mod`里看到什么内容，把它们复制到`GO_REPLACE.txt`

```
cat go.mod > GO_REPLACE.txt
```

现在，在为 insert 添加额外的代码之前，让我们检查一下构建是否仍然有效。

```
faas-cli build -f todo.yml --build-arg GO111MODULE=on
```

你会注意到我们现在传递了一个`--build-arg`来告诉模板使用 Go 模块。

> 截至 2022 年，你不再需要为这个 OpenFaaS 模板添加`--build-arg GO111MODULE=on`，因为这是一个新的默认设置。

在构建过程中，您会看到这些模块是根据需要从互联网上下载的。

```
Step 16/29 : RUN go test ./... -cover
 ---> Running in 9a4017438500
go: downloading github.com/lib/pq v1.3.0
go: extracting github.com/lib/pq v1.3.0
go: finding github.com/lib/pq v1.3.0
?       github.com/alexellis/todo1/todo [no test files]
Removing intermediate container 9a4017438500
```

## 配置密码以访问 Postgresql

我们可以在`init()`方法中建立一个连接池，它在程序启动时只运行一次。

```
// init establishes a persistent connection to the remote database
// the function will panic if it cannot establish a link and the
// container will restart / go into a crash/back-off loop
func init() { if _, err := os.Stat("/var/openfaas/secrets/password"); err == nil {
                password, _ := sdk.ReadSecret("password")
                user, _ := sdk.ReadSecret("username")
                host, _ := sdk.ReadSecret("host")
                dbName := os.Getenv("postgres_db")
                port := os.Getenv("postgres_port")
                sslmode := os.Getenv("postgres_sslmode") connStr := "postgres://" + user + ":" + password + "@" + host + ":" + port + "/" + dbName + "?sslmode=" + sslmodevar err error
                db, err = sql.Open("postgres", connStr) if err != nil {
                        panic(err.Error())
                } err = db.Ping()
                if err != nil {
                        panic(err.Error())
                }
        }
}
```

你会注意到一些信息是从`os.Getenv`环境中读取的。这些值是我认为非机密的，它们在 todo.yml 文件中设置。

对于其他的，比如密码和主机，这些都是保密的，存储在 [Kubernetes secrets](https://kubernetes.io/docs/concepts/configuration/secret/) 中。

您可以通过`faas-cli secret create`或`kubectl create secret generic -n openfaas-fn`创建这些。

行`sdk.ReadSecret`来自 OpenFaaS Cloud SDK，具有以下导入:`github.com/openfaas/openfaas-cloud/sdk`。它从磁盘中读取秘密文件并返回一个值或一个错误。

从`arkade info postgresql`获取秘密值。

现在，对每个密码运行以下命令:

```
export POSTGRES_PASSWORD=$(kubectl get secret --namespace default postgresql -o jsonpath="{.data.postgresql-password}" | base64 --decode)export USERNAME="postgres"
export PASSWORD=$POSTGRES_PASSWORD
export HOST="postgresql.default"faas-cli secret create username --from-literal $USERNAME
faas-cli secret create password --from-literal $PASSWORD
faas-cli secret create host --from-literal $HOST
```

检查机密是否如预期的那样存在:

```
faas-cli secret ls
NAME
username
password
host# And via kubectl:kubectl get secret -n openfaas-fn
NAME                  TYPE                                  DATA   AGE
username              Opaque                                1      13s
password              Opaque                                1      13s
host                  Opaque                                1      12s
```

编辑您的 YAML 文件，并添加以下内容:

```
secrets:
    - host
    - password
    - username
    environment:
      postgres_db: postgres
      postgres_sslmode: "disable"
      postgres_port: 5432
```

接下来，更新 Go 模块并再次运行构建:

```
cd todo
go get
go mod tidycd ..faas-cli build -f todo.yml --build-arg GO111MODULE=onSuccessfully built d2c609f8f559
Successfully tagged alexellis2/todo:latest
Image: alexellis2/todo:latest built.
[0] < Building todo done in 22.50s.
[0] Worker done.Total build time: 22.50s
```

构建按预期工作，所以使用相同的参数运行`faas-cli up`来推送和部署映像。如果凭据和 SQL 配置是正确的，我们将不会在日志中看到错误，但是如果它们是错误的，我们将在 init()中显示紧急代码。

检查日志:

```
faas-cli logs todo2020-03-26T14:10:03Z Forking - ./handler []
2020-03-26T14:10:03Z 2020/03/26 14:10:03 Started logging stderr from function.
2020-03-26T14:10:03Z 2020/03/26 14:10:03 Started logging stdout from function.
2020-03-26T14:10:03Z 2020/03/26 14:10:03 OperationalMode: http
2020-03-26T14:10:03Z 2020/03/26 14:10:03 Timeouts: read: 10s, write: 10s hard: 10s.
2020-03-26T14:10:03Z 2020/03/26 14:10:03 Listening on port: 8080
2020-03-26T14:10:03Z 2020/03/26 14:10:03 Metrics listening on port: 8081
2020-03-26T14:10:03Z 2020/03/26 14:10:03 Writing lock-file to: /tmp/.lock
```

到目前为止一切正常，现在尝试调用端点:

```
echo | faas-cli invoke todo -f todo.yml2020-03-26T14:11:02Z 2020/03/26 14:11:02 POST / - 200 OK - ContentLength: 35
```

因此，我们现在已经成功建立了数据库连接，可以执行插入操作了。我们怎么知道的？因为`db.Ping()`返回一个错误，否则会抛出一个异常:

```
err = db.Ping()
if err != nil {
    panic(err.Error())
}
```

参见[数据库/sql 包](https://golang.org/pkg/database/sql/)的完整参考。

## 编写插入代码

这段代码将一个新的 tow 插入到`todo`表中，并使用一种特殊的语法，其中的值没有被引用，而是被 db 替换。查询代码。在 LAMP 编程的“旧时代”,导致许多系统不安全的一个常见错误是没有对输入进行净化，没有将用户输入直接连接到 SQL 语句中。

想象一下有人有一个`; drop table todo`的描述输入，那就不好玩了。

所以我们运行`db.Query`，然后传入 SQL 语句，对每个值使用`$1, $2`等，然后可以检索结果和/或错误。我们还应该关闭这个结果，因此使用 defer。

```
func insert(description string) error {
        res, err := db.Query(`insert into todo (id, description, created_date) values (DEFAULT, $1, now());`,
                description) if err != nil {
                return err
        } defer res.Close()
        return nil
}
```

现在让我们把它加入到代码中。

```
func Handle(w http.ResponseWriter, r *http.Request) {
        if r.Method == http.MethodPost && r.URL.Path == "/create" {
                defer r.Body.Close()
                body, _ := ioutil.ReadAll(r.Body) if err := insert(string(body)); err != nil {
                        http.Error(w, fmt.Sprintf("unable to insert todo: %s", err.Error()), http.StatusInternalServerError)
                }
        }
}
```

让我们部署并试用它？

```
echo | faas-cli invoke todo -f todo.ymlcurl [http://127.0.0.1:8080/function/todo/create](http://127.0.0.1:8080/function/todo/create) --data "faas-cli build"
curl [http://127.0.0.1:8080/function/todo/create](http://127.0.0.1:8080/function/todo/create) --data "faas-cli push"
curl [http://127.0.0.1:8080/function/todo/create](http://127.0.0.1:8080/function/todo/create) --data "faas-cli deploy"
```

检查 API 的日志:

```
faas-cli logs todo2020-03-26T14:35:29Z 2020/03/26 14:35:29 POST /create - 200 OK - ContentLength: 0
```

使用 pgsql 检查表格内容:

```
export POSTGRES_PASSWORD=$(kubectl get secret --namespace default postgresql -o jsonpath="{.data.postgresql-password}" | base64 --decode)kubectl run postgresql-client --rm --tty -i --restart='Never' --namespace default --image docker.io/bitnami/postgresql:11.6.0-debian-9-r0 --env="PGPASSWORD=$POSTGRES_PASSWORD" --command -- psql --host postgresql -U postgres -d postgres -p 5432postgres=# select * from todo;
id |   description   |        created_date        | completed_date
----+-----------------+----------------------------+----------------
1 | faas-cli build  | 2020-03-26 14:36:03.367789 |
2 | faas-cli push   | 2020-03-26 14:36:03.389656 |
3 | faas-cli deploy | 2020-03-26 14:36:03.797881 |
```

恭喜，您现在有了一个 todo API，它可以通过`curl`或任何其他 HTTP 客户端接受传入的请求，并将它们记录在数据库表中。

## 查询待办事项

让我们创建一个新函数来查询表中的 todo 项:

```
func selectTodos() ([]Todo, error) {
    var error err
    var todos []Todo return todos, err
}
```

我们不能调用这个方法 select，因为那是一个保留的关键字，用于处理 go 例程。

现在将该方法挂接到主处理程序中:

```
} else if r.Method == http.MethodGet && r.URL.Path == "/list" {
    todos, err := selectTodos() if err != nil { http.Error(w, fmt.Sprintf("unable to get todos: %s", err.Error()), http.StatusInternalServerError) } out, _ := json.Marshal(todos)
    w.Header().Set("Content-Type", "application/json")
    w.Write(out)
}
```

现在我们的数据模式中有了额外的日期字段，更新 Todo 结构:

```
type Todo struct {
    ID int `json:"id"`
    Description   string `json:"description"`
    CreatedDate   *time.Time `json:"created_date"`
    CompletedDate *time.Time `json:"completed_date"`
}
```

现在让我们用查询代码更新我们的`selectTodos()`方法:

```
func selectTodos() ([]Todo, error) {
        rows, getErr := db.Query(`select id, description, created_date, completed_date from todo;`) if getErr != nil {
        return []Todo{}, errors.Wrap(getErr, "unable to get from todo table")
    } todos := []Todo{}
    defer rows.Close()
    for rows.Next() {
        result := Todo{}
        scanErr := rows.Scan(&result.ID, &result.Description, &result.CreatedDate, &result.CompletedDate)
        if scanErr != nil {
            log.Println("scan err:", scanErr)
        }
        todos = append(todos, result)
    }
    return todos, nil
}
```

就像以前一样，我们需要推迟查询行的关闭。每个值都通过行插入到一个新的结构中。扫描方法。在方法的末尾，我们有一部分 Todo 项。

尝试一下:

```
faas-cli up -f todo.yml --build-arg GO111MODULE=oncurl http://127.0.0.1:8080/function/todo/list
```

结果如下:

```
[
  {
    "id": 2,
    "description": "faas-cli build",
    "created_date": "2020-03-26T14:36:03.367789Z",
    "completed_date": null
  },
  {
    "id": 3,
    "description": "faas-cli push",
    "created_date": "2020-03-26T14:36:03.389656Z",
    "completed_date": null
  },
  {
    "id": 4,
    "description": "faas-cli deploy",
    "created_date": "2020-03-26T14:36:03.797881Z",
    "completed_date": null
  }
]
```

为了移除空值，我们可以更新结构的注释来添加`omitempty`:

```
CompletedDate *time.Time `json:"completed_date,omitempty"`
```

# 结束本次会议

我们还没有完成，但这是一个停下来回顾我们迄今为止所取得的成就的好时机。

*   安装了 Go、Docker、kubectl 和 VSCode(一个 IDE)
*   在我们的本地机器上部署 Kubernetes
*   使用 arkade 和 helm3 安装 Postgresql
*   为 Kubernetes 应用程序开发人员安装了 OpenFaaS 和 PLONK 堆栈
*   使用 Go 和 OpenFaaS `golang-middleware` 模板构建了一个初始的静态 REST API
*   在我们的 TODO API 中添加了“插入”功能
*   在我们的 TODO API 中添加了“选择”功能

我的 GitHub 帐户上有我们迄今为止构建的完整代码示例:

[](https://github.com/alexellis/kubernetes-todo-go-app) [## alexellis/kubernetes-todo-go-app

### 一个用 go 写的 Kubernetes 的 TODO API。为 alexellis/kubernetes-todo-go-app 开发做出贡献，创建一个…

github.com](https://github.com/alexellis/kubernetes-todo-go-app) 

我们还可以做更多的事情，例如:

*   使用静态承载令牌添加身份验证
*   使用静态 HTML 模板创建网页，或者 React 呈现待办事项列表并创建新项目
*   向 API 添加多用户支持

还有更多。我们还可以深入到 PLONK 栈中，部署一个 Grafana 仪表板，开始观察我们的 API，了解 Kubernetes 仪表板或 metrics-server(通过`arkade install`安装)使用了多少资源

*   在 GitHub 上主演 [arkade](https://arkade.dev/)
*   [探索 OpenFaaS](https://www.openfaas.com/)

你可能还会喜欢[我的电子书《天天向上》](https://openfaas.gumroad.com/l/everyday-golang)——里面有很多例子，你可以在本教程的基础上使用和扩展:

[](https://openfaas.gumroad.com/l/everyday-golang) [## 日常 Golang

### “日常生活”是从生产中使用的真实工具中学习工具、技术和模式的快捷方式。这本书是一本…

openfaas.gumroad.com](https://openfaas.gumroad.com/l/everyday-golang)
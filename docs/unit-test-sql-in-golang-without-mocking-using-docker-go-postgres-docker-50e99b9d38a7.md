# 使用 Docker (Go，Postgres，Docker)在 Golang 中单元测试 SQL，没有嘲讽

> 原文：<https://levelup.gitconnected.com/unit-test-sql-in-golang-without-mocking-using-docker-go-postgres-docker-50e99b9d38a7>

![](img/49c71a8c5a0315f91225bec5684fc064.png)

图片来自测试专家(https://tinyurl.com/y2k27j33**)**

> **更新**:我已经用 TestContainers 写了一篇配套文章，基于一个评论说我应该研究一下那个包。它实现了同样的目标，测试数据库代码而没有模仿。可以在这里阅读:[https://atkinsonbg . medium . com/unit-test-SQL-in-golang-without-mocking-using-test containers-go-postgres-docker-4f 61574 b 1989](https://atkinsonbg.medium.com/unit-test-sql-in-golang-without-mocking-using-testcontainers-go-postgres-docker-4f61574b1989)

最近，在开发一个与数据库接口的 API 时，我错误地将我们的 ORM 包更新到了最新版本。代码编译得很好，所有测试都顺利通过。然而，在现实中，最新版本的软件包带来了突破性的变化，这是非常微妙的。我的代码以前工作得很好，现在可以很好地连接数据库，没有任何问题地运行它的查询，但是总是返回一个空的数据集！这个问题直到代码被推送到 QA 时才被发现，在 QA 中很快就发现有问题。这是一个很难捕捉的问题，因为没有抛出错误，只是没有返回数据。

那么这个问题是如何通过测试的呢？像大多数 Go 工程师一样，我们模拟了所有用于单元测试的数据库代码和交互。在这个特定的场景中，我们的模拟不需要为更新的包而改变，所以一切都很好，直到我们遇到一个真正的数据库。这让我知道了如何在真实的数据库上运行我的测试，并完全消除对模拟的需求。

# 为什么要去掉仿制品，它们很棒？！

首先，我想说，我并不主张完全摆脱模仿。它们绝对在测试中占有一席之地，并且在很多事情上派上用场。对我来说，对于你无法控制的资源，模仿是至关重要的。例如，以 Twilio 这样的服务为例。如果您需要调用 Twilio 并处理来自它们的 API 的响应，这是一个很好的例子，说明了模仿的重要性。您无法控制它们的 API，您只是简单地发出一个调用并处理响应。通常，如果您在 CI/CD 管道中运行，您可能无法进行这些调用，也可能不只是想进行这些调用。这里模仿是完美的，因为您并不真正关心对 API 的调用，只是为了确保您的代码能够处理响应。

说到在 Golang 代码中嘲笑数据库交互，老实说，在我看来这是一次悲惨的经历。通常，您最终会为这些代码编写大量的接口，然后可以在您的测试中模拟出来。您的测试以大量样板代码和跨多个文件重复的 SQL 查询而告终。有一些软件包可以帮助解决这个问题，甚至消除了对接口的需求，但是你仍然要写很多样板代码，现在需要训练人们如何使用另一个软件包。以下是一些例子:

*   [https://medium . com/easy read/unit-test-SQL-in-golang-5af 19075 e68e](https://medium.com/easyread/unit-test-sql-in-golang-5af19075e68e)
*   【https://dev.to/pieohpah/mocking-database-in-go-55bo 
*   [https://github.com/DATA-DOG/go-sqlmock](https://github.com/DATA-DOG/go-sqlmock)

我想要的是在真实的数据库上运行我的测试，完全消除对模拟的需求。目标是提供以下内容:

*   编写没有接口、没有模仿、没有附加包的常规测试
*   针对实际数据库进行测试，该数据库应用了最新的模式并植入了测试数据
*   不仅要测试我的数据库代码，还要测试任何公开该数据的 API 处理程序
*   使它通用于任何 GitHub repo，以用于其单元测试
*   能够在真实的 CI/CD 管道中使用该流程

这篇文章将涵盖我提出的解决方案。虽然这不是一个完美的解决方案，但它确实符合我的所有要求。我将不断调整这个解决方案，以平滑边缘，因为我认为它在我的个人和专业工作中有很大的潜力。

# 设置

我创建了一个非常简单的 [Go API](https://github.com/atkinsonbg/go-gmux-unit-testing) ，它可以处理包含时区信息的数据库。这里没有什么过于复杂的，只有两个 get 和一个 POST 用于插入。数据库同样简单，一个存储了时区信息的表。让我们先来看看数据库的表模式。我有一个非常简单的`./scripts/init.sql`脚本，它创建了一个表并用一些数据作为种子:

```
-- SCHEMACREATE TABLE timezones(id serial PRIMARY KEY,created timestamptz DEFAULT now() NOT NULL,modified timestamptz DEFAULT now() NOT NULL,name text NOT NULL,timeoffset smallint NOT NULL,identifier text NOT NULL);-- SEEDINSERT INTO timezones(id, name, timeoffset, identifier)VALUES(1, 'eastern', '-5', 'est');INSERT INTO timezones(id, name, timeoffset, identifier)VALUES(2, 'central', '-6', 'cst');INSERT INTO timezones(id, name, timeoffset, identifier)VALUES(3, 'mountain', '-7', 'mst');INSERT INTO timezones(id, name, timeoffset, identifier)VALUES(4, 'pacific', '-8', 'pst');INSERT INTO timezones(id, name, timeoffset, identifier)VALUES(5, 'alaska', '-9', 'ast');ALTER SEQUENCE timezones_id_seq RESTART WITH 6;
```

在我的 Go API 中，我在我的`main.go`文件中定义了四条路线:

```
func main() { 
  database.InitDB()   r := mux.NewRouter()            r.HandleFunc("/healthcheck", handlers.HealthHandler).Methods("GET")   r.HandleFunc("/timezones", handlers.ListTimezonesHandler).Methods("GET")   r.HandleFunc("/timezones/{identifier}", handlers.GetTimezoneHandler).Methods("GET")   r.HandleFunc("/timezones", handlers.InsertTimezoneHandler).Methods("POST")   log.Fatal(http.ListenAndServe(":80", r))
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
// GetAllTimezones returns all timezones from the DBfunc GetAllTimezones() ([]Timezone, error) { rows, err := db.Query("SELECT * FROM timezones") if err != nil { log.Fatal(err) return nil, err } var timezones []Timezone defer rows.Close() for rows.Next() { var timezone Timezone err := rows.Scan(&timezone.ID, &timezone.Created,        &timezone.Modified, &timezone.Name, &timezone.Timeoffset, &timezone.Identifier) if err != nil { log.Print(err) } timezones = append(timezones, timezone) } return timezones, nil}
```

在本地运行时，我通过 Docker Compose 将所有这些连接起来，这样我就可以将我的 API 和本地 Postgres 容器一起运行，并通过 Postman 调用轻松测试我的代码:

```
version: "3.7"services: api: container_name: go-mux-api image: 
      github.com/atkinsonbg/go-gmux-proper-unit-testing/api:latest environment: DBHOST: go-mux-db DBNAME: go-mux-db DBUSER: postgres ports: - 80:80 database: container_name: go-mux-db image: postgres:11.6-alpine environment: POSTGRES_DB: go-mux-db volumes: - ./scripts/init.sql:/docker-entrypoint-initdb.d/init.sql ports: - 5432:5432
```

Docker Compose 在这种情况下非常有用，因为我可以快速旋转一个配置为满足我的需求的 DB，将其拆下，然后再旋转回来。如果我需要更复杂的测试，我总是可以分别启动数据库和 API，并使用[远程容器](/debugging-go-inside-docker-using-visual-studio-code-and-remote-containers-5c3724fe87b9)进行测试。Docker 在这里是一个救星，因为它对于我的测试用例非常灵活。更重要的是，当我们用真实的数据库进行测试时。

# 测试

正如我之前所说的，我不想嘲笑我的测试。同样，我也不想引入任何外部包。我想针对我的数据库代码和处理程序编写普通的 Golang 测试。下面的代码展示了它的样子。

**处理程序测试(时区获取)**

```
func TestListTimezonesHandler(t *testing.T) { req, err := http.NewRequest("GET", "/timezones", nil) if err != nil { t.Fatal(err) } rr := httptest.NewRecorder() handler := http.HandlerFunc(ListTimezonesHandler) handler.ServeHTTP(rr, req) if rr.Code != http.StatusOK { t.Errorf("handler returned wrong status code: got %v want %v", rr.Code, http.StatusOK) } var timezones []db.Timezone err = json.NewDecoder(rr.Body).Decode(&timezones) if err != nil { t.Error(err.Error()) t.Error("Error retreiving list of timezones.") } if len(timezones) == 0 { t.Error("Error retreiving list of timezones.") }}
```

这段代码利用了 Go 中的`[net/http/httptest](https://golang.org/pkg/net/http/httptest/)`包，它允许我从测试中轻松地调用我的 API 端点。

**数据库测试(时区获取)**

```
func TestGetAllTimezones(t *testing.T) { tzones, err := GetAllTimezones() if err != nil { t.Error("Get All Timezones failed.") } if len(tzones) == 0 { t.Error("Timezones did not return any values.") }}
```

在这一点上，你可以看到，我没有使用任何模拟，也没有特殊的软件包。这是我所能得到的最普通的测试代码，非常棒。它简明扼要。任何曾经编写过 Go 代码的人都可以阅读这段代码并理解它在做什么。没什么特别的，只是测试一下。然而，在博客文章的这一点上，如果你简单地发出一个`go test`命令，这个测试将绝对 100%失败。这个测试试图访问一个正在运行的数据库，但它还没有。

# 测试数据库

我们已经看到 Docker Compose 在我们的 API 旁边运行了一个测试数据库。它超级简单，非常管用。然而，Compose 并不能帮助我实现我的最终目标。不过，我肯定可以使用 Compose 来实现这一点。我可以创建一个特殊的组合文件来旋转这两个容器，并简单地从 API 容器中运行我的测试命令。假设我的代码复制正确，这将是好的。然而，我想要一个可以在其中运行测试的通用容器，所以 Compose 被淘汰了。

我最终采用的解决方案有两个要求:

*   一个 Postgres 基础 Docker 镜像，已经安装了 Go，加上一些不那么秘密的 sauce 代码来启动一切
*   一些必要的文件和目录结构在您的回购，使这一切工作。

让我们从探索代码运行所需的目录结构开始。这没什么大不了的，但仍然至关重要。不管你如何构建你的代码，docker 文件会假设两件事:

*   首先，有一个名为`scripts`的根级目录，它将包含以下内容:
*   -创建和播种数据库所需的所有 SQL 脚本
*   -一个名为`docker-test-commands.sh`的 shell 脚本，它包含了您想要运行来测试您的应用程序的所有定制代码。最终，这个脚本将控制数据库的创建、模式的创建和表的播种。它也负责启动你的`go test`命令。它是测试应用程序所需的一切的核心。
*   我们还需要已经创建了测试结果文件，在我们的例子中，它的`cover.out`的原因是，我们需要在 Docker 中挂载一个卷来获取测试结果。我不想挂载到一个目录，而是一个文件，因为这是我唯一关心的。如果这个文件不存在，那么 Docker 会在挂载时创建一个名为`/cover.out`的新目录，这显然不是我们想要的。

该结构看起来像这样:

```
/scripts
    init.sql
    docker-test-commands.sh
cover.out
go.mod
go.sum
main.go
```

上面我们已经看到了我的`init.sql`的样子。现在让我们看看我的`docker-test-commands.sh`文件中有什么:

```
#!/bin/bashset -eecho "Printing environment variables:"echo "DB USER: ${DBUSER}"echo "DB NAME: ${DBNAME}"echo "DB HOST: ${DBHOST}"echo "POSTGRES USER: ${POSTGRESUSER}"echo "Done printing environment variables:"echo "Wait for Postgres to start"until pg_isready -h ${DBHOST} -p 5432 -U ${POSTGRESUSER} do echo "Waiting for postgres to start at ${DBHOST}..." sleep 2;doneecho "Postgres has started"echo "Setting up the Postgres user"POSTGRES="psql --username ${POSTGRESUSER}"echo "Creating database: ${DBNAME}"$POSTGRES <<EOSQLCREATE DATABASE "${DBNAME}" OWNER ${POSTGRESUSER};EOSQLecho "Initializing database..."psql -d ${DBNAME} -a -U${POSTGRESUSER} -f ./scripts/init.sql &SQL_PID=$!wait $SQL_PIDecho "Finished initializing database"echo "Running Go Tests"go test -v ./... -coverpkg ./... -coverprofile cover.out &GO_PID=$!wait $GO_PIDecho "Finished with go tests"
```

让我们解开这个脚本:

*   首先，我们进行健全性检查，并打印出存在的环境变量。如果您喜欢覆盖内置变量，这很方便。
*   接下来，我们等待 Postgres 完全初始化，然后再做其他事情。在尝试创建表并对其运行测试之前，我们需要确保一切都已启动并运行。
*   一旦 Postgres 准备就绪，我们就设置我们的 Postgres 用户并创建我们的数据库。
*   接下来，我们运行我们的 init 脚本来创建这个表，并为它植入数据。我们将它作为后台进程运行，并捕获进程 ID，等待它完成后再继续。这样做的原因是，在发出任何测试命令之前，我们需要等待 SQL 命令运行。
*   最后，我们运行我们的`go test`命令，将结果输出到将在 Docker run 命令期间挂载的`cover.out`文件。

这个文件做了很多工作，但是你应该已经看到了你对测试的控制。如果你需要一些非标准的 Postgres 用户或配置，你可以在这里做。需要运行 Flyway 迁移，没问题，在这里发出命令。假设您的迁移在另一个 GitHub repo 中，不用担心，从这里克隆它，并运行它们。这是一个非常灵活的入口点。

现在让我们看看 docker 文件:

```
FROM postgres:11.6-alpineRUN apk add --no-cache git make musl-dev go busybox-suid# Set environment variables for the databaseENV POSTGRESUSER postgresENV DBUSER postgresENV DBNAME testdbENV DBHOST localhost# Configure GoENV GOROOT /usr/lib/goENV GOPATH /goENV PATH /go/bin:$PATHRUN mkdir -p ${GOPATH}/src ${GOPATH}/bin# Give postgres user full access to Go path, needed to install dependenciesRUN chown -R postgres /go# Create a workdir, this is needed to properly install Go dependencies# chown on the postgres user so it can clone the repo hereWORKDIR testdirRUN chown -R postgres /testdirRUN mkdir /tmpdir# used for local testing ONLY# COPY . .# create the entrypoint db scriptRUN echo -e "\#!/bin/bash \n \git clone \${GIT_URL} ../testdir/tmpdir/ \n \cp -R ../testdir/tmpdir/* ../testdir/ \n \/bin/sh ../testdir/scripts/docker-test-commands.sh & \n \COMMANDS_PID=\$! \n \(while kill -0 \$COMMANDS_PID; do sleep 1; done) && pg_ctl stop & \" >> /docker-entrypoint-initdb.d/entrypoint.sh# chmod the entrypoint db scriptRUN chmod +x /docker-entrypoint-initdb.d/entrypoint.sh
```

这里有很多东西要打开，所以让我们把它放下来:

*   首先，我们将我们的图像基于 Postgres Alpine 图像。我们从 Postgres 开始，因为他们的形象是野兽！不是不好，只是有很多，所以从这里开始安装 Go 比反过来更容易。
*   接下来，我们安装一些我们需要的依赖项，其中之一是 Go。我们需要 Git 来运行并稍后克隆我们的 repo，所以我们在这方面做得很好。
*   我们为数据库配置了一些环境变量。这里的目标是为数据库名称、用户等嵌入变量，如果您愿意，可以覆盖这些变量。然而，由于这是一个测试容器，意味着可以快速地创建和销毁，所以让它们保持原样可能更容易，谁在乎呢。
*   接下来，我们用标准设置配置 Go，这里没有什么疯狂的。
*   然后我们必须给`postgres`用户对`/go`目录的所有权。当 Postgres 容器启动时，这是用户发出的所有命令。我们将运行一个`go test`命令，该命令需要安装依赖项，因此`postgres`用户在这里需要权限。
*   接下来，我们创建一个 WORKDIR，因为我的代码使用 Go Mods，我需要在 GOPATH 之外才能工作。我们也给了`postgres`用户这个文件夹的所有权，因为这是我们的测试命令所在的地方，通常你想要写测试结果，代码覆盖率等等。
*   我们在工作目录下创建了一个`tmpdir`,稍后用于 Git 克隆命令。
*   我们有一个被注释掉的复制命令。稍后我会详细解释这一点。
*   现在我们来看容器启动时将被执行的文件，它是运行测试的“入口点”。我们运行一个 ECHO 命令，将所有内容传输到一个新文件`/docker-entrypoint-initdb.d/entrypoint.sh`中，如果您不熟悉 Postgres 容器，有一个名为`docker-entrypoint-initdb.d`的特殊文件夹，您可以在其中放置容器启动时自动运行的脚本。让我们来分析一下这个脚本的内容:
*   -首先我们`git clone`通过`GIT_URL`变量设置的回购。我们克隆到`tmpdir`中，然后发出`cp`命令，将所有内容复制到上一级。这样做有特定的原因，因为`/testdir`不会为空。我们需要提前安装我们的测试输出文件，它就在这个目录中。Git 不允许克隆到包含文件的目录中。简单的解决方法是复制下一层，然后复制上一层。
*   -接下来，我们从上面发出运行`docker-test-commands.sh`文件的命令。此时，我们已经将我们的 repo 克隆到了`/testdir`中，这样，我们的代码、SQL 脚本和命令就在这里，可以开始运行了。
*   -我们在后台运行这个脚本并捕获它的进程 id。接下来，我们循环并等待它结束，最后调用`pg_ctrl stop`来停止 Postgres 并杀死容器。这很关键，因为我们需要知道何时停止容器，否则，它将永远运行下去。

我承认，这里发生了很多事。在这一点上，你可能在想，“我本可以现在就把它嘲笑出来！”你并没有完全错，但是，你也将回到工作与模拟，而不是一个真正的数据库。在这一点上，我们可以看到这个 Dockerfile 是通用的，只需向它传递一个 GitHub repo，它就会提取它并启动您的 shell 脚本。一个 shell 脚本，你可以控制它，并且可以放入任何你想要的东西。现在，您有了一个针对真实数据库运行测试的可重复过程。让我们来看看实际情况。

# 运行容器

在我的 repo 中，我有一个 Makefile，它带有以下命令来启动这个容器:

```
docker run -v ${PWD}/cover.out:/testdir/cover.out -e GIT_URL='https://github.com/atkinsonbg/go-gmux-unit-testing.git' atkinsonbg/go-postgres-test:latest
```

这会产生以下输出:

```
...
waiting for server to start....2020-12-06 20:03:58.674 UTC [35] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
2020-12-06 20:03:58.696 UTC [36] LOG:  database system was shut down at 2020-12-06 20:03:58 UTC
2020-12-06 20:03:58.701 UTC [35] LOG:  database system is ready to accept connections
 done
server started/usr/local/bin/docker-entrypoint.sh: running /docker-entrypoint-initdb.d/entrypoint.sh
Cloning into '../testdir/tmpdir'...
Printing environment variables:DB USER: postgres
DB NAME: testdb
DB HOST: localhost
POSTGRES USER: postgres
Done printing environment variables:
Wait for Postgres to start
waiting for server to shut down....2020-12-06 20:03:59.076 UTC [35] LOG:  received fast shutdown request
localhost:5432 - no response
Waiting for postgres to start at localhost...
2020-12-06 20:03:59.081 UTC [35] LOG:  aborting any active transactions
2020-12-06 20:03:59.082 UTC [35] LOG:  background worker "logical replication launcher" (PID 42) exited with exit code 1
2020-12-06 20:03:59.083 UTC [37] LOG:  shutting down
2020-12-06 20:03:59.104 UTC [35] LOG:  database system is shut down
 done
server stoppedPostgreSQL init process complete; ready for start up.2020-12-06 20:03:59.192 UTC [1] LOG:  listening on IPv4 address "0.0.0.0", port 5432
2020-12-06 20:03:59.192 UTC [1] LOG:  listening on IPv6 address "::", port 5432
2020-12-06 20:03:59.199 UTC [1] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
2020-12-06 20:03:59.221 UTC [62] LOG:  database system was shut down at 2020-12-06 20:03:59 UTC
2020-12-06 20:03:59.227 UTC [1] LOG:  database system is ready to accept connections
localhost:5432 - accepting connections
Postgres has started
Setting up the Postgres user
Creating database: testdb
CREATE DATABASE
Initializing database...
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
CREATE TABLE
-- SEED
INSERT INTO timezones
    (id, name, timeoffset, identifier)
VALUES
    (1, 'eastern', '-5', 'est');
INSERT 0 1
INSERT INTO timezones
    (id, name, timeoffset, identifier)
VALUES
    (2, 'central', '-6', 'cst');
INSERT 0 1
INSERT INTO timezones
    (id, name, timeoffset, identifier)
VALUES
    (3, 'mountain', '-7', 'mst');
INSERT 0 1
INSERT INTO timezones
    (id, name, timeoffset, identifier)
VALUES
    (4, 'pacific', '-8', 'pst');
INSERT 0 1
INSERT INTO timezones
    (id, name, timeoffset, identifier)
VALUES
    (5, 'alaska', '-9', 'ast');
INSERT 0 1
ALTER SEQUENCE timezones_id_seq RESTART WITH 6;
ALTER SEQUENCE
Finished initializing database
Running Go Tests
go: downloading github.com/kelseyhightower/envconfig v1.4.0
go: downloading github.com/gorilla/mux v1.7.4
go: downloading github.com/cenkalti/backoff/v4 v4.0.2
go: downloading github.com/lib/pq v1.7.0
go: extracting github.com/kelseyhightower/envconfig v1.4.0
go: extracting github.com/cenkalti/backoff/v4 v4.0.2
go: extracting github.com/gorilla/mux v1.7.4
go: extracting github.com/lib/pq v1.7.0
go: finding github.com/gorilla/mux v1.7.4
go: finding github.com/cenkalti/backoff/v4 v4.0.2
go: finding github.com/kelseyhightower/envconfig v1.4.0
go: finding github.com/lib/pq v1.7.0
?       github.com/atkinsonbg/go-gmux-proper-unit-testing       [no test files]
2020/12/06 20:04:06 DB is ready!
=== RUN   TestConfig
--- PASS: TestConfig (0.00s)
=== RUN   TestGetAllTimezones
--- PASS: TestGetAllTimezones (0.00s)
=== RUN   TestGetTimezone
--- PASS: TestGetTimezone (0.00s)
=== RUN   TestInsertTimezone
--- PASS: TestInsertTimezone (0.00s)
PASS
coverage: 43.0% of statements in ./...
ok      github.com/atkinsonbg/go-gmux-proper-unit-testing/database      0.019s  coverage: 43.0% of statements in ./...
2020/12/06 20:04:06 DB is ready!
=== RUN   TestHealthHandler
--- PASS: TestHealthHandler (0.00s)
=== RUN   TestListTimezonesHandler
--- PASS: TestListTimezonesHandler (0.00s)
=== RUN   TestGetTimezoneHandler
--- PASS: TestGetTimezoneHandler (0.00s)
=== RUN   TestInsertTimezoneHandler
--- PASS: TestInsertTimezoneHandler (0.00s)
PASS
coverage: 65.8% of statements in ./...
ok      github.com/atkinsonbg/go-gmux-proper-unit-testing/handlers      0.022s  coverage: 65.8% of statements in ./...
Finished with go tests
/docker-entrypoint-initdb.d/entrypoint.sh: line 6: kill: (55) - No such process
waiting for server to shut down....2020-12-06 20:04:07.112 UTC [1] LOG:  received fast shutdown request
2020-12-06 20:04:07.120 UTC [1] LOG:  aborting any active transactions
2020-12-06 20:04:07.122 UTC [1] LOG:  background worker "logical replication launcher" (PID 68) exited with exit code 1
2020-12-06 20:04:07.123 UTC [63] LOG:  shutting down
2020-12-06 20:04:07.168 UTC [1] LOG:  database system is shut down
```

瞧啊。现在，我已经成功地在一个运行的数据库上测试了我所有的 API 代码，并且我的`cover.out`文件中填充了覆盖率指标！我现在有了一个通用的 Postgres + Go 容器，我可以在任何需要测试数据库代码的回购中使用它。没有嘲讽，也没有外包装。我正在测试实际的数据库交互代码以及一整套处理程序测试。

我现在也可以在运行 CI/CD 时使用它。大多数现代管道都允许您指定一个容器来运行您的测试，这个通用选项完全可以用于这个任务。

**本地运行**

还记得在 docker 文件中，有一个复制命令被注释掉了。这样做的原因是，如果您愿意，可以轻松地在本地运行这个容器。您可能希望在代码被推送到 GitHub 之前对其进行测试。因此，您可以下拉 docker 文件，取消对`COPY . .`行的注释，这样就可以在本地运行它了。您首先需要构建容器:

```
docker build -f Dockerfile.test -t atkinsonbg/go-postgres-test:local .
```

然后，您可以运行它:

```
docker run -v ${PWD}/cover.out:/testdir/cover.out -e GIT_URL='' atkinsonbg/go-postgres-test:local
```

我们仍然传递`GIT_URL`环境变量，但将其留空。因此，`COPY`命令会将所有代码拉入，而`git clone`会失败，这对于本地运行来说不是问题。

# 包扎

这篇文章中提出的解决方案并不适合所有人。在 Go 中模仿数据库代码有很多好处，但也有很多令人头疼的问题。由您甚至您的团队来决定测试代码的最佳方式。对我来说，我更喜欢一种类似于这里概述的方法，并用一个真实的数据库进行测试。与此同时，我在下面贴了 GitHub repo 和所有代码的链接，以及 Docker Hub，在那里你可以找到一个预建的图像。尽情享受吧！

**GitHub**:【https://github.com/atkinsonbg/go-gmux-unit-testing 

**Docker Hub**:[https://Hub . Docker . com/repository/Docker/atkinsonbg/go-postgres-test](https://hub.docker.com/repository/docker/atkinsonbg/go-postgres-test)

[![](img/3515ab52cb6fb5e74c27c7a2e06d3811.png)](https://ko-fi.com/O5O63ENS7)
# 围棋测试中的嵌入式数据库

> 原文：<https://levelup.gitconnected.com/embedded-database-in-go-testing-10d29a8e454a>

![](img/4946922f1719243cdad78e91864e89e4.png)

切斯特·阿尔瓦雷斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我想这个场景对很多开发者来说都是熟悉的。一个有数据库需求的项目进展很快，这给测试带来了压力。测试持久性的策略没有事先达成一致，出现了三种 [*反模式*](https://en.wikipedia.org/wiki/Anti-pattern) 。对于简单的测试，人们会模拟数据。其他人，没有数据库，将持久性测试推迟到集成测试。最后，有人在某处构建了一个“测试”数据库实例，并开始使用它。这些*解决方案*很快变得根深蒂固，并增加了技术债务。

我不打算讨论“数据库访问和持久性是集成测试的定义的一部分”这一基本观点。我不同意，所以这篇文章坚持这样的观点，即使是单元测试也涉及到持久性测试。

解决这个问题的方法是使用一个嵌入式数据库，它可以在测试中旋转起来并扔掉。大多数数据库框架都支持嵌入式数据库，如 H2 或 SQLite，但是如果您使用特定数据库的专有特性，这些选项只是权宜之计。

## 在围棋测试中嵌入 Postgres

虽然我将在这里专门讨论 Postgres，但这种模式适用于大多数数据库和语言。它可以在您的 IDE、命令行和 CI/CD 中工作。它是彻底的，并且允许测试中的并行性。

在您的代码中，策略是在一个测试或一组测试开始时，运行一个 docker 容器，其中包含允许 Docker 分配地址和端口的数据库。让您代码连接到数据库，运行您的计划迁移，运行您的测试，并在测试结束时，终止容器。

*   优点:您获得了所需数据库的合法版本，可以运行全功能测试，数据库是干净的，没有交叉污染，并且您还测试了您的方案迁移！
*   缺点:这将增加几秒钟的测试时间，并且像认证和集群这样的特性可能与其他环境中的不一样。

利大于弊，相信我。

## 实施

对于 Go 和 Postgres，我们将采用以下方法:

*   标准的“测试”和“证明”模块
*   github.com/testcontainers/testcontainers-go 模块
*   基于阿尔卑斯山的 Postgres docker 图像
*   用于持久性测试的 [GORM](https://gorm.io/index.html) 和 [go-pg](https://github.com/go-pg/pg) 模块
*   一点代码就可以很好地整合这些内容
*   Github 用 CI 展示了这一点

下面的示例代码可以在 [Github](https://github.com/nwillc/testdb) 上找到，但是我将在这里提供一些亮点。首先，让我们编写一个配置类:

```
package dbutil

import (
   "fmt"
   "net/url"
)

//DbConf holds the database configuration information.
type DbConf struct {
   Username   string
   Password   string
   Scheme     string
   Port       int
   Database   string
   mappedHost string
   mappedPort int
   flags      map[string][]string
}

//NewDbConf creates a new DbConf with the prerequisite information.
func NewDbConf(username string, password string, scheme string, port int, database string) *DbConf {
   return &DbConf{
      Username: username,
      Password: password,
      Scheme:   scheme,
      Port:     port,
      Database: database,
      flags:    make(map[string][]string),
   }
}

//Flag adds a configuration flag to the DbConf
func (c *DbConf) Flag(name string, value ...string) *DbConf {
   c.flags[name] = value
   return c
}

//Mapped adds the host and port mapped by a container.
func (c *DbConf) Mapped(mappedHost string, mappedPort int) *DbConf {
   c.mappedHost = mappedHost
   c.mappedPort = mappedPort
   return c
}

//String implements fmt.Stringer and produces a DSN format string.
func (c *DbConf) String() string {
   dsn := url.URL{
      User:     url.UserPassword(c.Username, c.Password),
      Scheme:   c.Scheme,
      Host:     fmt.Sprintf("%s:%d", c.mappedHost, c.mappedPort),
      Path:     c.Database,
      RawQuery: url.Values(c.flags).Encode(),
   }
   return dsn.String()
}
```

这将收集配置数据库所需的信息，稍后将使用我们启动的容器的一些配置进行更新。它包括生成数据库连接字符串的代码。

接下来，我们将创建一个测试助手函数来启动容器，将它的终止添加到测试清理中，并使用容器信息更新配置。这里我们使用[官方 Postgres 镜像](https://hub.docker.com/_/postgres)、 [testcontainers-go 模块](https://github.com/testcontainers/testcontainers-go)并集成到[测试模块](https://golang.org/pkg/testing/):

```
package model

import (
   "context"
   "fmt"
   "github.com/docker/go-connections/nat"
   "github.com/testcontainers/testcontainers-go"
   "github.com/testcontainers/testcontainers-go/wait"
   "godb/dbutil"
   "testing"
   "time"
)

const (
   image    = "postgres:12.4-alpine"
   logMsg   = "database system is ready to accept connections"
)

//EmbeddedPostgres spins up a postgres container.
func EmbeddedPostgres(t *testing.T, conf *dbutil.DbConf) {
   t.Helper()
   ctx := context.Background()
   natPort := fmt.Sprintf("%d/tcp", conf.Port) // Setup and startup container
   req := testcontainers.ContainerRequest{
      Image:        image,
      ExposedPorts: []string{ natPort },
      Env: map[string]string{
         "POSTGRES_PASSWORD": conf.Password,
         "POSTGRES_USER":     conf.Username,
         "POSTGRES_DATABASE": conf.Database,
      },
      WaitingFor: wait.ForLog(logMsg).
         WithPollInterval(100 * time.Millisecond).
         WithOccurrence(2),
   }
   pg, err := testcontainers.GenericContainer(
      ctx,
      testcontainers.GenericContainerRequest{
      ContainerRequest: req,
      Started:          true,
   },
   )
   if err != nil {
      t.Error(err)
   } // Even after log message found Postgres needs a touch more...
   time.Sleep(200 * time.Millisecond) // When test is done terminate container
   t.Cleanup(func() {
      _ = pg.Terminate(ctx)
   }) // Get the container info needed
   mp, err := pg.MappedPort(ctx, nat.Port(natPort))
   if err != nil {
      t.Error(err)
   }
   ma, err := pg.Host(ctx)
   if err != nil {
      t.Error(err)
   }

   // Note the containers mapped host and port
   conf.Mapped(ma, mp.Int())
}
```

跟随代码注释，看看那里发生了什么。

## 在测试中使用

让我们在测试中使用这些。下面使用一个测试套件作为例子，来设置在多个测试中使用的数据库。我们将为 [GORM](https://gorm.io/index.html) 使用一个简单的 Person 结构来保持测试:

```
package model

type Person struct {
   ID        uint   `json:"id"`
   FirstName string `json:"firstname"`
   LastName  string `json:"lastname"`
}
```

下面是使用内嵌 Postgres 和 GORM 的测试套件:

```
package model

import (
   "github.com/stretchr/testify/assert"
   "github.com/stretchr/testify/suite"
   "godb/dbutil"
   "gorm.io/driver/postgres"
   "gorm.io/gorm"
   "testing"
)// The test Suite fixture
type PersonDbTestSuite struct {
   suite.Suite
   db *gorm.DB
}// Setup the test suite fixture
func (suite *PersonDbTestSuite) SetupTest() {
   conf := dbutil.NewDbConf(
      "postgres",
      "admin",
      "postgres",
      5432,
      "postgres",
   )
   // Fire up the embedded Postgres
   EmbeddedPostgres(suite.T(), conf)
   // Open the GORM connection to it
   db, err := gorm.Open(
      postgres.Open(conf.String()),
      &gorm.Config{},
   )
   if err != nil {
      suite.T().Error(err)
   }
   // Explicitly clean up GORM after the test
   suite.T().Cleanup(func() {
      sqlDb, _ := db.DB()
      _ = sqlDb.Close()
   })
   suite.db = db
   // Use gorm's migration to set up our table
   if err := suite.db.AutoMigrate(&Person{}); err != nil {
      suite.T().Error(err)
   }
}// Run tests in the suite
func TestPersonDbTestSuite(t *testing.T) {
   suite.Run(t, new(PersonDbTestSuite))
}// This suite's single test
func (suite *PersonDbTestSuite) TestWrite() {

   // Create a person
   p1 := Person{FirstName: "John", LastName: "Doe"}

   // Persist it to database
   suite.db.Create(&p1)
   var p2 Person

   // Find the last Person in the database
   suite.db.Last(&p2)

   // Compare...
   assert.Equal(suite.T(), p1.ID, p2.ID)
   assert.Equal(suite.T(), p1.FirstName, p2.FirstName)
   assert.Equal(suite.T(), p1.LastName, p2.LastName)
}
```

同样，按照代码注释来查看发生了什么。repo 包含并行 go-pg 示例。

从冷启动开始，上面的代码运行了大约 14 秒，在冷启动时，它需要提取 Postgres 映像，在冷启动后，一旦映像是本地的，大约需要 3 秒。对于一个新的一次性数据库来说，每次都不错。

## 问题

这里有一些问题:

*   如前所述，您不会测试完全认证的数据库连接。只要对图像和证书之类的东西做一点工作，就可以解决这个问题。
*   Postgres 映像的不同版本支持不同的、部分功能性的支持，比如设置不同的用户、设置不同的数据库、多个数据库。我使用了默认值，但是在过去，我使用了自己的 Postgres docker 映像来支持其他配置。
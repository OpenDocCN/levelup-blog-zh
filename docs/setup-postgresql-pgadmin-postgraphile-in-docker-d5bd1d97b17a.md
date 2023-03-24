# 如何在 Docker 中设置 PostgreSQL+pg admin+PostGraphile

> 原文：<https://levelup.gitconnected.com/setup-postgresql-pgadmin-postgraphile-in-docker-d5bd1d97b17a>

## Docker、PostgreSQL、pgAdmin 和 PostGraphile 的组合是让应用程序数据库和 GraphQL API 运行的终极武器。

![](img/60aff5e8e21bea408cc8acc7500f65ee.png)

我试图建立一个简单的 PostGraphile 的例子，我没有找到任何简单的步骤，我决定创建这个。

GraphQL 是一个与服务器端数据通信的绝佳解决方案。它允许开发人员创建快速可靠的 API，通过允许那些与服务器交互的人指定服务器所需数据的精确结构，来解决数据的过度获取和不足获取问题。这意味着为那些使用 GraphQL APIs 构建应用程序的人提供更好的开发人员体验，也为最终用户提供更快的应用程序。

PostGraphile ，以前被称为 PostGraphQL，在将这两种技术结合起来方面做得很好，允许开发人员快速组装一个全功能的 GraphQL 服务器，在 PostgreSQL 数据库上存储数据。PostGraphile 利用数据库驱动的开发从 Postgres 数据库模式生成和更新您的 Graphql 服务器，自动检测您对模式所做的更改，并相应地更新您的服务器。

用他们自己的话说:

> PostgreSQL 已经有了惊人的授权和关系基础设施，为什么还要在自定义 API 中复制这种逻辑呢？

PostGraphile 处理高性能和符合标准的 GraphQL API 层的创建，允许开发人员专注于产品。这也大大减少了开发时间。

PostGraphile 还拥有一个强大的插件系统，有几个[社区开发的插件](https://www.graphile.org/postgraphile/community-plugins/)，可以帮助以各种方式扩展其功能。

在本文中，我们将了解如何使用 PostGraphile 在几分钟内启动并运行一个全功能的服务器。

![](img/caff16e653c973cdd7d9b093f9c3ff14.png)

# 设置网络以执行容器

```
$ docker network create --driver bridge postgres-network
```

该网络将用于容器之间的交互。

# 设置 Postgres 容器

以下命令下载一个 [PostgreSQL docker 映像](https://hub.docker.com/_/postgres/):

```
$ docker pull postgres
```

使用`postgres`图像运行 Docker 容器。为了保留在容器中创建的数据库和数据，必须使用以下参数运行映像:

```
$ docker run --name postgres --network=postgres-network -v /home/user/project/postgresql/data:/var/lib/postgresql/data -e POSTGRES_PASSWORD=postgres -p 5432:5432 -d postgres
```

## 参数描述:

*   **名称**:集装箱的名称。在上面的例子中，值是 **postgres** ，但是您可以选择自己喜欢的名称。
*   **网络**:我们之前为容器交互创建的桥的名称。
*   **v** : Volume，你主机上的 project 文件夹和你容器中的 PostgreSQL 文件夹之间的映射，格式`host_folder:container_folder`。在`container_folder`中生成的所有文件将被复制到`host_folder`中，这样当停止/重启容器时，您可以保存和检索您的数据。将主机文件夹替换为您选择的目标。如果它不存在，将自动创建它。
*   **e** :容器的环境变量。`POSTGRES_PASSWORD`设置 PostgreSQL 超级用户的密码。在上面的例子中，值是`postgres`，但是你应该选择一个更强的密码。
*   **p** : Port，你的主机端口和你的容器端口之间的映射，格式`host_port:container_port`。
*   **d** :以守护程序的方式运行容器。
*   **postgres** :用于运行容器的 Docker 图像的名称。

在这之后，你应该让你的 Postgres 运行在 [0.0.0.0:5432](http://0.0.0.0:5432) 中。

# 设置 pgAdmin 容器

以下命令下载 pgAdmin 映像:

```
$ docker pull dpage/pgadmin4
```

使用以下命令运行 pgAdmin 容器:

```
docker run --name pgadmin --network=postgres-network -p 80:80 -e PGADMIN_DEFAULT_EMAIL=admin -e PGADMIN_DEFAULT_PASSWORD=admin -d dpage/pgadmin4
```

## 参数描述:

*   **名称**:集装箱的名称。在上面的例子中，值是 **pgadmin** ，但是您可以选择自己喜欢的名称。
*   **网络**:我们之前为容器交互创建的桥的名称。
*   **e** :容器的环境变量。`PGADMIN_PASSWORD`设置 pgAdmin 超级用户的密码，`PGADMIN_DEFAULT_EMAIL`设置 pgAdmin 超级用户的电子邮件。在上面的两个例子中，值都是`admin`。
*   **p** : Port，你的主机端口和你的容器端口之间的映射，格式`host_port:container_port`。
*   **d** :以守护程序的方式运行容器。
*   **dpage/pgadmin4** :用于运行容器的 Docker 映像的名称。

在这之后，你应该让你的 pgAdmin 在 [0.0.0.0:80](http://0.0.0.0:80) 中运行。

# 创建 pgAdmin 和数据库之间的连接

转到 pgAdmin 并使用默认密码登录。

![](img/e1016d233e64161d967c3fd7800d6332.png)

创建新服务器后:

![](img/a293416bdd387e51572101fb51d66a9e.png)![](img/686ac38e8fb821431d8248bc76106043.png)![](img/314b4f58e9a8eb9096638ca24a3a77c8.png)

然后单击保存。你会有一个相似的结构:

![](img/a58baac53fc91b9a6de04bd60dabd7e3.png)

我们已经连接了数据库，让我们通过单击查询工具来创建一些关系，您将看到查询编辑器:

![](img/e4bf6d725969f81f6fd4f55ca9b9852d.png)

我们的数据库将是这样的:

![](img/337f3e3e44a5316635f72aef9add80ab.png)

因此，运行以下 SQL:

```
CREATE SCHEMA your_schema;CREATE TABLE your_schema.parent_table (
    id SERIAL PRIMARY KEY,
    name TEXT,
    description TEXT,
    created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);COMMENT ON TABLE your_schema.parent_table IS
'Provide a description for your parent table.';CREATE TABLE your_schema.child_table (
    id SERIAL PRIMARY KEY,
    name TEXT,
    description TEXT,
    created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    parent_table_id INTEGER NOT NULL REFERENCES your_schema.parent_table(id)
);COMMENT ON TABLE your_schema.child_table IS
'Provide a description for your child table.';
```

让我们插入一些数据:

```
INSERT INTO your_schema.parent_table (name, description) VALUES
('Parent name 1', 'Parent description 1'),
('Parent name 2', 'Parent description 2'),
('Parent name 3', 'Parent description 3');INSERT INTO your_schema.child_table (name, description, parent_table_id) VALUES
('Child name 1', 'Child description 1', 1),
('Child name 2', 'Child description 2', 2),
('Child name 3', 'Child description 3', 3);
```

如果您想查看数据:

```
SELECT A.id, A.name, A.description, B.id, B.name, B.description
FROM your_schema.parent_table A
INNER JOIN your_schema.child_table B ON A.id=B.parent_table_id;
```

# 设置后期图像

以下命令下载一个[postgrapile docker 图像](https://hub.docker.com/r/graphile/postgraphile/)。

```
$ docker pull graphile/postgraphile
```

为了将 PostgreSQL 容器连接到 PostgreSQL 容器，必须使用以下参数运行`graphile/postgraphile`图像:

```
$ docker run --name pgql -p 5000:5000 -d graphile/postgraphile --connection postgres://postgres:postgres@host:5432/your_database --schema your_schema --watch
```

参数描述:

*   **名称**:集装箱的名称。在上面的例子中，值是 **pgql** ，但是您可以选择一个自己喜欢的名称。
*   **p** : Port，你的主机端口和你的容器端口之间的映射，格式`host_port:container_port`。在上面的命令中，端口都被设置为 **5000** 。
*   d :以守护进程的方式运行容器。
*   **graphile/postgraphile** :用于运行容器的 Docker 图像的名称。
*   **连接**:PostgreSQL 容器和数据库的连接字符串，格式为`postgres://db_user:password@host:port/your_database`。在上面的命令中，值设置如下:
*   `db_user` : PostgreSQL 超级用户是 **postgres** 。
*   `password`:您在运行 postgres 容器时用于`POSTGRES_PASSWORD`参数的值。
*   `host`:运行 postgres 容器的本地机器的 IP 地址。你可以在终端窗口中输入`ifconfig`找到它。
*   `port`:在 postgres 容器上打开的端口。上一步打开的端口是 **5432** ，这是 PostgreSQL 的默认端口。
*   `your_database`:PostgreSQL 数据库的名称。
*   **模式**:PostgeSQL 数据库中模式的名称。
*   **watch**:watch 标志自动检测对数据库所做的更改，并相应地重新加载 GraphiQL 接口。

# 测试 GraphQL API

导航到[http://0 . 0 . 0 . 0:5000/GraphiQL](http://0.0.0.0:5000/graphiql)以可视化 API GraphiQL 文档。从那里，您可以执行查询和变异，如下例所示。API 端点本身就是[http://0 . 0 . 0 . 0:5000/graph QL](http://0.0.0.0:5000/graphql)。

# 查询示例

```
query {
  allParentTables {
    nodes {
      id
      name
      description
      childTablesByParentTableId {
        nodes {
          id
          name
          description
        }
      }
    }
  }
}
```

![](img/f3e258bcae70de0f5efcd0ce09f006ec.png)

# 突变示例

```
mutation {
  createParentTable(
    input: {
      parentTable: {
        name: "Parent name 4"
        description: "Parent description 4"
      }
    }
  ) {
    parentTable {
      id
      name
      description
    }
  }
}
```

![](img/b140c9a79da6aa2815f62ef0035ac46a.png)

正如您所看到的，您有一个功能性的 GraphQL API！

理解了这些概念之后，你就可以使用 docker-compose 文件轻松地创建和开发容器了，请阅读下面的文章:

[](/setup-postgresql-in-10-seconds-c22f7a1369cf) [## 在 10 秒内设置 PostgreSQL

### 上个月我写了一篇关于如何在 Docker 中设置 PostgreSQL+pg admin+PostGraphile 的文章，有一个…

levelup.gitconnected.com](/setup-postgresql-in-10-seconds-c22f7a1369cf)
# 在 GraphQL 中以声明方式连接 PostgreSQL 中的数据，而无需编写 SQL

> 原文：<https://levelup.gitconnected.com/join-data-from-postgresql-declaratively-in-graphql-without-writing-sql-e27489d03cac>

![](img/bbf8e4b12e1757b8ad95560dd28d61b1.png)

SQL 是与 PostgreSQL 等关系数据库交互的首选方式。在为 PostgreSQL 数据库创建 API 时，无论是 REST 还是 GraphQL，您都需要了解 SQL 来获取和输出数据。ORM(对象关系映射)工具通常可以帮助开发人员与数据库进行通信，这些工具抽象了一些构建 API 所需的 SQL 知识。

尽管这些工具可以帮助您入门，但它们并不能帮助您创建高效的 SQL。您不会是第一个编写低效查询，导致数据库过载或超时的开发人员。在 StepZen，我们已经构建了工具和服务来以声明方式创建 GraphQL APIs 包括改进您与数据库连接和通信的方式。这消除了编写高效 SQL 查询的一些痛苦。

在本文中，我将展示如何为 PostgreSQL 数据库自动生成 GraphQL API，并仅使用 GraphQL SDL 以声明方式连接数据。如果您认为 SQL 已经是声明性的，我想您会喜欢 StepZen 中的自定义指令。

## 从 PostgreSQL 创建一个 GraphQL API

使用 StepZen，您可以为任何数据源创建 GraphQL API，包括 PostgreSQL 数据库。如果已经有了一个数据库，可以使用 StepZen CLI 将其模式导入 GraphQL API。否则，您可以在[本页](https://stepzen.com/getting-started?details=examples&example=postgresql)使用我们演示数据库中的凭证。

从终端/命令行使用`psql`连接到数据库:

```
psql -h postgresql.introspection.stepzen.net -d introspection -U testUserIntrospection
```

用密码:`HurricaneStartingSample1934`。

一旦连接上，您就可以与 PostgreSQL 数据库通信了。使用命令`\dt`，您可以检查这个数据库中的表:

```
List of relations
 Schema |      Name       | Type  |  Owner
--------+-----------------+-------+----------
 public | address         | table | postgres
 public | customer        | table | postgres
 public | customeraddress | table | postgres
 public | lineitem        | table | postgres
 public | order           | table | postgres
 public | product         | table | postgres
(6 rows)
```

正如您在上面的结果中看到的，数据库有六个表。我们可以使用 SQL 查询每个表，或者使用 StepZen 从这个数据库中获取数据。因此，我们首先需要基于这个数据库的自省生成一个 GraphQL API。

要为这个数据库生成 GraphQL API，您需要安装 StepZen CLI:

```
npm i -g stepzen
```

并运行命令:

```
stepzen import postgresql
```

CLI 会询问数据库凭证，如果您自己没有 PostgreSQL 数据库，您可以从我们的[入门示例](https://stepzen.com/getting-started?details=examples&example=postgresql)中复制这些凭证。

> **注意**:CLI 将询问您是否想要使用`@materializer`链接类型。在此选择“是”。

当 CLI 完成 PostgreSQL 模式的导入并生成 GraphQL 模式时，您可以找到一个名为`postgresql/index.graphql`的新文件。该文件包含数据库的 GraphQL 模式，并具有类型声明和一组操作。要部署 GraphQL 模式并创建 API，可以运行`stepzen start`。

然后，StepZen 部署 GraphQL 模式，并在终端中直接返回您的端点。

让我们在下一节继续，我们将在 GraphQL 模式中查看来自不同表的连接数据。

## 将表格与`@materializer`链接

通过运行`stepzen import postgresql`为 PostgreSQL 数据库生成的 GraphQL 模式已经包含了一个查询，该查询组合了来自不同数据库表的数据。假设您想要获取一个客户的地址，该地址存储在一个单独的表中；您需要在 SQL 中连接不同的表。

当使用 SQL 获取 id 为`1`的客户以及该客户的地址时，您需要编写如下 SQL 查询:

```
SELECT T."city", T."countryregion", T."id", T."postalcode", T."stateprovince", T."street" FROM "public"."address" T, "public"."customeraddress" V WHERE V."customerid" = 1 AND V."addressid" = T."id"
```

这个 SQL 查询组合了来自表`customer`和`customeraddress`的数据。

在 StepZen 中，您可以使用这个相同的“原始”SQL 查询创建一个 GraphQL 查询来组合这些表。但是您也可以以声明的方式组合这些数据，而无需编写 SQL 查询。例如，当您使用 GraphQL 查询`getCustomer,`时，StepZen 从`table`客户那里获取信息，正如您在下面的`@dbquery`指令中看到的:

```
getCustomer(id: Int!): Customer
    @dbquery(
      type: "postgresql"
      schema: "public"
      table: "customer"
      configuration: "postgresql_config"
    )
```

但是看看它的响应类型`Customer.`注意，请求的字段包含地址信息和客户下的订单。响应类型`Customer`包含到这些表的连接，使用自定义指令`@materializer`。

```
type Customer {
  addressList: [Address] @materializer(query: "getAddressUsingCustomeraddress")
  email: String!
  id: Int!
  name: String!
  orderList: [Order] @materializer(query: "getOrderUsingCustomerid")
}
```

当您查询`getCustomer`时，StepZen 首先从`table`客户那里获取信息。当您包含字段`addressList`或`orderList`时，它使用`@materializer`中链接的 GraphQL 查询来获取这些字段的数据。

下面的 GraphQL 查询从 PostgreSQL 数据库的`customer`表中获取字段`id`和`email`的数据；通过执行`getAddressUsingCustomerid`查询得到`addressList`的数据。字段`Customer.id`的值作为参数传递给这个查询。

```
{
  getCustomer(id: "1") {
    id
    email
    addressList {
      street
      city
    }
  }
}
```

查询`getAddressUsingCustomerid`使用一个原始 SQL 查询来获取基于客户 id 的地址。这很简单，因为`customeraddress`表包含字段`customerid`:

```
getAddressUsingCustomerid(id: Int!): [Address]
    @dbquery(
      type: "postgresql"
      query: """
      SELECT T."city", T."countryregion", T."id", T."postalcode", T."stateprovince", T."street"
        FROM "public"."address" T, "public"."customeraddress" V
        WHERE V."customerid" = $1
          AND V."addressid" = T."id"
      """
      configuration: "postgresql_config"
    )
```

除了包含原始 SQL 查询或使用`@materializer`之外，您还可以使用另一个定制指令(`@sequence`)来执行一系列查询并收集结果，您将在下一节中看到。

## 使用`@sequence`收集数据

有时，要合并的数据并不直接在另一个表中，而是在一个表中，该表通过一个包含要连接的字段的表链接。假设您想要合并本文中使用的数据库中的表`order`和`product`;您将看到两个表都没有对另一个表的引用。相反，数据库包含一个名为`lineitem`的表，该表只包含字段`orderid`和`productid`。

您可以使用这个表来连接原始 SQL 查询中的数据，以获得订单的产品信息。并将其链接到新的 GraphQL 查询:

```
getProductsUsingOrderid(orderid: Int!): [Product]
  @dbquery(
    type: "postgresql"
    query: """
    SELECT T."id", T."title", T."description", T."image" FROM "public"."product" T, "public"."lineitem" V WHERE V."orderid" = $1 AND T."id" = V."productid"
    """
    configuration: "postgresql_config"
  )
```

这个查询可以用`@materializer`指令链接到`Order`类型。这样，您可以将产品信息添加到订单中。但是您可以做同样的事情，而不需要编写原始 SQL 来连接表`lineitem`和`product`。

相反，你可以使用自定义指令`@sequence`。使用`@sequence`您可以分步执行查询并收集结果:

```
getProductsUsingOrderid(id: Int!): [Product]
    @sequence(
      steps: [
        { query: "getLineitemUsingOrderid" }
        { query: "getProduct", arguments: [{ name: "id", field: "productid" }] }
      ]
    )
```

上面的代码首先执行查询`getLineitemUsingOrderid`并获取订单产品的 id。这些 id 被传递给`getProduct`查询来收集产品信息。这样，您就可以获得产品数据，而无需编写 SQL 查询来连接不同数据库表中的数据。

你可以用这个序列做更多的事情。假设您想要限制这个新的 GraphQL 查询返回的字段数量。然后，您可以使用一个`collect` GraphQL 查询来只收集您想要公开的字段:

```
collect(
    id: Int!
    title: String
  ): Product @connector(type: "echo")
getProductsUsingOrderid(id: Int!): [Product]
    @sequence(
      steps: [
        { query: "getLineitemUsingOrderid" }
        { query: "getProduct", arguments: [{ name: "id", field: "productid" }] }
        { query: "collect" }
      ]
    )
```

通过添加这第三步，由`product`表公开的字段现在只限于`id`和`title`。您还可以使用这个`collect`查询从`@sequence`中的任何其他步骤获取数据。

## 结论

在本文中，您了解了如何连接不同 PostgreSQL 表中的数据，而不必编写原始的 SQL 查询。相反，您可以使用 StepZen 的自定义指令来声明性地构建和联合 GraphQL APIs。我们很想听听你开始用 StepZen 和 PostgreSQL 构建什么项目。加入我们的 [Discord](https://discord.com/invite/9k2VdPn2FR) 来了解我们社区的最新动态。
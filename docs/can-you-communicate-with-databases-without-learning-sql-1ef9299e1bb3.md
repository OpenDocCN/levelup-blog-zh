# 不学 SQL 能和数据库交流吗

> 原文：<https://levelup.gitconnected.com/can-you-communicate-with-databases-without-learning-sql-1ef9299e1bb3>

第二部分——异议

![](img/91646525e492a31e93224fff4c07824b.png)

> Objection.js 是另一个 Node.js ORM [(就像 Sequelize)](https://www.theimmigrantprogrammers.com/p/can-you-communicate-with-databases) ，它的目标是让你尽可能容易地使用 SQL 和底层数据库引擎的全部功能，同时仍然让普通的东西变得容易和有趣。
> 
> —根据 [Objection.js 官方文档](https://vincit.github.io/objection.js/)

为了更好地理解 ObjectionJS 的作用，我们将使用*在线购物系统*的概念。为了简单起见，我们将只处理以下两个实体， *i)客户，ii)订单。*每个客户可以下一个或多个订单，每个订单属于一个客户。

# 1)安装

你可以使用`npm`或者`yarn`来安装 Objection.js，Objection 在后台使用 **knex SQL Query Builder** 作为它的数据库访问层，所以你也需要安装那个。

```
*npm install objection knex

yarn add objection knex*
```

您还必须为您选择的数据库手动安装下列驱动程序之一:

```
*npm install pg
npm install sqlite3
npm install mysql
npm install mysql2*
```

本文中的所有例子都基于 MySQL 驱动程序，即 mysql2。此外，数据库运行在我的本地机器上。

# 2)建立数据库连接

将以下代码放入您的**中。/util/database.js**

```
const { Model } = require('objection');
const Knex = require('knex');

// Initialize knex.
const knex = Knex({
  client: 'mysql',
  connection: {
    host: "127.0.0.1",
    user: "your_username", //replace with your username
    password: "your_password", //replace with your password
    database: "your_db_name" // replace with your db name
  },
});

// Give the Knex instance to Objection.
Model.knex(knex);

module.exports = knex;
```

# 3)定义模型和定义关系

模型是表示数据库中的表的抽象。在异议中，它是一个扩展了[模型](https://sequelize.org/master/class/lib/model.js~Model.html)类的类。

通过在您的**中放置以下代码来定义客户模型。/models/customer.js**

```
const { Model } = require('objection');
const Order = require('./order');

class Customer extends Model {
  static get tableName() {
    return 'customers';
  };

  $beforeInsert() {
    this.createdAt = new Date();
  };

  $beforeUpdate() {
    this.updatedAt = new Date();
  };

  static get nameColumn() {
    return 'name';
  };

  static get emailColumn() {
    return 'email';
  };

  static get jsonSchema() { //Optional JSON Schema Validation
    return {
      type: 'object',
      required: ['name', 'email'],
      properties: {
        id: { type: 'integer' },
        name: { type: 'string', minLength: 1, maxLength: 255 },
        email: { type: 'string' },
        createdAt: { type: 'string' },
        updatedAt: { type: 'string' }
      }
    };
  };

  static relationMappings = {
    order: {
      relation: Model.HasOneRelation,
      modelClass: Order,
      join: {
        from: 'customers.id',
        to: 'orders.customer_id'
      }
    }
  };

};

module.exports = Customer;
```

每个顾客都有一个 **id、**一个**名字、**和一个**电子邮件**。这代表了客户表的 3 个字段/列。因此，订单模型通过 *hasOne* 关系与客户模型相关联。现在，我们必须通知这个关联的异议，我们将通过定义`relationMappings` 对象来完成。

通过在您的**中放置以下代码来定义订单模型。/models/order.js**

```
const { Model } = require('objection');

class Order extends Model {
  static get tableName() {
    return 'orders';
  };

  $beforeInsert() {
    this.createdAt = new Date();
  };

  $beforeUpdate() {
    this.updatedAt = new Date();
  };

  static get totalColumn() {
    return 'total';
  };

  static get customerIdColumn() {
    return 'customer_id';
  };

  static get jsonSchema() { //Optional JSON Schema Validation
    return {
      type: 'object',
      required: ['total'],
      properties: {
        id: { type: 'integer' },
        total: { type: 'number' },
        customer_id: { type: 'integer' },
        createdAt: { type: 'string' },
        updatedAt: { type: 'string' }
      }
    };
  };
};

module.exports = Order;
```

每个订单将有一个 **id** 和一个**总**购买价格。这表示订单表的两个字段/列。

# 4)编写查询

在下面的代码片段中，我们执行以下操作:

I)从数据库中删除所有预先存在的客户/订单。

ii)插入新客户。

iii)获取我们的*客户*表中所有客户的列表。

iv)使用 ***customerId*** 外键为该客户插入一个新订单。

v)选择当前客户的所有订单。

```
const Customer = require('./models/customer');
const Order = require('./models/order');
const knex = require('./util/database');

async function main() {
    await Customer.query().delete();
    await Order.query().delete();

    // Insert one row to the database.
    const customer = await Customer.query().insert({
      name: 'Rachel Green',
      email: 'rg@gmail.com',
    });

    // Read all rows from the db.
    const customerRead = await Customer.query();
    console.log(customerRead);

    const order = await Customer.relatedQuery('order')
                        .for(customer.id)
                        .insert({ total: 55 });
    console.log(order);

    const orderTotal = await Order.query()
                                .select('total')
                                .where('customer_id', '=', customer.id)
                                .orderBy('total');

    console.log(orderTotal);
  }

  main()
    .then(() => knex.destroy())
    .catch((err) => {
      console.error(err);
      return knex.destroy();
    });
```

此外，不要忘记在你完成后销毁 ***knex*** 物体！

所以，这就是这个有用的 Node.js ORM。我希望本文提供的信息对您有价值，并帮助您简化和优化数据库操作。此外，您可以在这里阅读 ORMs 系列的第一部分，[不学习 SQL，能否与数据库通信，第 1 部分— Sequelize。](/can-you-communicate-with-databases-without-learning-sql-8bb6a50999d7)

# 其他资源:

*   [*阅读更多关于异议的信息*](https://vincit.github.io/objection.js/)
*   [*链接到我的 GitHub 库*](https://github.com/KritikaSharmaKS/Objection-Youtube)

*原载于 https://www.theimmigrantprogrammers.com*[](https://www.theimmigrantprogrammers.com/p/can-you-communicate-with-databases-d49)**。**
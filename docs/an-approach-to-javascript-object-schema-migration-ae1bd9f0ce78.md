# 一种 JavaScript 对象模式迁移的方法

> 原文：<https://levelup.gitconnected.com/an-approach-to-javascript-object-schema-migration-ae1bd9f0ce78>

![](img/ee62e03627dc09e7ada8fa9c0b2d6f9e.png)

# 介绍

最近，我发现自己的应用程序严重依赖于状态对象。这对于单页应用程序(SPAs)来说是相当典型的，当您的状态对象的模式发生重大变化，并且您的用户将数据保存在旧模式下时，这可能会带来挑战。

在这篇文章中，我将探索我为解决这个问题而提出的一个概念验证解决方案。虽然我确信已经存在模式迁移工具，但我认为这将是一个有趣且有教育意义的主题探索！

***

## 通过注册我的免费时事通讯，在您的收件箱中获得快速 JavaScript 技巧！

***

# 一个例题

假设我已经创建了一个应用程序，其中有一个用户，用户可以输入他们的宠物类型和品种。在启动 MVP 时，我的状态对象看起来像这样:

```
const state = {
  person: {
    name: 'Edgar',
    pets: {
      type: 'dog',
      name: 'Daffodil',
    },
  },
};
```

这对 MVP 来说很好，但很快我意识到我不希望`pets`地产生活在`person`地产下，而是希望它在`state`下成为自己的地产。换句话说，我的理想状态可能是这样的:

```
const state = {
  person: {
    name: 'Edgar',
  },
  pets: {
    type: 'dog',
    name: 'Daffodil',
  },
};
```

虽然我希望能够在我的 SPA 中进行这种更改，但我担心现有的应用程序用户会将我的原始模式保存在某个地方(例如，本地存储、nosql、JSON 字符串等)。).如果我加载了旧的数据，但是我的应用程序需要新的模式，我可能会尝试在错误的地方访问属性(例如，`state.pets.type`对`state.person.pets.type`)，从而导致问题。

# 模式迁移拯救世界！

模式迁移不是一个新概念；它被用于在不同版本的应用程序之间迁移数据库表已经有一段时间了。在这篇文章中，我将使用模式迁移背后相同的基本概念来迁移 JavaScript 对象。

# 定义我们的迁移阵列

让我们定义一组要运行的迁移。每个迁移将有一个`from`、`to`、`up`和`down`属性。`from`和`to`属性将分别代表较低和较高的版本，而`up`和`down`属性将是将模式从`from`版本移动到`to`版本的函数，反之亦然。这听起来可能有点混乱，但是我认为在我们的人/宠物的例子中会更有意义。

让我们写第一个迁移。

```
const migrations = [
  {
    from: '1.0',
    to: '1.1',
    up: schema => {
      const newSchema = {
        version: '1.1',
        person: {
          name: schema.person.name,
        },
        pets: {
          ...schema.person.pets,
        },
      };
      return newSchema;
    },
    down: schema => {
      const newSchema = {
        version: '1.0',
        person: {
          ...schema.person,
          pets: { ...schema.pets },
        },
      };
      return newSchema;
    },
  },
];
```

如果我们有一个“1.0”版本的模式，那么这个对象的`up`方法会将这个模式转换成“1.1”。相反，如果我们有一个“1.1”版本的模式，`down`方法会将该模式转换为“1.0”。

# 实现迁移

这在概念上很酷，但是我们需要创建一个实际执行迁移的函数。为此，我们将创建一个`migrate`函数，它将一个模式和该模式应该迁移到的版本号作为参数。

```
const migrate = (schema, toVersion) => {
  const fromVersion = schema.version;
  const direction = upOrDown(fromVersion, toVersion);
  if (direction === 'same') {
    return schema;
  }
  const currentMigration = migrations.find(
    migration => migration[direction === 'up' ? 'from' : 'to'] === fromVersion
  );
  const newSchema = currentMigration[direction](schema);
  return migrate(newSchema, toVersion);
};
```

您可能会注意到关于这个函数的一些事情:它是递归的(它不会停止，直到我们迁移到我们的目标版本)，并且它引用了一个助手函数，`upOrDown`，我已经在下面定义了它。这个函数只是帮助确定迁移的方向(1.0 到 1.1 向上，1.1 到 1.0 向下)。

```
const upOrDown = (fromVersion, toVersion) => {
  const fromNumbers = fromVersion.split('.').map(el => Number(el));
  const toNumbers = toVersion.split('.').map(el => Number(el));
  for (let i = 0; i < fromNumbers.length; i++) {
    if (fromNumbers[i] < toNumbers[i]) {
      return 'up';
    }
    if (fromNumbers[i] > toNumbers[i]) {
      return 'down';
    }
  }
  return 'same';
};
```

# 带它去试运行

让我们创建两个对象，一个是“1.0”版本的模式，另一个是“1.1”版本的模式。目标是将“1.0”模式迁移到“1.1”，将“1.1”模式迁移到“1.0”。

```
const schemaA = {
  version: '1.0',
  person: {
    name: 'Edgar',
    pets: {
      type: 'dog',
      name: 'Daffodil',
    },
  },
};const schemaB = {
  version: '1.1',
  person: {
    name: 'Edgar',
  },
  pets: {
    type: 'dog',
    name: 'Daffodil',
  },
};
```

现在，让我们运行我们的迁移。

```
// From 1.0 to 1.1
console.log(migrate(schemaA, '1.1'));
/*
{ version: '1.1',
  person: { name: 'Edgar' },
  pets: { type: 'dog', name: 'Daffodil' } }
*/// From 1.1 to 1.0
console.log(migrate(schemaB, '1.0'));
/*
{ version: '1.0',
  person: { name: 'Edgar', pets: { type: 'dog', name: 'Daffodil' } } }
*/
```

完美！我们现在可以从一个模式版本“向上”迁移到下一个版本，或者“向下”迁移回来。

# 又一次架构更改！

我现在意识到一个人可以养多只宠物，为什么不呢？所以，我们的`pets`键实际上应该是一个数组，而不是一个对象。此外，我意识到我们的`person`键可能只是这个人的名字，而不是有一个`name`键(我已经决定我们不会再有任何与这个人相关的道具)。这意味着一个新的模式，版本 1.2，看起来像这样:

```
const state = {
  person: 'Edgar',
  pets: [
    {
      type: 'dog',
      name: 'Daffodil',
    },
  ],
};
```

所以，让我们写一个从版本 1.1 到 1.2 的迁移。

```
const migrations = [
  {
    from: '1.0',
    to: '1.1',
    up: schema => {
      const newSchema = {
        version: '1.1',
        person: {
          name: schema.person.name,
        },
        pets: {
          ...schema.person.pets,
        },
      };
      return newSchema;
    },
    down: schema => {
      const newSchema = {
        version: '1.0',
        person: {
          ...schema.person,
          pets: { ...schema.pets },
        },
      };
      return newSchema;
    },
  },
  {
    from: '1.1',
    to: '1.2',
    up: schema => {
      const newSchema = {
        version: '1.2',
        person: schema.person.name,
        pets: [schema.pets],
      };
      return newSchema;
    },
    down: schema => {
      const newSchema = {
        version: '1.1',
        person: {
          name: schema.person,
        },
        pets: schema.pets[0],
      };
      return newSchema;
    },
  },
];
```

# 多版本迁移

还记得我们的`migrate`函数是如何递归的吗？当我们需要迁移多个版本时，这变得非常有用。假设我们想从 1.0 模式迁移到 1.2 模式，反之亦然。我们能做到！

```
// 1.0 to 1.2
console.log(migrate(schemaA, '1.2'));
/*
{ version: '1.2',
  person: 'Edgar',
  pets: [ { type: 'dog', name: 'Daffodil' } ] }
*/const schemaC = {
  version: '1.2',
  person: 'Edgar',
  pets: [
    {
      type: 'dog',
      name: 'Daffodil',
    },
  ],
};
// 1.2 to 1.0
console.log(migrate(schemaC, '1.1'));
/*
{ version: '1.0',
  person: { name: 'Edgar', pets: { type: 'dog', name: 'Daffodil' } } }
*/
```

嘿，成功了！

# 结论

这是对模式迁移世界的有趣探索！在拼凑了一些模式迁移功能后，我现在相当有信心能够使用“自己动手”的方法或现有的包来实现它。
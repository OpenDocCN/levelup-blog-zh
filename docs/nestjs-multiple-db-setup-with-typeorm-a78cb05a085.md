# 使用 TypeORM 的 NestJS ⚡多数据库设置

> 原文：<https://levelup.gitconnected.com/nestjs-multiple-db-setup-with-typeorm-a78cb05a085>

# 介绍

在这篇文章中，我将通过一个简单的例子来描述如何设置和使用多个数据库连接。

[NestJS 文档](https://docs.nestjs.com/techniques/database#multiple-databases)大部分都很棒，但是在多数据库部分有一些重要的遗漏。

我将假设您已经创建了一个 NestJS 应用程序，并且已经设置了 2 个或更多的数据库并准备好连接。让我们开门见山地说吧。

# 让我们开始吧

## 定义数据库连接选项

在 NestJS 文档之后，我们将找到直接在代码上设置的连接选项示例凭证。

```
...
@Module({
  imports: [
    TypeOrmModule.forRoot({
      type: 'mysql',
      host: 'localhost',
      port: 3306,
      username: 'root',
      password: 'root',
      database: 'test',
      entities: [Customer],
      synchronize: true,
    }),
  ],
})
...
```

我们应该使用环境配置文件或类似的策略，而不是像那样在代码上公开凭证。

对于这个例子，我们将利用`@nestjs/config`包从环境文件中获取数据库凭证。你可以在 [NestJS 配置文档](https://docs.nestjs.com/techniques/configuration)中找到更多关于如何安装和使用配置包的信息。这是一个简单的过程。

```
...
@Module({
  imports: [
    ConfigModule.forRoot({
      isGlobal: true,
    }),
    TypeOrmModule.forRootAsync({
      imports: [ConfigModule],
      inject: [ConfigService],
      useFactory: async (configService: ConfigService) => {
        return {
          type: 'mssql',
          host: configService.get('MAIN_DB_HOST'),
          port: parseInt(configService.get('MAIN_DB_PORT')),
          database: configService.get('MAIN_DB_DATABASE'),
          username: configService.get('MAIN_DB_USERNAME'),
          password: configService.get('MAIN_DB_PASSWORD'),
          schema: configService.get('MAIN_DB_SCHEMA'),
          entities: [Customer],
          synchronize: false,
        };
      },
    }),
...
```

我们不需要为我们的第一个连接定义一个连接名。默认情况下，它将被命名为“默认”😬。对于任何其他定义的连接，我们需要提供一个名称。

NestJS docs 告诉我们，`name`属性应该添加到 options 对象中，与 host、port 等处于同一级别。

```
...
@Module({
  imports: [
    TypeOrmModule.forRoot({
      ...defaultOptions,
      host: 'user_db_host',
      entities: [User],
    }),
    TypeOrmModule.forRoot({
      ...defaultOptions,
      name: 'albumsConnection',
      host: 'album_db_host',
      entities: [Album],
    }),
  ],
})
...
```

现在，因为我们从`.env`文件中获取凭证并使用`forRootAsync` **，我们需要在** `**useFactory**` **函数**之外添加连接 `name` **属性👀。这是一个非常重要的细节，但在文件中却没有提到。**

最终的`app.module.ts`文件应该是这样的。

## 实体

为了这个练习，我们创建了两个实体。客户实体将使用主数据库连接，AccessLog 实体将使用辅助数据库连接。有关 TypeOrm 实体和存储库模式的更多信息，请查看 [NestJS TypeOrm 文档](https://docs.nestjs.com/techniques/database)和 [TypeOrm 文档](https://typeorm.io/entities)。

## 使用主数据库连接

为了访问客户实体 TypeOrm 功能，我们需要将 TypeOrm 导入到客户模块中。

这是我们在客户服务中使用它的方式。

## 使用辅助数据库连接

正如我们对主连接和客户实体所做的那样，我们需要将 TypeOrm 导入 AccessLog 模块，但是这一次我们还需要将一个连接名称传递给`TypeOrmModule.forFeature([Entities, ...], connectionName)`方法。

这就是我们在 AccessLog 服务中使用它的方式。

# 结论

✅:我们设法用来自环境文件的凭证值设置了一个多数据库配置。✅定义了每个实体应该使用什么样的连接。
✅与如何添加一个二级连接的知识，我们应该能够添加更多的需要。
✅了解了异步初始化 TypeOrm 时的一些小技巧。

我真的希望这篇文章是有帮助的。

如果您有任何反馈或发现错误，请随时联系我。
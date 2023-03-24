# 普里斯马❤ PostgreSQL + pgAdmin

> 原文：<https://levelup.gitconnected.com/prisma-postgresql-pgadmin-ba398b7890e8>

让我们通过 **Docker** 用 **PostgreSQL** 和 **pgAdmin** 试试 **Prisma** ，开始吧！

# **TLDR**

[https://github.com/katopz/prisma-postgresql-pgadmin-example](https://github.com/katopz/prisma-postgresql-pgadmin-example)

# 来源

我们将使用[这个文档](https://www.prisma.io/docs/tutorials/bootstrapping-boilerplates/react-(fullstack)-tijghei9go/)生成 web

```
npm install -g prisma graphql-cli
graphql create web
```

![](img/bec43b17bcc2f90b1d97a9241b8a3624.png)

# 发展

## 1.上升`prisma`、`postgres`、`pgadmin`

```
cd dev
. up
```

![](img/78b5747d062888cc194cce6aea52349e.png)

## 2.Prisma 服务器

```
cd web/server
yarn dev
```

![](img/474218927edfcbe8a58b8b7ec6c1ed01.png)

## 3.阿波罗网络客户端

> 邮箱:`*developer@example.com*` 密码:`*nooneknows*`

```
cd web
yarn install
yarn start
```

![](img/1bd996bbc9eb9f7e1d055e1aba3c3de8.png)

## 4.PostgreSQL 管理

> 连接:`postgres` SSL : `Disable`

```
open [http://localhost:5050](http://localhost:5050)
```

![](img/3c409bd7c82ca11868bc20c2967e28b3.png)

完成了！感谢 Docker Compose！

[![](img/d9fe9ae1a34de6a28338eae427dd6438.png)](https://levelup.gitconnected.com)
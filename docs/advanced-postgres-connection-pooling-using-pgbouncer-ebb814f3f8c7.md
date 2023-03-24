# 使用 PgBouncer 的高级 Postgres 连接池

> 原文：<https://levelup.gitconnected.com/advanced-postgres-connection-pooling-using-pgbouncer-ebb814f3f8c7>

![](img/575e9c00f39be16c89ae96d691bc7b47.png)

# 使用 PgBouncer 扩展 Django/Postgres

有没有使用 Postgres 的 app？数据库连接快用完了吗？也许你已经尝试过设置 [CONN_MAX_AGE](https://docs.djangoproject.com/en/3.0/ref/settings/#conn-max-age) ，但那只帮了一会儿。是时候推出 P [gBouncer](https://www.pgbouncer.org/) ，Postgres 连接池事实上的标准了。

在本文中，我们将了解如何使用 PgBouncer 来扩展您的应用程序。我们将使用 Django(一个流行的 Python web 框架)作为设置的例子，使用 [Heroku](https://www.heroku.com) 作为部署平台。然而，PgBouncer 的安装步骤和概念应该适用于 Heroku 上的任何应用程序。

# 你将学到什么

虽然设置 PgBouncer 很简单，但是为您的特定用例找到正确的设置可能是一个挑战。对于 PgBouncer 配置，没有一种通用的解决方案。

本文将向您展示:

*   如何选择 PgBouncer 池模式和部署类型
*   如何通过将 [pgbouncer buildpack](https://github.com/heroku/heroku-buildpack-pgbouncer) 添加到您现有的 Django 应用程序来在 Heroku 上安装 PgBouncer
*   如何监控 PgBouncer 运行时统计数据以帮助微调您的设置
*   常见错误和注意事项

你可以在 GitHub 的 https://github.com/steuke/heroku-pgbouncer-django-demo[找到一个示例项目。](https://github.com/steuke/heroku-pgbouncer-django-demo)

注意:要使用本指南，您的应用程序必须使用 DATABASE_URL 环境变量来配置其数据库连接。这很重要。如果手动配置数据库，首先需要切换到使用 DATABASE_URL。对于 Django，看一看 [dj-database-url](https://github.com/jacobian/dj-database-url) 和示例项目，这会使这变得非常容易。您可以查看示例项目[来更好地理解这是如何实现的。](https://github.com/steuke/heroku-pgbouncer-django-demo/blob/7d20aff9b051995a8f1f8b07083512e695b59df7/pgbouncer_client_demo/settings.py#L10-L11)

# 开始之前需要考虑什么

## 选择正确的 PgBouncer 部署类型

PgBouncer 可以用不同的方式部署。Heroku 支持服务器端和客户端部署。Heroku 上的服务器端实现仍处于测试阶段，在配置选项上有些限制，并且只支持事务模式(见下一节)。因此，本指南使用客户端方法，将 PgBouncer 部署在与 Django 应用程序相同的 dyno 上，并为您提供对每个设置的更多控制。

如果您不确定使用哪种部署类型，Heroku 官方文档将帮助您决定客户端部署是否适合您。

## 选择正确的 PgBouncer 池模式和 Django 设置

PgBouncer 有[三种类型的连接池模式](http://www.pgbouncer.org/features.html)，这里从最礼貌到最积极的连接共享列出:会话、事务和语句模式。

在考虑使用 Django 应用程序的不同池模式时，您需要了解以下内容:

*   **Session-mode** (只有当用户会话关闭时连接才返回到池中) **:** 这种模式是最保守的。如果你使用这种模式，你不需要改变你的 Django 应用程序中的任何东西。如果其他模式不适用于您的应用程序，此模式可能会修复问题。该模式支持所有 Postgres 特性。
*   **事务模式**(事务一完成，连接就返回到池中) **:** 这是 Heroku PgBouncer buildpack 的默认模式。此模式要求您禁用服务器端游标的使用。在 Django 中，通过设置[DISABLE _ SERVER _ SIDE _ CURSORS = True](https://docs.djangoproject.com/en/3.0/ref/settings/#disable-server-side-cursors)来禁用它们。这种模式应该适用于大多数应用程序，但请注意，一些重要的 Postgres 功能，如预准备语句、监听/通知、[和其他、](http://www.pgbouncer.org/features.html)不受支持。如果你使用这些特性中的任何一个(或者你依赖于一个使用这些特性的库)，你将冒着破坏你的应用程序或者降低一些查询性能的风险。
*   **语句模式**(语句一执行连接就返回池) **:** 这甚至比事务模式更激进。在这种模式下，不能使用多语句事务。多状态事务将导致错误(“服务器意外关闭了连接”)。如果您在某些用例中需要此模式，请考虑设置多个具有不同设置的 PgBouncer 池。

作为最佳实践，从使用事务模式开始，并验证您的应用程序仍然工作。之后，可以随意试验其他模式及其对性能和资源使用的影响。例如，如果您的应用程序使用了不支持的功能，导致您遇到错误，请尝试切换到会话模式。如果你没有遇到错误，并且你想进一步提高应用程序的性能，你可以尝试使用语句模式。更多细节可在 Heroku 文档中找到。

# 使用构建包在 Heroku 上安装 PgBouncer

*给你一个建议:使用一个试运行环境来遵循这个指南。只有在试运行环境中测试您的应用程序后，才能将此更改推广到生产环境中。在解决问题之前，不正确的步骤可能会阻止您的应用程序运行。(已经警告过你了！)*

好了，我们开始吧。按照以下五个步骤将 PgBouncer 添加到您的应用程序中(在本文中，我们将使用 [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) 命令):

## 1.禁止使用服务器端游标

在 Django 应用程序中，只需将[这一行](https://github.com/steuke/heroku-pgbouncer-django-demo/blob/01de2fd2af0d4933cabed782066d0d2790bd3e1d/pgbouncer_client_demo/settings.py#L13)添加到您的 settings.py:

```
*DISABLE_SERVER_SIDE_CURSORS = True # required when using pgbouncer’s pool_mode=transaction*
```

## 2.添加 PgBouncer 构建包

```
*heroku buildpacks:add heroku/pgbouncer*
```

## 3.添加 python 构建包

在某些情况下，您必须手动添加 python buildpack(或您正在使用的语言的 buildpack)。既然这么做没有坏处，那就这么做吧:

```
*heroku buildpacks:add heroku/python*
```

## 4.更改您的 Procfile，以便在启动时启动 PgBouncer

如果您使用的是 python/Django，那么在此更改之前，您的 Procfile 将如下所示:

```
*web: gunicorn your_django_project.wsgi*
```

改成这样的:

```
*web: bin/start-pgbouncer gunicorn your_django_project.wsgi*
```

这一更改将把您原来的启动命令传递给 bin/start-pgbouncer 脚本。该脚本是 PgBouncer buildpack 的一部分。其主要目的是将`DATABASE_URL`环境变量改为指向本地 PgBouncer 连接池，而不是原来的 Postgres 数据库。

这就是 Django 应用程序必须使用 DATABASE_URL 环境变量来配置数据库连接的原因。如果您不使用 DATABASE_URL，您的应用程序仍将工作，但它不会通过 PgBouncer 连接到您的 Postgres。

## 5.提交更改并将其部署到 Heroku

使用您最喜欢的 git 客户端提交更改，并将它们推送到 Heroku。然后开始用`h*eroku logs -t --source app | grep pgbouncer*`查看日志，同时在你的应用上运行测试。您应该会看到一系列类似如下的日志条目:

```
*2020–04–30T18:59:48.918478+00:00 app[web.1]: 2020–04–30 18:59:48.918 53 LOG C-0x56211ba01900: pgbouncer/pgbouncer@unix(63):6000 login attempt: db=pgbouncer user=pgbouncer tls=no**2020–04–30T18:59:48.919044+00:00 app[web.1]: 2020–04–30 18:59:48.919 53 LOG C-0x56211ba01900: pgbouncer/pgbouncer@unix(63):6000 closing because: client close request (age=0)*
```

恭喜你！这表明您的应用程序已成功配置并使用 PgBouncer 连接到 Postgres。

## 你刚刚实现了什么？

您的 web dyno 现在正在运行一个 PgBouncer 实例，它为您的 Django 应用程序(运行在同一个 dyno 上)提供一个连接池。您的 Django 应用程序正在连接到这个本地 PgBouncer 连接池，而不是直接连接到 Postgres 数据库。

在下一节中，您将学习如何监视 PgBouncer。

# 监控 PgBouncer

有几种方法可以监控 PgBouncer。一种常见的方法是[使用 psql](https://devcenter.heroku.com/articles/postgres-connection-pooling#viewing-connection-pool-stats) 连接到 PgBouncer 管理控制台。本节演示了另外两种方法:使用 Heroku 日志和从 Django 应用程序中查询统计数据。

## 使用 Heroku 日志

PgBouncer buildpack 配置为以一分钟为间隔记录 PgBouncer 统计信息。运行以下命令会显示最新的统计信息:

```
*heroku logs — source app | grep “LOG Stats”*
```

输出如下所示:

```
*2020–04–30T19:11:25.720304+00:00 app[web.1]: 2020–04–30 19:11:25.720 54 LOG Stats: 5 xacts/s, 46 queries/s, in 5401 B/s, out 2756 B/s, xact 45640 us, query 992 us wait time 1396 us*
```

## 使用 SHOW STATS 从 Django 应用程序查询 PgBouncer 数据库

PgBouncer 提供了一个包含其统计数据的虚拟数据库。这也被称为[管理控制台](http://www.pgbouncer.org/usage.html#admin-console)。PgBouncer buildpack 通过名为/tmp 的 Unix 套接字提供对这个虚拟数据库的访问，使用用户名“PgBouncer ”,没有密码。

使用这些凭证，您可以通过使用低级别的 psycopg2 数据库 API 从 Django 应用程序中检索 PgBouncer 统计信息。下面是一个示例函数 pgbouncer_stats()，它使用该 API 检索统计信息:

```
*def pgbouncer_stats() -> List[Dict]:**try:**connection = psycopg2.connect(host=”/tmp/”, port=6000, dbname=”pgbouncer”, user=”pgbouncer”,**cursor_factory=extras.DictCursor)**connection.autocommit = True # must enable autocommit when querying pgbouncer stats**cursor = connection.cursor()**cursor.execute(‘SHOW STATS’)**pgbouncer_stats = [dict(row) for row in cursor]**return pgbouncer_stats**except Exception as e:**return [{‘error’: True, ‘description’: ‘Could not query pgbouncer stats.’, ‘reason’: f’{e}’}]**</code>*The return value of pgbouncer_stats() is a list of dictionaries, with one dictionary for each database that PgBouncer is pooling:*<code>**“pgbouncer_stats”:[**{**“database”:”db1",**“total_xact_count”:220,**“total_query_count”:2750,**“total_received”:330110,**“total_sent”:168517,**“total_xact_time”:15062895,**“total_query_time”:2837299,**“total_wait_time”:84837,**“avg_xact_count”:2,**“avg_query_count”:29,**“avg_recv”:3519,**“avg_sent”:1796,**“avg_xact_time”:68467,**“avg_query_time”:1031,**“avg_wait_time”:904**},**{**“database”:”pgbouncer”,**“total_xact_count”:110,**“total_query_count”:110,**“total_received”:0,**“total_sent”:0,**“total_xact_time”:0,**“total_query_time”:0,**“total_wait_time”:0,**“avg_xact_count”:1,**“avg_query_count”:1,**“avg_recv”:0,**“avg_sent”:0,**“avg_xact_time”:0,**“avg_query_time”:0,**“avg_wait_time”:0**}**]*
```

您可以看到“db1”数据库的 total_xact_count 为 220。这表明总共处理了 220 笔交易。PgBouncer 文档中有完整的统计列表及其含义。

# 常见错误和注意事项

如何修复开始使用 PgBouncer 时的一些常见问题:

*   "致命错误:数据库' pgbouncer '不存在:"您正在连接的服务器似乎不是 PgBouncer-server。有没有给 PgBouncer 加 Heroku buildpack？您是否使用 DATABASE_URL 来配置您的连接？有没有把 Procfile 改成启动 PgBouncer？
*   "错误:没有这样的用户:用户名。"如果您使用的是 Heroku buildpack，这应该不会发生。确保您是以用户“pgbouncer”的身份通过 Unix 套接字连接的，没有密码。更多详情请参见[pg bouncer-认证文档](https://www.pgbouncer.org/config.html#authentication-file-format)。
*   您需要在 Django 的数据库选项中禁用 SSL，即删除任何“SSL mode”:“require”。由于您使用的是客户端安装，流量不会离开您的机器，应该是安全的。或者，[参见 PgBouncer 文档以启用 TLS 支持](https://www.pgbouncer.org/config.html)。
*   如果连接到多个 Postgres 数据库，需要设置 PGBOUNCER_URLS 环境变量，让 PGBOUNCER 知道应该使用哪些环境变量进行连接池。例如，此命令告诉 PgBouncer 为 DATABASE_URL 和 EVENT_DATABASE_URL 创建连接池:heroku config:add PgBouncer _ URLS = " DATABASE _ URL EVENT _ DATABASE _ URL "
*   如果您的请求需要访问包含许多行的大型表，当您必须禁用服务器端游标时，您的应用程序的性能会受到影响。一种解决方案是使用多个连接池。对于这些大型表请求，可以有一个处于会话模式的池，而对于其他请求，可以使用另一个处于事务模式的池。

# 后续步骤:监控、基准测试和微调 PgBouncer

此时，PgBouncer 已经启动并运行在您的 dyno 上，为您的 Django 应用程序提供连接池。

你下一步应该做什么？

*   确保你的应用性能稳定。监控 PgBouncer 统计数据，关注面向用户的统计数据，如 avg_wait_time，它指示您的应用程序等待连接的时间。
*   执行基准测试并试验 PgBouncer 设置以及池类型。一种方法是使用负载测试工具，让你的应用程序承受不断增加的负载。负荷增加的同时，看 PgBouncer 的 maxwait-stat。如果这个数字越来越大，那么这个池处理客户端请求的速度就不够快。你可以尝试增加 [PGBOUNCER_DEFAULT_POOL_SIZE，减少 PGBOUNCER _ RESERVE _ POOL _ time out](https://github.com/heroku/heroku-buildpack-pgbouncer#tweak-settings)或者尝试使用更激进的池模式。如果这些都没有帮助，可能是服务器资源不足，这可能意味着您需要升级 Postgres 服务器。
*   熟悉 PgBouncer 设置，如 max_client_conn、default_pool_size 和 max_db_connections，以调整连接池。

# 结论

当数据库连接数成为问题时，PgBouncer 是帮助扩展 Postgres 应用程序的合适工具。请记住，虽然将 PgBouncer 添加到您的设置中会给您和您的团队带来额外的复杂性，但花时间仔细设置、监控和调整您的配置应该会提高您的应用程序的性能。
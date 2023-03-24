# FastAPI vs . express . js vs . Flask vs . nest . js 基准测试

> 原文：<https://levelup.gitconnected.com/fastapi-vs-express-js-vs-flask-vs-nest-js-benchmark-5e14d518cc00>

我想验证 FastAPI 宣称的与 Node.js 相当的性能，所以我决定进行一次基准测试。为此，我使用了 wrk，一个 HTTP 基准测试工具。

我还想通过调用一个端点来测试它，这个端点调用一个 PostgreSQL 数据库，以便模拟一个更“真实”的场景。在每种情况下，我都创建了一个从数据库返回 100 行数据的端点。

我测试了以下组合:

*   FastAPI + psycopg2
*   FastAPI + SQLModel
*   烧瓶+ psycopg2 +烧瓶运行
*   烧瓶+ psycopg2 + gunicorn
*   快递. js + pg
*   Nest.js + Prisma
*   烧瓶+ psycopg2 + gunicorn (4 名工人)
*   FastAPI + psycopg2 + gunicorn (4 名工人)
*   FastAPI + SQLModel + gunicorn (4 个工人)

这个测试的代码可以在这里找到:[https://github.com/travisluong/python-vs-nodejs-benchmark](https://github.com/travisluong/python-vs-nodejs-benchmark)。

结果如下:

# FastAPI+psycopg 2+uvicon

```
$ uvicorn fast_psycopg:app
$ wrk [http://localhost:8000](http://localhost:8000)Running 10s test @ http://localhost:8000
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    32.35ms    3.63ms  50.66ms   90.42%
    Req/Sec   154.76     13.60   202.00     78.00%
  3110 requests in 10.10s, 23.79MB read
Requests/sec:    308.01
Transfer/sec:      2.36MB
```

# FastAPI+SQL model+uvicon

```
$ uvicorn fast_sqlmodel:app
$ wrk [http://localhost:8000](http://localhost:8000)Running 10s test @ http://localhost:8000
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    54.50ms    9.41ms  99.36ms   78.56%
    Req/Sec    91.89     16.03   130.00     75.38%
  1842 requests in 10.10s, 11.28MB read
Requests/sec:    182.45
Transfer/sec:      1.12MB
```

# 烧瓶+ psycopg2 +烧瓶运行

```
$ FLASK_APP=flask_psycopg flask run
$ wrk [http://localhost:5000](http://localhost:5000)Running 10s test @ http://localhost:5000
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    14.06ms    4.48ms  42.07ms   73.73%
    Req/Sec   354.51     19.96   404.00     70.50%
  7070 requests in 10.01s, 37.77MB read
  Non-2xx or 3xx responses: 2501
Requests/sec:    705.95
Transfer/sec:      3.77MB
```

# 烧瓶+ psycopg2 + gunicorn (1 名工人)

```
$ gunicorn -w 1 --bind 0.0.0.0:5000 flask_psycopg:app
$ wrk [http://localhost:5000](http://localhost:5000)Running 10s test @ http://localhost:5000
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    18.50ms  743.07us  22.31ms   84.33%
    Req/Sec   269.51      7.33   287.00     55.00%
  5386 requests in 10.04s, 46.47MB read
Requests/sec:    536.65
Transfer/sec:      4.63MB
```

# 快递. js + pg

```
$ node express_pg.js
$ wrk [http://localhost:3000](http://localhost:3000)Running 10s test @ http://localhost:3000
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     5.19ms    1.04ms  20.60ms   89.87%
    Req/Sec     0.97k    89.72     1.08k    66.34%
  19521 requests in 10.10s, 151.39MB read
Requests/sec:   1931.99
Transfer/sec:     14.98MB
```

# Nest.js + Prisma

```
$ npm start
$ wrk [http://localhost:3000](http://localhost:3000)Running 10s test @ http://localhost:3000/feed
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     8.49ms    2.37ms  37.77ms   84.94%
    Req/Sec   594.97     91.32   720.00     65.00%
  11860 requests in 10.02s, 91.98MB read
Requests/sec:   1184.11
Transfer/sec:      9.18MB
```

# 烧瓶+ psycopg2 + gunicorn (4 名工人)

```
$ gunicorn -w 4 --bind 0.0.0.0:5000 flask_psycopg:app
$ wrk [http://localhost:5000](http://localhost:5000)Running 10s test @ http://localhost:5000
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     6.66ms    1.71ms  26.50ms   90.09%
    Req/Sec   743.20     60.59     0.89k    65.00%
  14814 requests in 10.02s, 127.81MB read
Requests/sec:   1478.02
Transfer/sec:     12.75MB
```

# FastAPI + psycopg2 + gunicorn (4 名工人)

```
$ gunicorn -w 4 -k uvicorn.workers.UvicornWorker fast_psycopg:app
$ wrk [http://localhost:8000](http://localhost:8000)Running 10s test @ http://localhost:8000
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    10.86ms    7.75ms  39.84ms   78.87%
    Req/Sec   499.02    291.50     0.91k    55.50%
  9962 requests in 10.07s, 76.19MB read
Requests/sec:    989.50
Transfer/sec:      7.57MB
```

# FastAPI + SQLModel + gunicorn (4 个工人)

```
$ gunicorn -w 4 -k uvicorn.workers.UvicornWorker fast_sqlmodel:app
$ wrk [http://localhost:8000](http://localhost:8000)Running 10s test @ http://localhost:8000
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    17.72ms   10.82ms  57.08ms   58.85%
    Req/Sec   286.39    112.97   515.00     56.00%
  5723 requests in 10.05s, 35.04MB read
Requests/sec:    569.46
Transfer/sec:      3.49MB
```

# 结论

看起来极简主义者 Express.js + pg 组合赢得了这一轮基准测试，其次是拥有 4 个 gunicorn 工人的 Flask 和 Nest.js + Prisma。

带有“Flask run”服务器的 flask 有大量非 2xx 或 3xx 响应，这是开发服务器所期望的。

另一方面，FastAPI+psyco pg 2+uvicon 似乎落后 Express.js + pg 超过 6 倍。

FastAPI + SQLModel + gunicorn 相比 Nest.js + Prisma 大约相差 2 倍。

有趣的是，Flask + psycopg2 + gunicorn 几乎以 2 倍的速度击败了 FastAPI + psycopg2 + gunicorn。

FastAPI 在性能方面与 Node.js 框架不相上下，这样说安全吗？还是我做的测试不正确？也许我没有以正确的方式利用 FastAPI 的异步功能？

最终，您选择哪个框架可能并没有太大的关系。只使用你最擅长的语言，因为开发人员的时间通常比计算能力更昂贵。

# 2012 年 1 月 3 日更新

感谢 Dmitry 指出我应该使用 asyncpg 库而不是 psycopg2。

我还包含了一个 encode/databases 库的基准测试，它在 FastAPI 网站的 Async SQL 部分中公布。

尽管添加了异步数据库驱动程序，FastAPI 在性能上似乎仍然落后于 Node.js。JSON 序列化是一个可能的瓶颈。如果有人知道如何优化这一点，请告诉我！

以下是 asyncpg 和数据库的更新结果。

# FastAPI+asyncpg+uvicon

```
$ uvicorn fast_asyncpg:app
$ wrk [http://localhost:8000](http://localhost:8000)Running 10s test @ http://localhost:8000
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    31.74ms    3.49ms  79.23ms   97.83%
    Req/Sec   158.01     13.26   181.00     85.00%
  3172 requests in 10.09s, 24.26MB read
Requests/sec:    314.52
Transfer/sec:      2.41MB
```

# FastAPI + asyncpg + gunicorn (4 名工人)

```
$ gunicorn -w 4 -k uvicorn.workers.UvicornWorker fast_asyncpg:app
$ wrk [http://localhost:8000](http://localhost:8000)Running 10s test @ http://localhost:8000
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    10.48ms    4.41ms  27.52ms   59.84%
    Req/Sec   478.83     84.78   666.00     60.50%
  9552 requests in 10.02s, 73.06MB read
Requests/sec:    952.99
Transfer/sec:      7.29MB
```

# FastAPI +数据库+uvicon

```
$ uvicorn fast_databases:app
$ wrk [http://localhost:8000](http://localhost:8000)Running 10s test @ http://localhost:8000
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    37.31ms    4.39ms  95.69ms   94.49%
    Req/Sec   134.30     12.86   151.00     77.50%
  2697 requests in 10.08s, 20.63MB read
Requests/sec:    267.69
Transfer/sec:      2.05MB
```

# 2012 年 1 月 4 日更新

我已经确认 JSON 序列化是瓶颈。默认的序列化器比 ujson 和 orjson 慢很多。

这是新的结果！

# FastAPI+psyco pg 2+uvicon+orjson

```
$ uvicorn fast_psycopg:app
$ wrk [http://localhost:8000/orjson](http://localhost:8000/orjson)Running 10s test @ http://localhost:8000/orjson
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency    12.06ms    1.77ms  30.71ms   87.39%
    Req/Sec   415.52     23.51   464.00     72.50%
  8317 requests in 10.05s, 63.61MB read
Requests/sec:    827.30
Transfer/sec:      6.33MB
```

# FastAPI+asyncpg+uvicon+orjson

```
$ uvicorn fast_asyncpg:app
$ wrk [http://localhost:8000/orjson](http://localhost:8000/orjson)Running 10s test @ http://localhost:8000/orjson
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     5.34ms    1.15ms  19.18ms   94.82%
    Req/Sec     0.95k    44.71     1.01k    78.50%
  18870 requests in 10.01s, 144.33MB read
Requests/sec:   1885.25
Transfer/sec:     14.42MB
```

# FastAPI+asyncpg+uvicon+ujson

```
$ uvicorn fast_asyncpg:app
$ wrk [http://localhost:8000/ujson](http://localhost:8000/ujson)Running 10s test @ http://localhost:8000/ujson
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     5.85ms    0.97ms  18.79ms   88.35%
    Req/Sec     0.86k    33.84     0.92k    82.00%
  17134 requests in 10.01s, 131.05MB read
Requests/sec:   1711.69
Transfer/sec:     13.09MB
```

# FastAPI + asyncpg + gunicorn (4 名工人)+ orjson

```
$ gunicorn -w 4 -k uvicorn.workers.UvicornWorker fast_asyncpg:app
$ wrk [http://localhost:8000/orjson](http://localhost:8000/orjson)Running 10s test @ http://localhost:8000/orjson
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     2.64ms    3.04ms  47.94ms   98.38%
    Req/Sec     2.11k   733.24     3.31k    54.50%
  41995 requests in 10.01s, 321.20MB read
Requests/sec:   4193.72
Transfer/sec:     32.08MB
```

# FastAPI + asyncpg + gunicorn (4 名工人)+ ujson

```
$ gunicorn -w 4 -k uvicorn.workers.UvicornWorker fast_asyncpg:app
$ wrk [http://localhost:8000/ujson](http://localhost:8000/ujson)Running 10s test @ http://localhost:8000/ujson
  2 threads and 10 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     2.27ms  794.94us  21.49ms   69.94%
    Req/Sec     2.21k   316.77     2.79k    63.00%
  44028 requests in 10.00s, 336.75MB read
Requests/sec:   4401.04
Transfer/sec:     33.66MB
```

# 定论

胜出者是 FastAPI+asyncpg+4 guni corn workers+ujson。

FastAPI 绝对很快，与 Node.js 不相上下，不负众望！根据这些基准。

只要确保你使用了正确的库就行了！

我意识到基准测试中还有另一个缺陷。Node.js 有一个集群模式，我并不知道。有关新的基准测试和完整的排名，请查看这篇基准测试文章的第 2 部分:

[https://medium . com/@ travisluong/fastapi-vs-fastify-vs-spring-boot-vs-gin-benchmark-b 672 a5 c 39d 6 c](https://medium.com/@travisluong/fastapi-vs-fastify-vs-spring-boot-vs-gin-benchmark-b672a5c39d6c)

如果您有兴趣了解 FastAPI 和其他神奇工具的更多信息，请查看我的完整堆栈教程:

[https://medium . com/@ travisluong/full-stack-next-js-fastapi-PostgreSQL-tutorial-86 f 0af 0747 b 7](https://medium.com/@travisluong/full-stack-next-js-fastapi-postgresql-tutorial-86f0af0747b7)

感谢阅读。

*原载于 2022 年 1 月 1 日*[](https://www.travisluong.com/fastapi-vs-express-js-vs-flask-vs-nest-js-benchmark/)**。**
# 用 Redis 构建无服务器直方图 API

> 原文：<https://levelup.gitconnected.com/build-a-serverless-histogram-api-with-redis-7aff7ec69114>

![](img/a25d5581d8827d1bd51199015408a232.png)

在开发[无服务器数据库(DynamoDB、FaunaDB、Upstash)](https://blog.upstash.com/latency-comparison) 的延迟基准测试时，我希望有一个 API 可以记录延迟数字并获取直方图。在本教程中，我将构建这样一个 API，您可以在其中记录来自任何应用程序的延迟值。它将是一个无服务器的 REST API，具有以下方法:

*   记录:将数值记录到直方图中。
*   get:返回直方图对象。

# 动机

我将展示使用 AWS Lambda 和无服务器 Redis 开发通用 API 有多容易。

# 创建一个 Redis 数据库

按照[入门](https://docs.upstash.com/)中的定义创建一个数据库

# `Step-2:`无服务器项目设置

如果您还没有，请通过以下方式安装无服务器框架:

`npm install -g serverless`

在任何文件夹中运行如下`serverless`:

```
*>> serverless

Serverless: No project detected. Do you want to create a new one? Yes
Serverless: What do you want to make? AWS Node.js
Serverless: What do you want to call this project? histogram-api

Project successfully created in 'histogram-api' folder.

You can monitor, troubleshoot, and test your new service with a free Serverless account.

Serverless: Would you like to enable this? No
You can run the “serverless” command again if you change your mind later.*
```

在项目文件夹中，使用以下命令创建一个节点项目:

`npm init`

然后安装 redis 客户端和直方图库，包括:

```
*npm install ioredis

npm install hdr-histogram-js*
```

如下更新`serverless.yml`。从控制台复制您的 Redis URL 并替换以下内容:

```
*service: histogram-api
frameworkVersion: '2'

provider:
  name: aws
  runtime: nodejs12.x
  lambdaHashingVersion: 20201221
  environment:
    REDIS_URL: REPLACE_YOUR_URL_HERE

functions:
  record:
    handler: handler.record
    events:
      - httpApi:
          path: /record
          method: post
          cors: true
  get:
    handler: handler.get
    events:
      - httpApi:
          path: /get
          method: get
          cors: true*
```

# 第三步:编码

如下所示编辑 handler.js:

```
*const hdr = require("hdr-histogram-js");
const Redis = require("ioredis");
if (typeof client === 'undefined') {
    var client = new Redis(fixUrl(process.env.REDIS_URL));
}
const headers = {
    'Access-Control-Allow-Origin': '*',
    'Access-Control-Allow-Credentials': true,
};
const SIZE = 10000;

module.exports.get = async (event) => {
    if (!event.queryStringParameters || !event.queryStringParameters.name) {
        return {
            statusCode: 400,
            headers: headers,
            body: JSON.stringify(
                {
                    message: 'Invalid parameters. Name is needed.',
                }
            ),
        };
    }
    const name = event.queryStringParameters.name;
    const data = await client.lrange(name, 0, SIZE);
    const histogram = hdr.build();
    data.forEach(item => {
        histogram.recordValue(item);
    })

    return {
        statusCode: 200,
        body: JSON.stringify(
            {
                histogram: histogram
            }
        ),
    };
};

module.exports.record = async (event) => {
    let body = JSON.parse(event.body)
    if (!body || !body.name || !body.values) {
        return {
            statusCode: 400,
            headers: headers,
            body: JSON.stringify(
                {
                    message: 'Invalid parameters. Name and values are needed.',
                }
            ),
        };
    }
    const name = body.name;
    const values = body.values;
    await client.lpush(name, values)
    return {
        statusCode: 200,
        body: JSON.stringify(
            {
                message: 'Success',
                name: name
            }
        ),
    };
};

function fixUrl(url) {
    if (!url) {
        return ''
    }
    if (url.startsWith('redis://') && !url.startsWith('redis://:')) {
        return url.replace('redis://', 'redis://:')
    }
    if (url.startsWith('rediss://') && !url.startsWith('rediss://:')) {
        return url.replace('rediss://', 'rediss://:')
    }
    return url
}*
```

上面我们有两个无服务器功能。`get`以`name`为参数，从 Redis 加载一个列表。然后使用列表中的值构建直方图。

`record`函数以`name`和`values`为参数。它将`values`添加到名为`name`的 Redis 列表中。

`get`函数计算最近 10000 条延迟记录的直方图。更新 SIZE 参数以更改该数字。

`fixUrl`是一个帮助器方法，用于纠正 Redis url 格式。

# 步骤 4:部署您的功能

通过以下方式部署您的功能:

```
*serverless deploy*
```

该命令将部署两个函数并输出两个端点。尝试使用如下设置参数的端点:

将延迟次数记录到`perf-test-1`:

```
 *curl --header "Content-Type: application/json" -d "{\"name\":\"perf-test-1\", \"values\": [90,80,34,97,93,45,49,57,99,12]}" https://v7xx4aa2ib.execute-api.us-east-1.amazonaws.com/record*
```

获取`perf-test-1`的直方图:

```
*curl https://v7xx4aa2ib.execute-api.us-east-1.amazonaws.com/get?name=perf-test-1*
```

# 定量

每次调用远程函数进行延迟计算的成本可能很高。在您的应用程序中，您应该保留一个数组或队列作为延迟数的缓冲区，然后在数组达到批处理大小时将它们成批提交给 API。如下图所示:

*最初发表于*[*【https://docs.upstash.com】*](https://docs.upstash.com/tutorials/histogram)*。*
# 使用 Elasticsearch、Nodejs、Reactjs 和 IMDb API 构建一个电影搜索应用程序:[第 3 部分]完成我们后端的配置

> 原文：<https://levelup.gitconnected.com/building-a-movies-search-app-using-elasticsearch-nodejs-reactjs-and-imdb-api-part-3-finishing-ce48f5a0be99>

![](img/26a1ca9296b83c659db0e6779f8114a8.png)

照片由[劳塔罗·安德烈亚尼](https://unsplash.com/@lautaroandreani?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/full-stack?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

欢迎回到使用 Elasticsearch 开发全栈电影搜索应用程序的系列文章😁。在前两篇文章中，我们讨论了 Elasticsearch 的部署部分以及规划数据存储。今天，在准备好基础之后，我们继续我们应用程序后端的实际代码，希望你们都准备好了。

![](img/5caaea71dcd74ac5d9a38669a9aba68e.png)

让我们再次回忆一下我们的应用程序的架构。

![](img/afcb9442e8f553a7e0acb36b34c102f9.png)

在后端的`src`文件夹中，我们将首先添加一个包含环境变量的文件。与第一部分类似，创建一个`.env`文件，并填写通过创建 IMDb 帐户获得的 **API 密钥**。为了将所有东西归入同一个文件，我们还添加了一个`IMDB_URL`变量。您可以自由选择您的 API，只需注意返回文档的结构以及字段的名称。让我们不要忘记提及我们的应用程序将被部署的端口以及 Elasticsearch 的登录凭证。该文件将如下所示:

```
PORT = 3001
ELASTICSEARCH_CLIENT = elastic
ELASTICSEARCH_PASSWORD = TYPE_HERE IMDb API KEYAPI_KEY = TYPE_HERE
IMDB_URL = [https://imdb-api.com/en/API/InTheaters](https://imdb-api.com/en/API/InTheaters)
```

现在，为了将我们的后端连接到 Elasticsearch，我们使用 JavaScript 客户端。在`src`文件夹中，创建一个名为`elasticsearch`的 a 文件夹，并将下面的`client.js`文件放在那里:

```
const fs = require("fs");
const { Client } = require("@elastic/elasticsearch");
const client = new Client({
  node: "https://localhost:9200",
  auth: {
    username: process.env.ELASTICSEARCH_CLIENT,
    password: process.env.ELASTICSEARCH_PASSWORD,
  },
  tls: {
    ca: fs.readFileSync("./certs/ca/ca.crt"),
    rejectUnauthorized: false,
  },
});

client
  .ping()
  .then((response) => console.log("Connected to Elasticsearch"))
  .catch((error) => console.log(error));

module.exports = client;
```

这里没有什么非常复杂的，我们只是通过指定`tls`对象来配置到我们的 Elasticsearch 安全部署的连接，这当然要感谢我们之前从 Elasticsearch 容器中复制的`ca.crt`文件。您可以通过点击[此链接](https://www.elastic.co/guide/en/elasticsearch/client/javascript-api/current/client-connecting.html#auth-tls)获得关于该主题的更多信息。

仍然在后端文件夹中，创建另一个文件夹`data`，在这里我们将配置一个`moviesRouter`，它将负责从 IMDb 检索数据，并在 Elasticsearch 上索引它，现在连接已经建立。我们的`movie_api.js`文件看起来像这样:

```
const express = require('express')
const axios = require('axios')
const client = require('../elasticsearch/client')

const moviesRouter = express.Router()

moviesRouter.get('/', async (request, response) => {
  response.json('Retrieving in theaters movies ...')
  try {
    console.log('Processing ...')

    const movies = await axios.get(
      `${process.env.IMDB_URL}/${process.env.API_KEY}`
    )

    console.log('Data retrieved successfuly')

    const results = movies.data.items

    console.log('Starting data indexation ...')

    results.map(async (result) => {
      await client.index({
        index: 'movies',
        id: result.id,
        body: result,
        pipeline: 'movies_pipeline',
      })
    })

    console.log('Data has been indexed')
  } catch (error) {
    console.log(error)
  }
})

module.exports = moviesRouter
```

一旦使用 **axios** 检索到数据，我们就应用一个循环来索引 Elasticsearch 上的每个文档，当然是通过我们在[第 2 部分](https://medium.com/@mhdabdel151/building-a-movies-search-app-using-elasticsearch-nodejs-reactjs-and-imdb-api-part-2-initiating-394796e4ecbc)中创建的管道，如果您还没有读过，我邀请您阅读。

控制台日志是查看应用程序运行情况的好方法。如果发生撞车事故💢，您将直接知道失败的步骤，并可以轻松地解除问题。

现在一切都配置好了，我们只需要修改后端文件夹根目录下的`index.js`文件，如下所示:

```
require('dotenv').config()
const express = require('express')
const client = require('./elasticsearch/client')
const moviesRouter = require('./data/movie_api')
const cors = require('cors')

const app = express()

app.use('/ingest_movies', moviesRouter)
app.use(cors())

app.get('/all', async (request, response) => {
  const body = await client.search({
    index: 'movies',
    body: {
      size: 100,
    },
  })
  response.json(body.hits.hits)
})

app.get('/api/movies', async (request, response) => {
  const body = await client.search({
    index: 'movies',
    body: {
      sort: [
        {
          imDbRating: {
            order: request.query.order,
          },
        },
      ],
      query: {
        bool: {
          must: [
            {
              terms: {
                genres: request.query.genres,
              },
            },
            {
              match: {
                title: request.query.title,
              },
            },
          ],
        },
      },
    },
  })
  response.json(body.hits.hits)
})

app.listen(process.env.PORT, () => {
  console.log(`Server running on port ${process.env.PORT}`)
})
```

在这里，我们首先提到，对于数据索引来说，到`/ingest_movies`端点就足够了。`/all` 检索所有已被索引的电影，最后，我们将基于过滤器的搜索选项与`/api/movies`端点相关联。如果你觉得合适的话，可以随意更改名字，你只需要在实现前端的时候小心一点。

因为我们已经知道了文档的结构，所以我们可以很容易地为搜索选项选择过滤器。在这里，我们可以按`imDbRating`和`genres`对结果进行排序，并在`title`字段进行搜索。至此，与 Elasticsearch 的连接已经配置完毕，数据索引也已完成，您所要做的就是使用以下命令启动后端:

```
yarn run dev
```

如果您在控制台中有“*连接到 Elasticsearch* ”，很好，否则请查找并修复错误。现在转到[http://localhost:3001/ingest _ movies](http://localhost:3001/ingest_movies)来检索电影并对其进行索引。您仍然可以在 **Discover** 部分查看 Kibana 上的数据。

这部分到此为止。您可以通过以下链接访问包含应用程序代码的 GitHub repo:

[](https://github.com/AbdoulBaguiM/movies-search) [## GitHub-AbdoulBaguiM/movies-search:用 Reactjs、Nodejs 和…

### 用 Reactjs、Nodejs 和 Elasticsearch 构建的全栈搜索应用程序-GitHub-AbdoulBaguiM/movies-search…

github.com](https://github.com/AbdoulBaguiM/movies-search) 

感谢您的阅读，如果您对本文有任何问题或评论，请在下面留下您的评论。

关于使用 React 构建应用前端的下一部分即将到来，敬请关注🚀。

阿卜杜尔-巴吉
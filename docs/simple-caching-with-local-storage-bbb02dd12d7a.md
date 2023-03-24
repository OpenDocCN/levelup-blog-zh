# 使用本地存储的简单缓存

> 原文：<https://levelup.gitconnected.com/simple-caching-with-local-storage-bbb02dd12d7a>

## 电脑很快。非常快。那么，为什么我们会在网上看到这么多的加载 spinners，并且要等这么长时间才能加载东西呢…

电脑很快。非常快。那么，为什么我们会在网上看到这么多加载微调器，并且要等这么长时间才能加载呢？因为网络很慢！在世界各地发送信息很慢，通过使用出色的第三方服务来解决我们的问题——Netlify、Stripe、AWS 等——在用户有机会与页面交互之前，我们的 webapps 可以向世界各地发出请求。这是一个要解决的复杂问题，可能需要很多聪明的 devops 人员和技术领导，或者我们可以在 JS 中解决一部分吗？剧透，我们可以！

上面的视频和这篇博客的例子一样，但是对于那些视觉学习者/在实现缓存的时候想要一些额外的陪伴的人来说可能更有趣！

比方说，我们有一个请求，点击东尼·霍克职业选手 API 并请求所有选手。

> 注意:为了简单起见，我使用了 axios 库，但这肯定可以用 fetch 来完成

```
import axios from 'axios'

const getSkaters = async () => {
  const url = 'https://thps.now.sh/api/skaters'

  // wait for a response from the API
  const { data } = await axios.get(url)

  return data
}
```

这将返回如下所示的响应。

```
[
  {
    "name": "Tony Hawk",
    "style": "Vert",
    "stance": "Goofy",
    "speed": 6,
    "air": 7,
    "hangtime": 5
  },
  {
    "name": "Bob Burnquist",
    "style": "All around",
    "stance": "Regular",
    "speed": 5,
    "air": 6,
    "hangtime": 5
  },
  ...
]
```

每次我们调用`getSkaters()`函数时，我们都在等待我们的应用程序向托尼·霍克 API 发送请求。在理想情况下，这可能只需要几毫秒的时间——低 API 流量，小数据集，我们的应用程序和 API 托管在相似的地理位置——但要解决我敢肯定你们都在想的问题，每天肯定有数百万人整天都想不停地访问 API 以获得那些甜蜜的溜冰者统计数据！

现在，我们不能对第一个请求做太多事情，因为客户端不知道球员的统计数据，直到我们的 API 告诉他们，但是，我们可以在第一次实现我们自己的缓存数据的方法——使它在没有到 API 服务器的完整往返的情况下可用。现在，我们将如何建立一个缓存？缓存只是我们记得从特定请求(或 URL)中获取的一些数据。谢天谢地，浏览器有这个奇妙而简单的方法来存储数据，叫做`localStorage`。

```
localStorage.setItem(key, data) // writes the data against that key

localStorage.getItem(key) // returns the data associated with that key
```

LocalStorage 允许我们从浏览器的存储中读取和写入键/值对。要将一些数据写入 localStorage，我们需要一个键——用来标识我们的数据的唯一的东西——以及我们想要存储在那里的数据。让我们构建一些方便的函数来与 localStorage 接口。

```
// src/utils/cache.js

const writeToCache = (url, data) =>
  localStorage.setItem(url, JSON.stringify(data))

const readFromCache = url => JSON.parse(localStorage.getItem(url)) || null

export { readFromCache, writeToCache }
```

希望您对上面使用的 ES6 语法、箭头函数和隐式返回感到满意。如果没有，这是两个从缓存中抽象读写的函数。`=>`右边的是从函数返回的内容。

我们使用`url`作为我们的密钥，因为它总是独一无二的。

这里还发生了一些奇怪的事情，尤其是`parse`和`stringify`。这些函数互为反函数。一个获取一个 json 对象并将其转换成一个大字符串(stringify)，另一个获取一个大字符串并将其转换成一个 json 对象(parse)。这是必需的，因为 localStorage 只允许我们存储字符串。

`||`语法用于提供一个回退值。因此，我们检查是否有任何存储在该 url 下的数据可以解析成 json 对象，如果没有，我们希望返回`null`。

`export`用于从文件中暴露这些函数，以便其他文件可以`import`它们。

接下来，我们希望将我们的请求从我们的`getSkaters()`函数中抽象出来，使它更通用，更可缓存。

```
// src/utils/request.js

import axios from 'axios'
import { writeToCache } from './cache'

const getFreshData = async (url, cacheResponse = false) => {
  const { data } = await axios.get(url)
  cacheResponse && writeToCache(url, data)
  return data
}
```

同样，我们正在创建一个新文件来抽象出我们的可重用逻辑。我们的`getFreshData()`函数现在接受一个`url`和一个`cacheResponse`布尔值。`url`用于从 API 请求我们的数据，而`cacheResponse`告诉我们的函数是否要将响应写入缓存。让我们扩展这个文件以包含一个`getCachedData()`函数。

```
const getCachedData = url => readFromCache(url)
```

这个文件实际上只是包装了`readFromCache`，但这意味着任何需要请求数据的东西都可以使用这个文件，而不管他们想要的是新内容还是缓存的内容。该文件的最终版本应该如下所示。

```
import axios from 'axios'
import { readFromCache, writeToCache } from './cache'

const getFreshData = async (url, cacheResponse = false) => {
  const { data } = await axios.get(url)
  cacheResponse && writeToCache(url, data)
  return data
}

const getCachedData = url => readFromCache(url)

export { getCachedData, getFreshData }
```

现在让我们创建我们的`Skaters`组件。

```
import React, { useState } from 'react'

const renderSkater = ({ name, stance }) => (
  <div key={name}>
    <p>
      {name} - {stance}
    </p>
  </div>
)

const Skaters = () => {
  const [skaters, setSkaters] = useState([])

  return (
    <>
      <div>{skaters.map(renderSkater)}</div>
      <button>Load</button>
    </>
  )
}

export default Skaters
```

这只是简单地交互一组溜冰者并显示每个人的名字和姿势。

让我们扩展它以获取一个`useCache`道具，并请求一个新的或缓存版本的溜冰者。

```
import React, { useState } from 'react'
import { getCachedData, getFreshData } from './utils/request'

const url = 'https://thps.now.sh/api/skaters'

const renderSkater = ({ name, stance }) => (
  <div key={name}>
    <p>
      {name} - {stance}
    </p>
  </div>
)

const Skaters = ({ useCache }) => {
  const [skaters, setSkaters] = useState([])

  const getSkaters = async () => {
    setSkaters([])

    if (useCache) {
      const cachedSkaters = getCachedData(url)
      if (cachedSkaters) {
        setSkaters(cachedSkaters)
      }
    } else {
      const freshSkaters = await getFreshData(url, useCache)
      setSkaters(freshSkaters)
    }
  }

  return (
    <div>
      <div>{skaters.map(renderSkater)}</div>
      <button onClick={getSkaters}>Load</button>
    </div>
  )
}

export default Skaters
```

很好，现在我们可以呈现我们的溜冰者组件了，要么显示缓存中的溜冰者列表，要么从 API 请求一个新的副本。不过这里有几个 bug。

1.  如果我们还没有请求新的选手并将他们写入缓存，我们实际上不能显示缓存中的选手。
2.  一旦我们将溜冰者写入缓存，他们就永远在那里了。如果 API 更新，我们永远也不能覆盖它们或者得到新的溜冰者。

解决这两个问题并显著改善用户体验的方法是，首先请求缓存版本，然后请求新的副本，而不管它是否在缓存中。这意味着如果我们有一个缓存版本，它会立即显示，当新的内容可用时，它会自动更新用户界面。这听起来很复杂，但我们真正要做的是移除包装新请求逻辑的`else`。

```
import React, { useState } from 'react'
import { getCachedData, getFreshData } from './utils/request'

const url = 'https://thps.now.sh/api/skaters'

const renderSkater = ({ name, stance }) => (
  <div key={name}>
    <p>
      {name} - {stance}
    </p>
  </div>
)

const Skaters = ({ useCache }) => {
  const [skaters, setSkaters] = useState([])

  const getSkaters = async () => {
    setSkaters([])

    if (useCache) {
      const cachedSkaters = getCachedData(url)
      if (cachedSkaters) {
        setSkaters(cachedSkaters)
      }
    }

    const freshSkaters = await getFreshData(url, useCache)
    setSkaters(freshSkaters)
  }

  return (
    <div>
      <div>{skaters.map(renderSkater)}</div>
      <button onClick={getSkaters}>Load</button>
    </div>
  )
}

export default Skaters
```

厉害！现在，我们的用户将立即看到缓存的版本，并在 API 响应后收到任何更新。这里有一个 gif 演示了这是什么样子。

![](img/510ff989c941c69dbfc4c923874d2c07.png)

正如你所看到的，我们不能在用户第一次访问页面时提供缓存版本，但是每次他们回来看到那些甜蜜的溜冰者统计数据时，我们可以让体验感觉更加灵敏！

看看这个例子的[现场版本](https://thps.now.sh/)或者 [GitHub repo](https://github.com/dijonmusters/thps) 看看它们是如何一起点击的！

> 注意:您需要清除 dev 工具中的本地存储，以便在缓存版本中再次看到第一个请求(右图)。

在下一篇博客中，我们将着眼于把`getCachedData()`和`getFreshData()`抽象成我们可以在任何应用程序中使用的钩子！

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)
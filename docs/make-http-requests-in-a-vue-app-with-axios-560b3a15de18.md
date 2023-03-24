# 使用 Axios 在 Vue 应用程序中发出 HTTP 请求

> 原文：<https://levelup.gitconnected.com/make-http-requests-in-a-vue-app-with-axios-560b3a15de18>

![](img/f88d6bac8bae03cbe7f1c9155c08a904.png)

照片由[丹·爱德华兹](https://unsplash.com/@de?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

Axios 是一个流行的 HTTP 客户端，用于 Vue 应用程序。

# 装置

我们可以通过运行以下命令来安装该软件包:

```
npm i axios
```

此外，我们可以添加带有脚本标签的库。我们可以写:

```
<script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
```

或者:

```
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```

# 提出请求

要使用 Axios 发出请求，我们只需调用库附带的方法。

例如，要发出 GET 请求，我们可以写:

```
<template>
  <div id="app"></div>
</template>
<script>
import axios from "axios";export default {
  name: "App",
  async beforeMount() {
    const { data } = await axios.get(
      "https://jsonplaceholder.typicode.com/posts"
    );
    console.log(data);
  }
};
</script>
```

我们将 URL 传递给`get`方法来发出 GET 请求。

要发出 POST 请求，我们可以使用 POST 方法:

```
<template>
  <div id="app"></div>
</template>
<script>
import axios from "axios";export default {
  name: "App",
  async beforeMount() {
    const { data } = await axios.post(
      "https://jsonplaceholder.typicode.com/posts",
      {
        title: "foo",
        body: "bar",
        userId: 1
      }
    );
    console.log(data);
  }
};
</script>
```

第二个参数是请求体。

也可以直接调用`axios`对象来发出请求。

要发出 GET 请求，我们可以写:

```
<template>
  <div id="app"></div>
</template>
<script>
import axios from "axios";export default {
  name: "App",
  async beforeMount() {
    const { data } = await axios({
      method: "post",
      url: "https://jsonplaceholder.typicode.com/posts",
      data: {
        title: "foo",
        body: "bar",
        userId: 1
      }
    });
    console.log(data);
  }
};
</script>
```

我们有设置请求方法的`method`属性。

`url`有请求 URL。

`data`有请求体。

要发出 GET 请求，我们可以写:

```
<template>
  <div id="app"></div>
</template>
<script>
import axios from "axios";export default {
  name: "App",
  async beforeMount() {
    const { data } = await axios({
      method: "get",
      url: "https://jsonplaceholder.typicode.com/posts/1"
    });
    console.log(data);
  }
};
</script>
```

# Axios 实例

我们可以用`axios.create`方法创建自己的 Axios 对象:

```
<template>
  <div id="app"></div>
</template>
<script>
import axios from "axios";
const instance = axios.create({
  baseURL: "https://jsonplaceholder.typicode.com/posts",
  timeout: 1000,
  headers: { "X-Custom-Header": "foobar" }
});export default {
  name: "App",
  async beforeMount() {
    const { data } = await instance.get("/1");
    console.log(data);
  }
};
</script>
```

我们用`baseURL`属性设置请求的基本 URL。

此外，我们可以设置头、超时和其他请求参数。

然后我们用相对于`baseURL`的 URL 调用`get`，得到`data`。

# 截击机

我们可以为请求和响应添加拦截器。例如，我们可以写:

```
<template>
  <div id="app"></div>
</template>
<script>
import axios from "axios";
axios.interceptors.request.use(
  config => {
    console.log(config);
    return config;
  },
  error => {
    return Promise.reject(error);
  }
);export default {
  name: "App",
  async beforeMount() {
    const { data } = await axios({
      method: "get",
      url: "https://jsonplaceholder.typicode.com/posts/1"
    });
    console.log(data);
  }
};
</script>
```

我们调用`axios.interceptors.request.use`来调用拦截请求。

为了截取响应，我们可以写:

```
<template>
  <div id="app"></div>
</template>
<script>
import axios from "axios";
axios.interceptors.response.use(
  config => {
    console.log(config);
    return config;
  },
  error => {
    return Promise.reject(error);
  }
);export default {
  name: "App",
  async beforeMount() {
    const { data } = await axios({
      method: "get",
      url: "https://jsonplaceholder.typicode.com/posts/1"
    });
    console.log(data);
  }
};
</script>
```

# 结论

我们可以在我们的 Vue 应用程序中使用 Axios 发出 HTTP 请求。
# 如何在 Vue.js 中安全实现 OAuth

> 原文：<https://levelup.gitconnected.com/how-to-securely-implement-oauth-in-vue-js-57d08724efe1>

在本文中，我们将讨论在 Vue.js 应用程序中实现 OAuth 授权代码授权的逐步过程。我们将使用 FusionAuth 作为 IdP，并向您展示如何配置 FusionAuth。

最后，您的应用将能够:

*   让用户登录
*   注销用户
*   从 FusionAuth 读取用户数据
*   将用户数据写入 FusionAuth

我们将使用 Express 作为我们的后端服务器，它将充当我们的 Vue 客户端和 FusionAuth 之间的中间件。它将安全地存储访问令牌、客户机 id、客户机机密和其他信息。

**先决条件**

*   Vue 知识和表达概念。
*   Docker(可选，但安装 FusionAuth 时首选)。
*   本地计算机上的节点(12.x)/NPM。
*   您选择的任何代码编辑器。

您还需要确保您的系统满足 FusionAuth 的[内存、存储和 CPU 要求](https://fusionauth.io/docs/v1/tech/installation-guide/system-requirements)。

如果你在任何时候卡住了，可以随时参考完成的 app 的 [GitHub 库](https://github.com/fusionauth/fusionauth-example-vue)。

# 使用 Docker Compose 设置 FusionAuth

如果您还没有安装 FusionAuth，我们建议您使用 Docker Compose 选项来实现最快的设置:

```
curl -o docker-compose.yml https://raw.githubusercontent.com/FusionAuth/fusionauth-containers/master/docker/fusionauth/docker-compose.yml
curl -o .env https://raw.githubusercontent.com/FusionAuth/fusionauth-containers/master/docker/fusionauth/.env
docker-compose up
```

如果您没有安装 Docker，请查看 [FusionAuth 下载页面](https://fusionauth.io/download)了解其他安装选项(rpm、deb 等)。您也可以遵循 [FusionAuth 安装指南](https://fusionauth.io/docs/v1/tech/installation-guide/fusionauth-app)了解更多详细步骤。

FusionAuth 运行后(默认地址是[http://localhost:9011/),](http://localhost:9011/),)创建一个新的应用程序。本教程使用一个名为`fusionauth-vue-example`的应用程序。

然后，配置您的应用程序。对于本教程，您只需要更改两个配置设置。在应用程序的 **OAuth** 选项卡中:

*   将`Authorized redirect URLs`设置为`http://localhost:9000/oauth-callback`。这是 Express 服务器 URL，它将在用户登录后处理 FusionAuth 回调。
*   将`Logout URL`设置为`http://localhost:8081`。这是 FusionAuth 服务器在注销后将我们重定向到的 URL。这也是 Vue 应用程序存在的地方。注销后，用户会到达应用程序的主登录页面。

点击**保存**。

![](img/cbcbd524c528f50c5ea9e728c7198700.png)

接下来，将我们的当前用户添加到新应用程序中。在仪表盘上选择**用户**，选择**管理**，进入**注册**选项卡。然后点击`Add Registration`，将自己添加到刚刚创建的应用程序中。

![](img/1b86c9136d2bc9e54a6eb6ab6c75eca5.png)

最后，导航至**设置**，然后导航至 **API 键**。您应该有一个 API 密匙，但是可以随意创建一个。对于本教程，我们不会限制权限，但是您应该为生产部署进行限制。记录 API 键值以备后用。

我们今天不会讨论这个，但是您可以在 FusionAuth 中创建多个应用程序并配置多租户。如果您有多个应用程序，并且希望它们的所有用户数据都存储在 FusionAuth 中，这将非常有用。

现在您已经完成了 FusionAuth 的配置。我们可以开始开发我们最初的 Vue 应用程序。

# 项目结构

下面是这个项目目录的样子:

```
fusionauth-example-vue
├─client
└─server
```

所有的 Express 或服务器端代码都将在`server`文件夹中，我们的 Vue 应用程序将驻留在`client`文件夹中。您不需要立即创建文件夹；我们将在接下来的步骤中这样做。

# 创建 Vue 应用程序

我们将使用官方的 Vue CLI 来初始化我们的项目。这是搭建单页面应用程序(spa)的最佳方式。它为现代前端工作流程提供包含电池的构建设置。只需几分钟就可以启动并运行热重装、lint-on-save 和生产就绪版本。你可以在这里阅读更多关于 Vue CLI 的信息。

在我们创建我们的 Vue 应用程序之前，我建议安装官方的 Vue.js 浏览器扩展，以便于调试和检查。你可以在这里下载。

使用以下命令全局安装 Vue CLI:

```
$ npm install -g @vue/cli
# OR
$ yarn global add @vue/cli
```

现在，通过在项目目录中运行以下命令来创建项目:

```
$ vue create client
```

系统会提示您选择一个预设。通过选择**手动选择功能**，您可以选择带有基本 **Babel + ESLint** 设置或手动选项的**默认预设**。后者将允许您根据自己的需要定制功能。此项目将使用默认预设。你可以在这里了解更多。

项目初始化后，通过运行以下命令启动开发服务器:

```
$ cd client
$ npm run serve -- --port 8081
```

打开浏览器，查看 [http://localhost:8081/。这是你的应用程序的外观:](http://localhost:8081/.)

![](img/738a8ef980d4b4394bfd102e14e87007.png)

# 删除示例代码

现在，您需要清理并删除 CLI 生成的一些示例代码。

> 根据您选择的配置，您可能会看到不同的项目结构。如果你不确定如何清理，就坚持这个例子。

删除`src`中的`components`、`views`、`router`和`assets`文件夹，然后修改你的`main.js`文件，如下所示:

```
import Vue from 'vue';
import App from './App.vue';Vue.config.productionTip = false;new Vue({
  render: (h) => h(App),
}).$mount('#app');
```

接下来，修改您的`App.vue`文件，如下所示:

```
<template>
  <div id='app'>
  </div>
</template><script>
export default {
  name: 'app',
  components: {
  },
};
</script><style>
</style>
```

访问 [http://localhost:8081/](http://localhost:8081/) 现在会给你一个黑屏。

> 有趣的事实:您可以在大多数 SPA 模板中加载环境变量，如 Vue 或 React，而无需安装任何额外的依赖项。Vue 的一个小区别是您必须在每个环境变量前添加`VUE_APP_`。您可以在[模式和环境变量](https://cli.vuejs.org/guide/mode-and-env.html#environment-variables) Vue 文档中了解更多信息。

让我们暂时把客户机放在一边，把注意力放在 Express 服务器上

# 使用 Express 作为我们的后端服务器

我们将使用 [Express.js](https://expressjs.com/) 作为我们的后端服务器。这是一个被开发人员广泛使用的流行库。

> 有趣的事实:字母 **E** 在 **MERN** 、**梅文**或**中的意思是**代表快递。

在根目录中，我们将创建另一个名为`server`的文件夹，并在其中初始化一个 NodeJS 应用程序。在您的根应用程序目录中运行以下命令:

```
$ mkdir server
$ cd server
$ npm init -y
$ npm install express cors morgan nodemon dotenv axios express-session query-string
```

我们安装了很多包，所以让我们来看看它们:

*   这是一个帮助我们进行跨来源请求的中间件。
*   这是一个用于 node.js 的 HTTP 请求日志中间件，你可以用它来生产。
*   每次我们做出改变时重启服务器是一件麻烦的事情。当检测到文件更改时，Nodemon 会自动重启节点应用程序。
*   [dotenv](https://www.npmjs.com/package/dotenv) —从`.env`文件加载环境变量。我们将使用它来保护我们的 API 密钥和客户端配置。
*   [axios](https://www.npmjs.com/package/axios) —这允许我们发出 HTTP 请求。
*   [快速会话](https://www.npmjs.com/package/express-session) —它安全地存储我们的访问令牌。
*   [query-string](https://www.npmjs.com/package/query-string) —这用于将我们使用`axios`发送的数据字符串化。

既然我们已经安装了`nodemon`，要在`package.json`中使用它，只需添加以下脚本:

```
//...
"scripts": {
  "start": "node index.js",
  "dev": "nodemon index.js"
},
//...
```

接下来，设置您的环境变量。在`server`文件夹中创建一个`.env`文件，并在其中存储您的配置，比如客户端信息、端口或 API 凭证:

```
SERVER_PORT = 9000
FUSIONAUTH_PORT = 9011
CLIENT_ID = 'c8642b18-5d1d-42b4-89fb-a37a5b750186'
CLIENT_SECRET = 'oo06PflPxQrpfxqP8gY9ioOmfzQxARIW5R3BjJrlbS4'
REDIRECT_URI = 'http://localhost:9000/oauth-callback'
APPLICATION_ID = 'c8642b18-5d1d-42b4-89fb-a37a5b750186'
API_KEY = 'Dy9bphElA3L3_ayW86T5KvrZkyK1Gj5EDV_2m9i39ow'
```

您可能会注意到，每个环境变量都是大写字母。这不是一条规则，只是将环境变量与代码中的变量分开的惯例。

`REDIRECT_URI`与您在 FusionAuth `Authorized redirect URLs`字段中配置的 URL 相同。`APPLICATION_ID`与`CLIENT_ID`相同。你可以把`SERVER_PORT`换成任何你想要的端口；本教程将为 Express 服务器使用端口 **9000** 。使用上面创建的 API 密钥。

现在，您可能想知道从哪里为您的`.env`文件获取所有这些信息。转到您之前在 FusionAuth 仪表板中创建的应用程序，并单击`View`按钮。这是绿色放大镜。您可以从那里复制/粘贴`CLIENT_ID`和`CLIENT_SECRET`:

![](img/7f054cecb44574d9ae231b5c9910deab.png)

下面是基本 Express 服务器的代码。注意，我们通过在`index.js`文件中添加以下代码来使用`dotenv`包:

```
//...
require("dotenv").config();
//...
```

然后，只要我们在代码中需要，我们就可以通过在环境变量的名字前写`process.env.`来读取环境变量。

由于`.env`文件由于`.gitignore`文件而被 git 忽略，您会注意到源代码中有一个`.env.example`文件。要在本地机器上运行，将文件重命名为`.env`，并添加您的客户端 Id、客户端密码等。

下面是一个 Express 服务器的示例代码，它使用了我们所有已安装的软件包:

```
const express = require("express");
const cors = require("cors");
const morgan = require("morgan");// dotenv
require("dotenv").config();const app = express();// Use our middlewares
app.use(cors({ origin: true, credentials: true }));
app.use(morgan("common"));
app.use(express.json());// Provide a default port 
const port =  process.env.SERVER_PORT || 3000;// Listen to server  
app.listen(port, () => {
  console.log(`Listening on port ${port}`);
});
```

为了从浏览器访问我们的服务器，我们需要`cors`中间件。记得使用选项`{ origin: true, credentials: true }`和`app.use(cors())`通话。你可以在这里阅读更多关于这个[的内容](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)。

在新的终端窗口中运行以下命令，启动开发服务器:

```
$ npm run dev
```

这可能是我们唯一一次启动服务器；由于我们已经安装了`nodemon`，服务器将在每次检测到文件更改时重启。

前往[http://localhost:9000/；](http://localhost:9000/;)您将看到一个错误！

> 有趣的事实:这实际上是意料之中的，因为我们还没有创建任何路线。

![](img/3d935b6e70b662efb6a8ed5c6a0fa57a.png)

在你的终端，你可以看到`morgan`在行动。每当向我们的服务器发出请求时，它都会像这样在终端中记录它:

```
::ffff:127.0.0.1 - - [10/Jul/2020:08:48:21 +0000] "GET / HTTP/1.1" 404 139
```

这对于在开发和生产中调试应用程序都很有用。

通过将它添加到`index.js`文件中，为我们的主页创建一个简单的路径:

```
//...
// Main Page
app.get("/", (req, res) => {
  res.send({
   message:"FusionAuth Example With Vue"
  });
});
//...
```

现在，如果您访问[http://localhost:9000/:](http://localhost:9000/:)，您将看到一个响应

```
{
  "message": "FusionAuth Example With Vue"
}
```

# 为我们的 Vue 应用程序创建登录

我们将开始为我们的应用程序创建登录功能。我们的 Vue 应用程序是空的，主要是因为我们删除了样板文件。让我们添加一个标题和一个容器，我们将在其中呈现不同的组件。

在`client/src/App.vue`内添加以下内容:

```
<template>
  <div id='app'>
    <header>
      <h1>FusionAuth Example Vue</h1>
    </header>
    <div id = 'container'></div>
  </div>
</template>
<script>
export default {
  name: 'app',
  components: {
  },
};
</script>
<style>
h1 {
  text-align: center;
  font-size: 40px;
  font-family: Arial, Helvetica, sans-serif;
}
#container{
  box-sizing: border-box;
  border: 5px solid gray;
  border-radius: 15%;
  width: 400px;
  height: 400px;
  margin: auto;
}
</style>
```

CSS 将不在本教程中涉及；用自定义 CSS 或 UI 库美化这个应用程序是你自己的事。

以下是您的应用程序的外观:

![](img/f90cbdf7c3ac10ae5955d976034cd390.png)

根据用户是否登录，我们应该显示不同的消息。例如，只有当用户`dinesh@fusionauth.io`登录时，才会显示“欢迎光临，dinesh@fusionauth.io”的消息。

我们将首先对这个响应进行硬编码，然后修改代码以显示我们从 FusionAuth 获得的响应。

在`src`文件夹中创建一个名为`Greeting.vue`的新文件。我们将添加逻辑来检查用户是否登录；我们将使用条件渲染。如果`email`存在，则用户登录，否则不登录。你可以[在这里](https://vuejs.org/v2/guide/conditional.html)阅读更多相关内容。

```
<template>
  <div class="greet">
    <h3 v-if="email">Welcome  </h3>
    <h3 v-else>You are not logged in</h3>
  </div>
</template>
<script>
export default {
  name: 'Greet',
  props: ["email"],
};
</script>
<style > 
*{
  margin-top:30px;
  text-align: center;
  font-size: 20px;
  font-family: 'Courier New', Courier, monospace;
}
</style>
```

您会注意到上面的代码有些奇怪，我们使用`email`来检查用户是否登录。但是`email`值是从哪里来的呢？

我们正在传递`App.vue`的道具`email`。因此，为什么在`<script>`部分有一个`prop`字段。至于我们为什么现在这样做，这可能没有意义，但请记住，我们的应用程序中还会有其他组件需要我们从服务器获得的响应数据。与其在每个单独的组件中调用相同的数据，不如在我们的中央`App.vue`文件中请求它，然后将所需的数据作为道具传递给其他组件。

接下来，我们需要在`App.vue`中导入这个文件，并将数据发送到`<Greet />`组件。这是通过`v-bind`完成的:

```
<template>
  <div id='app'>
    <header>
      <h1>FusionAuth Example Vue</h1>
    </header>
    <div id = 'container'>
      <Greet v-bind:email="email" />
    </div>
  </div>
</template>
<script>
import Greet from './Greeting';
export default {
  name: 'app',
  components: {
      Greet,
  },
  data(){
    return {
      email : 'dinesh@fusionauth.io'
    }
  }
};
</script>
<style>
h1 {
  text-align: center;
  font-size: 40px;
  font-family: Arial, Helvetica, sans-serif;
}
#container{
  box-sizing: border-box;
  border: 5px solid gray ;
  border-radius: 15%;
  width: 400px;
  height: 400px;
  margin: auto;
}
</style>
```

在你的浏览器中，进入[http://localhost:8081/；](http://localhost:8081/;)你会看到`Welcome dinesh@fusionauth.io`:

![](img/737bd8373986bdfce9a2b8a8a809c28b.png)

现在注释掉`App.vue`调用中的`email`。

```
//...
data(){
  return {
    //email : "dinesh@fusionauth.io"
    }
}
//...
```

再次前往 [http://localhost:8081/。](http://localhost:8081/.)如您所见，由于我们移除了`email`，我们现在看到了“您没有登录”的消息。

![](img/07e4c2a347b790d26e4111b032327e64.png)

太好了，客户成功了！我们现在将基于来自服务器的数据实现相同的逻辑。

# 从 Express 服务器获取用户信息

我们将在 Express 服务器中创建一个`user`路由，向我们的应用程序发送虚假的用户数据。然后，我们将基于对 FusionAuth 的请求用真实数据替换它。

在您的`server`文件夹中，创建一个新文件夹`routes`，并在该文件夹中创建一个名为`user.js`的新文件。

```
server
├──node_modules
├──routes
│  └─user.js
├──index.js
├──package.json
└─package-lock.json
```

用代码在`user.js`中创建新的`get`路线:

```
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
  res.send({
    user: {
      email: 'dinesh@fusionauth.io'
    }
  });
});
module.exports = router;
```

要让 Express 公开这条路线，请将以下内容添加到我们的`index.js`文件中:

```
app.use('/user', require('./routes/user'))
```

转到 [http://localhost:9000/user，](http://localhost:9000/user,)会看到以下响应:

```
{
  "user": {
    "email": "dinesh@fusionauth.io"
  }
}
```

记住，从 FusionAuth 返回的一个真正的用户对象不仅仅是一个电子邮件地址，还会有更多的属性。它看起来会像这样:

```
{
  active: true,
  applicationId: '1ac76336-9dd9-4048-99cb-f998af681d3e',
  aud: '1ac76336-9dd9-4048-99cb-f998af681d3e',
  authenticationType: 'PASSWORD',
  email: 'dinesh@fusionauth.io',
  email_verified: true,
  exp: 1594893748,
  iat: 1594890148,
  iss: 'acme.com',
  roles: [],
  sub: 'abdee025-fa3c-4ce2-b6af-d0931cfb4cea'
}
```

在我们的`App.vue`文件中，我们将使用`mounted()`生命周期钩子调用服务器获取我们需要的数据:

```
//...
mounted() {
  fetch(`http://localhost:9000/user`, {
    credentials: "include" // fetch won't send cookies unless you set credentials
  })
  .then(response => response.json())
  .then(data=> console.log(data));
}
//...
```

下面是控制台中上述代码的输出:

```
{
  "user": {
    "email": "dinesh@fusionauth.io"
  }
}
```

我们现在可以使用这个对象来检查用户是否登录。我们需要首先在`data()`函数中将`email`定义为`null`。如果从服务器收到响应，我们将用收到的值更新`email`属性。在本例中，这是一个属性为`email`的对象，因此我们将确保取消对它的引用，以便将`email`属性设置为一个电子邮件地址，而不是一个 JavaScript 对象。

```
<template>
  <div id="app">
    <header>
      <h1>FusionAuth Example Vue</h1>
    </header>
    <div id="container">
      <Greet v-bind:email="email" />
    </div>
  </div>
</template>
<script>
import Greet from "./Greeting";
export default {
  name: "app",
  components: {
    Greet
  },
  data() {
    return {
      email: null
    };
  },
  mounted() {
    fetch(`http://localhost:9000/user`, {
      credentials: "include" // fetch won't send cookies unless you set credentials
    })
      .then(response => response.json())
      .then(data => (this.email = data.user.email));
  }
};
</script>
<style>
h1 {
  text-align: center;
  font-size: 40px;
  font-family: Arial, Helvetica, sans-serif;
}
#container {
  box-sizing: border-box;
  border: 5px solid gray;
  border-radius: 15%;
  width: 400px;
  height: 400px;
  margin: auto;
}
</style>
```

上面的输出与我们在`data()`中硬编码`email`值时的输出相同:

![](img/ebd7bef43f4953c8b773f154e9a5200d.png)

如果我们在`server/routes/user.js`中注释掉`email`，我们将在应用程序中看到“您没有登录”的消息。我们可以在`server/routes/user.js`中更改电子邮件，并查看相应的 DOM 更改:

```
user: {
  email: 'richard@fusionauth.io'
}
```

![](img/b664708cc620d9291a854e973d3abd1d.png)

# 从 FusionAuth 发送数据

最后，我们将从 FusionAuth 中提取数据，而不是使用硬编码的值。为此，我们首先需要创建一条`login`路线；如果没有用户登录，我们如何发送用户数据？

创建一个新文件`server/routes/login.js`并将此`route`添加到`index.js`中。

```
server
├──node_modules
├──routes
│  ├─login.js
│  └─user.js
├──index.js
├──package.json
└─package-lock.json
```

在`index.js`中，添加登录路径:

```
//...
// Routes
app.use('/user', require('./routes/user'))
app.use('/login', require('./routes/login'))
//...
```

以下是`login.js`的代码:

```
const express = require('express');
const router = express.Router();router.get('/', (req, res) => { const stateValue = Math.random().toString(36).substring(2,15) + Math.random().toString(36).substring(2,15) + Math.random().toString(36).substring(2,15) + Math.random().toString(36).substring(2,15) + Math.random().toString(36).substring(2,15) + Math.random().toString(36).substring(2,15); req.session.stateValue = stateValue res.redirect(`http://localhost:${process.env.FUSIONAUTH_PORT}/oauth2/authorize?client_id=${process.env.CLIENT_ID}&redirect_uri=${process.env.REDIRECT_URI}&response_type=code&state=${stateValue}`);
});
module.exports = router;
```

需要注意的一件重要事情是我们发出请求的端点:`/oauth2/authorize`。这个端点将为我们提供一个授权代码，我们稍后将讨论这个代码。你可以在这里了解更多。

另一件事是`stateValue`或状态参数，它通常被用作跨站点请求伪造(CSRF)保护令牌。该字段中提供的任何值都必须在成功重定向时返回，如果不存在，则通信可能已经受损。我们稍后将在`oauth-callback`路线中使用这个值。你可以在这里阅读更多相关内容。

让我们讨论一下上面使用的其他参数。`redirect_uri`通知 FusionAuth 登录后将用户重定向到哪里。`response_type`告诉 FusionAuth 我们正在使用哪个 OAuth 授权(本例中是授权代码)。

尝试导航到[http://localhost:9000/log in。如果一切都是正确的，你会看到一个`invalid_client`错误。是的，你的代码工作正常，不需要重新检查。](http://localhost:9000/login.)

如果你再看一下`login.js`，你会发现`REDIRECT_URI`被设置为我们`.env`文件中`http://localhost:9000/oauth-callback`的值。但是我们实际上还没有创建那条路线。所以这个错误是有道理的。我们实际上已经登录，因为我们在设置 FusionAuth 的过程中登录了 FusionAuth 仪表板。

如果您使用新的浏览器或匿名窗口，您可能会看到登录屏幕:

![](img/9a13e051cd086a62e45c73f17e2eadce.png)

# 为授权代码授权创建 OAuth 回调

现在，让我们通过创建一条`oauth-callback`路线来消除这个错误。在`routes`中创建一个新文件`oauth-callback.js`。

将此路线添加到`index.js`:

```
//...
// Routes
app.use('/user', require('./routes/user'))
app.use('/login', require('./routes/login'))
app.use('/oauth-callback', require('./routes/oauth-callback'))
//...
```

在重定向期间，`/oauth-callback`路由将从 FusionAuth 接收一个授权码。大概是这样的(注意`code=`后面的字符串):

```
[http://localhost:9000/oauth-callback?code=SSXVv3xkNTKEhnY4XzjUVvRZp7eyhgCuuREAgSeByrw&locale=en&userState=Authenticated](http://localhost:9000/oauth-callback?code=SSXVv3xkNTKEhnY4XzjUVvRZp7eyhgCuuREAgSeByrw&locale=en&userState=Authenticated)
```

此授权码不足以访问用户信息。为此，我们需要一个`access_token`。为了获得一个`access_token`,我们将用这个授权码向`/oauth2/token`端点发出 post 请求。

在我们发出请求后，我们需要存储`access_token`。我们不能将它存储在内存变量中，因为我们需要它用于将来的请求。我们需要一个安全的存储机制，不会暴露给我们的 Vue 客户端，因为它运行的浏览器容易受到 XSS 攻击。我们将使用`express-session`中间件存储这个`access_token`；我们需要导入`express-session`。

```
//...
const session = require("express-session")
//...
```

然后，我们需要在`index.js`中添加以下内容。查看[快速会议文档](https://github.com/expressjs/session#readme)以获取更多信息可能是值得的。

```
//...
// configure sessions
app.use(session(
  {
    secret: '1234567890', // don't use this secret in prod :)
    resave: false,
    saveUninitialized: false,
    cookie: {
      secure: 'auto',
      httpOnly: true,
      maxAge: 3600000
    }
  })
);
//...
```

现在，我们可以继续编写`oauth-callback.js`文件了。我们将发出 post 请求来接收`access_token`。不要让下面的代码迷惑了你，我们会一条一条的讨论。

```
const express = require("express");
const router = express.Router();
const axios = require("axios").default;
const qs = require("query-string");const config = {
  headers: {
    "Content-Type": "application/x-www-form-urlencoded",
  },
};
const url = `http://localhost:${process.env.FUSIONAUTH_PORT}/oauth2/token`;router.get("/", (req, res) => {
// State from Server
const stateFromServer = req.query.state;
if (stateFromServer !== req.session.stateValue) {
  console.log("State doesn't match. uh-oh.");
  console.log(`Saw: ${stateFromServer}, but expected: &{req.session.stateValue}`);
  res.redirect(302, '/');
  return;
}
  //post request to /token endpoint
  axios
    .post(
      url,
      qs.stringify({
        client_id: process.env.CLIENT_ID,
        client_secret: process.env.CLIENT_SECRET,
        code: req.query.code,
        grant_type: "authorization_code",
        redirect_uri: process.env.REDIRECT_URI,
      }),
      config
    )
    .then((result) => { // save token to session
      req.session.token = result.data.access_token;
      console.log(result)
      //redirect to Vue app
     res.redirect(`http://localhost:8081`);
    })
    .catch((err) => {
      console.error(err);
    });
});
module.exports = router;
```

我们从路线的标准代码开始，就像`login.js`。然后我们进口`axios`和`querystring`。然后我们使用一个`if`语句来检查状态参数；如果不匹配，我们会记录一条错误消息。

我们使用`axios`向`oauth2/token`端点发出 post 请求；这是我们将请求的完整 URL:

```
const url = `http://localhost:${process.env.FUSIONAUTH_PORT}/oauth2/token`;
```

你会注意到的另一件事是`config`变量。`oauth2/token`端点需要表单编码的数据，这就是我们在头中显式设置内容类型的原因:

```
//...
const config = {
  headers: {
    "Content-Type": "application/x-www-form-urlencoded",
  },
};
//...
```

现在，让我们来谈谈请求的主体。如果你浏览一下 [FusionAuth 文档](https://fusionauth.io/docs/v1/tech/oauth/endpoints#token)，你会发现有`oauth2/token`端点所期望的标准请求参数。有些是可选的，有些是必需的。`code`是我们从`oauth2/authorize`端点收到的授权码，而`grant_type`告诉 FusionAuth 我们正在使用授权码流。

```
//...
qs.stringify({
  client_id: process.env.CLIENT_ID,
  client_secret: process.env.CLIENT_SECRET,
  code: req.query.code,
  grant_type: "authorization_code",
  redirect_uri: process.env.REDIRECT_URI,
})
//...
```

`query-string`库字符串表示这个请求对象，如下所示。这使我们不必手动这样做，并使代码更具可读性:

```
// the stringified parameters
'client_id=1ac76336-9dd9-4048-99cb-f998af681d3e&client_secret=NLmIgHC65zHeHOPlQMmOMG4Nberle41GT85RUgijdqA&code=e_oTyBn_7WPTPgtFUjvEZk6TwBBLYajRi8NMixQehd0&grant_type=authorization_code&redirect_uri=http%3A%2F%2Flocalhost%3A9000%2Foauth-callback'
```

在成功的 post 请求之后，我们使用`.then()`方法来访问来自端点的响应。我们用名称`token`存储在会话中收到的`access_token`。上面的代码记录了这个响应，以便您可以在调试时看到它。我们只关心`data.access_token`值，尽管会返回其他信息。存储此`access_token`后，我们重定向到我们的 Vue 应用程序。下面是一个向`token`端点成功请求后可能返回的内容的示例:

```
data: {
  access_token: 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IjcxNDcxZGE3ZiJ9.eyJhdWQiOiIxYWM3NjMzNi05ZGQ5LTQwNDgtOTljYi1mOTk4YWY2ODFkM2UiLCJleHAiOjE1OTQ4ODkzODAsImlhdCI6MTU5NDg4NTc4MCwiaXNzIjoiYWNtZS5jb20iLCJzdWIiOiJhYmRlZTAyNS1mYTNjLTRjZTItYjZhZi1kMDkzMWNmYjRjZWEiLCJhdXRoZW50aWNhdGlvblR5cGUiOiJQQVNTV09SRCIsImVtYWlsIjoiYXNodXNpbmdoMTU2NzNAZ21haWwuY29tIiwiZW1haWxfdmVyaWZpZWQiOnRydWUsImFwcGxpY2F0aW9uSWQiOiIxYWM3NjMzNi05ZGQ5LTQwNDgtOTljYi1mOTk4YWY2ODFkM2UiLCJyb2xlcyI6W119.Dcktd6933XI7iDEsH2RbR49lse-Mamx7B5k1q4hSz_o',
  expires_in: 3599,
  token_type: 'Bearer',
  userId: 'abdee025-fa3c-4ce2-b6af-d0931cfb4cea'
}
```

你可以看到一个`access_token`长什么样；这是 JWT。axios 请求以 catch 块结束，以处理我们可能遇到的任何错误。

前往[http://localhost:9000/log in。](http://localhost:9000/login.)如果一切顺利，您将会出现在您的 Vue 应用主页上，因为这是我们在`redirect_uri`中设置的。您应该在控制台(运行服务器的终端)中看到响应，因为您已经登录了。

# 添加注销路由

因此，我们有一个`login`路径，用户登录后会重定向回我们的 Vue 应用。在我们在 Vue 应用程序中添加链接之前，让我们在 Express 服务器中创建一条`logout`路线。然后，我们将能够轻松地将它们添加到 Vue 应用程序中。

在`server/routes`中创建一个名为`logout.js`的新文件。

```
server
├──node_modules
├──routes
│  ├─login.js
│  ├─oauth-callback.js
│  ├─logout.js
│  └─user.js
├──index.js
├──package.json
└─package-lock.json
```

添加然后将这条路线添加到`index.js`:

```
//...
// Routes
app.use('/user', require('./routes/user'))
app.use('/login', require('./routes/login'))
app.use('/logout', require('./routes/logout'))
app.use('/oauth-callback', require('./routes/oauth-callback'))
//...
```

在`logout.js`文件中添加以下代码:

```
const express = require('express');
const router = express.Router();
router.get('/', (req, res) => {
  // delete the session
  req.session.destroy();
  // end FusionAuth session
  res.redirect(`http://localhost:${process.env.FUSIONAUTH_PORT}/oauth2/logout?client_id=${process.env.CLIENT_ID}`);
});
module.exports = router;
```

与`oauth-callback.js`相比，这相当简单。我们首先销毁 Express 服务器端会话(以及我们存储的`token`),然后用我们的`CLIENT_ID`重定向到`oauth2/logout`端点。

前往[http://localhost:9000/logout](http://localhost:9000/logout)，你将被注销。导航到[http://localhost:9000/log in](http://localhost:9000/login)，您将看到登录页面。登录后，您将回到您的 Vue 应用程序。

你可能想知道为什么在**注销**后，我们**重定向回我们的 Vue 应用**，然而我们在`logout.js`文件中没有做任何类似的事情。这是因为我们将 Vue 应用程序的主入口点配置为 FusionAuth 中的注销 URL。

# 正在检索用户数据

到目前为止，我们一直在使用伪造的用户数据。因为我们现在已经将`access_token`存储在会话中，所以我们可以使用它从 FusionAuth 请求用户数据。

将`user.js`文件内容修改为:

```
const express = require("express");
const router = express.Router();
const axios = require("axios");
const qs = require("querystring");

router.get("/", (req, res) => {
  // token in session -> get user data and send it back to the vue app
  if (req.session.token) {
    axios
      .post(
        `http://localhost:${process.env.FUSIONAUTH_PORT}/oauth2/introspect`,
        qs.stringify({
          client_id: process.env.CLIENT_ID,
          token: req.session.token,
        })
      )
      .then((result) => {
        let introspectResponse = result.data;
        // valid token -> get more user data and send it back to the Vue app
        if (introspectResponse) {

          // GET request to /registration endpoint
          axios
            .get(
              `http://localhost:${process.env.FUSIONAUTH_PORT}/api/user/registration/${introspectResponse.sub}/${process.env.APPLICATION_ID}`,
              {
                headers: {
                  Authorization: process.env.API_KEY,
                },
              }
            )
            .then((response) => {
              res.send({
                introspectResponse: introspectResponse,
                body: response.data.registration,
              });
            })
        }
      // expired token -> send nothing 
        else {
          req.session.destroy();
          res.send({});
        }
      })
      .catch((err) => {
        console.log(err);
      });
  }
  // no token -> send nothing
  else {
    res.send({});
  }
});
module.exports = router;
```

让我们检查一下这段代码。首先，我们检查一个`access_token`是否存在，然后向`oauth2/introspect`端点发出一个 **POST** 请求，该请求需要客户端 Id 和令牌。像`oauth2/token`端点一样，这个端点期望表单编码的数据，所以我们再次使用了`query-string`库。

当这个请求成功时，我们得到一个响应对象。这包含用户数据。

这里有一个 JSON 的例子:

```
{
  active: true,
  applicationId: '9d5119d4-71bb-495c-b762-9f14277c116c',
  aud: '9d5119d4-71bb-495c-b762-9f14277c116c',  
  authenticationType: 'PASSWORD',
  email: 'richard@fusionauth.io',
  email_verified: true,
  exp: 1594904052,
  iat: 1594900452,
  iss: 'acme.com',
  roles: [],
  sub: 'abdee025-fa3c-4ce2-b6af-d0931cfb4cea'   
}
```

然后我们再次请求收集更多的用户信息。这次我们向`/api/user/registration`端点发出一个 **GET** 请求。这个 API 需要用户 Id，它与由`introspect`端点提供的`sub`值相同。对 **GET** 请求的响应包含用户`data`属性，其中有我们需要的信息。注意，这不是标准的，但是来自`/oauth2/introspect`端点的响应是标准的。

当这个最终请求成功时，我们通过`res.send()`将所有数据发送到我们的 Vue 客户端。下面是来自`/api/user/registration`调用的响应:

```
{
  "applicationId": "9d5119d4-71bb-495c-b762-9f14277c116c",
  "data": "",
  "id": "c756e203-ea1f-491e-9446-b70ed4eecc17",
  "insertInstant": 1594898302209,
  "lastLoginInstant": 1594900452281,
  "username": "ashu",
  "usernameStatus": "ACTIVE",
  "verified": true
}
```

我们在`Authorization` HTTP 头中传递的 API 键不是 OAuth 标准的一部分。您需要它来调用非标准端点，如[用户注册 API](https://fusionauth.io/docs/v1/tech/apis/registrations#retrieve-a-user-registration) 。我们添加这一点是为了展示如果您决定访问受 API 密钥保护的端点，如何使用该密钥。

# 显示用户数据

Express 服务器现在可以访问存储在 FusionAuth 中的用户信息。下一步是显示数据。在我们的`App.vue`文件中，我们修改了`mounted()`方法，因为这次我们得到了一个包含来自`introspect`和`registration`端点的数据的响应对象。

我们只需要在`App.vue`中增加一行。不是`data.user.email`，这次会是`data.introspectResponse.email`。当我们这样做时，让我们在`data()`中将`body`定义为 null，并将响应对象的`body`字段存储在其中。

```
//...
data() {
  return {
    email: null,
    body: null,
  };
},
mounted() {
  fetch(`http://localhost:9000/user`, {
    credentials: "include" // fetch won't send cookies unless you set credentials
  })
  .then((response) => response.json())
  .then((data) => {
    this.email = data.introspectResponse.email;
    this.body= data.body;
  });
}
//...
```

其他一切都保持不变。我们现在在应用程序中从 FusionAuth 获取用户信息，而不是伪造的用户数据。

再次执行登录过程，在成功认证后，您应该会看到“欢迎[您的电子邮件地址]”。

# 在 Vue 中添加登录和注销

我们之前已经为`login`和`logout`创建了服务器端点。让我们将它们添加到 Vue 应用程序中。创建一个名为`Login.vue`的新文件，并添加以下内容:

```
<template>
  <h1 v-if="email"><a href='http://localhost:9000/logout'>Sign Out</a></h1>
  <h1 v-else><a href='http://localhost:9000/login'>Sign In</a></h1>
</template>
<script>
export default {
  name: "Login",
  props: ["email"],
};
</script>
```

根据上面的代码，如果用户没有登录，将显示`Sign In`文本，否则将显示一条`Sign Out`消息。`email`预计会从`App.vue`这里传过来作为道具，那就这么办吧。在我们的`App.vue`文件中，首先导入`Login`组件:

```
//...
import Login from "./Login";
//...
```

然后把这个加到`components`:

```
//...
components: {
  Greet,
  Login 
}
//...
```

最后在`<template>`标签中使用它，将`email`作为属性传递:

```
//...
<div id="container">
  <Greet v-bind:email="email" />
  <Login v-bind:email="email" />
</div>
//...
```

我们现在可以点击登录和注销。这是您注销后的应用程序:

![](img/136c74339e6a895f82ea44fb9dfb26c2.png)

这是你登录时的应用程序(如果你用`richard@fusionauth.io`注册的话):

![](img/158c8bd2c36466a6faaf2a08cb6be54e.png)

# 更改用户信息

这最后一节处理从我们的 Vue 应用程序设置 FusionAuth 用户数据。

我们将创建`/set-user-data`路线；在`routes`中添加一个`set-user-data.js`文件，并将以下代码添加到其中:

```
const express = require("express");
const router = express.Router();
const axios = require("axios");
const qs = require("query-string");
router.post("/", (req, res) => {
  // POST request to /introspect endpoint
  axios
    .post(
      `http://localhost:${process.env.FUSIONAUTH_PORT}/oauth2/introspect`,
      qs.stringify({
        client_id: process.env.CLIENT_ID,
        token: req.session.token,
      })
    )
    .then((response) => {
      let introspectResponse = response.data;

      // PATCH request to /registration endpoint
      axios.patch(
        `http://localhost:${process.env.FUSIONAUTH_PORT}/api/user/registration/${introspectResponse.sub}/${process.env.APPLICATION_ID}`,
        {
          registration: {
            data: req.body,
          },
        },
        {
          headers: {
            Authorization: process.env.API_KEY,
          },
        }
      ).catch(err=>{
          console.log(err)
      })
    })
    .catch((err) => {
      console.error(err);
    });});
module.exports = router;
```

为了确保我们正在更新当前登录的用户，我们通过向`oauth/introspect`端点发出 **POST** 请求，从我们的 FusionAuth 服务器中找到令牌；这类似于我们在`user`路线中所做的。

一旦这个请求成功，我们就向`/api/user/registration` API 发出一个**补丁**请求。如果你浏览一下[用户注册文档](https://fusionauth.io/docs/v1/tech/apis/registrations#update-a-user-registration)，你会发现这个 API 同时接受**上传**和**补丁**请求。这里我们使用**补丁**，因为我们只想更新用户注册对象的单个部分，而**补丁**会将请求参数合并到现有对象中。

要发送的`data`存储在从`req.body`获取值的`registration`对象中。这个`registration`表示用户与应用程序的关联。属性允许我们在应用程序中存储与用户注册相关的任意键值数据。

我们在从 Express 到 FusionAuth 的通信中使用了**补丁**，但是我们将通过 JSON 在 **POST** HTTP 消息的正文中把用户数据从我们的 Vue 应用程序发送到 Express 服务器。

# 从 Vue 设置用户数据

既然我们已经创建了用于更新用户数据的服务器路由，那么让我们在 Vue 应用程序中创建一个`text-area`。用户将在那里输入数据，当点击`Submit`按钮时，数据将被发送到服务器。

在`client/src`中，创建一个名为`Update.vue`的新文件，并添加以下内容:

```
<template>
  <form>
    <textarea
      v-model="userData"
      placeholder="Update FusionAuth user data."
    ></textarea>
    <button type="submit" class="button">Submit</button>
  </form>
</template>
<script>
export default {
  name: "Update",
  data() {
    return {
      userData: "",
    };
  },
</script>
<style>
textarea {
  display: block;
  margin-left: auto;
  margin-right: auto;
}
button {
  margin-left: auto;
  margin-right: auto;
  margin-top: 5px;
}
</style>
```

Vue 的一个很酷的特性是，通过在`data()`函数中使用`v-model="userData"`并将`userData`初始化为空字符串，在`textarea`元素和`userData`属性之间配置了双向数据绑定。

我们现在可以在`userData`中访问我们在`textarea`中输入的任何内容。你可以在这里了解更多。

将该组件添加到`App.vue`。然而，当用户没有登录时，显示这个组件是没有意义的。要隐藏它，将`v-if="email"`添加到该组件。它将检查`email`是否存在。因此，如果用户注销，该组件将隐藏自己。

```
<Update v-if="email" />
```

我们还没有配置`Submit`按钮。让我们这样做，将我们在`textarea`中键入的任何内容发送到我们的服务器进行存储。在`methods()`部分创建一个函数`update`。

```
//...
methods: {
  update: function() {
    fetch(`http://localhost:9000/set-user-data`, {
      credentials: "include",
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        userData: this.userData,
      }),
    }).catch((err) => {
        console.log(err);
      });
      this.userData=''
  },
},
//...
```

在上面的函数中，我们用`fetch()`到 **POST** JSON 编码的数据来表示。如果你熟悉`fetch()`，你会看到这是一个简单的**岗位**要求，没什么花哨的。你可以[在这里](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)了解更多。

一旦我们将`userData`发送到我们的服务器，我们通过设置`userData`等于`''`来重置`textarea`，因为这是一个双向绑定。为了将该函数绑定到`submit`事件，我们将在`form`标签中添加以下内容:

```
<form @submit.prevent="update">
  //
</form>
```

> 有趣的事实:每当点击提交按钮时，使用`.prevent`可以阻止页面重新加载。

下面是我们的应用程序现在的样子:

![](img/bb0fd2e62fbe88597fc119f642d2e71f.png)

转到您的 Vue 应用程序，在`textarea`中键入一些文本，然后单击提交按钮。如果您登录到 FusionAuth 仪表板，您现在可以看到您添加的文本存储在 FusionAuth 中。它位于您的用户帐户详细信息中的**用户数据**选项卡下。

# 结论

恭喜，您已经构建了一个 Vue 应用程序，它允许用户登录、注销和修改他们的用户数据。本文是使用 FusionAuth 实现 OAuth 的基础。您可以添加许多其他特性、组件和路线来扩展这个应用程序。

同样，这里有[代码](https://github.com/fusionauth/fusionauth-example-vue)，你可以派生并试验。

以下是您接下来可以做的一些想法:

*   [从 App 本身注册用户。](https://fusionauth.io/docs/v1/tech/apis/registrations#create-a-user-and-registration-combined)
*   使用像[头盔](https://www.npmjs.com/package/helmet)这样的中间件来保护你的服务器
*   探索第三方认证选项，如 [Google](https://fusionauth.io/docs/v1/tech/apis/identity-providers/google) 、 [Twitter](https://fusionauth.io/docs/v1/tech/apis/identity-providers/twitter) 或其他。

*原载于*[*https://fusion auth . io*](https://fusionauth.io/blog/2020/08/06/securely-implement-oauth-vuejs)*。*
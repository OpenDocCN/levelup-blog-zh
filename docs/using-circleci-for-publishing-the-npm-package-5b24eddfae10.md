# 使用 CircleCI 发布 NPM 软件包

> 原文：<https://levelup.gitconnected.com/using-circleci-for-publishing-the-npm-package-5b24eddfae10>

![](img/bfd425ccf27e448b966d91cd54c53669.png)

## 序言

在我开始用 CircleCI 发布 NPM 套餐的博客之前，我想激励一下你们。

Relsell Global 的每一项任务都是遵循已故著名的史蒂夫·乔布斯先生的一条伟大建议完成的。

*如果我尽了最大努力却失败了*

*嗯* *我有*

*尽力了。*

## 执行

结果，马拉松开始于一个为开源做贡献的想法。我们擅长后端[节点](https://nodejs.org/en/)编程，所以我们认为最好继续在 Node.js 中开发一个包/应用程序。但是我们的 CEO 给了我们一个挑战:

*“我知道你们会找到一种方法将节点包发布到公共的 NPM 注册中心。但我希望所有这些都可以使用*[*circle ci*](https://circleci.com/)*实现自动化。”*

众所周知，挑战和问题都是伪装的机遇。最初，我们很焦虑，现在做起来可能要花很大力气。但是作为软件开发者，我们喜欢技术挑战。对吗？

## 开发节点应用程序

首先，我们开发了一个简单的节点包，它提供输入城市的当前温度。

让我们在您的系统中创建一个名为 weather-app 的文件夹。让我们在命令行或终端中打开这个文件夹。和清空 npm 项目

```
npm init -y
```

在这个文件夹中创建一个名为 app.js 的文件

```
const geocode = require("./geocode/geocode.js");
const weather = require("./weather/weather.js");var startProcess = argv => {
   var p = getGeoCoding(argv);
   p
   .then((results)=>{
      return getWeather(argv,results);
   })
   .then((result)=>{
      console.log(result);
   })
   .catch((err)=>{ })
};var getGeoCoding = argv => {
  return new Promise((resolve, reject) => {
    geocode.geocodeAddress(argv.a, argv.gapi_key, (errorMessage, results) => {
      if (errorMessage) {
        reject(errorMessage);
      } else {
        resolve(results);
      }
    });
  });
};var getWeather = (argv, results) => {
  return new Promise((resolve, reject) => {
    weather.getWeather(
      argv.dapi_key,
      results.latitude,
      results.longitude,
      (errorMessage, weatherResults) => {
        if (errorMessage) {
          reject(errorMessage);
        } else {
          resolve(weatherResults);
        }
      }
    );
  });
};module.exports = {
  startProcess,
  getWeather,
  getGeoCoding
};
```

代码的前两行需要添加不同的文件。在天气应用文件夹 ie 中创建两个文件夹。地理编码和天气。在这两个文件夹中，分别创建两个文件 geocode.js 和 weather.js。

将以下代码添加到 geocode.js 文件中

```
const axios = require('axios');var geocodeAddress = (address,gmapkey,callback) => {
  var encodedAddress = encodeURIComponent(address);
  var url = 'https://maps.googleapis.com/maps/api/geocode/json?address='+encodedAddress+'&key='+gmapkey;
  axios.get(url)
  .then((response)=>{
    callback(undefined,{
      latitude: response.data.results[0].geometry.location.lat,
      longitude: response.data.results[0].geometry.location.lng
    });
  })
  .catch((err)=>{
    callback('Unable to connect to Google servers');
  })
  .finally(()=>{ });
 } module.exports = {
  geocodeAddress
};
```

为 weather.js 文件添加以下代码

```
const axios = require('axios');var getWeather = (apikey,lat,lng,callback) => {
  var url = `https://api.forecast.io/forecast/${apikey}/${lat},${lng}`; 
  axios.get(url)
  .then((response)=>{
    callback(undefined,{
            tempreture:response.data.currently.temperature,
            apparentTemperature: response.data.currently.apparentTemperature
          });
  })
  .catch((err)=>{
    callback('Unable to connect to forecast.io server '+err);
  })
  .finally(()=>{ });}module.exports.getWeather = getWeather;
```

现在，你可能还记得，我们初始化了一个空的 npm 项目。结果，创建了一个名为 package.json 的文件。一个文件，包含关于你的包的信息，依赖于什么，等等。

我们使用了一些依赖项，如 [axios](https://www.npmjs.com/package/axios) (用于网络调用)、 [yargs](https://www.npmjs.com/package/yargs) (用于命令行参数解析)。使用以下命令

```
npm install axios yargs
```

复制粘贴到 package.json 文件下面

```
{
  "name": "rg-weather-app-node",
  "version": "1.0.16",
  "description": "",
  "repository": {
    "type": "git",
    "url": "git+https://github.com/successanil/WeatherAppNode.git"
  },
  "keywords": [
    "react-native",
    "react-component",
    "ios",
    "android",
    "weather"
  ],
  "author": "Author Name",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/successanil/WeatherAppNode/issues"
  },
  "homepage": "https://github.com/successanil/WeatherAppNode/#readme",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "dependencies": {
    "axios": "^0.18.1",
    "npm-cli-login": "^0.1.1",
    "yargs": "^4.8.1"
  },
  "peerDependencies": {
    "axios": "^0.18.1",
    "yargs": "^4.8.1"
  },
  "devDependencies": {
    "metro-react-native-babel-preset": "^0.58.0"
  }
}
```

这个应用程序从[谷歌地图 API](https://developers.google.com/maps/) 和[黑暗天空 API](https://darksky.net/) 获取结果。

因此，您需要从上述网站获取所需的 API 密钥。

您可以将以下代码添加到您的 app.js 中，位于 modules.exports 行之上

```
var argv = {
    a:"<City Name>", 
    gapi_key:"<google maps key>",
    dapi_key:"<darksky api key>"
};startProcess(argv);
```

你会得到如下结果

```
{ tempreture: <value>, apparentTemperature: <value> }
```

`<value>`是上面的占位符。

## 使用 Git 版本控制管理您的源代码

运行命令

```
git init
```

这样做将用一个空的 git 存储库初始化您的项目。稍后向我们的另一个[博客](http://relsellglobal.in/technical-blogs/git-command-line-integration/)解释 Git 库头。

添加一个. gitignore 文件，在那里你可以指定什么都不应该去远程 git repo。

我要强调这个事实:**请添加 node_modules/到其中。**

然后将相关文件添加到本地 git 存储库中。将您的远程存储库附加到您创建的本地存储库中。最后，将您的更改推送到远程存储库。

## 正在准备要发布的包

既然我们已经来到这里，我们已经准备好将我们的包发布到 npm 注册中心。让我们研究一下 package.json 文件中的几行代码

有一个“name”属性，它是 npm 注册表中的软件包的名称，其他人可以公开看到它。

然后，每当您计划为您的软件包推送更新时，都需要升级一个“版本”。

“描述”我们可以在一行中描述我们的包是做什么的。

“资源库”这里我们给出了一个链接，链接到我们在 [GitHub](http://www.github.com/) 或 [bitbucket](https://bitbucket.org/) 上的开源资源库，它是公开的，所以人们可以看到它。喜欢下面的代码

```
"repository": {
    "type": "git",
    "url": "git+https://github.com/successanil/WeatherAppNode.git"
  }
```

“关键字”是一种数组，其他人可以根据它找到我们的包。

```
"keywords": [
    "react-native",
    "react-component",
    "ios",
    "android",
    "weather"
  ]
```

你可以加上“作者”。还可以添加一些关于您的软件包的其他信息。

```
"license": "ISC",
  "bugs": {
    "url": "https://github.com/successanil/WeatherAppNode/issues"
  },
  "homepage": "https://github.com/successanil/WeatherAppNode/#readme",
```

然后添加 metro 作为包的开发依赖项。

```
"devDependencies": {
    "metro-react-native-babel-preset": "^0.58.0"
  }
```

我们将运行下面的命令，

```
npm login    (This may ask your some login info so ready with that) 
npm publish
```

瞧。你的包裹就出版了。祝贺你为自己的成长投入时间。

## 集成 CircleCI 以实现发布流程的自动化

前往 [Circle CI](https://circleci.com/) 并创建一个免费帐户。在那里添加一个新项目。我将写另一篇关于如何与 CircleCI 合作的博客，现在，让我们坚持这个主题。

创建名为的空文件夹。circleci 和其中的 config.yml 文件。在 config.yml 中编写以下代码

```
version: 2.0
jobs:
  build:
    docker:
      - image: circleci/node
    steps:
      - checkout
      - run:
          name: Install npm 
          command: npm install
      - add_ssh_keys:
          fingerprints:
            - "05:74:84:21:d3:a9:a7:bb:7a:a7:5d:d8:7b:64:82:a3"    
      - run:
          name: Execute Index.js 
          command: node index.js -a Dehradun --gapi_key $GAPI_KEY --dapi_key $DAPI_KEY
      - run: 
          name: Auth With NPM
          command: echo "//registry.npmjs.org/:_authToken=$NPM_TOKEN" > .npmrc
      - run:
          name: Publish to NPM
          command: npm publish
```

GAPI _ 钥匙，DAPI _ 钥匙，NPM _ 令牌是在 circleci 控制台的项目设置中设置的环境变量。

一旦所有这些都连接起来，您的主分支 git 中的一个 commit 将通过 circleci 触发自动构建，您的新包将在几分钟内发布到 npm registry。

## 补充相关博客

我们已经写了[博客](http://relsellglobal.in/react-native-development/publishing-an-npm-package-to-npm-registry/)来手动发布 react-native 包到 npm 注册表。

## NPM 注册表中的源代码和包

该项目的源代码可从[链接](https://github.com/successanil/WeatherAppNode/)获得

国家预防机制登记处的软件包可通过[链接](https://www.npmjs.com/package/rg-weather-app-node)获得

## 结论

这就把我们带到了这篇博客的结尾。为此，我们勤奋的开发人员付出了大量真诚的努力。感谢他们不屈不挠的支持。无论你第一次尝试是成功还是失败，真诚的努力永远伴随着你。这可能需要一些时间来支付，但它值得。这是生活中最可靠的方面。

感谢阅读。

故事最初发布在 easy Node.js 类别下的这个[链接](http://relsellglobal.in/category/npm-node/easy-nodejs/)
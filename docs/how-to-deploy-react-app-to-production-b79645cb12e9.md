# 如何将 React 应用部署到生产中

> 原文：<https://levelup.gitconnected.com/how-to-deploy-react-app-to-production-b79645cb12e9>

了解将 React App 部署到 Heroku、now.sh 和 surge.sh 的步骤。

![](img/9944308c8962b0c2606ea566fb2c10c9.png)

由[都铎·巴休](https://unsplash.com/@baciutudor?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

**将 React 应用部署到 Heroku:**

1.  使用以下命令在您的机器上安装 Heroku

```
npm install -g heroku or yarn global add heroku
```

2.通过在终端或命令提示符下执行以下命令，导航到 React 项目文件夹并登录 heroku

```
heroku login
```

这将把你重定向到 Heroku 网站，在那里你必须使用你的 Heroku 凭证登录，一旦登录，返回到命令行。

3.通过运行以下命令在 Heroku 上创建一个新的应用程序

```
heroku create app-name
For ex. heroku create my-react-app-demo
```

4.通过执行以下命令，使当前项目成为 git 存储库

```
git init . // it's ( git init dot )
```

5.现在，使用以下命令将所有更改添加到暂存中

```
git add --all .  ( dot for current directory)
```

6.使用提交更改

```
git commit -m "your commit message"
```

7.通过运行以下命令将代码推送到 Heroku

```
git push heroku master
```

8.恭喜你，你已经成功地在 heroku 上部署了你的应用。

**演示**:[https://my-node-react-app.herokuapp.com/](https://my-node-react-app.herokuapp.com/)(创建于[本文](https://medium.com/javascript-in-plain-english/build-an-amazing-application-using-react-and-nodejs-together-fad13ab7b49c))

**将 React App 部署到 now.sh 或 zeit.io:**

1.  通过运行以下命令安装 npm 软件包

```
npm install -g now 
or 
yarn global add now
```

2.从终端或命令提示符导航到 react 项目文件夹，并执行以下命令

```
npm run build 
or 
yarn run build
```

3.一旦创建了`build`文件夹，在构建文件夹中导航并运行`now`命令

```
cd build
now
```

4.它会要求您提供您的电子邮件，并向您发送一封带有代码的电子邮件，以验证您首次登录时的电子邮件

5.一旦确认，再次执行`now`命令，它会询问你项目的名称和其他细节。使用与项目名称相同的名称，以便于识别。如果你不确定任何问题该输入什么，只需按回车键，它将采用默认值。

6.完成后，它将显示部署应用程序的 URL，您可以在浏览器中访问该 URL。

7.使用您的凭证登录`now.sh`后，您可以访问您部署的所有应用程序。

**演示:**[https://children-props-demo-now.now.sh/](https://children-props-demo-now.now.sh/)(创作于[本文](https://medium.com/javascript-in-plain-english/everything-you-need-to-know-about-children-prop-in-react-9c0e0913f5a2))

**将 React app 部署到 surge.sh:**

1.  通过运行以下命令安装浪涌 npm 软件包

```
npm install -g surge 
or 
yarn global add surge
```

2.从终端或命令提示符导航到 React 项目，并执行以下命令

```
npm run build 
or 
yarn run build
```

3.一旦创建了`build`文件夹，在构建文件夹中导航并运行`surge`命令

```
cd build
surge
```

4.它会要求您输入您的电子邮件和密码

5.然后，它将显示应用程序将部署到的默认域。

6.如果需要，请输入新的域值或按 Enter 键

7.然后，它将部署应用程序，并显示访问应用程序的 URL

**演示:**[http://children-prop-demo.surge.sh/](http://children-prop-demo.surge.sh/)(创建于[本文](https://medium.com/javascript-in-plain-english/everything-you-need-to-know-about-children-prop-in-react-9c0e0913f5a2))

**注意:**在`now.sh`或`surge.sh`上启动您的应用程序既简单又快速，而且它会一直保持活动状态。

但是在`Heroku`的情况下，如果你是免费用户，你最多可以创建 4 个应用程序，如果没有请求，你的应用程序将在 30 分钟后进入睡眠模式。因此，当你再次访问该网站时，需要一些时间来加载应用程序。

但这不会在 surge 或 now 上发生，而且 now 或 surge 上可以部署的应用程序数量没有限制。

今天到此为止。我希望你学到了新东西。

**别忘了订阅我的每周简讯，里面有惊人的技巧、窍门和文章，直接在这里的收件箱里** [**。**](https://yogeshchavan.dev/)
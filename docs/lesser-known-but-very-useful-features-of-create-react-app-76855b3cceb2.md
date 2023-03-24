# create-react-app 鲜为人知但非常有用的特性

> 原文：<https://levelup.gitconnected.com/lesser-known-but-very-useful-features-of-create-react-app-76855b3cceb2>

## 你应该知道的 create-react-app 提供的额外功能

![](img/b349a564253dcb96c86a7b7f87f2c7fd.png)

由[萨法尔·萨法罗夫](https://unsplash.com/@codestorm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将探索 create-react-app 提供的鲜为人知的特性。所以让我们开始吧

1.**通过 https 而不是 http 为您的应用提供服务**

有时我们需要在`https`上测试我们的应用程序，以在部署到生产环境之前检查所有的 API 是否工作正常。

Create-react-app 提供了一种简单的方法

在你的项目文件夹中创建一个`.env` (dot env)文件，并在其中添加`HTTPS=true`。

```
HTTPS=true
```

通过运行`yarn start`或`npm start`重启你的应用程序，现在你的整个应用程序将在 https 上运行。

**2。为导入使用绝对路径**

在每个应用程序中，我们都有 import 语句，在这里我们必须从当前文件夹中找到另一个文件，如下所示:

```
import { login } from '../actions/login';
import profileReducer from '../reducers/profile';
```

因此，我们必须检查我们在哪个文件夹中，然后添加那些双点数字，以导入另一个文件。Create-react-app 让这一切变得简单。

在项目文件夹中创建一个新文件`jsconfig.json`，并在其中添加以下代码

```
{
 "compilerOptions": {
   "baseUrl": "./src"
 }
}
```

在这里，我们指定了所有文件所在的基本文件夹。(主要是 React 应用程序中的`src`文件夹)。

所以现在我们可以简化上面的导入，如下所示

```
import { login } from 'actions/login';
import profileReducer from 'reducers/profile';
```

有了这个配置，现在它将把`src`作为一个基本 url，所以我们只需要在 src 文件夹中指定从*开始的文件夹路径。*

这将避免为深度嵌套的路径添加额外的点。

**3。访问 React 中的环境变量，无需任何额外的库**

环境变量很重要，因为它们允许我们保护私人信息的安全。可以是`username`或`password`或`API key`。

它们还允许我们根据环境(开发、试运行、生产等)为应用程序提供不同的数据值。

Create-react-app 允许我们在不使用任何外部库的情况下读取环境变量。

要在 React 中创建环境变量，创建一个新的`.env` (dot env)文件，并在其中声明环境变量，如下所示

```
REACT_APP_API_BASE_URL=environment_variable_value
REACT_APP_PASSWORD=your_password
```

以`REACT_APP_`开始你的环境变量名是很重要的，这样`create-react-app`将能够读取它。

一旦你用你的变量创建了一个`.env`文件，它将在你的 React 应用程序的`process.env`对象中可用。

```
process.env.REACT_APP_API_BASE_URL
process.env.REACT_APP_PASSWORD
```

**注意:**出于隐私原因，您不应该将`.env`文件推送到您的 git 存储库，所以请确保在您的`.gitignore`文件中添加`.env`条目。

今天到此为止。我希望你学到了新东西。

不要忘记订阅我的每周简讯，里面有惊人的技巧、窍门和文章，请直接在这里的收件箱 [**。**](https://yogeshchavan.dev/)
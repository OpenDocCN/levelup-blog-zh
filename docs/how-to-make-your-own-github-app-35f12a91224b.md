# 如何制作自己的 GitHub App

> 原文：<https://levelup.gitconnected.com/how-to-make-your-own-github-app-35f12a91224b>

![](img/ae03cc6393f1b36252cda4b78cd7e796.png)

GitHub 是一个很棒的存储库托管服务，可以让你免费托管 Git 存储库。它提供了一个存储所有源代码和管理版本历史的平台，并且它还公开了一个我们可以利用的优秀 API。

开发者为 GitHub 的 API 做了 Node.js 客户端。我们可以使用它来对代码执行操作，比如操作提交、添加、删除或编辑存储库、跟踪问题、操作构建管道等等。

`[octokit/rest.js](https://octokit.github.io/rest.js/)` [包](https://octokit.github.io/rest.js/)是编写 Node.js GitHub 应用程序最简单的包之一。我们所要做的就是用我们的 GitHub 用户名和密码登录 GitHub 实例，并调用内置函数来做我们想用 GitHub 包做的几乎任何事情。

在本文中，我们将制作一个简单的带身份验证的 GitHub 应用程序。我们将让用户注册一个帐户，并为他们的帐户设置 GitHub 用户名/密码。一旦用户登录并设置了他们的 GitHub 凭证，他们就可以查看他们的存储库和每个存储库的提交。

> 注意:这个应用程序仅用于演示目的。您不应该管理用户的 GitHub 用户名和密码。如果您想要一个产品 GitHub 集成，您应该使用 OAuth 和 GitHub 来接收应用程序的访问令牌。

我们的应用程序将包括后端和前端。后端处理用户认证，并通过 GitHub API 与 GitHub 通信。前端有注册表单，登录表单，添加密码的设置表单和 GitHub 用户名和密码。

为了开始构建应用程序，我们创建一个项目文件夹，其中包含一个`backend`文件夹。在`backend`文件夹中，通过执行`npx express-generator`运行快速生成器。接下来我们将自己安装一些包。我们需要 Babel 使用`import`，BCrypt 使用哈希密码，而`@octokit/rest`使用 GitHub API。我们还需要 Crypto-JS 来加密和解密我们的 GitHub 密码，Dotenv 来存储哈希和加密秘密，Sequelize 用于 ORM，JSON Web Token packages 用于认证，CORS 用于跨域通信，SQLite 用于数据库。

要安装软件包，请运行:

```
npm i @babel/cli @babel/core @babel/node @babel/preset-env bcrypt @octokit/rest cors crypto-js dotenv jsonwebtoken sequelize sqlite3
```

在`package.json`的`script`部分，输入:

```
"start": "nodemon --exec npm run babel-node --  ./bin/www",
"babel-node": "babel-node"
```

这将使用 Babel Node 而不是常规的 Node.js 运行时启动我们的应用程序，以便我们获得最新的 JavaScript 特性。

然后，我们在`backend`文件夹中创建一个名为`.babelrc`的文件，并添加:

```
{
    "presets": [
        "[@babel/preset-env](http://twitter.com/babel/preset-env)"
    ]
}
```

这是启用这些功能的配置

然后我们需要通过运行`npx sequelize-cli init`来添加序列代码。之后，我们应该在`config`文件夹中得到一个`config.json`文件。

在`config.json`中，我们输入:

```
{
  "development": {
    "dialect": "sqlite",
    "storage": "development.db"
  },
  "test": {
    "dialect": "sqlite",
    "storage": "test.db"
  },
  "production": {
    "dialect": "sqlite",
    "storage": "production.db"
  }
}
```

这将 SQLite 设置为我们的数据库。

接下来，我们添加一个中间件来验证身份验证令牌。首先在`backend`文件夹中添加一个`middllewares`文件夹，然后在里面添加`authCheck.js`。该文件应包含以下代码:

```
const jwt = require("jsonwebtoken");
const secret = process.env.JWT_SECRET;export const authCheck = (req, res, next) => {
  if (req.headers.authorization) {
    const token = req.headers.authorization;
    jwt.verify(token, secret, (err, decoded) => {
      if (err) {
        res.send(401);
      } else {
        next();
      }
    });
  } else {
    res.send(401);
  }
};
```

如果令牌无效，我们将返回 401 响应。

接下来，我们创建一些迁移和模型。运行:

```
npx sequelize-cli model:create --name User --attributes username:string,password:string,gitHubUsername:string,gitHubPassword:string
```

这就创建了模型。请注意，属性选项没有空格。

然后，我们向 Users 表的`username`列添加一个惟一约束。为此，请运行:

```
npx sequelize-cli migration:create addUniqueConstraintToUser
```

然后在新创建的迁移文件中，添加:

```
"use strict";module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.addConstraint("Users", ["username"], {
      type: "unique",
      name: "usernameUnique"
    });
  },down: (queryInterface, Sequelize) => {
    return queryInterface.removeConstraint("Users", "usernameUnique");
  }
};
```

运行`npx sequelize-cli db:migrate`运行所有迁移。

接下来，我们创建路线。创建一个名为`github.js`的文件，并添加:

```
var express = require("express");
const models = require("../models");
const CryptoJS = require("crypto-js");
const jwt = require("jsonwebtoken");
const Octokit = require("[@octokit/rest](http://twitter.com/octokit/rest)");
import { authCheck } from "../middlewares/authCheck";var router = express.Router();router.post("/setGithubCredentials", authCheck, async (req, res, next) => {
  const { gitHubUsername, gitHubPassword } = req.body;
  const token = req.headers.authorization;
  const decoded = jwt.verify(token, process.env.JWT_SECRET);
  const id = decoded.userId;
  const cipherText = CryptoJS.AES.encrypt(
    gitHubPassword,
    process.env.CRYPTO_SECRET
  );
  await models.User.update(
    {
      gitHubUsername,
      gitHubPassword: cipherText.toString()
    },
    {
      where: { id }
    }
  );
  res.json({});
});router.get("/repos/:page", authCheck, async (req, res, next) => {
  const page = req.params.page || 1;
  const token = req.headers.authorization;
  const decoded = jwt.verify(token, process.env.JWT_SECRET);
  const id = decoded.userId;
  const users = await models.User.findAll({ where: { id } });
  const user = users[0];
  const bytes = CryptoJS.AES.decrypt(
    user.gitHubPassword.toString(),
    process.env.CRYPTO_SECRET
  );
  const password = bytes.toString(CryptoJS.enc.Utf8);
  const octokit = new Octokit({
    auth: {
      username: user.gitHubUsername,
      password
    }
  });
  console.log(user.gitHubUsername);
  const data = await octokit.repos.list({
    username: user.gitHubUsername,
    page
  });
  res.json(data);
});router.get("/commits/", authCheck, async (req, res, next) => {
  const token = req.headers.authorization;
  const decoded = jwt.verify(token, process.env.JWT_SECRET);
  const id = decoded.userId;
  const users = await models.User.findAll({ where: { id } });
  const user = users[0];
  const repoPath = req.query.repo;
  const bytes = CryptoJS.AES.decrypt(
    user.gitHubPassword.toString(),
    process.env.CRYPTO_SECRET
  );
  const password = bytes.toString(CryptoJS.enc.Utf8);
  const octokit = new Octokit({
    auth: {
      username: user.gitHubUsername,
      password
    }
  });
  const [owner, repo] = repoPath.split("/");
  const data = await octokit.repos.listCommits({
    owner,
    repo
  });
  res.json(data);
});module.exports = router;
```

在每条路由中，我们从包含用户 ID 的令牌中获取用户，然后从令牌中获取 GitHub 用户名和密码，用于登录 GitHub API。请注意，我们必须解密密码，因为我们在将密码保存到数据库之前对其进行了加密。

我们在`setGithubCredentials`路由中设置 GitHub 凭证。我们在保存密码之前对其进行加密，以确保其安全。

接下来，我们将`user.js`添加到`routes`文件夹中，并添加:

```
const express = require("express");
const models = require("../models");
const bcrypt = require("bcrypt");
const jwt = require("jsonwebtoken");
import { authCheck } from "../middlewares/authCheck";
const router = express.Router();router.post("/signup", async (req, res, next) => {
  try {
    const { username, password } = req.body;
    const hashedPassword = await bcrypt.hash(password, 10);
    const user = await models.User.create({
      username,
      password: hashedPassword
    });
    res.json(user);
  } catch (error) {
    res.status(400).json(error);
  }
});router.post("/login", async (req, res, next) => {
  const { username, password } = req.body;
  const users = await models.User.findAll({ where: { username } });
  const user = users[0];
  const response = await bcrypt.compare(password, user.password);
  if (response) {
    const token = jwt.sign({ userId: user.id }, process.env.JWT_SECRET);
    res.json({ token });
  } else {
    res.status(401).json({});
  }
});router.post("/changePassword", authCheck, async (req, res, next) => {
  const { password } = req.body;
  const token = req.headers.authorization;
  const decoded = jwt.verify(token, process.env.JWT_SECRET);
  const id = decoded.userId;
  const hashedPassword = await bcrypt.hash(password, 10);
  await models.User.update({ password: hashedPassword }, { where: { id } });
  res.json({});
});router.get("/currentUser", authCheck, async (req, res, next) => {
  const token = req.headers.authorization;
  const decoded = jwt.verify(token, process.env.JWT_SECRET);
  const id = decoded.userId;
  const users = await models.User.findAll({ where: { id } });
  const { username, gitHubUsername } = users[0];
  res.json({ username, gitHubUsername });
});module.exports = router;
```

我们有注册、登录和更改密码的路径。当我们注册或更改密码时，我们会在保存之前对密码进行哈希处理。

`currentUser`路径将用于前端的设置表单。

在`app.js`中，我们将现有代码替换为:

```
require("dotenv").config();
var createError = require("http-errors");
var express = require("express");
var path = require("path");
var cookieParser = require("cookie-parser");
var logger = require("morgan");
const cors = require("cors");var indexRouter = require("./routes/index");
var usersRouter = require("./routes/users");
var githubRouter = require("./routes/github");var app = express();// view engine setup
app.set("views", path.join(__dirname, "views"));
app.set("view engine", "jade");app.use(logger("dev"));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, "public")));
app.use(cors());app.use("/", indexRouter);
app.use("/users", usersRouter);
app.use("/github", githubRouter);// catch 404 and forward to error handler
app.use(function(req, res, next) {
  next(createError(404));
});// error handler
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get("env") === "development" ? err : {};// render the error page
  res.status(err.status || 500);
  res.render("error");
});module.exports = app;
```

这添加了 CORS 插件以支持跨域通信，并通过添加以下内容在我们的应用程序中添加了`users`和`github`路由:

```
app.use("/users", usersRouter);
app.use("/github", githubRouter);
```

这完成了应用程序的后端部分。现在我们可以构建前端了。我们将使用 React 来构建它，因此我们首先在项目的根文件夹中运行以下代码。

```
npx create-react-app frontend
```

接下来，我们必须安装一些软件包。我们将安装用于样式化的 Bootstrap，用于路由的 React Router，分别用于表单值处理和表单验证的 Formik 和 Yup，以及用于发出 HTTP 请求的 Axios。

要执行此运行，请执行以下操作:

```
npm i axios bootstrap formik react-bootstrap react-router-dom yup
```

接下来，我们修改`App.js`文件夹，用以下代码替换现有代码:

```
import React from "react";
import HomePage from "./HomePage";
import "./App.css";
import ReposPage from "./ReposPage";
import CommitsPage from "./CommitsPage";
import SettingsPage from "./SettingsPage";
import { createBrowserHistory as createHistory } from "history";
import { Router, Route } from "react-router-dom";
import SignUpPage from "./SignUpPage";
import RequireAuth from "./RequireAuth";
const history = createHistory();function App() {
  return (
    <div className="App">
      <Router history={history}>
        <Route path="/" exact component={HomePage} />
        <Route path="/signup" exact component={SignUpPage} />
        <Route
          path="/settings"
          component={props => (
            <RequireAuth {...props} Component={SettingsPage} />
          )}
        />
        <Route
          path="/repos"
          exact
          component={props => <RequireAuth {...props} Component={ReposPage} />}
        />
        <Route
          path="/commits"
          exact
          component={props => (
            <RequireAuth {...props} Component={CommitsPage} />
          )}
        />
      </Router>
    </div>
  );
}export default App;
```

我们在这里为应用程序中的页面添加路线。`RequiredAuth`是一个更高级的组件，它接受一个需要身份验证才能访问的组件，如果用户没有被授权，则返回 redirect。如果用户被授权，它返回页面。我们将在本文的后面构建这个特设。

在`App.css`中，我们将现有代码替换为:

```
.page {
    padding: 20px;
}
```

接下来，我们将`CommitsPage.js`添加到`src`文件夹中，并添加:

```
import React, { useState, useEffect } from "react";
import { withRouter } from "react-router-dom";
import { commits } from "./requests";
import Card from "react-bootstrap/Card";
import LoggedInTopBar from "./LoggedInTopBar";
const moment = require("moment");
const querystring = require("querystring");function CommitsPage({ location }) {
  const [initialized, setInitialized] = useState(false);
  const [repoCommits, setRepoCommits] = useState([]); const getCommits = async page => {
    const repoName = querystring.decode(location.search)["?repo"];
    const response = await commits(repoName, page);
    setRepoCommits(response.data.data);
  }; useEffect(() => {
    if (!initialized) {
      getCommits(1);
      setInitialized(true);
    }
  }); return (
    <>
      <LoggedInTopBar />
      <div className="page">
        <h1 className="text-center">Commits</h1>
        {repoCommits.map(rc => {
          return (
            <Card style={{ width: "90vw", margin: "0 auto" }}>
              <Card.Body>
                <Card.Title>{rc.commit.message}</Card.Title>
                <p>Message: {rc.commit.message}</p>
                <p>Author: {rc.author.login}</p>
                <p>
                  Date:{" "}
                  {moment(rc.commit.author.date).format("dddd, MMMM Do YYYY, h:mm:ss a")}
                </p>
                <p>Hash: {rc.sha}</p>
              </Card.Body>
            </Card>
          );
        })}
      </div>
    </>
  );
}export default withRouter(CommitsPage);
```

我们获得 URL 参数中给定的存储库名称的提交，并将它们显示在 React Bootstrap 提供的`Cards`列表中。

接下来，我们创建主页，让我们登录。在`src`文件夹中创建一个`HomePage.js`文件，并添加:

```
import React, { useState, useEffect } from "react";
import { Formik } from "formik";
import Form from "react-bootstrap/Form";
import Col from "react-bootstrap/Col";
import Button from "react-bootstrap/Button";
import { Link } from "react-router-dom";
import * as yup from "yup";
import "./HomePage.css";
import { logIn } from "./requests";
import { Redirect } from "react-router-dom";
import Navbar from "react-bootstrap/Navbar";
import Nav from "react-bootstrap/Nav";const schema = yup.object({
  username: yup.string().required("Username is required"),
  password: yup.string().required("Password is required")
});function HomePage() {
  const [redirect, setRedirect] = useState(false); const handleSubmit = async evt => {
    const isValid = await schema.validate(evt);
    if (!isValid) {
      return;
    }
    try {
      const response = await logIn(evt);
      localStorage.setItem("token", response.data.token);
      setRedirect(true);
    } catch (ex) {
      alert("Invalid username or password");
    }
  }; if (redirect) {
    return <Redirect to="/repos" />;
  } return (
    <>
      <Navbar bg="primary" expand="lg" variant="dark">
        <Navbar.Brand href="#home">GitHub App</Navbar.Brand>
      </Navbar>
      <div className="page">
        <h1 className="text-center">Log In</h1>
        <Formik validationSchema={schema} onSubmit={handleSubmit}>
          {({
            handleSubmit,
            handleChange,
            handleBlur,
            values,
            touched,
            isInvalid,
            errors
          }) => (
            <Form noValidate onSubmit={handleSubmit}>
              <Form.Row>
                <Form.Group as={Col} md="12" controlId="username">
                  <Form.Label>Username</Form.Label>
                  <Form.Control
                    type="text"
                    name="username"
                    placeholder="Username"
                    value={values.username || ""}
                    onChange={handleChange}
                    isInvalid={touched.username && errors.username}
                  />
                  <Form.Control.Feedback type="invalid">
                    {errors.username}
                  </Form.Control.Feedback>
                </Form.Group>
                <Form.Group as={Col} md="12" controlId="password">
                  <Form.Label>Password</Form.Label>
                  <Form.Control
                    type="password"
                    name="password"
                    placeholder="Password"
                    value={values.password || ""}
                    onChange={handleChange}
                    isInvalid={touched.password && errors.password}
                  /> <Form.Control.Feedback type="invalid">
                    {errors.password}
                  </Form.Control.Feedback>
                </Form.Group>
              </Form.Row>
              <Button type="submit" style={{ marginRight: "10px" }}>
                Log In
              </Button>
              <Link
                className="btn btn-primary"
                to="/signup"
                style={{ marginRight: "10px", color: "white" }}
              >
                Sign Up
              </Link>
            </Form>
          )}
        </Formik>
      </div>
    </>
  );
}export default HomePage;
```

我们使用 Formik 提供的`Formik`组件来自动处理表单值。它将把这些值直接传递给`onSubmit`处理程序的参数。在`handleSubmit`函数中，我们运行`schema.validate`函数，根据从 Yup 库生成的`schema`对象检查表单值的有效性，如果成功，那么我们从我们将创建的`requests.js`文件中调用`login`。

接下来，我们将建立顶部酒吧。在`src`文件夹中创建一个`LoggedInTopBar.js`文件，并添加:

```
import React, { useState } from "react";
import Navbar from "react-bootstrap/Navbar";
import Nav from "react-bootstrap/Nav";
import { withRouter, Redirect } from "react-router-dom";function LoggedInTopBar({ location }) {
  const [redirect, setRedirect] = useState(false);
  const { pathname } = location;const isLoggedIn = () => !!localStorage.getItem("token");if (redirect) {
    return <Redirect to="/" />;
  }return (
    <div>
      {isLoggedIn() ? (
        <Navbar bg="primary" expand="lg" variant="dark">
          <Navbar.Brand href="#home">GitHub App</Navbar.Brand>
          <Navbar.Toggle aria-controls="basic-navbar-nav" />
          <Navbar.Collapse id="basic-navbar-nav">
            <Nav className="mr-auto">
              <Nav.Link href="/settings" active={pathname == "/settings"}>
                Settings
              </Nav.Link>
              <Nav.Link href="/repos" active={pathname == "/repos"}>
                Repos
              </Nav.Link>
              <Nav.Link>
                <span
                  onClick={() => {
                    localStorage.clear();
                    setRedirect(true);
                  }}
                >
                  Log Out
                </span>
              </Nav.Link>
            </Nav>
          </Navbar.Collapse>
        </Navbar>
      ) : null}
    </div>
  );
}export default withRouter(LoggedInTopBar);
```

这包含 React 引导程序`Navbar`显示为一个顶栏，带有指向主页和应用程序名称的链接。我们只显示本地存储器中的`token`。我们检查`pathname`以通过设置`active`属性来突出显示正确的链接。

接下来在`src`文件夹中创建`ReposPage.js`来显示用户拥有的存储库列表。我们补充:

```
import React, { useState, useEffect } from "react";
import { repos } from "./requests";
import Card from "react-bootstrap/Card";
import { Link } from "react-router-dom";
import Pagination from "react-bootstrap/Pagination";
import LoggedInTopBar from "./LoggedInTopBar";function ReposPage() {
  const [initialized, setInitialized] = useState(false);
  const [repositories, setRepositories] = useState([]);
  const [page, setPage] = useState(1);
  const [totalPages, setTotalPages] = useState(1); const getRepos = async page => {
    const response = await repos(page);
    setRepositories(response.data.data);
    setTotalPages(Math.ceil(response.data.size / response.data.pagelen));
  }; useEffect(() => {
    if (!initialized) {
      getRepos(1);
      setInitialized(true);
    }
  }); return (
    <div>
      <LoggedInTopBar />
      <h1 className="text-center">Your Repositories</h1>
      {repositories.map((r, i) => {
        return (
          <Card style={{ width: "90vw", margin: "0 auto" }} key={i}>
            <Card.Body>
              <Card.Title>{r.name}</Card.Title>
              <Link className="btn btn-primary" to={`/commits?repo=${r.full_name}`}>
                Go
              </Link>
            </Card.Body>
          </Card>
        );
      })}
      <br />
      <Pagination style={{ width: "90vw", margin: "0 auto" }}>
        <Pagination.First onClick={() => getRepos(1)} />
        <Pagination.Prev
          onClick={() => {
            let p = page - 1;
            getRepos(p);
            setPage(p);
          }}
        />
        <Pagination.Next
          onClick={() => {
            let p = page + 1;
            getRepos(p);
            setPage(p);
          }}
        />
        <Pagination.Last onClick={() => getRepos(totalPages)} />
      </Pagination>
      <br />
    </div>
  );
}export default ReposPage;
```

我们获得了一个存储库列表，并添加了分页功能来加载更多的存储库。

在`requests.js`中，我们添加:

```
const axios = require("axios");
const APIURL = "[http://localhost:3000](http://localhost:3000)";axios.interceptors.request.use(
  config => {
    config.headers.authorization = localStorage.getItem("token");
    return config;
  },
  error => Promise.reject(error)
);axios.interceptors.response.use(
  response => {
    return response;
  },
  error => {
    if (error.response.status == 401) {
      localStorage.clear();
    }
    return error;
  }
);export const signUp = data => axios.post(`${APIURL}/users/signup`, data);export const logIn = data => axios.post(`${APIURL}/users/login`, data);export const changePassword = data =>
  axios.post(`${APIURL}/users/changePassword`, data);export const currentUser = () => axios.get(`${APIURL}/users/currentUser`);export const setGithubCredentials = data =>
  axios.post(`${APIURL}/github/setGithubCredentials`, data);export const repos = page =>
  axios.get(`${APIURL}/github/repos/${page || 1}`);export const commits = (repoName) =>
  axios.get(`${APIURL}/github/commits?repo=${repoName}`);
```

这个文件包含我们注册、登录、设置凭证、获取存储库和提交等所有 HTTP 请求。

对于处理响应，如果我们得到 401 响应，那么我们清除本地存储，这样我们就不会使用无效的令牌。代码如下:

```
axios.interceptors.response.use(
  response => {
    return response;
  },
  error => {
    if (error.response.status == 401) {
      localStorage.clear();
    }
    return error;
  }
);
```

我们使用以下内容将令牌附加到请求头:

```
axios.interceptors.request.use(
  config => {
    config.headers.authorization = localStorage.getItem("token");
    return config;
  },
  error => Promise.reject(error)
);
```

这使我们不必为每个经过身份验证的请求设置它。

接下来在`src`文件夹中创建一个名为`RequiredAuth.js`的文件，并添加:

```
import React from "react";
import { Redirect } from "react-router-dom";function RequireAuth({ Component }) {
  if (!localStorage.getItem("token")) {
    return <Redirect to="/" />;
  }
  return <Component />;
}export default RequireAuth;
```

我们检查令牌是否存在于已认证的路由中，如果令牌存在，则呈现传入的`Component`属性。

然后，我们在`src`文件夹中创建一个名为`SettingsPage.js`的文件，并添加:

```
import React, { useState, useEffect } from "react";
import { Formik } from "formik";
import Form from "react-bootstrap/Form";
import Col from "react-bootstrap/Col";
import Button from "react-bootstrap/Button";
import * as yup from "yup";
import {
  currentUser,
  setGithubCredentials,
  changePassword
} from "./requests";
import LoggedInTopBar from "./LoggedInTopBar";const userFormSchema = yup.object({
  username: yup.string().required("Username is required"),
  password: yup.string().required("Password is required")
});const githubFormSchema = yup.object({
  gitHubUsername: yup.string().required("Username is required"),
  gitHubPassword: yup.string().required("Password is required")
});function SettingsPage() {
  const [initialized, setInitialized] = useState(false);
  const [user, setUser] = useState({});
  const [githubUser, setGithubUser] = useState({}); const handleUserSubmit = async evt => {
    const isValid = await userFormSchema.validate(evt);
    if (!isValid) {
      return;
    }
    try {
      await changePassword(evt);
      alert("Password changed");
    } catch (error) {
      alert("Password change failed");
    }
  }; const handlegithubSubmit = async evt => {
    const isValid = await githubFormSchema.validate(evt);
    if (!isValid) {
      return;
    }
    try {
      await setGithubCredentials(evt);
      alert("github credentials changed");
    } catch (error) {
      alert("github credentials change failed");
    }
  }; const getCurrentUser = async () => {
    const response = await currentUser();
    const { username, gitHubUsername } = response.data;
    setUser({ username });
    setGithubUser({ gitHubUsername });
  }; useEffect(() => {
    if (!initialized) {
      getCurrentUser();
      setInitialized(true);
    }
  }); return (
    <>
      <LoggedInTopBar />
      <div className="page">
        <h1 className="text-center">Settings</h1>
        <h2>User Settings</h2>
        <Formik
          validationSchema={userFormSchema}
          onSubmit={handleUserSubmit}
          initialValues={user}
          enableReinitialize={true}
        >
          {({
            handleSubmit,
            handleChange,
            handleBlur,
            values,
            touched,
            isInvalid,
            errors
          }) => (
            <Form noValidate onSubmit={handleSubmit}>
              <Form.Row>
                <Form.Group as={Col} md="12" controlId="username">
                  <Form.Label>Username</Form.Label>
                  <Form.Control
                    type="text"
                    name="username"
                    placeholder="Username"
                    value={values.username || ""}
                    onChange={handleChange}
                    isInvalid={touched.username && errors.username}
                    disabled
                  />
                  <Form.Control.Feedback type="invalid">
                    {errors.username}
                  </Form.Control.Feedback>
                </Form.Group>
                <Form.Group as={Col} md="12" controlId="password">
                  <Form.Label>Password</Form.Label>
                  <Form.Control
                    type="password"
                    name="password"
                    placeholder="Password"
                    value={values.password || ""}
                    onChange={handleChange}
                    isInvalid={touched.password && errors.password}
                  /> <Form.Control.Feedback type="invalid">
                    {errors.password}
                  </Form.Control.Feedback>
                </Form.Group>
              </Form.Row>
              <Button type="submit" style={{ marginRight: "10px" }}>
                Save
              </Button>
            </Form>
          )}
        </Formik> <br />
        <h2>GitHub Settings</h2>
        <Formik
          validationSchema={githubFormSchema}
          onSubmit={handlegithubSubmit}
          initialValues={githubUser}
          enableReinitialize={true}
        >
          {({
            handleSubmit,
            handleChange,
            handleBlur,
            values,
            touched,
            isInvalid,
            errors
          }) => (
            <Form noValidate onSubmit={handleSubmit}>
              <Form.Row>
                <Form.Group as={Col} md="12" controlId="gitHubUsername">
                  <Form.Label>GitHub Username</Form.Label>
                  <Form.Control
                    type="text"
                    name="gitHubUsername"
                    placeholder="GitHub Username"
                    value={values.gitHubUsername || ""}
                    onChange={handleChange}
                    isInvalid={
                      touched.gitHubUsername && errors.gitHubUsername
                    }
                  />
                  <Form.Control.Feedback type="invalid">
                    {errors.gitHubUsername}
                  </Form.Control.Feedback>
                </Form.Group>
                <Form.Group as={Col} md="12" controlId="gitHubPassword">
                  <Form.Label>Password</Form.Label>
                  <Form.Control
                    type="password"
                    name="gitHubPassword"
                    placeholder="GitHub Password"
                    value={values.gitHubPassword || ""}
                    onChange={handleChange}
                    isInvalid={
                      touched.gitHubPassword && errors.gitHubPassword
                    }
                  /><Form.Control.Feedback type="invalid">
                    {errors.gitHubPassword}
                  </Form.Control.Feedback>
                </Form.Group>
              </Form.Row>
              <Button type="submit" style={{ marginRight: "10px" }}>
                Save
              </Button>
            </Form>
          )}
        </Formik>
      </div>
    </>
  );
}export default SettingsPage;
```

我们有两个表单:一个用于设置用户密码，另一个用于保存 GitHub 凭证。我们在单独的 Yup 模式中验证两者，如果输入的数据有效，就向后端发出请求。

接下来，我们通过在`src`文件夹中创建`SignUpPage.js`来创建注册页面。我们补充:

```
import React, { useState, useEffect } from "react";
import { Formik } from "formik";
import Form from "react-bootstrap/Form";
import Col from "react-bootstrap/Col";
import Button from "react-bootstrap/Button";
import { Redirect } from "react-router-dom";
import * as yup from "yup";
import { signUp } from "./requests";
import Navbar from "react-bootstrap/Navbar";const schema = yup.object({
  username: yup.string().required("Username is required"),
  password: yup.string().required("Password is required")
});function SignUpPage() {
  const [redirect, setRedirect] = useState(false); const handleSubmit = async evt => {
    const isValid = await schema.validate(evt);
    if (!isValid) {
      return;
    }
    try {
      await signUp(evt);
      setRedirect(true);
    } catch (ex) {
      alert("Username already taken");
    }
  }; if (redirect) {
    return <Redirect to="/" />;
  } return (
    <>
      <Navbar bg="primary" expand="lg" variant="dark">
        <Navbar.Brand href="#home">GitHub App</Navbar.Brand>
      </Navbar>
      <div className="page">
        <h1 className="text-center">Sign Up</h1>
        <Formik validationSchema={schema} onSubmit={handleSubmit}>
          {({
            handleSubmit,
            handleChange,
            handleBlur,
            values,
            touched,
            isInvalid,
            errors
          }) => (
            <Form noValidate onSubmit={handleSubmit}>
              <Form.Row>
                <Form.Group as={Col} md="12" controlId="username">
                  <Form.Label>Username</Form.Label>
                  <Form.Control
                    type="text"
                    name="username"
                    placeholder="Username"
                    value={values.username || ""}
                    onChange={handleChange}
                    isInvalid={touched.username && errors.username}
                  />
                  <Form.Control.Feedback type="invalid">
                    {errors.username}
                  </Form.Control.Feedback>
                </Form.Group>
                <Form.Group as={Col} md="12" controlId="password">
                  <Form.Label>Password</Form.Label>
                  <Form.Control
                    type="password"
                    name="password"
                    placeholder="Password"
                    value={values.password || ""}
                    onChange={handleChange}
                    isInvalid={touched.password && errors.password}
                  /> <Form.Control.Feedback type="invalid">
                    {errors.password}
                  </Form.Control.Feedback>
                </Form.Group>
              </Form.Row>
              <Button type="submit" style={{ marginRight: "10px" }}>
                Sign Up
              </Button>
            </Form>
          )}
        </Formik>
      </div>
    </>
  );
}export default SignUpPage;
```

它类似于其他的登录请求，除了我们调用注册请求而不是登录。

最后，在`index.html`中，我们将现有代码替换为:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="theme-color" content="#000000" />
    <meta
      name="description"
      content="Web site created using create-react-app"
    />
    <link rel="apple-touch-icon" href="logo192.png" />
    <!--
      manifest.json provides metadata used when your web app is installed on a
      user's mobile device or desktop. See [https://developers.google.com/web/fundamentals/web-app-manifest/](https://developers.google.com/web/fundamentals/web-app-manifest/)
    -->
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
    <!--
      Notice the use of %PUBLIC_URL% in the tags above.
      It will be replaced with the URL of the `public` folder during the build.
      Only files inside the `public` folder can be referenced from the HTML.Unlike "/favicon.ico" or "favicon.ico", "%PUBLIC_URL%/favicon.ico" will
      work correctly both with client-side routing and a non-root public URL.
      Learn how to configure a non-root public URL by running `npm run build`.
    -->
    <title>GitHub App</title>
    <link
      rel="stylesheet"
      href="[https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css](https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css)"
      integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T"
      crossorigin="anonymous"
    />
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="root"></div>
    <!--
      This HTML file is a template.
      If you open it directly in the browser, you will see an empty page.You can add webfonts, meta tags, or analytics to this file.
      The build step will place the bundled scripts into the <body> tag.To begin the development, run `npm start` or `yarn start`.
      To create a production bundle, use `npm run build` or `yarn build`.
    -->
  </body>
</html>
```

这增加了引导 CSS 并改变了`<title />`。

写完所有代码后，我们就可以运行我们的应用程序了。首先，通过运行`backend`文件夹中的`npm start`和`frontend`文件夹中的`npm start`来运行后端，然后如果要求您从不同的端口运行它，请选择“是”。

然后你会得到:

![](img/ae03cc6393f1b36252cda4b78cd7e796.png)![](img/009c7c0f2eab837ca666f9709cdd4277.png)![](img/79d94def90aa4444adfcbf5ace89dacd.png)![](img/a00e63a53474167e4700f91be5c9fab0.png)![](img/77e04302daea6cf4a85613b7cb76aed4.png)
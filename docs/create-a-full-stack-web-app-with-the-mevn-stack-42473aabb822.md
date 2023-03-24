# 使用 MEVN 堆栈创建全堆栈 Web 应用程序

> 原文：<https://levelup.gitconnected.com/create-a-full-stack-web-app-with-the-mevn-stack-42473aabb822>

![](img/ff228ea99c37a169990f8da1b6c0d099.png)

维克多·塔拉舒克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

MEVN 堆栈是一套用于全堆栈应用的技术。MEVN 代表 MongoDB、Express、Vue.js 和 Node.js。在本文中，我们将了解如何使用 MEVN 堆栈创建一个简单的带有身份验证的 todo 应用程序。

# 设置项目

创建全栈 MEVN 应用的第一步是设置项目。

首先，我们创建一个项目文件夹，然后在其中添加`backend`和`frontend`文件夹。

`backend`有快递 app。

`frontend`有 Vue.js app。我们会用 Vue 3 做前端。

接下来，为了创建我们的 Express 应用程序，我们运行 Express 生成器来创建文件。

为此，进入`backend`并运行:

```
npx express-generator
```

我们可能需要管理员权限来做到这一点。

接下来，我们进入`frontend`文件夹。

我们通过运行以下命令来安装最新版本的 Vue CLI:

```
npm install -g @vue/cli
```

Vue CLI 4.5 或更高版本可以创建 Vue 3 项目。

然后在`frontend`文件夹中，我们运行:

```
vue create .
```

然后我们选择“Vue 3(默认)”选项。

接下来，我们必须安装软件包。

我们停留在`frontend`文件夹中并运行:

```
npm i axios vue-router@4.0.0-beta.12
```

安装 Axios HTTP 客户端和与 Vue 3 兼容的 Vue Router 4.x。

在`backend`文件夹中，我们运行:

```
npm i cors momgoose jsonwebtoken
```

安装 CORS 和 Mongoose 包以支持跨域通信并使用 MongoDB。

`jsonwebtoken`让我们将 JSON web 令牌认证添加到我们的应用程序中。

# 创建后端

现在我们已经准备好创建后端应用程序来使用 MongoDB 并添加身份验证。

首先，我们在后端文件夹中创建`db.js`,并添加:

```
const { Schema, createConnection } = require('mongoose');
const connection = createConnection('mongodb://localhost:27017/mevn-example', { useNewUrlParser: true });const userSchema = new Schema({
  name: String,
  password: String
});const User = connection.model('User', userSchema);const todoSchema = new Schema({
  name: String,
  done: Boolean,
  user: { type: Schema.Types.ObjectId, ref: 'User' },
});const Todo = connection.model('Todo', todoSchema);module.exports = {
  User,
  Todo
}
```

我们用 Mongoose 连接到 MongoDB 数据库并创建模型。

为了创建模型，我们使用`Schema`构造函数来创建模式。

我们添加属性和它们的数据类型。

`user`属性引用来自`User`模型的对象 ID，以便我们可以将 todo 条目链接到用户。

然后我们调用`connection.model`来使用模式创建模型。

然后，我们在`backend`文件夹中创建`constants.js`，并写入:

```
module.exports = {
  SECRET: 'secret'
}
```

我们将 JSON web 令牌的秘密放在这个文件中，并在路由文件中使用它们。

接下来，我们进入`routes`文件夹，为 API 路由添加文件。

我们在`routes`文件夹中创建`todos.js`，并写道:

```
var express = require('express');
var router = express.Router();
const { Todo } = require('../db');
const jwt = require('jsonwebtoken');
const { SECRET } = require('../constants');const verifyToken = (req, res, next) => {
  try {
    req.user = jwt.verify(req.headers.authorization, SECRET);
    return next();
  } catch (err) {
    console.log(err)
    return res.status(401);
  }
}router.get('/', verifyToken, async (req, res) => {
  const { _id } = req.user;
  const todos = await Todo.find({ user: _id })
  res.json(todos);
});router.get('/:id', verifyToken, async (req, res) => {
  const { _id } = req.user;
  const { id } = req.params;
  const todo = await Todo.findOne({ _id: id, user: _id })
  res.json(todo);
});router.post('/', verifyToken, async (req, res) => {
  const { name } = req.body;
  const { _id } = req.user;
  const todo = new Todo({ name, done: false, user: _id })
  await todo.save()
  res.json(todo);
});router.put('/:id', verifyToken, async (req, res) => {
  const { name, done } = req.body;
  const { id } = req.params;
  const todo = await Todo.findOneAndUpdate({ _id: id }, { name, done })
  await todo.save();
  res.json(todo);
});router.delete('/:id', verifyToken, async (req, res) => {
  const { id } = req.params;
  await Todo.deleteOne({ _id: id })
  res.status(200).send();
});module.exports = router;
```

`verifyToken`中间件有`jwt.verify`方法用我们的`SECRET`密钥验证令牌。

如果有效，我们将`req.user`设置为解码后的令牌。

然后我们调用`next`来调用路由器处理程序。

然后我们使用 GET `/`路由从`req.user`获取用户数据，然后调用`Todo.find`来查找用户的 todo 条目。

`Todo`是我们之前创建的模型。

GET `/:id`路由通过 ID 获取`Todo`条目。

`findOne`获取`todos`集合中的第一个结果。

`req.params`从 URL 参数中获取 URL 参数。

然后 POST `/` route 从`req.body`属性中获取`name`，该属性具有请求体 daya。

我们从`req.user`属性中获取`_id`属性来获取用户数据。

我们使用这两者来创建 todo 条目。

`user`拥有用户的对象 ID。

输入`/:id`路线用于更新用户。

我们从有请求体的`req.body`中得到`name`和`done`。

`req.params`具有`id`属性，该属性具有 todo 条目的 ID。

我们使用`id`来查找 todo 条目。

并更新`name`和`done`属性以更新条目。

然后我们调用`todo.save()`保存数据。

删除`/:id`路径允许我们通过 ID 删除 todo 条目。

我们用`_id`调用`deleteOne`来做这件事。

在我们运行路由处理程序之前，`verifyToken`将确保令牌有效。

`req.headers.authorization`有授权令牌。`req.headers`有 HTTP 请求头。

接下来，我们在`routes`文件夹中创建`users.js`，并写入:

```
var express = require('express');
var router = express.Router();
const bcrypt = require('bcrypt');
const { User } = require('../db');
const { SECRET } = require('../constants');
const jwt = require('jsonwebtoken');
const saltRounds = 10;router.post('/register', async (req, res) => {
  const { name, password } = req.body;
  const existingUser = await User.findOne({ name });
  if (existingUser) {
    return res.json({ err: 'user already exists' }).status(401);
  }
  const hashedPassword = await bcrypt.hash(password, saltRounds);
  const user = new User({
    name,
    password: hashedPassword
  })
  await user.save();
  res.json(user).status(201);
});router.post('/login', async (req, res) => {
  const { name, password } = req.body;
  const { _id, password: userPassword } = await User.findOne({ name });
  const match = await bcrypt.compare(password, userPassword);
  if (match) {
    const token = await jwt.sign({ name, _id }, SECRET);
    return res.json({ token });
  }
  res.status(401);
});module.exports = router;
```

它类似于`todos.js`。

我们有 POST `/register`路径从 JSON 请求体获取`name`和`password`属性。

然后，我们检查具有给定名称的用户是否存在。

然后我们得到`bcrypt.hash`来散列密码和秘密。

然后我们用`name`和`hashedPassword`保存用户。

POST `/login`路由从 JSON 请求体获得`name`和`password`。

我们使用`bcrypt.compare`方法来比较密码。

然后，我们创建令牌，如果验证成功，就在响应中返回它。

否则，我们发送 401 响应。

最后，我们将所有内容都放在`backend`文件夹的`app.js`中。

我们用以下内容替换现有内容:

```
var createError = require('http-errors');
var express = require('express');
var path = require('path');
var cookieParser = require('cookie-parser');
var logger = require('morgan');
var cors = require('cors')var todorouter = require('./routes/todo');
var usersRouter = require('./routes/users');var app = express();
app.use(cors())
// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'jade');app.use(logger('dev'));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));app.use('/todos', todorouter);
app.use('/users', usersRouter);// catch 404 and forward to error handler
app.use(function(req, res, next) {
  next(createError(404));
});// error handler
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};// render the error page
  res.status(err.status || 500);
  res.render('error');
});module.exports = app;
```

我们添加了`cors`中间件，这样我们就可以和前端通信了。

我们使用`todorouter`和`usersRouter`将路线添加到我们的应用程序中。

# Vue 3 前端

现在我们完成了后端，我们在前端工作。

首先，在`components`文件夹中，我们创建`TodoForm.vue`并写入:

```
<template>
  <div>
    <h1>{{ edit ? "Edit" : "Add" }} Todo</h1>
    <form [@submit](http://twitter.com/submit).prevent="submit">
      <div class="form-field">
        <label>Name</label>
        <br />
        <input v-model="form.name" />
      </div>
      <div>
        <label>Done</label>
        <input type="checkbox" v-model="form.done" />
      </div>
      <div>
        <input type="submit" value="Submit" />
      </div>
    </form>
  </div>
</template><script>
import axios from "axios";
import { APIURL } from "../constants";export default {
  name: "TodoForm",
  data() {
    return {
      form: { name: "", done: false },
    };
  },
  props: {
    edit: Boolean,
    id: String,
  },
  methods: {
    async submit() {
      const { name, done } = this.form;
      if (!name) {
        return alert("Name is required");
      }
      if (this.edit) {
        await axios.put(`${APIURL}/todos/${this.id}`, { name, done });
      } else {
        await axios.post(`${APIURL}/todos`, { name, done });
      }
      this.$router.push("/todos");
    },
    async getTodo() {
      const { data } = await axios.get(`${APIURL}/todos/${this.id}`);
      this.form = data;
    },
  },
  beforeMount() {
    if (this.edit) {
      this.getTodo();
    }
  },
};
</script>
```

我们有一个表单让我们添加或编辑待办事项条目。

它使用`edit`属性来指示我们是否正在编辑一个 todo。

我们使用`v-model`将输入绑定到无功属性。

然后我们检查`this.edit`值。

如果是`true`，我们用 todo 条目的`id`发出一个 PUT 请求。

否则，我们发出 POST 请求来创建一个新的 todo 条目。

在我们编辑的时候，`getTodo`方法让我们通过 ID 获得 todo。

接下来，在`src`文件夹中，我们创建`views`文件夹来添加路线组件。

我们创建`AddTodoForm.vue`文件并添加:

```
<template>
  <div>
    <TodoForm></TodoForm>
  </div>
</template><script>
import TodoForm from "@/components/TodoForm";export default {
  components: {
    TodoForm,
  },
};
</script>
```

我们注册了`TodoForm`组件，并将其呈现在模板中。

接下来，我们在`views`文件夹中创建`EditTodoForm.vue`并添加:

```
<template>
  <div>
    <TodoForm edit :id='$route.params.id'></TodoForm>
  </div>
</template><script>
import TodoForm from "@/components/TodoForm";export default {
  components: {
    TodoForm,
  },
};
</script>
```

我们将`edit`和`id`属性传递给`TodoForm`，这样它就可以通过 ID 获得 todo 条目。

`$route.params.id`具有 todo 条目的对象 ID。

接下来，我们在`views`文件夹中创建`Login.vue`文件来添加一个登录表单。

在这个文件中，我们添加了:

```
<template>
  <div>
    <h1>Login</h1>
    <form @submit.prevent="login">
      <div class="form-field">
        <label>Username</label>
        <br />
        <input v-model="form.name" type="text" />
      </div>
      <div class="form-field">
        <label>Password</label>
        <br />
        <input v-model="form.password" type="password" />
      </div>
      <div>
        <input type="submit" value="Log in" />
        <button type="button" @click="$router.push('/register')">
          Register
        </button>
      </div>
    </form>
  </div>
</template><script>
import axios from "axios";
import { APIURL } from "../constants";export default {
  data() {
    return {
      form: { name: "", password: "" },
    };
  },
  methods: {
    async login() {
      const { name, password } = this.form;
      if (!name || !password) {
        alert("Username and password are required");
      }
      try {
        const {
          data: { token },
        } = await axios.post(`${APIURL}/users/login`, {
          name,
          password,
        });
        localStorage.setItem("token", token);
        this.$router.push("/todos");
      } catch (error) {
        alert("Invalid username or password.");
      }
    },
  },
};
</script>
```

我们有一个表单，接受用户名和密码，让用户登录。

当我们提交表单时，会调用`login`方法。

在该方法中，我们检查`name`和`password`是否有值。

如果它们都不为空，我们继续发出`login`请求。

如果成功了，我们就走`/todos`路线。

否则，我们应该得到一个错误信息。

类似地，我们为注册表单创建`Register.vue`组件。

然后我们用下面的代码填充它:

```
<template>
  <div>
    <h1>Register</h1>
    <form @submit.prevent="register">
      <div class="form-field">
        <label>Username</label>
        <br />
        <input v-model="form.name" type="text" />
      </div>
      <div class="form-field">
        <label>Password</label>
        <br />
        <input v-model="form.password" type="password" />
      </div>
      <div>
        <input type="submit" value="Register" />
        <button type="button" @click="$router.push('/')">Login</button>
      </div>
    </form>
  </div>
</template><script>
import axios from "axios";
import { APIURL } from "../constants";export default {
  data() {
    return {
      form: { name: "", password: "" },
    };
  },
  methods: {
    async register() {
      const { name, password } = this.form;
      if (!name || !password) {
        alert("Username and password are required");
      }
      try {
        await axios.post(`${APIURL}/users/register`, {
          name,
          password,
        });
        alert("Registration successful");
      } catch (error) {
        alert("Registration failed.");
      }
    },
  },
};
</script>
```

我们以同样的方式获得用户名和密码。

如果它们都不为空，那么我们向`register`路线发出请求。

一旦成功，我们将显示一条成功消息。

否则，我们显示一条失败消息，该消息在`catch`块中。

接下来，我们有一个组件来显示待办事项。

我们在`views`文件夹中创建`Todo.vue`并写入:

```
<template>
  <div>
    <h1>Todos</h1>
    <button @click="$router.push(`/add-todo`)">Add Todo</button>
    <button @click="logOut">Logout</button>
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Done</th>
          <th>Edit</th>
          <th>Delete</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="t of todos" :key="t._id">
          <td>{{ t.name }}</td>
          <td>{{ t.done }}</td>
          <td>
            <button @click="$router.push(`/edit-todo/${t._id}`)">Edit</button>
          </td>
          <td>
            <button @click="deleteTodo(t._id)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template><script>
import axios from "axios";
import { APIURL } from "../constants";export default {
  data() {
    return {
      todos: [],
    };
  },
  methods: {
    async getTodos() {
      const { data: todos } = await axios.get(`${APIURL}/todos`);
      this.todos = todos;
    },
    async deleteTodo(id) {
      await axios.delete(`${APIURL}/todos/${id}`);
      this.getTodos();
    },
    logOut() {
      localStorage.clear();
      this.$router.push("/");
    },
  },
  beforeMount() {
    this.getTodos();
  },
};
</script><style scoped>
th:first-child,
td:first-child {
  width: 60%;
}th {
  text-align: left;
}
</style>
```

我们有 Add Todo 按钮，当我们点击它时会转到`add-todo`路线。

这将被映射到`AddTodoForm.vue`组件。

当注销按钮被点击时，它调用`logOut`方法。

它只是清除本地存储并将用户重定向到登录页面。

`getTodos`方法获取用户的 todo 条目。

从解码的令牌中确定用户身份。

指令呈现并显示这些项目。

“编辑”按钮转到编辑表单。

当我们点击删除按钮时，它会调用`deleteTodo`。

`deleteTodo`向`todos`路由发出删除请求。

我们在删除一个 todo 条目后或者在加载页面时调用`getTodos`。

接下来在`src/App.vue`中，我们添加我们的`router-view`来显示路线:

```
<template>
  <router-view></router-view>
</template><script>
export default {
  name: "App",
};
</script><style>
.form-field input {
  width: 100%;
}#app {
  margin: 0 auto;
  width: 70vw;
}
</style>
```

然后我们在`src`文件夹中创建`constants.js`并添加:

```
export const APIURL = 'http://localhost:3000'
```

让我们在任何地方都可以使用`APIURL`,这样我们就不必在发出请求时重复后端 API 的基本 URL。

最后，在`main.js`中，我们写道:

```
import { createApp } from 'vue'
import App from './App.vue'
import { createRouter, createWebHashHistory } from 'vue-router';
import Login from '@/views/Login';
import Todo from '@/views/Todo';
import Register from '@/views/Register';
import AddTodoForm from '@/views/AddTodoForm';
import EditTodoForm from '@/views/EditTodoForm';
import axios from 'axios';axios.interceptors.request.use((config) => {
  if (config.url.includes('login') || config.url.includes('register')) {
    return config;
  }
  return {
    ...config, headers: {
      Authorization: localStorage.getItem("token"),
    }
  }
}, (error) => {
  return Promise.reject(error);
});const beforeEnter = (to, from, next) => {
  const token = localStorage.getItem('token');
  if (token) {
    next()
    return true;
  }
  next({ path: '/' });
  return false
}const routes = [
  { path: '/', component: Login },
  { path: '/register', component: Register },
  { path: '/todos', component: Todo, beforeEnter },
  { path: '/add-todo', component: AddTodoForm, beforeEnter },
  { path: '/edit-todo/:id', component: EditTodoForm, beforeEnter },
]const router = createRouter({
  history: createWebHashHistory(),
  routes
})const app = createApp(App);
app.use(router);
app.mount('#app')
```

添加`vue-router`和 Axios 请求拦截器，为认证路由添加认证令牌。

`beforeEnter`是一个路由保护，允许我们限制对已认证路由的访问。

在将用户重定向到他们要去的页面之前，我们检查令牌是否存在。

否则，我们重定向到登录页面。

`routes`拥有所有的航线。`path`有路由 URL，而`component`有我们想要加载的组件。

`beforeEnter`是在路线加载之前加载的路线防护。

`createRouter`创建路由器对象。

让我们对 URL 使用散列模式。

因此，URL 在基本 URL 和其余 URL 段之间会有一个`#`符号。

然后我们调用`app.use(router)`来添加路由器，并使`this.$route`和`thuis.$router`属性在组件中可用。

现在我们可以从`frontend`文件夹和`node bin/www`中运行`npm run serve`来启动后端。

# 结论

我们可以毫不费力地用最新的 JavaScript 框架和特性创建一个简单的 MEVN stack 应用程序。
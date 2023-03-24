# 使用 V-Click-Outside 轻松添加弹出窗口和菜单

> 原文：<https://levelup.gitconnected.com/add-popups-and-menus-easily-with-v-click-outside-179c8e5c01fa>

![](img/3694f930b1200e9b968730e26096865c.png)

乔尔·霍兰德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

弹出窗口和菜单是 web 应用程序中的常见功能。它通常用于为用户提供一个地方，让他们在应用程序中添加功能。它的使用频率导致开发人员开发了一些库，供我们在应用程序中添加弹出窗口和菜单。像 Bootstrap 这样的 UI 库有内置菜单。对于定制菜单，我们可以通过创建一个 div 来添加一个，这个 div 带有一个切换菜单打开和关闭的按钮。

当用户在菜单和按钮之外单击时，菜单会关闭。对于 Vue.js 应用程序，我们有 V-Click-Outside 库来处理元素外部的点击。我们可以很容易地使用它来添加弹出窗口和菜单到我们的应用程序。

在本文中，我们将编写一个笔记应用程序，让用户做笔记。将有一个显示注释的表格，每行有一个弹出菜单，让用户单击按钮来编辑或删除注释。为了开始构建项目，我们通过运行以下命令来运行 Vue CLI:

```
npx @vue/cli create bookmark-app
```

当向导运行时，我们选择“手动选择功能”，并选择 Babel、CSS 预处理器、Vuex 和 Vue 路由器。

接下来，我们安装一些包。我们需要 Axios 向我们的后端发出 HTTP 请求，Bootstrap-Vue 用于样式化，Vee-Validate 用于表单验证，V-Click-Outside 用于处理输入的焦点状态。为了安装包，我们运行`npm i axios bootstrap-vue vee-validate v-click-outside`。安装完软件包后，我们就可以开始构建笔记应用程序了。

首先，我们创建表单让用户添加和编辑他们的账单。在`components`文件夹中，创建一个名为`NoteForm.vue`的文件，并添加:

```
<template>
  <ValidationObserver ref="observer" v-slot="{ invalid }">
    <b-form [@submit](http://twitter.com/submit).prevent="onSubmit" novalidate>
      <b-form-group>
        <ValidationProvider name="name" rules="required" v-slot="{ errors }">
          <label>Name</label>
          <b-form-input
            type="text"
            v-model="form.name"
            placeholder="Name"
            name="name"
            :state="errors.length == 0"
          ></b-form-input>
          <b-form-invalid-feedback :state="errors.length == 0">Name is requied.</b-form-invalid-feedback>
        </ValidationProvider>
      </b-form-group> <b-form-group>
        <ValidationProvider name="note" rules="required" v-slot="{ errors }">
          <label>Note</label>
          <b-form-textarea
            type="text"
            :state="errors.length == 0"
            v-model="form.note"
            required
            placeholder="Note"
            name="note"
            rows="5"
          ></b-form-textarea>
          <b-form-invalid-feedback :state="errors.length == 0">{{errors.join('. ')}}</b-form-invalid-feedback>
        </ValidationProvider>
      </b-form-group> <b-button type="submit" variant="primary" style="margin-right: 10px">Submit</b-button>
      <b-button type="reset" variant="danger" [@click](http://twitter.com/click)="cancel()">Cancel</b-button>
    </b-form>
  </ValidationObserver>
</template><script>
import { requestsMixin } from "@/mixins/requestsMixin";export default {
  name: "NoteForm",
  mixins: [requestsMixin],
  props: {
    note: Object,
    edit: Boolean
  },
  methods: {
    async onSubmit() {
      const isValid = await this.$refs.observer.validate();
      if (!isValid) {
        return;
      } if (this.edit) {
        await this.editNote(this.form);
      } else {
        await this.addNote(this.form);
      }
      const { data } = await this.getNotes();
      this.$store.commit("setNotes", data);
      this.$emit("saved");
    },
    cancel() {
      this.$emit("cancelled");
    }
  },
  data() {
    return {
      form: {}
    };
  },
  watch: {
    note: {
      handler(val) {
        this.form = JSON.parse(JSON.stringify(val || {}));
      },
      deep: true,
      immediate: true
    }
  }
};
</script>
```

该表单允许用户使用给定的关键字搜索菜肴，然后返回菜肴的配料列表，然后用户可以将它们添加到删除重复项的列表中。我们使用 Vee-Validate 来验证我们的输入。我们使用`ValidationObserver`组件来观察组件内部表单的有效性，使用`ValidationProvider`来检查组件内部输入值的有效性规则。在`ValidationProvider`中，我们为文本输入字段提供了 BootstrapVue 输入。在`b-form-input`组件中。我们还添加了 Vee-Validate 验证，以确保用户在提交之前填写了日期。我们在`rules`道具中制作了必需的`name`和`note` 字段，这样用户就必须输入它们来保存笔记。

我们通过运行`this.$refs.observer.validate()`来验证`onSubmit`函数中的值。如果解析为`true`，那么我们运行代码，通过调用`if`块中的函数来保存数据，然后我们调用`getNotes`来获取注释。这些功能来自我们将要添加的`requestsMixin`。通过调用`this.$store.commit`将获得的数据存储在我们的 Vuex 存储中。

在这个组件中，我们还有一个`watch`块来观察`note` 值，该值是从我们必须构建的 Vuex 存储中获得的。随着`note` 值的更新，我们获得了最新的配料列表，这样当我们将这些值复制到`this.form`时，用户可以编辑最新的列表。

接下来，我们创建一个`mixins`文件夹，并将`requestsMixin.js`添加到`mixins`文件夹中。在文件中，我们添加了:

```
const APIURL = "[http://localhost:3000](http://localhost:3000)";
const axios = require("axios");export const requestsMixin = {
  methods: {
    getNotes() {
      return axios.get(`${APIURL}/notes`);
    }, addNote(data) {
      return axios.post(`${APIURL}/notes`, data);
    }, editNote(data) {
      return axios.put(`${APIURL}/notes/${data.id}`, data);
    }, deleteNote(id) {
      return axios.delete(`${APIURL}/notes/${id}`);
    }
  }
};
```

这些是我们在组件中使用的函数，用于向后端发出 HTTP 请求以保存书签。

接下来在`Home.vue`中，将现有代码替换为:

```
<template>
  <div class="page">
    <h1 class="text-center">Note Taking App</h1>
    <b-button-toolbar>
      <b-button [@click](http://twitter.com/click)="openAddModal()">Add Note</b-button>
    </b-button-toolbar>
    <br />
    <b-table-simple responsive>
      <b-thead>
        <b-tr>
          <b-th>Name</b-th>
          <b-th>Note</b-th>
          <b-th></b-th>
        </b-tr>
      </b-thead>
      <b-tbody>
        <b-tr v-for="n in notes" :key="n.id">
          <b-td>{{n.name}}</b-td>
          <b-td>{{n.note}}</b-td>
          <b-td>
            <b-button [@click](http://twitter.com/click)="toggleMenu(n.id)" class="menu-button">Menu</b-button>
            <div class="dropdown" v-show="openMenu[n.id]" v-click-outside="onClickOutside">
              <b-list-group>
                <b-list-group-item [@click](http://twitter.com/click)="openEditModal(n)">Edit</b-list-group-item>
                <b-list-group-item [@click](http://twitter.com/click)="deleteOneNote(n.id)">Delete</b-list-group-item>
              </b-list-group>
            </div>
          </b-td>
        </b-tr>
      </b-tbody>
    </b-table-simple> <b-modal id="add-modal" title="Add Note" hide-footer>
      <NoteForm [@saved](http://twitter.com/saved)="closeModal()" [@cancelled](http://twitter.com/cancelled)="closeModal()" :edit="false"></NoteForm>
    </b-modal> <b-modal id="edit-modal" title="Edit Note" hide-footer>
      <NoteForm [@saved](http://twitter.com/saved)="closeModal()" [@cancelled](http://twitter.com/cancelled)="closeModal()" :edit="true" :note="selectedNote"></NoteForm>
    </b-modal>
  </div>
</template><script>
// @ is an alias to /src
import NoteForm from "@/components/NoteForm.vue";
import { requestsMixin } from "@/mixins/requestsMixin";export default {
  name: "home",
  components: {
    NoteForm
  },
  mixins: [requestsMixin],
  computed: {
    notes() {
      return this.$store.state.notes;
    }
  },
  beforeMount() {
    this.getAllNotes();
  },
  data() {
    return {
      selectedNote: {},
      openMenu: {}
    };
  },
  methods: {
    toggleMenu(id) {
      this.$set(this.openMenu, id, !this.openMenu[id]);
    },
    onClickOutside(event, el) {
      if (!event.target.className.includes("menu-button")) {
        this.openMenu = {};
      }
    },
    openAddModal() {
      this.$bvModal.show("add-modal");
    }, openEditModal(note) {
      this.$bvModal.show("edit-modal");
      this.selectedNote = note;
    }, closeModal() {
      this.$bvModal.hide("add-modal");
      this.$bvModal.hide("edit-modal");
      this.selectedNote = {};
      this.getAllNotes();
    }, async deleteOneNote(id) {
      await this.deleteNote(id);
      this.getAllNotes();
    }, async getAllNotes() {
      const { data } = await this.getNotes();
      this.$store.commit("setNotes", data);
    }
  }
};
</script><style lang="scss" scoped>
.dropdown {
  position: absolute;
  max-width: 100px;
}.list-group-item {
  cursor: pointer;
}
</style>
```

这是我们在 BootstrapVue 表中显示账单的地方。这些列是名称、金额和截止日期，以及打开编辑模式的编辑按钮和单击时删除条目的删除按钮。我们还添加了一个“添加账单”按钮来打开模式，让用户添加账单。通过运行将数据存储在我们的 Vuex 存储中的`beforeMount`钩子中的`this.getAllNotes`函数，可以从后端获得注释。

在表格中，我们将注释显示在表格行中。在最右边的一栏，我们有从头开始构建的菜单。我们为每一行添加了一个菜单按钮，并在其下方添加了一个`div`作为列表组的容器，其中包含我们的编辑和删除项，供用户分别点击来编辑和删除注释。当用户点击菜单按钮时，我们用`toggleMenu`功能切换菜单。注意，我们需要调用`this.$set`函数来强制 Vue.js 刷新，因为我们正在修改一个对象中的条目。Vue 不能自动检测对象内的变化。有关该功能的更多详情，请参见[https://vuejs.org/v2/api/#Vue-set](https://vuejs.org/v2/api/#Vue-set)。

在`styles`部分，我们通过用`absolute`位置设置`dropdown`类并将其`max-width`设置为 100px 来设计菜单弹出。`absolute`位置将使它堆叠在我们的表的顶部，就在每一行按钮的下面。

`openAddModal`、`openEditModal`、`closeModal`分别打开打开和关闭模态，关闭模态。当调用`openEditModal`时，我们设置`this.selectedNote`变量，这样我们可以将它传递给我们的`NoteForm`。

接下来在`App.vue`中，我们将现有代码替换为:

```
<template>
  <div id="app">
    <b-navbar toggleable="lg" type="dark" variant="info">
      <b-navbar-brand to="/">Note Taking App</b-navbar-brand><b-navbar-toggle target="nav-collapse"></b-navbar-toggle><b-collapse id="nav-collapse" is-nav>
        <b-navbar-nav>
          <b-nav-item to="/" :active="path  == '/'">Home</b-nav-item>
        </b-navbar-nav>
      </b-collapse>
    </b-navbar>
    <router-view />
  </div>
</template><script>
export default {
  data() {
    return {
      path: this.$route && this.$route.path
    };
  },
  watch: {
    $route(route) {
      this.path = route.path;
    }
  }
};
</script><style lang="scss">
.page {
  padding: 20px;
}button,
.btn.btn-primary {
  margin-right: 10px !important;
}.button-toolbar {
  margin-bottom: 10px;
}
</style>
```

在页面顶部添加一个引导导航条，并添加一个`router-view`来显示我们定义的路线。此`style`部分没有限定范围，因此样式将全局应用。在`.page`选择器中，我们给页面添加一些填充。我们在剩余的`style`代码中给按钮添加一些填充。

然后在`main.js`中，将现有代码替换为:

```
import Vue from "vue";
import App from "./App.vue";
import router from "./router";
import store from "./store";
import BootstrapVue from "bootstrap-vue";
import { ValidationProvider, extend, ValidationObserver } from "vee-validate";
import { required } from "vee-validate/dist/rules";
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";
import vClickOutside from "v-click-outside";Vue.use(BootstrapVue);
Vue.use(vClickOutside);
Vue.component("ValidationProvider", ValidationProvider);
Vue.component("ValidationObserver", ValidationObserver);
extend("required", required);Vue.config.productionTip = false;new Vue({
  router,
  store,
  render: h => h(App)
}).$mount("#app");
```

我们在这里添加了我们需要的所有库，包括 BootstrapVue JavaScript 和 CSS，以及 Vee-Validate 组件和`required` 验证规则。我们还在这里包含了我们的 V-Click-Outside 库，所以我们可以在任何组件中使用它。

在`router.js`中，我们将现有代码替换为:

```
import Vue from "vue";
import Router from "vue-router";
import Home from "./views/Home.vue";Vue.use(Router);export default new Router({
  mode: "history",
  base: process.env.BASE_URL,
  routes: [
    {
      path: "/",
      name: "home",
      component: Home
    }
  ]
});
```

将主页包含在我们的路线中，以便用户可以看到该页面。

在`store.js`中，我们将现有代码替换为:

```
import Vue from "vue";
import Vuex from "vuex";Vue.use(Vuex);export default new Vuex.Store({
  state: {
    notes: []
  },
  mutations: {
    setNotes(state, payload) {
      state.notes = payload;
    }
  },
  actions: {}
});
```

将我们的`notes`状态添加到存储中，这样我们就可以在`NoteForm` 和`HomePage`组件的`computed`块中观察到它。我们有`setNotes` 函数来更新`notes` 状态，我们通过调用`this.$store.commit(“setNotes”, data);`在组件中使用它，就像我们在`NoteForm`和`HomePage`中所做的那样。

最后，在`index.html`中，我们将现有代码替换为:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width,initial-scale=1.0" />
    <link rel="icon" href="<%= BASE_URL %>favicon.ico" />
    <title>Note Taking App</title>
  </head>
  <body>
    <noscript>
      <strong
        >We're sorry but v-click-outside-tutorial-app doesn't work properly
        without JavaScript enabled. Please enable it to continue.</strong
      >
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

更改我们应用程序的标题。

经过所有的努力，我们可以通过运行`npm run serve`来启动我们的应用程序。

为了启动后端，我们首先通过运行`npm i json-server`来安装`json-server`包。然后，转到我们的项目文件夹并运行:

```
json-server --watch db.json
```

在`db.json`中，将文本改为:

```
{
  "notes": []
}
```

所以我们有了定义在可用的`requests.js`中的`notes` 端点。

经过所有的努力，我们得到了:

![](img/ac607cc22208bcbfd91fd14e61a36067.png)![](img/7b522ffeba53431c4dc82a6025d62625.png)![](img/c94d125bebf2cf1484e271e7cafbf1a8.png)
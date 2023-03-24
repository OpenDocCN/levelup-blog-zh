# 在 Vue 应用 Vuefire 中使用 Firebase 引用和查询

> 原文：<https://levelup.gitconnected.com/using-firebase-in-a-vue-app-vuefire-references-and-querying-d32650e7eb57>

![](img/7e7849e7985e99129304d6fd9f4e0e63.png)

由[巴德·汉森](https://unsplash.com/@ibaard?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Vuefire 库允许我们直接从我们的 Vue 应用程序添加 Firebase 数据库操作功能。在本文中，我们将了解如何使用 Vuefire 在我们的 Vue 应用程序中添加对云 Firestore 数据库操作的支持。

# 参考其他文件

我们可以通过引用其他文档来存储对它们的引用。这只适用于云 Firestore。

例如，我们可以写:

`App.vue`

```
<template>
  <div>
    <div v-for="c of cities" :key="c.id">{{c}}</div>
  </div>
</template>
<script>
import { db } from "./db";
const books = db.collection("books");
export default {
  data() {
    return {
      cities: []
    };
  },
  firestore: {
    cities: db.collection("cities")
  },
  async mounted() {
    await db.collection("cities").add({
      name: "London",
      books: [books.doc("1")]
    });
  }
};
</script>
```

`db.js`

```
import firebase from "firebase/app";
import "firebase/firestore";
export const db = firebase
  .initializeApp({ projectId: "project-id" })
  .firestore();
const { Timestamp, GeoPoint } = firebase.firestore;
export { Timestamp, GeoPoint };
```

我们只是在数组中引用我们想要的文档来引用它们。

然后，无论我们在哪里引用该文档，我们都可以看到它。

# 取消绑定或取消订阅更改

我们可以通过使用`this.$unbind`方法解除对集合的任何引用。

我们只需将集合名传递给方法:

```
this.$unbind('book')
```

默认情况下，Vuefire 将重置该属性。

所以如果我们有:

```
this.$unbind('book')
```

或者

```
this.$unbind('book', true)
```

然后`this.book`值将被重置为`null`。

如果我们有:

```
this.$unbind('book', false)
```

然后`this.book`会保持之前的值。

我们还可以通过传入一个返回我们想要重置的值的函数，将它重置为我们想要的值:

```
this.$unbind('book', () => ({ title: 'foo' }))
```

如果我们重置一个集合，那么它所绑定的值将被重置为一个空数组。

所以如果我们有:

```
this.$unbind('books')
```

如果`books`被绑定到`this.books`，那么`this.books`在被重置后将成为`[]`。

我们也可以在调用`this.$bind`时改变这个值:

```
await this.$bind('user', userRef)
this.$bind('user', otherUserRef, { reset: false })
```

他们让我们绑定到我们想要的值。

# 查询数据库

我们可以用 Vuefire 查询数据库。

例如，我们可以写:

```
<template>
  <div>
    <div v-for="b of books" :key="b.id">{{b}}</div>
  </div>
</template>
<script>
import { db } from "./db";
export default {
  data() {
    return {
      books: []
    };
  },
  mounted() {
    db.collection("books")
      .get()
      .then(querySnapshot => {
        const books = querySnapshot.docs.map(doc => doc.data());
        this.books = books;
      });
  }
};
</script>
```

我们调用了`db.collection`方法来获取集合。

`get`方法获取查询结果快照。

然后`docs.map`将快照映射到我们可以在组件中使用的数据。

在模板中，我们循环遍历条目。

我们还可以使用`doc`方法通过 ID 查询单个文档:

```
<template>
  <div>{{book}}</div>
</template>
<script>
import { db } from "./db";
export default {
  data() {
    return {
      book: {}
    };
  },
  mounted() {
    db.collection("books")
      .doc("UGRbRLiAzIl2efo1tmVP")
      .get()
      .then(snapshot => {
        const book = snapshot.data();
        this.book = book;
      });
  }
};
</script>
```

其中`UGRbRLiAzIl2efo1tmVP`是我们正在寻找的文档的 ID。

`snapshot.data()`返回文档的 JavaScript 对象。

# 结论

我们可以在应用程序中保存对其他文档的引用。

此外，我们可以手动取消订阅对集合的更改。

使用 Vuefire 查询数据库有几种方法。
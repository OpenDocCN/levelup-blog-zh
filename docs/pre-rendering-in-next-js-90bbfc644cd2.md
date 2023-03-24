# Next.js 中的预渲染

> 原文：<https://levelup.gitconnected.com/pre-rendering-in-next-js-90bbfc644cd2>

![](img/a0e707d43eb4a110d3f7d71601a09d52.png)

背景图片由 [Gradienta](https://unsplash.com/@gradienta?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/abstract-background?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 什么是预渲染？

预渲染意味着预先为每个页面生成 HTML，以及该页面所需的最少 javascript 代码。

页面加载到浏览器后，Javascript 代码被执行并使页面完全交互。这部分过程被称为**水合**。

# 为什么要预渲染？

1.  预渲染提高了性能

*   在典型的 react 应用程序中，您需要等待 javascript 代码加载到浏览器并执行。因此，渲染用户界面需要更多的时间
*   有了预先呈现的页面，HTML 就已经生成了，而且加载速度更快

2.预渲染有助于搜索引擎优化

*   如果你正在建立一个博客或电子商务网站，搜索引擎优化是一个问题。
*   如果您检查使用 CRA( `create-react-app`)创建的 web 应用程序的源代码，您只会在它的主体中看到一个 id 等于 root 的 div 标签。这是因为它的内容是使用客户端呈现来呈现的。如果搜索引擎点击这样的页面，搜索引擎将无法看到内容。正因为如此，一个典型的使用 CRA 创建的 react 应用程序对搜索引擎优化并不友好。
*   如果搜索引擎找到了一个使用 Next.js 创建的预呈现页面，所有内容都会出现在源代码中，这将有助于对该页面进行索引。

# **检查现有网站预渲染的测试**

让我们做一个小测试，检查给定的网站是否配置了预渲染。

*   这个页面([https://next-learn-starter.vercel.app/](https://next-learn-starter.vercel.app/))是一个使用 Next.js 构建的 web 应用程序。因此，它具有预渲染功能。从这里开始我们就叫它**舒的页面**吧。
*   这个页面([https://create-react-template.vercel.app/](https://create-react-template.vercel.app/))是一个使用 Create React App 构建的 web 应用程序。这意味着它不是为预渲染而配置的。为了便于参考，我们称它为 **CRA 页面**。

1.  首先，访问这两个页面，看看它们是否工作。
2.  现在，您必须在浏览器中禁用 javascript。选择您正在使用的浏览器的相关指南，禁用 javascript
    [Chrome/Brave 指南](https://developer.chrome.com/docs/devtools/javascript/disable/)
    [Firefox 指南](https://www.lifewire.com/disable-javascript-in-firefox-446039)
    [Safari 指南](https://www.lifewire.com/disable-javascript-in-safari-4103708)
3.  禁用 javascript 后，您必须使用同一个浏览器选项卡(您禁用了 javascript)再次访问这两个站点，以检查它们是否工作。
    你会注意到舒的页面运行良好，的页面显示一条消息，称*“你需要启用 JavaScript 来运行这个应用。”*您看到此消息是因为应用程序没有预渲染为静态 HTML。

*   在 Shu 的页面上，初始加载时会显示一个预渲染的 HTML。之后，javascript 代码被加载到浏览器中，然后应用程序变得具有交互性(这个过程被称为**水合)。**
*   在 CRA 页面上，初始加载期间什么都没有，因为 javascript 在初始加载期间没有被加载。一旦浏览器加载了 javascript 代码，整个页面就会呈现出来，变得具有交互性。

# 预渲染的形式

Next.js 中有两种形式的预渲染。

1.  静态生成
2.  服务器端渲染

我们可以配置 Next.js 应用程序的页面，以这两种形式中的任何一种进行预呈现。但是“静态生成”是 Next.js 团队推荐的形式。

# 静态生成

使用*静态生成*作为其预呈现形式的页面，**在构建期间生成其 HTML。** 在生产中，这个过程只有在你运行`next build`命令时才会发生。但是在开发模式下(当您运行`npm run dev`时)，页面会在每次请求时被预渲染。

使用这个过程生成的 HTML 可以在每个请求上重用，也可以被 CDN 缓存。

当没有数据需要从外部来源(外部 API，查询数据库等)获取时，静态生成将自动发生。)

但是如果你必须从外部来源获取一些数据，你将不得不使用`getStaticProps`函数来获取这些数据。

如果页面中的数据不经常更改(例如，博客帖子、营销页面、电子商务产品列表、帮助和文档等)，建议使用静态生成。)

下面是一个 Next.js 组件的代码，它从一个免费的 API 获取用户列表。这里我们使用了`getStaticProps`来获取数据。

```
// pages/sg/index.js

import styles from "../../styles/Home.module.css";
import Link from "next/link";

export default function Component({ users }) {
  return (
    <>
      <main className={styles.main}>
        <h1>Static Generation</h1>
        <div className={styles.description}>
          <ul>
            {users.map((user) => (
              <li key={user.email}><Link href={`/sg/${user.id}`}>{user.name}</Link></li>
            ))}
          </ul>
        </div>
      </main>
    </>
  );
}

// this function gets called during the build time
export async function getStaticProps() {
  // calling an external API to fetch data
  const res = await fetch("https://jsonplaceholder.typicode.com/users");
  const users = await res.json();

  // this returning data can be accessed from the component using the prop name
  return { props: { users } };
}
```

下面是该代码的输出。

![](img/b69150ef0cd3f8e8062766b1481b3e84.png)

静态生成代码的输出

本指南将为您提供更多关于`getStaticProps`的信息

如果当你点击上面用户列表中的一个名字时，你想路由到一个特定的页面(包含关于那个特定用户的更多细节)，你可以从一个动态页面调用`getStaticPaths()`函数。

```
// pages/sg/[id].js

import styles from "../../styles/Home.module.css";

const Post = ({ user }) => {
  console.log("user", user);

  return (
    <>
      <main className={styles.main}>
        <h1>Static Generation (Paths)</h1>
        <div className={styles.description}>Selected User : {user.name}</div>
      </main>
    </>
  );
};

// this function gets called during the build time
export async function getStaticPaths() {
  // calling an external API to fetch all data
  const res = await fetch("https://jsonplaceholder.typicode.com/users");
  const users = await res.json();

  const paths = users.map((item) => ({
    params: { id: item.id.toString() },
  }));

  //these paths will get pre-rendered only at build time.
  // { fallback: false } means other routes should 404.
  return { paths, fallback: false };
}

// This also gets called at build time
export async function getStaticProps({ params }) {
  // this fetch call has been made only to get the specific data
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/users/${params.id}`
  );
  const user = await res.json();

  return { props: { user } };
}

export default Post;
```

下面是上面代码的输出

![](img/26766fc2a15a38d2942c1c74731dbf8f.png)

Next.js 建议，如果你是使用动态路径的预渲染页面，并且数据来自下面列表中的一个来源，你应该使用`getStaticPaths`

*   无头 CMS
*   数据库
*   文件系统

使用本指南，你可以了解更多关于`getStaticPaths`的信息。

# 服务器端渲染

使用服务器端呈现作为其预呈现形式的页面，**为每个请求生成 HTML。**

如果您的页面显示经常更新的数据，那么服务器端呈现是用于预呈现的推荐形式。但是这将比静态生成慢得多。

如果您需要在您的应用程序中配置一个页面用于服务器端渲染，您必须包含`getServerSideProps`函数，该函数将在每次请求时被服务器调用。

```
import styles from "../../styles/Home.module.css";

export default function Component({ users }) {
  return (
    <>
      <main className={styles.main}>
        <h1>Server-side rendering</h1>
        <div className={styles.description}>
          <ul>
            {users.map((user) => (
              <li key={user.email}>{user.name}</li>
            ))}
          </ul>
        </div>
      </main>
    </>
  );
}

// this function gets called on each request
export async function getServerSideProps() {
  // calling an external API to fetch data
  const res = await fetch("https://jsonplaceholder.typicode.com/users");
  const users = await res.json();

  // this returning data can be accessed from the component using the prop name
  return { props: { users } };
}
```

跟随本指南[了解更多关于`getServerSideProps`的信息](https://nextjs.org/docs/basic-features/data-fetching/get-server-side-props)

# 参考

预渲染和数据获取(Next.js 学习指南)——[https://nextjs.org/learn/basics/data-fetching/pre-rendering](https://nextjs.org/learn/basics/data-fetching/pre-rendering)

Next.js 预渲染文档—[https://nextjs.org/docs/basic-features/pages](https://nextjs.org/docs/basic-features/pages)

我在本文中使用的 Github Repo—[https://github.com/sankharr/next_pre-render](https://github.com/sankharr/next_pre-render)
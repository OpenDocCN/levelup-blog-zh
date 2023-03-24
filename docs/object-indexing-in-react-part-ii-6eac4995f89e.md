# React 第二部分中的对象索引

> 原文：<https://levelup.gitconnected.com/object-indexing-in-react-part-ii-6eac4995f89e>

## 如何停止过度使用数组函数的另一个例子

![](img/0c9fa03188d7a4c4b9e80d59c05ea72f.png)

照片由[法尔扎德·纳兹菲](https://unsplash.com/@euwars?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在[之前的一篇文章](/object-indexing-in-react-269295e1eae2)中，我提供了一个对象索引的潜在用例，以避免在 react 应用程序中过度使用数组函数。虽然它确实说明了对象索引的适用性， [Vachev T](https://medium.com/@vachev.t) 指出它可能不是最好的例子，因为有更有效的替代方案。

> 这个想法很好，我只是不明白为什么我们必须在 useEffect 中重新映射对象，并创建新的索引对象，为什么我们不简单地使用原始数组，当在渲染函数中映射项目列表时，我们只使用 map 的索引参数？它已经存在了… items.map((item，index，array)=> { … })

这是一个伟大的观点，它促使我想出一个更好的用例。新的用例不能简单地使用当前条目的索引，但可以通过对象索引进行极大的优化。

假设您有一个由组和用户组成的应用程序。应用程序呈现一个组列表，其中包含它们的元数据(名称、是否私有等。).群组对象的属性之一是`users`。该属性指向属于该组中每个用户对象的用户 id 列表。见下文。

```
var users = [
  {
   id: 43562, 
   username: 'mattman', 
   email: 'mattman@mattman.com', 
   img: 'path/to/profile-pics/43562'
  }, 
  {
   id: 10453, 
   username: 'chester_cheetuh', 
   email: 'thuh_cheetuh@chester.com', 
   img: 'path/to/profile-pics/10453'
  }
]var groups = [
  {
   id: 234, 
   name: 'WestCoastDoges', 
   isPrivate: false,
   users: [43562, 10453]
  }, 
  {
   id: 738, 
   name: 'EastCoastFloofs', 
   isPrivate: true,
   users: [10453]
  }
]
```

在我们的应用程序中，我们可以编写`groups.map(group=><Group group={group}/>)`来呈现我们的组列表。这很容易，但是如果我们想为每个组呈现一个`user`组件列表(包含用户名、个人资料图片等)呢？)来代表用户那个组？

我们可以将`users`作为道具传递给每个`Group`组件，然后在该组件中，当用户被映射时，执行`props.users.find(user=>user.id === groupUser)`，然后为该用户呈现数据。这是可行的，但是这篇文章的主旨是向你展示为什么使用对象索引是一个更有效的选择。在开始应用索引之前，让我们简单回顾一下大 O 符号的概念。

> “大 O 符号是计算机科学家分析算法成本的最基本工具之一”[黄慎](https://www.freecodecamp.org/news/author/shen/)

简而言之，[大 O](https://www.freecodecamp.org/news/big-o-notation-why-it-matters-and-why-it-doesnt-1674cfa8a23c/) 的概念是，算法的成本是以运行一个与所提供的数据集大小相关的进程所花费的时间来衡量的。这被称为*时间复杂度*。例如，JavaScript 中的一个`find`数组函数的时间复杂度可以表示为 O(n)。这意味着处理时间相对于数据集的增加是线性的(100 项=== 100 次迭代，200 项=== 200 次迭代，等等。).

如果我们要执行一个`find`函数，当我们在`groups`的每次迭代中迭代`users`时，我们正在执行一个`find`，在一个循环中，在**另一个**循环中。相对而言，简单地渲染我们的组和他们的用户，我们的执行速度是 O(n ),这很糟糕。这是一个**指数**关系。迭代次数等于组数(渲染每个组)、乘以每个组中的用户数(渲染组中的每个用户)、**乘以总用户数(为每个用户执行一次查找)。**

![](img/7799f48f5d2b6305833b5f5474540fe1.png)

Ew 大卫

对象索引至少可以帮助我们避免这些循环中的一个，即`find`。这是我们能做的。在`GroupsContainer`中，我们可以创建一个映射，其中键是用户 id，值是用户对象。见下文。

```
const [userMap, setUserMap] = useState({})useEffect(()=>{ 
  var newUserMap = {}
  users.forEach(user=>userMap[user.id] = user)
  setUserMap(newUserMap)
})
```

现在我们有了更新的`userMap`，我们可以在渲染时将它作为道具传递给我们的`Group`组件，而不是所有的`users`。

```
return (<>
  <h1>Groups</h1>
  {groups.map(group => {
     return <Group group={group} **userMap={userMap}**/> 
  })}
</>)
```

现在我们的`Group`组件已经可以访问`userMap`，我们可以在迭代组用户时使用它。参见下面的`Group`组件。

```
import React from 'react'const Group = ({group, userMap}) =>{return (<div>
   <h3>{group.name}</h3>
   {group.users.map(user=>{
     return <User user={userMap[user]}/>
   })}</div>)
}export default Group;
```

现在，我们可以避免使用昂贵的数组函数，只需在当前迭代中从用户 id 的关键字处的`userMap`中获取值，就可以找到我们的用户对象。现在，当我们的应用程序加载时，我们只需在`useEffect`中执行一次循环，而不必在`group.users`的每次迭代中搜索整个用户列表。

这篇文章的所有代码都可以在 GitHub 上找到。

在此 *将你的免费中级会员升级为付费会员，每月只需 5 美元，你就可以获得数以千计作家的无限量无广告故事。这是一个附属链接，你的会员资格的一部分帮助我为我创造的内容获得奖励。谢谢大家！*

# 参考

[](/object-indexing-in-react-269295e1eae2) [## React 中的对象索引

### 停止过度使用数组函数

levelup.gitconnected.com](/object-indexing-in-react-269295e1eae2)  [## 瓦切夫 T 培养基

### 在介质上阅读瓦切夫 T 的作品。每天，瓦切夫 T 和成千上万的其他声音阅读，写作，并分享…

medium.com](https://medium.com/@vachev.t) [](https://www.freecodecamp.org/news/big-o-notation-why-it-matters-and-why-it-doesnt-1674cfa8a23c/) [## 大 O 符号解释的是什么:空间和时间复杂性

### 你真的了解大 O 吗？如果是这样，那么这将在面试前刷新你的理解。如果没有，就不要…

www.freecodecamp.org](https://www.freecodecamp.org/news/big-o-notation-why-it-matters-and-why-it-doesnt-1674cfa8a23c/) [](https://github.com/macro6461/medium-object-indexing-part-II) [## macro 6461/medium-对象索引-第二部分

### 在 GitHub 上创建一个帐户，为 macro 6461/medium-object-indexing-part-II 开发做出贡献。

github.com](https://github.com/macro6461/medium-object-indexing-part-II)
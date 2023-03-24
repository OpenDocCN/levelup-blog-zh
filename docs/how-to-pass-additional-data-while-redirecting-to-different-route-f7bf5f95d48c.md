# 在 React 中重定向到路由时如何传递附加数据

> 原文：<https://levelup.gitconnected.com/how-to-pass-additional-data-while-redirecting-to-different-route-f7bf5f95d48c>

![](img/750e741dc664f78b3dc7091f52913dff.png)

照片由[爆裂](https://unsplash.com/@burst?utm_source=medium&utm_medium=referral)上[未飞溅](https://unsplash.com?utm_source=medium&utm_medium=referral)

在本文中，我们将看到如何在重定向到不同的路由时传递额外的数据。

所以让我们开始吧。

## **使用链接**

通常我们使用来自`react-router-dom`的`Link`组件，如下所示:

```
<Link to="/register">Register</Link>
```

所以当我们点击`Register`链接时，它会重定向到`/register`路由，但是`Link`也允许我们在重定向时传递额外的数据。

```
<Link to={{ 
 pathname: "/register", 
 state: data_you_need_to_pass 
}}>
 Register
</Link>
```

这里，在`data_you_need_to_pass`处，我们可以通过一个`string`或`object`、`array`等。在`/register`路线中，我们将在`props.location.state`中获得该数据。

除了将变量命名为 state 之外，我们可以使用任何名称，但通常称为 state。

## **演示**

## **用史书推**

通常，要使用 push 方法进行重定向，我们像这样使用它:

```
props.history.push('/register');
```

如果您需要在发送数据之前做一些处理，比如删除一些值或者修剪值，我们可以在提交按钮点击时调用一个处理程序并传递数据，如下所示

```
props.history.push({ 
 pathname: '/register',
 state: data_you_need_to_pass
});
```

## **演示**

今天到此为止。我希望你学到了新东西。

不要忘记订阅我的每周简讯，里面有惊人的技巧、窍门和文章，直接在这里的收件箱里。

# 分级编码

感谢您成为我们社区的一员！升级正在改变技术招聘。在最好的公司找到你最理想的工作。

[](https://jobs.levelup.dev/talent) [## 提升——改变招聘流程

### 🔥让软件工程师找到他们热爱的完美角色🧠寻找人才是最痛苦的部分…

作业. levelup.dev](https://jobs.levelup.dev/talent)
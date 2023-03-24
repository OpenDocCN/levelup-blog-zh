# 酏剂中的意外列表

> 原文：<https://levelup.gitconnected.com/unexpected-lists-in-elixir-f5b6629e89d3>

## 嗯——当时对我来说是意想不到的，尽管是语言的一部分。

![](img/6f2c45b773b100f7f68773889f892052.png)

由[马库斯·温克勒](https://unsplash.com/@markuswinkler?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我一直在 [Phoenix](https://www.phoenixframework.org/) 中整理一些东西，其中一个页面一直抛出一个它找不到`item_path/3`的错误。这很奇怪，因为模板中的生成代码有四个参数。

同样的事情发生在我闯入 IEx 去戳它的时候:

```
iex(1)> Routes.page_path(conn, :index, user_id: user.id, item_id: item.id)
** (UndefinedFunctionError) function AppWeb.Router.Helpers.item_path/3 is undefined or private. Did you mean one of: * item_path/4
 * item_path/5
```

熟悉长生不老药的人已经能发现问题所在…

在[药剂](https://elixir-lang.org/)中，`key: value, key: value`形成了所谓的关键字列表。我没有想到的是，最后两个参数会被自动转换成一个关键字列表:

```
iex(2)> i one: 1, two: 2
Term
  [one: 1, two: 2]
Data type
  List
```

去掉关键词，一切都很好。

```
iex(3)> Routes.item_path(conn, :index, user.id, item.id)
"/users/1/item/2"
```

至少等到下一个 bug。:-)最近写 JavaScript 和复习 Python 的时间太多了我觉得！

*原载于*[*https://www.solarwinter.net*](https://www.solarwinter.net/unexpected-list/)
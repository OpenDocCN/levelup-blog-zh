# Go 模块的更换

> 原文：<https://levelup.gitconnected.com/go-modules-replace-3ea6d0139c91>

![](img/a6ada2756911490411b21bd94e286504.png)

由 [SK Yeong](https://unsplash.com/@yskeong?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我相信及时更新软件依赖关系是正确的。我从来不喜欢*如果它没坏，就不要更新它*的方法。我以前写过关于这个的文章。对于我定期运行的 Go 项目:

```
$ go get -u the/module/name
```

使用当前项目的/module/name*来更新项目中的依赖关系。通常，这只是与 *go.mod* 文件相冲突，并做正确的事情，将所有模块及其依赖项更新到最新最好的。有时，当您运行这个时，如果一个依赖项引入了一个突破性的变化，您的代码将不会构建，或者您的测试会失败。此时，你只需*做必要的事情*，修改你的代码，就大功告成了。*

然而，偶尔您会遇到以下情况。你依赖模块*一个*，后者依赖模块 *X* 。你也依赖模块*两个*，那个**也**依赖模块 *X (* 模块 X 被称为[传递依赖](https://en.wikipedia.org/wiki/Transitive_dependency))。在这个场景中，X 引入了一个突破性的变化，模块一对此做出了响应，但是模块二忽略了它。你被夹在中间，你想要最新的东西，但最新的想要最新的 X，但最新的两个想要旧的，不兼容的 X。

你是做什么的？好吧，你可以等待，希望两个赶上来，也许礼貌地要求他们这样做，或者你可以尝试自己解决问题。

## 好公民解决方案

你可以做一个好公民，做到以下几点:

1.  叉回购二
2.  运行*go-u 两个*处理突发变化。
3.  将更新提交到您的 fork。
4.  在两点钟向你的叉子提交一个拉请求
5.  请他们接受更新
6.  等待拉请求被合并

希望他们很高兴有人帮助他们，合并你的更改，你现在可以更新你的原始项目，一切都会正常工作。

## 如果他们不合并变化呢？

如果拉请求没有被合并，Go 有一个解决方案。你可以在你的 *go.mod* 中用你的叉子[替换](https://golang.org/ref/mod#go-mod-file-replace)原来的两个参考！

## 实际例子

我有一个项目 [snipgo](https://github.com/nwillc/snipgo) ，使用了 github.com/gdamore/tcell 和 github.com/pgavlin/femto 的*:*

```
*module* github.com/nwillc/snipgo

*go* 1.15

*require* (
  ...
   github.com/gdamore/tcell v1.4.0
  ...github.com/pgavlin/femto v0.0.0-20191028012355-31a9964a50b5
  ...)
```

另外，*github.com/pgavlin/femto***也**用了 *tcell* ，所以在那里也是传递依赖。

当我跑的时候:

```
go get -u github.com/nwillc/snipgo
```

*tcell* 模块更新了，以一种我可以轻松应对的中断方式，但是 *femto* 模块还没有更新。我遵循了上面的六个步骤…*【蟋蟀的声音】*他们没有回应我的拉请求。

## 去模块更换救援！

我不想让自己等着，所以有个解决办法。在我的分叉回购中，我做到了:

```
go list all -m github.com/nwillc/femto
```

这个命令显示了我的 fork 的详细版本信息。接下来，我编辑了我的原始项目的 *go.mod* ,在最后添加了一个替换版本信息:

```
replacegithub.com/pgavlin/femto => github.com/nwillc/femto v0.0.0-20201217031030-474e70183a86
```

那个用我的叉子*github.com/nwillc/femto*把*github.com/pgavlin/femto*给“替换”了。最后，回到 snipgo，我再次尝试:

```
go get -u github.com/nwillc/snipgo
```

维奥拉。现在两个 *tcell* 和 *femto* ，特别是我用叉子替换的 *femto* ，两个都是同一版本，其他的都自己解决了！

## 何必呢？

似乎过分了？何必呢？这条路线有几个主要优点:

1.  你的好公民努力可能仍然有回报，他们可能会合并你的工作。
2.  在他们这么做之前，替换策略允许你**保持你的代码导入不变**，也允许你以一种对使用你的回购的人透明的方式**将变更推送到你的项目的回购。**
3.  当 Pull 请求合并时，您可以轻松地删除 replace。

## 更新

*femto* 回购最终接受了我的 PR，我只是移除了 *replace* ，项目使用原始回购一切正常。
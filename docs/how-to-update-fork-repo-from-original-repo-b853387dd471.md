# 如何从原始回购更新分叉回购

> 原文：<https://levelup.gitconnected.com/how-to-update-fork-repo-from-original-repo-b853387dd471>

## 源代码管理

## 有些事情我们不会每天都做，但是当我们需要它的时候它会很方便

![](img/a92c226eba89ee1944d846480b68a2e3.png)

图片由 [jplenio](https://pixabay.com/users/jplenio-7645255/) 在 [Pixabay](https://pixabay.com/photos/tree-nature-hill-heart-light-glow-3384831/) 上拍摄

你已经创建了一个存储库。一段时间后，您的分叉存储库就过时了。怎么同步呢？

# 更新主分支

## 1.在本地克隆您的 fork 存储库

```
git clone <forked-repository>
```

## 2.将原始回购设置为您的上游回购

```
git remote add upstream <original repo>
```

注意:如果您不知道什么是原始回购，您可以转到原始回购文件夹并键入`git remote -v`

## 3.更新您的本地主机以与原始回购同步

假设您的分叉回购主数据没有被更改，那么您可以执行以下操作

```
git pull upstream master
```

如果你改变了它，那么你需要重新设定它的基数。

```
git rebase upstream/master
```

并解决冲突(如果有的话)。

## 4.通过向上推本地回购更新分叉回购主数据

如果所有东西都相应地更新了，而没有重定基数，那么你可以像下面这样向上推。

```
git push origin master
```

如果你已经重置了它，那么你需要用力推它

```
git push -f origin master
```

# 移开一些树枝

如果你有兴趣从原来的回购协议中引入一些新的分支。您可以执行以下操作(假设您已经完成上述同步主分支等操作)

## 1.从原始回购中提取

```
git fetch upstream
```

## 2.从原始回购中取出分支

```
git checkout <the original repo branch to transfear>
```

## 3.将分支从本地推至分叉回购

```
git push origin <the original repo branch to transfear>
```

对所有感兴趣的分支重复上述步骤 2 和 3。

希望这个小过程有助于您的 fork repo 同步工作。如果需要更多参考资料，请查看[官方费用文档](https://git-scm.com/book/en/v2)
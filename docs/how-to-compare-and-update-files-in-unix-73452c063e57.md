# 如何在 Unix 中比较和更新文件

> 原文：<https://levelup.gitconnected.com/how-to-compare-and-update-files-in-unix-73452c063e57>

## 关于 diff 和 patch 命令的教程

![](img/4572866e2d94ed6cae79bde27c450a8b.png)

照片由[耶鲁安穴獭](https://unsplash.com/@jeroendenotter?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

假设我们想要比较 Unix 中的两个文件。`first.R`和`final.R`分别是:

**首先。R**

```
my_models<-list()

for (s in unique(iris$Species)) {
    tmp<-iris[iris$Species==s,]
    my_models[[s]]<-lm(Sepal.Length~Sepal.Width+Petal.Length+Petal.Width, data=tmp)
}my_models
```

**决赛。R**

```
# create an empty list
# to store the models
my_models<-list()

for (s in unique(iris$Species)) {
    tmp<-iris[iris$Species==s,]
    my_models[[s]]<-lm(Sepal.Length~Sepal.Width+Petal.Length+Petal.Width, data=tmp)
}

# get the 'setosa' model
my_models[['setosa']]
```

# 使用 diff 命令比较文件

在 Unix 中，我们可以如下运行`diff`命令:

```
$ diff -u first.R final.R
```

我们得到了:

```
$ diff -u first.R final.R
--- first.R     2021-02-21 13:47:57.875639200 +0200
+++ final.R     2021-02-21 13:46:59.259101400 +0200
@@ -1,8 +1,11 @@
+# create an empty list
+# to store the models
 my_models<-list()

 for (s in unique(iris$Species)) {
     tmp<-iris[iris$Species==s,]
     my_models[[s]]<-lm(Sepal.Length~Sepal.Width+Petal.Length+Petal.Width, data=tmp)
 }
-
-my_models
\ No newline at end of file
+
+# get the 'setosa' model
+my_models[['setosa']]
\ No newline at end of file
```

其中`--`是指`first.R`，而`++`是指`final.R`。它告诉我们哪些行被添加了，哪些行被删除了。

# 创建差异文件

您可以创建一个`patch`文件，新旧文件的区别如下:

```
$ diff -u first.R final.R > diff_file.patch
```

# 使用 patch 命令更新差异文件

如果您有差异文件，如`diff_file.patch`和初始文件`first.R`，您可以如下创建`final.R`:

让我们创建一个`first.R`的副本

```
$ cp first.R first_mod.R
```

现在我们可以在`first_mod.R`上运行批处理

```
$ patch first_mod.R < diff_file.patch
```

并且`first_mod.R`变得与`final.R`相同

您可以通过键入以下命令进行确认:

```
diff -u first_mod.R final.R
```

最初由[预测黑客](https://predictivehacks.com/?all-tips=how-to-compare-and-update-files-in-unix-with-diff-and-patch-commands)发布
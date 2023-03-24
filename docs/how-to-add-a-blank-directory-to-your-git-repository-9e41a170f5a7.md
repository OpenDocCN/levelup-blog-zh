# 如何向 Git 存储库添加一个空白目录

> 原文：<https://levelup.gitconnected.com/how-to-add-a-blank-directory-to-your-git-repository-9e41a170f5a7>

![](img/f32859ba75f0cb1abdd8d3599d4174ff.png)

有时在 Git 中，我们希望保留一个目录供存储库中使用，但保持它没有文件。你想这么做有很多原因，但是可能是因为**文件夹是用来存储克隆库的人需要创建的文件**，或者**脚本创建定制文件放入文件夹**。

如果我们想创建一个空白目录，我们可以很容易地做到这一点，但当我们使用`git push`推送到我们的遥控器时，它不会被推。因此，我们需要做一些稍微不同的事情。

# 在 Git 存储库中创建一个空白目录

最简单的方法是在您想要维护的目录中创建一个`.gitignore`文件。

```
- index.html
- myRepository <-- Empty directory
- myCss
--- style.css
--- main.css
```

我们希望将`myRepository`推送到我们的 git 库。为此，在`myRepository`中创建一个名为`.gitignore`的新文件。在该文件中，放置以下代码:

```
*
*/
!.gitignore
```

这将**忽略所有文件、文件夹和子目录**，但包括`.gitignore`文件本身。这意味着文件将被推送，我们可以保留目录，而不保留它的任何内容。

接下来，像平常一样通过运行以下命令来推送您的 git 存储库:

```
git add .
git commit -m "Added .gitignore"
git push
```

您的目录现在将保留在您的存储库中，而目录的内容不会。
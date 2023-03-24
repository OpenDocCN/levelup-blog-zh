# 如何建立理想的黑曜石插件开发工作流程

> 原文：<https://levelup.gitconnected.com/how-to-set-up-the-ideal-obsidian-plugin-development-workflow-b222fe72280f>

![](img/dfdde6056b05e8fd78c788ec35581f48.png)

从[https://obsidian.md/](https://obsidian.md/)手中抢过。

像开发网站一样快速开发 Obisidian 插件

开发 [ObsidianMD](https://obsidian.md/) 插件可能会很烦人。这不像开发网站，你所要做的就是运行`npm run dev`，然后看着你的作品出现在`[http://localhost](http://localhost.)` [。](http://localhost.)

你必须做一些设置，然后才能用一个黑曜石插件做类似的事情。

## 步骤 1:创建开发库

要隔离您的环境并确保您的生产保管库的插件和文件不会干扰您正在开发的插件或被您正在开发的插件干扰，最好只为开发创建一个新保管库。

你可以为每个插件创建一个新的库，或者只创建一个来处理所有的插件。

最好将保管库存储在不由云提供商管理的文件夹中，如 iCloud、Google Drive、Onedrive 或 Dropbox。这是因为您将在 vault 的文件中使用 Git。

例子:`user\obsidian\plugin-development-vault`

## 步骤 2:创建你的插件库并将其克隆到你的库的“插件”文件夹中

使用一个像 [GitHub Desktop](https://desktop.github.com/) 这样的应用程序，你可以指定你的本地存储库的位置。

您必须放置存储库的位置是`{your-vault}\.obsidian\plugins`。

在大多数情况下。黑曜石文件夹会被隐藏。

在 Mac 上，你可以使用`shift+command+.`快捷键来取消隐藏文件和文件夹。

在 Windows 上，您可以在这里查看说明[。](https://support.microsoft.com/en-us/windows/view-hidden-files-and-folders-in-windows-97fbc472-c603-9d90-91d0-1166d1d9f4b5#WindowsVersion=Windows_11)

## 步骤 3:在你的插件库中运行"`npm run dev”`

为了让黑曜石识别你的插件，它必须有一个`main.js`文件。当您运行`npm run dev`时，将会生成该文件。

`npm run dev`将一直运行到取消，因此它将接受您在运行时所做的任何更改。

但是，任何时候进行了更改，您都必须退出您的保管库并重新进入以刷新您的插件。

但是这不是理想的开发工作流程。我们希望成为一个现代网站，如果你做了改变，这种改变会立即反映在插件中。

幸运的是，您可以执行这个额外的步骤来获得热重装。

## (额外)步骤 4:将“`pjeby/hot-reload" Repository` ”克隆到你的“插件”文件夹中

到存储库的链接是这里的。

`main.js`文件已经生成，所以您所要做的就是将存储库克隆到其他插件所在的位置。

任何时候你改变你的插件代码，它几乎都会立即反映在你的插件中。对位于`manifest.json`的元信息的更改可能需要关闭并重新打开保险库才能反映出来。

否则，您应该会看到一个警告，提示您热重新加载插件已经在任何时候运行。

现在唯一的警告是，如果你有一个设置标签，你做了改变，你每次都会被踢出那个标签。

## 包扎

这就是你如何让黑曜石插件开发不那么容易引发偏头痛。

如果这篇文章对你的黑曜石插件开发之旅有所帮助，请一定留下掌声。

如果您有任何建议或反馈，请不要犹豫，留下您的评论。
# 如何在你的 Ionic 应用程序中添加新字体？

> 原文：<https://levelup.gitconnected.com/how-to-add-a-new-font-into-your-ionic-app-b7e0871ff5f4>

![](img/7a6b46ddac40755e95639046ef43cf68.png)

照片由[布雷特·乔丹](https://unsplash.com/@brett_jordan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

在典型的应用程序用户界面(UI)中，字体在提供应用程序试图传达的内容方面起着关键作用。毕竟，它是应用程序的关键标识，也是应用程序直接向用户传达信息的方式。它可以唤起一种有趣的感觉，也可以唤起一种粗糙的感觉，甚至是一种严肃和细致的感觉。

大多数用户界面设计者仅仅在字体选择上就花费了无数的时间来寻找一种完美地封装了环境和情绪的字体。

记住这一点，看到字体在应用程序中的重要性，可以在你的 Ionic 应用程序中添加定制字体，我将在本文中向你展示这是如何实现的。请继续阅读:

> 声明:在撰写本文时，我正在使用 Ionic Angular 版本 5 和 Ionic 命令行界面(CLI)版本 6.11.8

**第一步:选择想要使用的字体**

寻找字体的最佳起点是谷歌字体:

[](https://fonts.google.com) [## 谷歌字体

### 通过出色的排版使网页更加漂亮、快捷和开放

fonts.google.com](https://fonts.google.com) 

这个网站上有一大堆字体，而且都是免费的！截至 2020 年 4 月，有超过 884 种字体供您选择。

选择好要使用的字体后，点击*下载系列，将这些字体下载到您的电脑中。*

你的 Ionic 应用程序中可以有不止一种字体，那就下载一些吧。

**步骤 2:将选定的字体放入资产文件夹**

在典型的 Ionic Angular 应用程序中，通常可以在以下位置找到资产文件夹:

```
projectFolder/src/assets/
```

在 assets 文件夹中，创建一个`fonts`文件夹并将下载的字体放在那里可能是个好主意。

您必须首先解压缩字体文件，并将。otf，。ttf，。woff 还是。fonts 文件夹中的 eot 文件。不要就这样放弃。fonts 文件夹中的 zip 文件，这将不起作用。请记得拉开拉链。先压缩文件，然后把里面的文件放到 fonts 文件夹中。

**第三步:将字体导入你的应用**

将以下代码添加到您的`global.scss` 文件中。在这种情况下，我正在导入一个字体名 **Varela** ，它的文件在我的`assets/font` 文件夹中。在下面的例子中，我也添加了另一种叫做 **Lato** 的字体

```
@font-face {   
  font-family: 'Varela';    //This is what we are going to call
  src: url('../assets/fonts/VarelaRegular.ttf');}@font-face {   
  font-family: 'Lato';  //This is what we are going to call  
  src: url('./assets/fonts/LatoRegular.ttf');}
```

**第四步:使用应用程序中的字体**

现在你已经成功地将字体文件添加到你的应用程序中，你现在可以通过内嵌样式或通过`.scss`文件从你的应用程序中的任何地方调用这些字体。例如，我可以在我的`home.page.scss`文件中设置一个类来改变我的`<ion-content>`中的字体，如下所示:

```
ion-content{ 
  font-family:Varela !important;
  font-size:1.1em;
}
```

在上面的样式中，我已经将`<ion-content>`中的所有字体都改成了`Varela`字体(这是之前在`global.scss`中导入的)。现在，将使用 Varela 字体。如果我想为`Lato`字体创建一个特殊的类，我需要添加如下内容:

```
ion-content{ 
  font-family:Varela !important;
  font-size:1.1em;
}.specialDiv{ font-family: Lato !important;
  font-size:1.1em;}
```

在 html 文件中:

```
<ion-content>This will use the Varela font<div class="specialDiv">
   This will use the Lato font
</div></ion-content>
```

# **总结**

好了，你可以把 Ionic 应用程序中的字体改成你手头上的任何字体文件。只需三个简单的步骤。